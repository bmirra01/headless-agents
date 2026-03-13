# Headless Agents

A **project-agnostic AI agent ecosystem** that scales from simple automation to complex multi-step orchestration. Drop it into any codebase and watch it self-adapt to your project's specific stack, conventions, and constraints.

## The Problem

AI agents are powerful but fragile:

- They make assumptions about your codebase structure
- They don't learn your project's naming conventions or design patterns
- They can't decompose complex tasks without explicit guidance
- Multi-agent coordination requires manual choreography
- Knowledge disappears at the end of each session

## The Solution

**Headless Agents** — a team of AI agents that:

1. **Self-document** your project in machine-readable form
2. **Self-adapt** to your specific conventions and constraints
3. **Self-organize** around complex tasks through a smart orchestrator
4. **Self-improve** by capturing learnings and patterns across sessions
5. **Self-scale** from individual agent work to full ecosystems

No assumptions. No guessing. Full project awareness.

## How It Works

```
┌─────────────────────────────────────────────────────────────┐
│ User asks for a feature / fix / refactor                    │
└───────────────┬─────────────────────────────────────────────┘
                │
        ┌───────▼────────────────────────────────────────────┐
        │ Is this a single task? → Invoke single agent       │
        │ Is this complex/multi-step? → Proceed below        │
        └───────┬──────────────────────────────────────────┬─┘
                │                                          │
    ┌───────────▼──────────────┐              ┌──────────--▼────────┐
    │ NEW PROJECT?             │              │ KNOWN PROJECT?      │
    │ Run Technical Writer     │              │ Use existing docs   │
    │ to scan & document       │              │ (project-scan.md)   │
    └───────────┬──────────────┘              └──────────┬──────────┘
                │                                        │
                └───────────────┬──────────────────────-─┘
                                │
                        ┌───────▼──────────┐
                        │ Orchestrator     │
                        │ Decomposes task  │
                        │ into subtasks    │
                        │ Delegates work   │
                        │ Tracks progress  │
                        └───────┬──────────┘
                                │
                    ┌───────────┼───────────┐
                    │           │           │
            ┌───────▼──┐  ┌─────▼─--─┐ ┌────▼───--┐
            │ Agent A  │  │ Agent B  │ │ Agent C  │
            │ Works in │  │ Works in │ │ Works in │
            │ domain A │  │ domain B │ │ domain C │
            └───────┬──┘  └───────┬──┘ └────┬─────┘
                    │             │         │
                    └─────────────┼────────┬┘
                                  │        │
                    ┌─────────────▼────────▼──────────┐
                    │ All agents update shared memory │
                    │ Records decisions & patterns    │
                    │ Orchestrator publishes learnings│
                    └───────────────────────────────-─┘
```

### The Onboarding Loop

For any new project:

1. **Technical Writer** scans the entire codebase and produces exhaustive, structured documentation (`project-scan.md`)
2. **Orchestrator** reads this documentation and adapts all agents to respect the project's specific:
   - Tech stack (frontend, backend, databases)
   - Naming conventions and file structure
   - Design patterns and architecture
   - Build/test/deploy processes
   - External integrations
   - Known constraints and technical debt
3. Agents operate with **full project awareness** — not assumptions

---

## Core Principles

### 🎯 Goal-Backward Thinking

**"What must be TRUE when done?"** — not "What should we build?"

A task "create auth endpoint" can exist while password hashing is missing. Goal-backward forces us to verify the full picture.

### 📋 Plans Are Prompts

Task descriptions ARE the instructions agents execute. Specificity matters:

- ❌ "Add authentication"
- ✅ "Add JWT auth with refresh rotation using `jose`, store in httpOnly cookie, 15min access / 7day refresh"

### 💭 Context Budget Awareness

Quality degrades past ~50% context. Tasks are sized to stay in the quality zone:

- 0–30% context → Peak performance
- 30–50% context → Solid work
- 50%+ context → Degraded quality

More smaller delegations beats fewer large ones.

### 🔗 Existence ≠ Integration

A component can exist without being imported. An API can exist without being called. We don't just create — we **wire together**.

### 📖 Honest Reporting

"Not detected" and "LOW confidence" are more valuable than fabricated completeness.

---

## Project Structure

```
headless-agents/
├── README.md                               # This file
├── AGENTS.md                               # Agent architecture & roster
│
└── .github/
    ├── agents/                             # Agent ecosystem (owned by orchestrator)
    │   ├── orchestrator.agent.md          # The orchestrator — manages everything
    │   ├── technical-writer.agent.md      # Scans projects & generates docs
    │   │
    │   └── state/                         # Shared orchestration state
    │       ├── current-task.md            # Active task context
    │       ├── learning-log.md            # Chronological log of learnings
    │       ├── knowledge-bank.md          # Reusable patterns & templates
    │       └── memory/                    # Per-agent persistent memory
    │           ├── orchestrator.md
    │           └── technical-writer.md
    │
    ├── docs/                              # Generated documentation
    │   └── project-scan.md               # Full technical scan (auto-generated)
    │
    └── instructions/                      # Project-specific rules (auto-generated)
```

