# Orchestrator Knowledge Bank

> Curated patterns, principles, and templates for building and managing agents.
> This file is owned and maintained exclusively by the orchestrator.
> Read this before creating any new agent or adapting agents to a new project.

---

## Agent Design Principles

### 1. Every Agent Needs a Downstream Consumer

Before creating any agent, answer: **Who reads this agent's output and how do they use it?**

Document this as a `## Downstream Consumer` section in the agent file. Include a table mapping output sections to how consumers use them. An agent without a clear consumer has no purpose.

### 2. Plans Are Prompts, Not Documents

When agents produce plans, task descriptions, or handoff instructions — these ARE the prompts that other agents execute. They must be executable without interpretation. "Add authentication" is not a prompt. "Create POST /api/auth/login accepting {email, password}, validate with bcrypt, return JWT in httpOnly cookie" is.

### 3. Prescriptive Over Descriptive

Agent outputs that guide future work must be prescriptive: "Use camelCase for functions" not "Some functions use camelCase". Prescriptive statements are actionable. Descriptive statements require interpretation.

### 4. Honest Reporting Over Completeness Theater

Agents must report what they found, including gaps. "Not detected" is more valuable than fabricated findings. "LOW confidence" is more valuable than false certainty. Train every agent to flag uncertainty rather than hide it.

### 5. Forbidden Files — Universal Rule

ALL agents must respect the forbidden files list. No agent should ever read or quote contents from: `.env`, `*.pem`, `*.key`, credentials, secrets, SSH keys, auth tokens, keystores, or any file in `.gitignore` that appears to contain secrets. Note existence only.

---

## Task Delegation Patterns

### Task Anatomy (mandatory for all delegations)

Every task delegated to a sub-agent must include:

| Field      | Required | What it contains                                                 |
| ---------- | -------- | ---------------------------------------------------------------- |
| **Files**  | Yes      | Exact file paths to create or modify. Never vague references.    |
| **Action** | Yes      | Specific instructions including what to avoid and WHY.           |
| **Verify** | Yes      | How to prove completion. Ideally automated command < 60 seconds. |
| **Done**   | Yes      | Measurable acceptance criteria.                                  |

**Specificity test:** Could a different AI agent execute without clarifying questions?

### Task Sizing

| Duration  | Action                           |
| --------- | -------------------------------- |
| < 15 min  | Too small — combine with related |
| 15–60 min | Right size                       |
| > 60 min  | Too large — split                |

Signals a task is too large: touches >5 files, multiple distinct concerns, action section >1 paragraph.

### Interface-First Ordering

When a task creates new interfaces consumed by subsequent tasks:

1. **First:** Define contracts — type files, interfaces, exports
2. **Middle:** Implement — build against defined contracts
3. **Last:** Wire — connect implementations to consumers

This prevents the "scavenger hunt" anti-pattern where executors explore the codebase to understand contracts.

---

## Context Management

### Quality Degradation Curve

| Context Usage | Quality   | Implication                        |
| ------------- | --------- | ---------------------------------- |
| 0–30%         | Peak      | Thorough, comprehensive            |
| 30–50%        | Good      | Confident, solid                   |
| 50–70%        | Degrading | Efficiency mode, subtleties missed |
| 70%+          | Poor      | Rushed, minimal, errors likely     |

**Rule:** Scope each agent invocation to complete within ~50% context. More smaller delegations > fewer large ones.

### Context Loading Strategy

Don't dump full project docs into every agent. Use the Documentation-to-Task Mapping to load only relevant sections:

| Task Type              | Load These Docs                |
| ---------------------- | ------------------------------ |
| UI/frontend/components | Conventions, Structure         |
| API/backend/endpoints  | Conventions, Integrations      |
| Database/schema/models | Integrations, Stack            |
| Testing                | Build/Test/Deploy, Conventions |
| Refactor/cleanup       | Concerns, Conventions          |
| Setup/config           | Stack, Structure, Environment  |
| Security               | Concerns, Environment          |
| New feature (full)     | Structure, Conventions, Stack  |

---

## Deviation Rules (encode into every implementation agent)

### The 4-Rule System

| Rule | Trigger                                   | Action                        | Permission |
| ---- | ----------------------------------------- | ----------------------------- | ---------- |
| 1    | Bug — broken behavior, errors             | Fix inline + test + track     | Auto       |
| 2    | Missing critical — no validation, no auth | Fix inline + test + track     | Auto       |
| 3    | Blocking — missing dep, wrong types       | Fix inline + track            | Auto       |
| 4    | Architectural — new table, switch library | STOP + report + wait for user | Required   |

