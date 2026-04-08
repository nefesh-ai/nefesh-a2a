# Nefesh A2A Agent

A2A v1.0 (Agent-to-Agent Protocol) reference implementation for the Nefesh Human State Agent.

**Docs:** [nefesh.ai/docs/a2a](https://nefesh.ai/docs/a2a) · **Endpoint:** [mcp.nefesh.ai/a2a](https://mcp.nefesh.ai/a2a) · **Agent Card:** [agent-card.json](https://mcp.nefesh.ai/.well-known/agent-card.json)

## What is this?

While [MCP](https://github.com/nefesh-ai/nefesh-mcp-server) handles tool-calling (your agent calls Nefesh), A2A enables agent-collaboration. Other AI agents can communicate with Nefesh as a peer agent, not just as a tool.

## Endpoints

| Endpoint | Method | Description |
|---|---|---|
| `/.well-known/agent-card.json` | GET | Agent discovery (public, no auth) |
| `/a2a` | POST | JSON-RPC 2.0 message endpoint |
| `/a2a/health` | GET | Health check |

**Base URL:** `https://mcp.nefesh.ai`

## Agent Card

The agent card is available at [`https://mcp.nefesh.ai/.well-known/agent-card.json`](https://mcp.nefesh.ai/.well-known/agent-card.json).

See [`agent-card.json`](agent-card.json) for the full card.

## Skills

| Skill | Description |
|---|---|
| `get-human-state` | Stress state (0-100), suggested_action, adaptation_effectiveness |
| `ingest-signals` | Send biometric signals, receive unified state |
| `get-trigger-memory` | Psychological trigger profile (active vs resolved topics) |
| `get-session-history` | Timestamped history with trend (rising/falling/stable) |

## Authentication

Same as the MCP server: `X-Nefesh-Key` header or `Authorization: Bearer` token.

Free tier: 1,000 calls/month, no credit card. [Get a key](https://nefesh.ai/signup).

## Example: message/send

```bash
curl -X POST https://mcp.nefesh.ai/a2a \
  -H "Content-Type: application/json" \
  -H "X-Nefesh-Key: YOUR_KEY" \
  -d '{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "message/send",
    "params": {
      "message": {
        "role": "user",
        "parts": [{
          "kind": "data",
          "data": {
            "skill": "get-human-state",
            "params": {"session_id": "my-session"}
          },
          "type": "application/json"
        }]
      }
    }
  }'
```

Response: A Task object with status, artifacts (state data), and conversation history.

## Supported A2A methods

| Method | Description |
|---|---|
| `message/send` | Send a message, get a completed Task back |
| `message/sendStream` | Send a message with SSE streaming |
| `tasks/get` | Retrieve a task by ID |
| `tasks/cancel` | Cancel a running task |

## How it relates to MCP

Nefesh supports both protocols on the same host:

```
mcp.nefesh.ai
├── POST /mcp          → MCP (Streamable HTTP, 7 tools)
├── POST /a2a          → A2A (JSON-RPC 2.0, 4 skills)
├── /.well-known/mcp.json         → MCP discovery
└── /.well-known/agent-card.json  → A2A discovery
```

Same API key, same backend, same free tier. Choose the protocol that fits your architecture.

## Resources

- [A2A Protocol Specification](https://a2a-protocol.org/latest/specification/)
- [Nefesh MCP Server](https://github.com/nefesh-ai/nefesh-mcp-server)
- [Nefesh Gateway](https://github.com/nefesh-ai/nefesh-gateway)
- [Human State Protocol](https://github.com/nefesh-ai/human-state-protocol)
- [API Documentation](https://nefesh.ai/docs)
- [Live Demo](https://sandbox.nefesh.ai)

## License

MIT