---

## The Agent Ecosystem

### Orchestrator

The **meta-agent** that:

- Manages the entire agent ecosystem
- Decomposes complex tasks into ordered subtasks
- Delegates to specialized agents with full context
- Tracks shared state and progress
- Captures learnings and distributes them across agent memory

**Only the Orchestrator is allowed to:**

- Invoke other agents
- Modify agent files
- Update the knowledge bank
- Write to learning logs

### Technical Writer

A **read-only documentation agent** that:

- Performs exhaustive project scans
- Maps tech stack, architecture, patterns
- Catalogs conventions and constraints
- Produces structured, machine-readable docs
- Feeds project awareness to all other agents

Output consumed by: Orchestrator (for agent adaptation), all agents (for project conventions).

### Expanding the Ecosystem

As the ecosystem grows, specialized agents are created for specific domains (frontend, backend, testing, security, etc.). Each agent has:

- A clear scope and trigger conditions
- Persistent memory for cross-session learning
- Defined constraints and safety rules
- Integration with the shared state system

---

## Quick Start

### 1. Copy the Agent Ecosystem to Your Project

```bash
cp -r .github/ /path/to/your/project/
```

### 2. Onboard the Project

Invoke the orchestrator and ask it to onboard your project:

```
@orchestrator: Please onboard this project. Run the technical-writer to scan the codebase,
then adapt all agents to the project's specific stack and conventions.
```

The technical-writer will scan everything and produce `project-scan.md`. The orchestrator will then:

- Analyze the documentation
- Create project-specific instruction files
- Adapt agent configurations
- Update the knowledge bank with any new patterns

### 3. Work with Agents

Now you can use agents directly:

```
@orchestrator: Implement a dark mode toggle for the UI.
(Orchestrator will decompose and delegate)
```

or for simpler tasks, invoke agents directly:

```
@technical-writer: Please document the current project state in project-scan.md
```

---

## Use Cases

### Individual Developers

- Automate repetitive coding tasks
- Get instant codebase familiarization for new projects
- Maintain consistency across a growing codebase
- Delegate multi-step features with confidence

### AI-Assisted Development

- Pair with AI agents that understand your project's constraints
- Avoid re-explaining your architecture every message
- Capture learnings permanently (not lost between sessions)
- Scale from "write this function" to "implement this feature end-to-end"

### Knowledge Management

- Generate exhaustive, up-to-date technical documentation
- Encode project patterns and conventions permanently
- Track architectural decisions and their rationale
- Onboard new team members (or future-you) faster

### Multi-Agent Coordination

- Complex features that require frontend + backend + tests
- Refactors spanning multiple file types
- Infrastructure + application changes together
- All coordinated by a smart orchestrator that tracks dependencies

---

## Key Files to Read

1. **[AGENTS.md](AGENTS.md)** — Complete architecture and agent roster
2. **[.github/agents/orchestrator.agent.md](.github/agents/orchestrator.agent.md)** — Orchestrator specification
3. **[.github/agents/technical-writer.agent.md](.github/agents/technical-writer.agent.md)** — Technical writer specification
4. **[.github/agents/state/knowledge-bank.md](.github/agents/state/knowledge-bank.md)** — Patterns & principals for building agents

---

## Philosophy

This project is built on the conviction that **AI agents work better when they're project-aware**.

Instead of training agents on every possible codebase or asking users to repeat their context endlessly, we use a **self-documenting, self-adapting approach**:

- Agents scan and understand your specific project
- They learn your conventions and patterns
- They remember decisions across sessions
- They improve their own coordination over time

The result: Agents that work **with** your project, not against it.

---

## Contributing

Improvements to the agent ecosystem are welcome! Areas for collaboration:

- New specialized agents (frontend, backend, testing, security, etc.)
- Better orchestration patterns for complex tasks
- Expanded knowledge bank with domain-specific patterns
- Improved project scanning and documentation templates
- Process improvements and defensive rules

See [AGENTS.md](AGENTS.md#ecosystem-manager-protocol) for agent creation guidelines.

---

## License

This project is open for adaptation and use. See LICENSE for details.

---

## Next Steps

1. **Copy this repo to your project** (or run the agents against your existing codebase)
2. **Invoke the orchestrator** to onboard your project
3. **Start delegating tasks** — simple ones to agents, complex ones to the orchestrator
4. **Watch agents improve** as they capture patterns and learn your conventions

Let the agents handle the mechanical work. You focus on the vision.
