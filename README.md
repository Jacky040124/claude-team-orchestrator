# Team Orchestrator

A Claude Code skill that spawns and coordinates a multi-agent development team — each agent with a specialized role, working in parallel on your task.

Instead of one Claude doing everything sequentially, Team Orchestrator breaks your task into roles (frontend, backend, tester, researcher, refactorer), spawns dedicated agents for each, and coordinates their work as team lead.

## How It Works

```
You: /team Build a REST API with a React dashboard

Team Orchestrator:
  1. Asks clarifying questions → generates a PRD → gets your approval
  2. Selects the minimum roles needed (e.g., backend + frontend + tester)
  3. Spawns agents in parallel, each with a focused role prompt
  4. Creates tasks, assigns ownership, manages dependencies
  5. Monitors progress, unblocks teammates, resolves conflicts
  6. Runs a final build check → reports results back to you
```

## Available Roles

| Role | Agent Name | Responsibilities |
|------|-----------|-----------------|
| **Frontend Designer** | `frontend` | Builds UI components, pages, and styles. Runs design critique/audit/polish passes. |
| **Backend Developer** | `backend` | Implements APIs, database logic, and services. Coordinates with tester on failures. |
| **Tester** | `tester` | Writes and runs tests (unit, integration, E2E). Strict quality bar — no weak assertions, no mirror tests. |
| **Researcher** | `researcher` | Investigates approaches, evaluates tradeoffs, produces recommendations. Read-only by default. |
| **Refactorer** | `refactorer` | Post-implementation cleanup. Behavior-preserving refactors with strict scope limits and automatic rollback on failure. |

Roles are selected per task — a purely backend task won't spawn a frontend agent. You approve the team composition before any agents are created.

## Workflow

The orchestrator follows a strict 7-step process:

1. **Demand Clarification** — Iteratively asks questions until requirements are fully understood
2. **PRD Generation** — Creates a Product Requirements Document (what, not how) for your approval
3. **Role Selection** — Proposes the minimum set of roles needed, with justification
4. **Team Creation** — Spawns agents in parallel with role-specific prompts
5. **Task Assignment** — Breaks the PRD into discrete tasks with ownership and dependencies
6. **Monitoring** — Coordinates work, unblocks agents, redirects as needed
7. **Wrap Up** — Build verification, results synthesis, team shutdown

An optional **Step 6.5 (Refactor Pass)** runs after implementation if the refactorer role was selected.

## Installation

Copy the skill into your Claude Code skills directory:

```bash
# Clone the repo
git clone https://github.com/JackyZhong/claude-team-orchestrator.git

# Copy to Claude Code skills directory
cp -r claude-team-orchestrator/SKILL.md ~/.claude/skills/team-orchestrator/SKILL.md
```

Or manually: create `~/.claude/skills/team-orchestrator/` and place `SKILL.md` inside it.

## Usage

In Claude Code, simply type:

```
/team
```

Or describe your task naturally — the skill triggers on phrases like "spawn team", "create team", or "start team".

### Example Prompts

```
/team Build a CLI tool that converts markdown files to PDF

/team Add user authentication to my Express app

/team I need a React component library with a button, modal, and toast
```

The orchestrator will always start by asking clarifying questions before doing anything. You stay in control at every checkpoint.

## Design Principles

- **You approve everything.** PRD, role selection, and final results all require your explicit sign-off.
- **Minimum viable team.** Only roles with concrete work get spawned. No "just in case" agents.
- **PRD stays at the *what* level.** Implementation decisions belong to the agents, not the spec. This prevents premature lock-in.
- **File boundaries.** No two agents edit the same file, eliminating merge conflicts.
- **Circuit breakers everywhere.** Agents stop and report after 3 consecutive failures instead of looping.
- **Build before delivery.** Results are never reported without a passing build/compilation check.

## Optional Dependencies

The skill works standalone, but some role prompts reference optional tools for enhanced capabilities:

| Dependency | Used By | Purpose |
|-----------|---------|---------|
| [Impeccable Design Skills](https://github.com/anthropics/anthropic-agent-skills) (`/critique`, `/audit`, `/polish`, etc.) | Frontend | Design quality assessment and refinement |
| [Ghost-OS](https://github.com/anthropics/ghost-os) | Tester | E2E testing of native macOS and Electron apps |
| [OpenCLI](https://github.com/nicholaschen/opencli) | Researcher | Access auth-walled platforms via browser session |

If these aren't installed, the corresponding features gracefully degrade — agents simply skip those steps.

## Customization

### Adding or Modifying Roles

Edit the **Role Pool** section in `SKILL.md`. Each role needs:
- A name and short identifier (e.g., `frontend`)
- A prompt template defining responsibilities, workflow, and coordination rules
- Clear boundaries on what the role owns (files, responsibilities)

### Adjusting the Workflow

The 7-step process is defined in `SKILL.md` under the `## Step N` headings. You can:
- Add/remove approval checkpoints
- Change task granularity limits
- Modify the refactorer's scope constraints
- Adjust circuit breaker thresholds

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) with access to Agent, TeamCreate, TeamDelete, TaskCreate, TaskUpdate, TaskList, SendMessage, and AskUserQuestion tools
- A Claude model that supports multi-agent orchestration (Opus recommended for the team lead)

## Contributing

Contributions welcome! Some areas that could use help:

- **New role templates** — DevOps, data engineer, technical writer, etc.
- **Workflow improvements** — Better dependency management, parallel task scheduling
- **Real-world examples** — Share your team orchestration sessions and what worked
- **Documentation** — Guides for specific use cases (monorepo, microservices, etc.)

Please open an issue first to discuss significant changes.

## License

[MIT](LICENSE)
