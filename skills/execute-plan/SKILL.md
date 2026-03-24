---
name: execute-plan
description: 'Execute a plan document using an agent team. Use when asked to "execute a plan", "run this plan", "implement this plan", or given a plan file to carry out. The main agent delegates all work to teammates and does not implement directly.'
---

# Execute Plan

Disciplined execution of a plan document using an agent team. The main agent acts as team lead — it delegates all implementation work and does not write code or make changes directly.

## When to Use This Skill

- Executing a plan document (markdown, PRD, or similar)
- User says "execute this plan", "run the plan", or "implement the plan"
- A plan file path is provided for execution

## Workflow

### 1. Read the Plan

- Parse the plan file provided by the user (if no path given, ask for it)
- Identify all discrete tasks described in the plan
- If the plan is ambiguous or missing details, ask clarifying questions before proceeding

### 2. Git Safety

Verify the current branch is NOT `main` or `master`:
```bash
git branch --show-current
```
If on `main` or `master`, **stop immediately** and tell the user to create a feature branch first. Do not proceed.

### 3. Create Agent Team

Use `TeamCreate` to create a team named after the plan (e.g., `plan-<short-description>`). The main agent monitors progress and reacts to issues — it does not implement.

### 4. Plan Task Delegation

Analyze the plan and decide how to distribute work. **The goal is context efficiency** — not a rigid 1:1 mapping of agents to tasks.

**Grouping:** Assign related or sequential tasks to the same agent when they share context (same module, same feature area, one builds on another).

**Splitting:** When a single task is large or has clearly independent sub-parts, split it across multiple agents.

**Parallelizing:** Assign independent tasks to separate agents so they run concurrently.

**Sub-teams:** Teammates may spawn their own sub-teams when their assigned work is complex enough to benefit from further delegation.

**Shared state:** Tasks that touch the same files should go to the same agent to avoid merge conflicts.

Decision guide:
- Simple related tasks → group on one agent
- Independent tasks → parallelize across agents
- Large complex task → split across agents or let the agent spawn a sub-team
- Tasks with shared files/state → same agent

**Agent types:** Check `~/.claude/agents/` for domain-specific agents. Use a matching agent's `subagent_type` when one fits the work. Fall back to `general-purpose` when no specific agent applies.

### 5. Create and Assign Tasks

- Use `TaskCreate` for each task, setting up `addBlockedBy` dependencies where order matters
- Copy the full task text directly into each teammate's prompt — **never make teammates read the plan file themselves**
- Use `~/.claude/skills/execute-plan/teammate-prompt.md` as the template for structuring teammate prompts
- Use `TaskUpdate` to assign tasks to teammates

### 6. Strict Scope

- Execute ONLY what the plan describes
- Do not add features, refactors, or improvements beyond the plan
- If a teammate identifies necessary work not in the plan, they must report it to the team lead rather than acting on it

### 7. Commits

- Each teammate makes exactly **one commit** covering all their assigned work
- Teammates must run `make lint` and `make test` before committing (if the project has these commands)
- Commit messages should clearly describe what was implemented

### 8. Monitor Progress

Track teammate progress through:
- Automatic message delivery from teammates
- `TaskList` to check overall status
- React to any issues or blockers teammates report — reassign work if needed

### 9. Cleanup & Summary

Shut down all teammates with `SendMessage` type `shutdown_request`, then use `TeamDelete` to clean up the team.

Present a final summary:

```
## Execution Summary

### Tasks Completed
- [ ] Task 1: <title> — commit `<sha>` — files: <list>
- [ ] Task 2: <title> — commit `<sha>` — files: <list>

### Issues Encountered
- <any problems, deviations, or deferred scope>

### Files Changed
<consolidated list of all files changed across all tasks>
```

## Key Constraints

- **The main agent does not write code** — delegate everything to teammates
- **Execute ONLY what the plan describes** — no added features, refactors, or improvements
- **One commit per teammate** — covers all their assigned work
- **Never execute on main/master** — always require a feature branch
- **Copy task text into prompts** — never make teammates read the plan file themselves
- **Respect dependencies** — tasks with blockers wait before starting
