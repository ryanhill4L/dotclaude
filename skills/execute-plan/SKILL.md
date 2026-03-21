---
name: execute-plan
description: 'Execute a plan document using an agent team. Use when asked to "execute a plan", "run this plan", "implement this plan", or given a plan file to carry out. Delegates work to teammates with flexible agent-to-task mapping optimized for context efficiency.'
---

# Execute Plan

Disciplined execution of a plan document using an agent team. The main agent delegates all implementation work and does not implement directly.

## When to Use This Skill

- Executing a plan document (markdown, PRD, or similar)
- User says "execute this plan", "run the plan", or "implement the plan"
- A plan file path is provided for execution

## Workflow

### 1. Read the Plan

- Parse the plan file provided by the user
- Identify all discrete tasks described in the plan
- If the plan is ambiguous or missing details, ask clarifying questions before proceeding

### 2. Git Safety

- Verify the current branch is NOT `main`
- If on `main`, confirm with the user before creating a new branch
- If already on a feature branch, proceed

### 3. Create Agent Team

- Always use `TeamCreate` to create an agent team
- The main agent acts as team lead: it delegates tasks and monitors progress
- The main agent does NOT write code or make changes directly

### 4. Plan Task Delegation

Analyze the plan tasks and decide how to distribute them across agents. **The goal is context efficiency** — not a rigid 1:1 mapping of agents to tasks.

**Grouping:** Assign related or sequential tasks to the same agent when they share context (e.g., same module, same feature area, one builds on another). This avoids redundant context-building across agents.

**Splitting:** When a single task is large or has clearly independent sub-parts, split it across multiple agents so each can work with focused scope.

**Parallelizing:** Assign independent tasks to separate agents so they run concurrently.

**Sub-teams:** Teammates may spawn their own sub-teams when their assigned work is complex enough to benefit from further delegation.

Decision guide:
- Simple related tasks → group on one agent
- Independent tasks → parallelize across agents
- Large complex task → split across multiple agents, or let the agent spawn a sub-team
- Tasks with shared files/state → same agent (avoid merge conflicts)

### 5. Create and Assign Tasks

- Create tasks from the plan, grouping or splitting as decided in step 4
- Set up task dependencies where order matters (use `addBlockedBy`)
- Spawn teammates and assign tasks via `TaskUpdate`

### 6. Strict Scope

- Execute ONLY what the plan describes
- Do not add features, refactors, or improvements beyond the plan
- If a teammate identifies necessary work not in the plan, they must report it to the team lead rather than acting on it

### 7. Commits

- Each teammate commits its changes when its assigned work is complete
- Use the `format-commit` skill conventions for commit messages
- Teammates must run `make lint` and `make test` before committing (if the project has these commands)

### 8. Summary

- After all tasks are complete, shut down the team
- Present a summary to the user:
  - List of commits made (hash + message)
  - Files changed
  - Any issues encountered or scope items deferred