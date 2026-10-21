# Project context
Kin: caretaking agent. Ring events (webhooks) -> Bedrock agent -> Alexa+ MCP server -> live dashboard.

## Stack
- server/: TypeScript, official MCP SDK, Streamable HTTP, AWS SDK v3 (Bedrock)
- dashboard/: Next.js, dark-only UI, no theme toggle, live motion

## Rules
- Never commit secrets; read config from env vars.
- Anything simulated (Ring events, Alexa device) must be labeled as simulated in code, README, and UI.
- Every feature claimed in the README must exist in code.
- Small commits, one feature each.