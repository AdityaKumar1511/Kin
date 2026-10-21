# Contributing to Kin

Two people, a few days, one hard deadline. This file exists so we never block each other and never fight merge conflicts.

**Hard deadline:** Oct 23, 2026, 12:00 PM PDT = **Oct 24, 12:30 AM IST**
**Our deadlines:** feature freeze **Oct 23, 2:00 PM IST** · submit by **Oct 23, 8:00 PM IST**

Read this file, then `docs/CONTRACT.md` and `docs/TASKS.md`. Those three are the whole process.

---

## 1. Roles and ownership

| Area | Owner | Folders and files |
|---|---|---|
| Backend, agent, MCP server, deployment | **Person A: Adi** (`@AdityaKumar1511`) | `server/`, `scripts/`, `.env.example` |
| Dashboard, design, demo assets | **Person B: Sweety** (`@sg293307-cmd`) | `dashboard/` |
| Shared (both must agree before changing) | Both | `README.md`, `AGENTS.md`, `docs/CONTRACT.md`, `docs/ARCHITECTURE.md` |

**Rule 1: edit only your own area.** If you need a change in the other person's folder, ask them, or open a PR that touches it and tag them. Never "just quickly fix" their files.

**Rule 2: the contract is the handshake.** `docs/CONTRACT.md` defines every endpoint, event type, and MCP tool shared between `server/` and `dashboard/`. Change it only through a PR the other person approves, and post a message when it merges.

**Rule 3: nobody waits on anybody.** The dashboard has a mock mode (`NEXT_PUBLIC_MOCK=1`, fixtures in `dashboard/fixtures/`) that follows the contract. The server has a scripted event simulator (`scripts/`). Build against those until the real thing lands.

---

## 2. Git workflow

- `main` always runs. Never push broken code to it.
- Work on short-lived branches named `a/<task-id>-<slug>` or `b/<task-id>-<slug>`, for example `a/A5-ring-webhook` or `b/B2-event-timeline`.
- One task per branch, one branch per PR. Keep PRs small, ideally under about 300 lines.
- Run `git pull --rebase origin main` before you push and before you open a PR.
- **Merging:**
  - PR touches only your own area: run it locally, then merge your own PR.
  - PR touches a shared file or the contract: needs the other person's approval. If they haven't replied in 30 minutes, ping them.
- Delete the branch after merging.

### Commit messages

```
type(scope): short description
```

Types: `feat`, `fix`, `docs`, `chore`, `refactor`. Scopes: `server`, `dashboard`, `mcp`, `agent`, `ring`, `docs`.
Examples: `feat(ring): verify webhook HMAC signature`, `fix(dashboard): stop SSE reconnect loop`.

Commit small and often. Judges can read the history, so make it look like real work.

---

## 3. Definition of done

A task is done when all of these are true:

1. It runs locally with the documented commands.
2. It follows `docs/CONTRACT.md` (or the contract was updated through a PR).
3. Anything simulated is labelled as simulated in code, UI, and README.
4. No secrets are committed (see section 5).
5. The task's checkbox in `docs/TASKS.md` is ticked in the same PR.
6. If a Ring, Alexa+, MCP, or Bedrock behaviour surprised you, it is in `FRICTION.md`.

---

## 4. Working with AI coding tools ("vibe coding")

We use AI tools heavily, so we use them carefully.

- Point your tool at `AGENTS.md` and `docs/CONTRACT.md` at the start of every session.
- Give it one task ID at a time. Do not ask it to "build the whole backend".
- **Read every generated file before committing it, and run it.** Unreviewed generated code is how demos break at 11 PM.
- Tell the tool to stay inside your folders. If it rewrites files in the other person's area, discard those changes.
- Keep a running list of the AI tools each of us used. Devpost asks for it, and it must be honest.
- Never paste secrets into a prompt.

---

## 5. Secrets and safety

- Config comes from environment variables. Copy `.env.example` to `.env`. **Never commit `.env`.**
- If a key is ever committed or pasted somewhere public, **rotate it immediately**. Deleting the line is not enough, because git history keeps it.
- The Ring webhook endpoint must verify the HMAC signature before doing anything with a payload.
- No real personal data anywhere: all demo people, names, and events are fictional.

---

## 6. The honesty rule

Judges check whether the code actually does what the README and video claim.

- If Ring events are simulated, say so.
- If the Alexa+ surface is a simulator, say so.
- Do not claim a feature in the README, Devpost story, or video unless it exists in the repo.
- Track-required tech must be called in code, not just mentioned: the MCP server entry point, the Bedrock calls, and the Ring webhook handler.

---

## 7. Communication

- Post a short update at the start and end of each work block: what you merged, what you are doing next, what is blocking you.
- **If you are blocked for more than 30 minutes, say so immediately.**
- Do not silently change plans. If scope changes, update `docs/TASKS.md` and tell the other person.
- If we fall behind, cut scope using the cut list in `docs/TASKS.md`. Do not cut sleep before the final day.

---

## 8. Submission ownership

| Item | Owner |
|---|---|
| AWS Builder write-up, Open Source fields, repo licence and visibility | Person A |
| Feedback answers for Bedrock, MCP, Alexa+ | Person A |
| Feedback answers for Ring and the dashboard | Person B |
| Thumbnail and gallery images (3:2) | Person B |
| Demo video: record and edit | Both (B leads editing, A runs the scripted scenario) |
| Friction log | Both, kept in `FRICTION.md` |
| Final Devpost review and Submit | Person A submits, Person B proofreads the preview first |

Both of us must tick the eligibility checkboxes truthfully for ourselves.

---

## 9. Licence

This project is MIT licensed. By contributing you agree your contributions are released under the same licence.
