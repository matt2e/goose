# Goose is getting a new architecture — and a new TUI

We're rearchitecting how goose clients talk to the agent core, and shipping a brand-new terminal UI along the way. Here's what's happening and how you can try it today.

## The problem

Goose currently has two separate paths into the agent: the Rust CLI talks to the agent in-process, while the Electron desktop app goes through `goosed`, a custom REST + SSE server. That split means every new feature has to be wired up twice, and third-party clients have no standard way to connect at all.

## The plan: ACP over HTTP

We're replacing `goosed`'s bespoke API with **ACP (Agent Communication Protocol) over HTTP** — a JSON-RPC 2.0 interface transported over Streamable HTTP. One protocol, one server, every client.

The work is tracked in [#6642](https://github.com/aaif-goose/goose/issues/6642) and breaks down into four phases:

| Phase | What | Status |
|-------|------|--------|
| **1 — Stabilize ACP server** | Production-ready server with session persistence, extensions, streaming | ✅ Done |
| **2 — TypeScript TUI alpha** | Feature-complete terminal UI built on the ACP client | 🚧 In progress |
| **3 — Desktop update** | Electron app migrated to ACP | Planned |
| **4 — Consolidation** | Remove `goosed` and the old Rust CLI; single unified architecture | Planned |

### What ACP gives us

- **Standardized protocol** — JSON-RPC 2.0 methods (`session/new`, `session/prompt`, `session/cancel`, …) with SSE streaming for agent responses, tool calls, and thought chunks.
- **Client portability** — Any ACP-compatible client can drive goose. Build your own IDE plugin, web UI, or bot integration against the same interface.
- **Multi-agent orchestration** — The protocol supports it natively; a prototype already demonstrates single- and multi-agent flows.

## Try the new TUI right now

The TypeScript TUI alpha is available today. It already supports messages, tool calling, syntax-highlighted code, rendered markdown, diffs, and themes. Give it a spin:

```bash
npx @aaif/goose
```

That's it — one command, no install. It will pull down the latest alpha and start an interactive session.

### What's coming next for the TUI

- Extension management (add / remove / configure)
- Session list, resume, and export
- `-t` and `-i` modes for non-interactive / zero-shot use
- MCP features like sampling and elicitation

## Get involved

This is all happening in the open. Follow along or jump in:

- **Tracking issue:** [#6642 — project: goosed to ACP-over-HTTP](https://github.com/aaif-goose/goose/issues/6642)
- **Prototype branch:** `alexhancock/goosed-acp-and-new-cli`
- **Feedback?** Open an issue or drop a comment on #6642 — we'd love to hear how the new TUI works for you.
