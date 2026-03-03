# ponder

Feature lifecycle CLI for AI-assisted development. Tracks features through structured phases, emits directives for AI agents, and records approvals — no LLM calls, no database, no network. Just state.

## Install

**macOS / Linux:**

```sh
curl -sSfL https://raw.githubusercontent.com/orchard9/sdlc/main/install.sh | sh
```

Override install directory:

```sh
PONDER_INSTALL=/usr/local/bin curl -sSfL ... | sh
```

**Windows:**

```powershell
irm https://raw.githubusercontent.com/orchard9/sdlc/main/install.ps1 | iex
```

Installs `ponder` and an `sdlc` alias pointing to the same binary.

## First Steps

```sh
cd your-project
sdlc init
sdlc ui
```

`sdlc init` creates `.sdlc/`, injects `AGENTS.md`, and installs slash commands for Claude Code, Gemini CLI, and OpenCode. `sdlc ui` opens the dashboard — state is live via SSE, no refresh needed.

Create a feature and see the directive the state machine emits:

```sh
sdlc feature create auth-login --title "OAuth login"
sdlc next --for auth-login --json
```

## Usage

```sh
sdlc init                                   # bootstrap a project
sdlc feature create <slug> --title "..."    # new feature
sdlc next --for <slug>                      # what to do next
sdlc next --for <slug> --json               # machine-readable directive
sdlc artifact draft <slug> <type>           # mark artifact as drafted
sdlc artifact approve <slug> <type>         # approve artifact, advance phase
sdlc task add <slug> --title "..."          # add a task to a feature
sdlc ui                                     # open dashboard (http://localhost:3141)
sdlc ui --port 8080                         # custom port
```

## How It Works

Ponder is a deterministic state machine. Each feature moves through phases:

```
DRAFT → SPECIFIED → PLANNED → READY → IMPLEMENTATION → REVIEW → AUDIT → QA → MERGE
```

At each phase, `sdlc next` tells you exactly what artifact to write or action to take. AI agents read the directive, execute it, and call `sdlc artifact approve` — the machine derives the next phase automatically.

All state lives in `.sdlc/` as plain YAML and Markdown files. Git is the audit trail.

## Agent Integration

Agents use `sdlc next --for <slug> --json` as the oracle — always authoritative on what to do next. After `sdlc init`, slash commands are available in Claude Code (`/sdlc-run`), Gemini CLI, and OpenCode.

```sh
/sdlc-run <slug>     # autonomous run to completion
/sdlc-next <slug>    # one step at a time
/sdlc-status         # project overview
```

## Requirements

- macOS 12+, Linux (x86_64 or aarch64), or Windows 10+
- No runtime dependencies — single static binary
