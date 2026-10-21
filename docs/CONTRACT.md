# Contract v0.1

The handshake between `server/` (Person A) and `dashboard/` (Person B). Both sides build against this file. **Changing it requires a PR approved by the other person**, and you bump the version above when you do.

Ports: server `3000`, dashboard `3001` (`next dev -p 3001`).
The server allows CORS from `http://localhost:3001` and from `DASHBOARD_ORIGIN`.

> **Ring payloads.** The shapes below are Kin's *internal, normalized* events. The mapping from the real Ring webhook payload to `RingEvent` lives in `server/src/ring/normalize.ts`. Check the Ring API docs for the exact payload fields and signature header before finalizing it.

---

## 1. Types

Each side keeps its own copy of these types (`server/src/contract.ts`, `dashboard/lib/contract.ts`). This file is the source of truth.

```ts
type Source = 'simulated' | 'ring';

interface RingEvent {
  id: string;              // uuid
  ts: string;              // ISO-8601, UTC
  deviceId: string;
  deviceName: string;      // e.g. "Front door", "Kitchen"
  kind: 'motion' | 'doorbell';
  source: Source;          // must always be set, drives the "simulated" label in the UI
}

type Status = 'normal' | 'checkin' | 'escalate';

interface AgentDecision {
  id: string;
  ts: string;
  status: Status;
  reason: string;          // plain English, shown in the reasoning panel
  confidence: number;      // 0..1
  basedOn: string[];       // RingEvent ids the decision used
}

type CheckinState = 'pending' | 'resolved' | 'escalated';

interface Checkin {
  id: string;
  startedAt: string;
  deadlineAt: string;
  state: CheckinState;
  reason: string;
  response?: { ok: boolean; note?: string; at: string };
}

interface Escalation {
  id: string;
  ts: string;
  severity: 'info' | 'urgent';
  reason: string;
  acknowledged: boolean;
}

type ScenarioName = 'normal-day' | 'missed-morning' | 'night-visitor';

interface Snapshot {
  now: string;
  status: Status;
  events: RingEvent[];          // today's events, oldest first
  decisions: AgentDecision[];   // oldest first
  checkin: Checkin | null;
  escalations: Escalation[];
  scenario: { name: ScenarioName; running: boolean } | null;
}

interface ToolCallTrace {
  name: string;
  input: unknown;
  output: unknown;
}
```

---

## 2. REST endpoints (server, used by the dashboard)

| Method | Path | Body | Returns |
|---|---|---|---|
| GET | `/health` | none | `{ ok: true }` |
| GET | `/api/state` | none | `Snapshot` |
| POST | `/api/sim/start` | `{ scenario: ScenarioName, speed?: number }` | `{ ok: true }` |
| POST | `/api/sim/reset` | none | `{ ok: true }` (clears all state) |
| POST | `/api/sim/utterance` | `{ text: string, persona: 'parent' \| 'family' }` | `{ reply: string, toolCalls: ToolCallTrace[] }` |
| POST | `/api/escalations/:id/ack` | none | `Escalation` |

`speed` multiplies simulated time (default `1`, demo uses about `60`).
`/api/sim/utterance` is the simulated Alexa+ surface: the server runs the text through the agent, which calls the same tool handlers the MCP server exposes.

Errors: HTTP 4xx/5xx with `{ error: string }`.

## 3. Live stream (server to dashboard)

`GET /api/stream` is Server-Sent Events. Each message is `event: <type>` plus `data: <json>`.

| Event type | Payload | When |
|---|---|---|
| `snapshot` | `Snapshot` | immediately on connect, and after reset |
| `ring_event` | `RingEvent` | each new event |
| `agent_decision` | `AgentDecision` | each agent decision |
| `checkin_update` | `Checkin` | check-in started, answered, or timed out |
| `escalation` | `Escalation` | family alert raised or acknowledged |
| `scenario_state` | `{ name: ScenarioName, running: boolean }` | scenario starts or ends |

Example:

```
event: agent_decision
data: {"id":"d_01","ts":"2026-10-22T03:10:00Z","status":"checkin","reason":"No kitchen activity by 9am; usual first activity is around 7:30.","confidence":0.82,"basedOn":["e_14","e_15"]}
```

The dashboard must reconnect on drop and rebuild its state from the next `snapshot`.

## 4. Ring ingest (Ring to server, not used by the dashboard)

| Method | Path | Notes |
|---|---|---|
| POST | `/webhooks/ring` | Verify the HMAC signature using `RING_WEBHOOK_HMAC_KEY` **before** parsing. Reject with 401 on mismatch. Normalize to `RingEvent`. |

## 5. MCP tools (Alexa+ to server)

Served at `POST /mcp` over Streamable HTTP (MCP spec 2025-11-25 or later).

| Tool | Input | Output | Purpose |
|---|---|---|---|
| `get_day_summary` | none | `{ text: string, counts: Record<string, number>, lastActivityAt: string \| null, status: Status }` | Family asks "How is the day going?" |
| `get_status` | none | `{ status: Status, checkin: Checkin \| null, lastDecision: AgentDecision \| null }` | Current state |
| `start_checkin` | `{ reason: string }` | `Checkin` | Open a check-in with a deadline |
| `record_checkin` | `{ ok: boolean, note?: string }` | `Checkin` | Parent answers "I'm fine" or "I need help" |
| `escalate_to_family` | `{ reason: string, severity: 'info' \| 'urgent' }` | `Escalation` | Raise a family alert |

Tool descriptions must be written for an LLM reader: say when to call the tool and what it returns.

## 6. Mock mode (dashboard)

`NEXT_PUBLIC_MOCK=1` makes the dashboard read from `dashboard/fixtures/*.json` instead of the server. Fixtures must validate against the types above. Fixture files to provide: `snapshot-normal.json`, `snapshot-checkin.json`, `snapshot-escalated.json`, plus a scripted event sequence for the live feed.

## 7. Scenarios (server, used by the simulator)

| Name | What it shows |
|---|---|
| `normal-day` | Routine activity, agent stays `normal` |
| `missed-morning` | No usual morning activity, agent starts a check-in, no reply, escalates |
| `night-visitor` | Unexpected motion and doorbell at night, agent escalates with context |

Scenarios must be deterministic: same script, same timestamps, same outcome, so the demo video is repeatable.