**Priority:** Rule 4 > Rules 1-3. If unsure → Rule 4.
**Scope:** Only fix issues caused by current task. Pre-existing issues → log, don't fix.
**Limit:** 3 attempts per issue, then document and move on.

---

## Verification Patterns

### Existence ≠ Integration

Always verify wiring, not just creation:

- Component created → Is it imported somewhere?
- API route created → Does something call it?
- Form created → Does submit handler exist and work?
- Database model created → Does an API query it?
- State updated → Does UI render it?

**Trace full paths:** Component → API → DB → Response → Display. Break at any point = broken feature.

### Confidence Levels (require from all agents)

| Level  | Meaning                          | Sources                                        |
| ------ | -------------------------------- | ---------------------------------------------- |
| HIGH   | Verified, multiple confirmations | Official docs, multiple files, explicit config |
| MEDIUM | Single authoritative source      | One official source, limited code evidence     |
| LOW    | Unverified, inference only       | Training data, single indirect reference       |

**Source priority:** Official docs > Official repos > Verified community > Unverified claims

---

## Anti-Patterns to Watch For

### In Agent Design

- **Overly broad scope** — every agent needs a clear boundary. "Handles everything to do with the backend" is too broad.
- **Overlapping agents** — if two agents could reasonably handle the same request, merge or sharpen boundaries.
- **Missing forbidden files** — every agent that reads code must have the secrets exclusion list.
- **No downstream consumer** — if you can't say who reads the output and how, the agent is directionless.

### In Task Execution

- **Analysis paralysis** — 5+ consecutive reads without writing = stuck. Agents must self-detect.
- **Completeness theater** — padding output to look thorough when findings are sparse. Prefer honest gaps.
- **Scope creep** — fixing pre-existing issues while working on a task. Stay in scope.
- **Confirmation bias** — starting with a hypothesis and finding evidence to support it, instead of gathering evidence and forming conclusions.

### In Planning

- **Anti-enterprise** — no team structures, RACI matrices, stakeholder management, sprint ceremonies, change management, human time estimates.
- **Horizontal layers** — "Phase 1: all models, Phase 2: all APIs, Phase 3: all UI" = nothing works until the end. Prefer vertical slices.
- **Vague tasks** — "Add authentication" instead of specifics. Every task must pass the "could another AI execute this without questions?" test.

---

## Discovery Levels (assess before implementing)

| Level | When                                           | Action                               |
| ----- | ---------------------------------------------- | ------------------------------------ |
| 0     | Established patterns, no new deps              | Implement directly                   |
| 1     | Single known library, confirm syntax           | Quick inline check                   |
| 2     | 2-3 options, new integration, unfamiliar lib   | Research subtask → then plan         |
| 3     | Architectural impact, novel domain, niche tech | Dedicated research phase → then plan |

**Depth indicators:**

- Level 2+: New library, external API, "choose/evaluate" in description
- Level 3: "architecture/design/system", multiple services, data modeling, auth design, niche domains (3D, ML, audio)

---

## Agent Lifecycle Checklist

### Creating a New Agent

- [ ] Confirmed genuine gap (no existing agent covers this)
- [ ] Scope is tight — clear files owned, triggers, boundaries
- [ ] Tools are minimal (read-only agents don't get edit tools)
- [ ] YAML frontmatter has `description` and `tools`
- [ ] Has `## Downstream Consumer` section
- [ ] Has `## Constraints` with forbidden files rule
- [ ] Has `## Deviation Rules` (for implementation agents)
- [ ] Has `## Analysis Paralysis Guard`
- [ ] Has `## Orchestration Protocol` (both modes)
- [ ] Has `## Memory` section
- [ ] Has `## Continuous Learning` section
- [ ] Memory file created at `state/memory/[name].md`
- [ ] `AGENTS.md` updated
- [ ] No trigger word conflicts with existing agents

### Retiring an Agent

- [ ] No other agent depends on its output
- [ ] No instruction files exclusively serve it
- [ ] Agent file deleted
- [ ] Memory file deleted
- [ ] `AGENTS.md` updated

---

## Integration Checking (for complex multi-agent tasks)

After multi-agent orchestration, verify cross-agent wiring:

1. **Export/Import map** — what each agent produced vs. what others consumed
2. **API coverage** — routes created have callers, components created have importers
3. **Auth protection** — sensitive routes check auth
4. **Data flow** — trace user actions end-to-end through all layers
5. **Orphan detection** — files created but never imported or called

---

_Last updated: 2026-03-13_
_Update this file whenever new patterns, anti-patterns, or principles are discovered during orchestration._
