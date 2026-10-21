# Task board

All times are IST. Tick a box in the same PR that finishes the task. **A** = Person A (Adi), **B** = Person B (teammate).

**Feature freeze: Oct 23, 2:00 PM · Submit by: Oct 23, 8:00 PM · Real deadline: Oct 24, 12:30 AM**

---

## Phase 0: tonight (Oct 21, until about 11 PM). Clear blockers.

| ID | Owner | Task | Done |
|---|---|---|---|
| X0 | Both | Read the Devpost Rules and Resources tabs. Note anything that changes the plan in `docs/ARCHITECTURE.md` (open questions) | [ ] |
| X1 | Both | Confirm each of us meets the eligibility checkboxes (age, jurisdiction, no Amazon or Devpost affiliation) | [ ] |
| A0 | A | AWS account, enable Bedrock model access, record model ID and region | [ ] |
| A1 | A | Register on the Ring developer portal, create an app, note the wait time in `FRICTION.md` | [ ] |
| A2 | A | Alexa developer account. Read the MCP add-on docs. Answer: can an add-on speak proactively? Is the web simulator accessible? | [ ] |
| A2b | A | Public GitHub repo with MIT licence, teammate added as collaborator | [ ] |
| B0 | B | Scaffold `dashboard/` (Next.js, dark-only, port 3001) with `NEXT_PUBLIC_MOCK=1` and fixtures from `docs/CONTRACT.md` | [ ] |

**11 PM gate:** if the Alexa+ simulator or Ring portal is blocked, switch to the fallback (own simulated device, own signed-webhook simulator) and note it in `docs/ARCHITECTURE.md`.

---

## Phase 1: Oct 22 morning. Skeleton and plumbing.

| ID | Owner | Task | Depends on | Done |
|---|---|---|---|---|
| A3 | A | MCP server skeleton: Streamable HTTP at `/mcp`, five stub tools from the contract, tested with the MCP Inspector | A2b | [ ] |
| A4 | A | Deploy the server to a public HTTPS URL, `/health` reachable | A3 | [ ] |
| A5 | A | `/webhooks/ring`: HMAC verification, normalize to `RingEvent`, store | A3 | [ ] |
| A6 | A | Scenario simulator in `scripts/`: signed Ring-format webhooks for `normal-day`, `missed-morning`, `night-visitor` | A5 | [ ] |
| B1 | B | Design tokens and layout shell (dark-only, alive: subtle motion, no template look) | B0 | [ ] |
| B2 | B | Live event timeline component, driven by fixtures | B1 | [ ] |
| B3 | B | Simulated Alexa device component (chat-style, shows tool-call trace) | B1 | [ ] |

---

## Phase 2: Oct 22 afternoon and evening. The brain.

| ID | Owner | Task | Depends on | Done |
|---|---|---|---|---|
| A7 | A | Routine model: baseline from past simulated days (rules) | A6 | [ ] |
| A8 | A | Bedrock agent: structured `AgentDecision` output, validation, one retry | A0, A7 | [ ] |
| A9 | A | Check-in state machine with deadlines and escalation | A8 | [ ] |
| A10 | A | `/api/state`, `/api/stream` (SSE), `/api/sim/*` per the contract | A9 | [ ] |
| B4 | B | Switch dashboard from mock to real SSE (keep mock mode working) | A10 | [ ] |
| B5 | B | Agent reasoning panel (decisions appear live, with the reason text) | B4 | [ ] |
| B6 | B | Family view: status card and escalation banner with acknowledge | B4 | [ ] |

---

## Phase 3: Oct 23 morning. Integrate and harden.

| ID | Owner | Task | Depends on | Done |
|---|---|---|---|---|
| A11 | A | `/api/sim/utterance`: simulated Alexa surface running the real tool handlers | A10 | [ ] |
| A12 | A | Test against the real Alexa+ web simulator if accessible, log results in `FRICTION.md` | A4, A2 | [ ] |
| A13 | A | Error handling, `/api/sim/reset`, make all three scenarios fully deterministic | A10 | [ ] |
| B7 | B | Polish: motion, empty and error states, reconnect handling, responsive | B6 | [ ] |
| B8 | B | Deploy dashboard to Vercel, set `NEXT_PUBLIC_API_URL` | B7 | [ ] |
| X2 | Both | Full run-through of all three scenarios on the deployed stack | A13, B8 | [ ] |

---

## Phase 4: Oct 23 afternoon. Freeze, document, submit.

**2:00 PM FEATURE FREEZE. After this: bug fixes only.**

| ID | Owner | Task | Done |
|---|---|---|---|
| A14 | A | Final README: resolve every `TODO`, flip status markers to match reality | [ ] |
| A15 | A | Devpost technical fields: AWS Builder write-up (services and where in code), Open Source fields, repo link, testing instructions | [ ] |
| A16 | A | Friction log finalized, linked from Devpost | [ ] |
| B9 | B | Thumbnail (3:2, 1500x1000, under 5 MB) and 3 to 5 gallery images | [ ] |
| X3 | Both | Record the demo video (under 3 minutes, public, English), then upload to YouTube | [ ] |
| X4 | Both | Write the Devpost project story and the five feedback answers (split per CONTRIBUTING.md section 8) | [ ] |
| X5 | Both | Check the Devpost preview, tick eligibility boxes, list AI tools honestly, then **Submit by 8 PM** | [ ] |

---

## Cut list (drop in this order if we fall behind)

1. Real Ring account integration (keep simulated, labelled)
2. Real Alexa+ web simulator testing (keep our simulated device)
3. Streaming reveal of agent reasoning (show it statically)
4. `night-visitor` scenario (keep the other two)
5. Responsive layout polish (desktop only)

**Never cut:** the Ring-to-agent-to-MCP loop, the deployed public URL, the README honesty section, the video.

---

## Demo storyboard (under 3:00, best material first)

| Time | Beat |
|---|---|
| 0:00 to 0:15 | One-sentence problem: a parent lives alone and family only hears about problems too late, or gets noisy false alarms |
| 0:15 to 0:45 | `normal-day`: events stream in, the agent stays calm and explains why |
| 0:45 to 1:30 | `missed-morning`: no activity, agent starts a check-in, simulated Alexa prompt, no answer, family alert |
| 1:30 to 2:10 | Family asks Alexa+ "How is the day going?" and the MCP tool calls are visible on screen |
| 2:10 to 2:40 | Architecture in one slide: Ring events, Bedrock agent, MCP server. State clearly what is simulated |
| 2:40 to 3:00 | Why it matters and what comes next |

No third-party logos or copyrighted music.

---

## Submission checklist

- [ ] Repo is public with a visible MIT licence (or private and shared exactly as the Devpost form says)
- [ ] Repo calls the track tech in code: MCP server entry point, Bedrock calls, Ring webhook handler
- [ ] README states what is simulated
- [ ] Video: public, English, under 3 minutes, best material first
- [ ] Feedback answers 1 to 5 written from real experience
- [ ] Friction log linked (up to 10% judging bonus)
- [ ] Tracks and mini challenges selected in the form
- [ ] AI tools listed honestly
- [ ] Teammate is on the team in Devpost
- [ ] Preview checked on Devpost, then submitted
