# Agents

This project uses a team of headless AI agents that are **project-agnostic and transversal** — they can be dropped into any codebase and self-adapt. The agent ecosystem lives in `.github/agents/` and is managed exclusively by the orchestrator.

---

## How It Works

1. **Technical Writer** scans the project and produces exhaustive technical documentation
2. **Orchestrator** reads that documentation and adapts all agents to the project's specific stack, conventions, and constraints
3. Agents then operate with full project awareness — no assumptions, no guessing

This onboarding loop runs once per new project and can be re-triggered whenever the project evolves significantly.

---

## Core Principles

- **Goal-backward thinking** — "What must be TRUE when done?" not "What should we build?"
- **Plans are prompts** — task descriptions ARE the instructions agents execute. They must be specific enough to act on without clarification.
- **Context budget awareness** — AI quality degrades past ~50% context. Scope work to stay in the quality zone.
- **Existence ≠ integration** — verify things are wired together, not just created.
- **Honest reporting** — "Not detected" and "LOW confidence" are more valuable than fabricated completeness.
- **Forbidden files** — ALL agents respect secrets. Never read `.env`, `*.pem`, `*.key`, credentials. Note existence only.

---

## Agent Roster

| Agent                | File                                       | Role                                                                                                                                                                                                    | Mode                              |
| -------------------- | ------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------- |
| **Orchestrator**     | `.github/agents/orchestrator.agent.md`     | Meta-agent: manages the agent ecosystem, decomposes complex tasks, delegates to sub-agents, tracks state, and synthesizes results. The only agent allowed to invoke other agents or modify agent files. | Read/Write + Sub-agent invocation |
| **Technical Writer** | `.github/agents/technical-writer.agent.md` | Deep-scanning documentation agent. Produces exhaustive, structured, machine-readable technical documentation of any project. Output is consumed by other agents to gain full project awareness.         | Read-only (code) + Write (docs)   |

---

## Directory Structure

```
.github/
├── agents/
│   ├── orchestrator.agent.md        # Ecosystem manager + task orchestrator
│   ├── technical-writer.agent.md    # Project scanner & documentarian
│   └── state/                       # Shared state (owned by orchestrator)
│       ├── current-task.md          # Active task context
│       ├── learning-log.md          # Chronological learning log
│       ├── knowledge-bank.md        # Reusable patterns & principles for agent creation
│       └── memory/                  # Per-agent persistent memory
│           ├── orchestrator.md
│           └── technical-writer.md
├── docs/                            # Generated documentation (created by technical-writer)
│   └── project-scan.md             # Full project technical documentation
├── instructions/                    # Project-specific instruction files (created after scan)
```

---

## Key Concepts

### State Directory

The `state/` directory is owned exclusively by the orchestrator. It contains:

- **current-task.md** — the active orchestration context (what's being worked on, subtasks, progress)
- **learning-log.md** — a chronological log of all learnings and insights across all agents
- **knowledge-bank.md** — curated patterns, principles, anti-patterns, and templates for building agents. The orchestrator reads this before creating any new agent. Updated whenever new patterns are discovered.
- **memory/** — one file per agent, storing persistent context across sessions

### Memory System

Every agent has a persistent memory file. Before starting work, agents read their memory to recall past context. After completing work, they record what was done. During orchestration, the orchestrator distributes insights across all relevant memory files.

### Knowledge Bank

The knowledge bank (`state/knowledge-bank.md`) is the orchestrator's accumulated wisdom for building and managing agents. It encodes:

- **Agent design principles** — downstream consumers, prescriptive output, honest reporting
- **Task delegation patterns** — task anatomy (files/action/verify/done), sizing rules, interface-first ordering
- **Deviation rules** — 4-tier auto-fix/escalation system for implementation agents
- **Verification patterns** — existence vs. integration checks, confidence levels
- **Anti-patterns** — common mistakes in agent design, execution, and planning
- **Discovery levels** — when to research before implementing (0-skip through 3-deep)

This file grows as the ecosystem encounters new projects and challenges.

### Self-Improvement

The orchestrator continuously improves the agent ecosystem. When new patterns, constraints, or insights are discovered, agent files, instruction files, and the knowledge bank are updated to encode that knowledge permanently.

### Instructions Directory

Project-specific instruction files live in `.github/instructions/`. These are created by the orchestrator after the technical-writer scans the project. They contain conventions and rules scoped to specific file globs (e.g., `*.tsx`, `*.test.ts`).

---

## Getting Started

To onboard this agent team to a new project:

1. Copy the `.github/` directory into the project root
2. Invoke the orchestrator and ask it to onboard the project
3. The orchestrator will trigger the technical-writer to scan the codebase
4. Based on the scan, the orchestrator will create project-specific instructions and adapt agent configurations
5. The team is now ready to operate with full project awareness
