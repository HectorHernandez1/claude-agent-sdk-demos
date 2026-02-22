# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a collection of demo applications showcasing the [Claude Agent SDK](https://docs.anthropic.com/en/docs/claude-code/sdk/sdk-overview). Each demo is self-contained with its own dependencies, build system, and README. These are **local development only** demos — not for production deployment.

## Demo Applications

| Demo | Stack | Package Manager | Run Command |
|------|-------|----------------|-------------|
| `hello-world/` | TypeScript, Node.js | npm | `npx tsx hello-world.ts` |
| `hello-world-v2/` | TypeScript, Node.js | npm | `npx tsx v2-examples.ts [basic\|multi-turn\|one-shot\|resume]` |
| `resume-generator/` | TypeScript, Node.js, docx | npm | `npm start "Person Name"` |
| `simple-chatapp/` | Express, React, WebSocket | npm | `npm run dev` (ports 3001 + 5173) |
| `email-agent/` | Bun, React, SQLite, IMAP | bun | `bun run dev` |
| `excel-demo/` | Electron, React, Webpack | npm | `npm start` |
| `research-agent/` | Python, UV | uv | `uv run python research_agent/agent.py` |

**Important**: `email-agent` uses **Bun** exclusively — do not use npm/node there. `research-agent` uses **UV** for Python dependency management.

## Architecture Patterns

### SDK Integration Approaches
- **CLI tools** (`hello-world`, `resume-generator`): Direct `query()` calls with tool lists
- **Web servers** (`simple-chatapp`, `email-agent`): SDK wrapped in WebSocket handlers for real-time streaming to React frontends
- **Multi-agent** (`research-agent`): Lead agent delegates to parallel subagents via the Task tool
- **V2 Session API** (`hello-world-v2`): `unstable_v2_createSession()` / `unstable_v2_resumeSession()` for multi-turn conversations
- **Desktop** (`excel-demo`): Electron app with SDK in the main process

### Hook System
Demos use `PreToolUse` hooks to sandbox file operations — restricting `Write`/`Edit` tool calls to `agent/custom_scripts/` directories. This pattern appears in `hello-world` and `email-agent`.

### Custom MCP Servers
`email-agent` provides custom MCP servers for email operations (search, read) injected via the `mcpServers` config option.

### Settings Sources
`resume-generator` uses `settingSources: ['project']` to load skills from `.claude/skills/` — enabling the docx skill for Word document generation.

## Per-Demo Notes

- `simple-chatapp` uses **in-memory storage** — data is lost on restart
- `email-agent` stores emails in **SQLite** and requires IMAP credentials via environment variables
- `excel-demo` has a complex **Webpack** build (Electron React Boilerplate) with separate configs for main/renderer/preload
- `research-agent` outputs to `files/` directory: `research_notes/`, `data/`, `charts/`, `reports/`

## Core Dependency

All TypeScript demos depend on `@anthropic-ai/claude-agent-sdk`. The research-agent uses the Python equivalent `claude-agent-sdk`.
