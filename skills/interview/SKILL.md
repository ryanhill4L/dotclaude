---
name: interview
description: Conduct an in-depth interview about a topic and write a detailed spec
---

# Deep Interview & Spec Writer

Topic: $ARGUMENTS

## Mission

Use **AskUserQuestion** to probe deeply and uncover what the user hasn't thought about. Don't ask obvious questions—challenge assumptions and surface edge cases.

## Interview Domains (Cover All)

1. **Technical**: Failure modes, dependencies, migration paths, interactions
2. **Data & State**: Source of truth, lifecycle, caching, indexes
3. **UX**: Unhappy paths, loading states, edge cases, mobile/accessibility
4. **Security**: Permissions, audit trail, PII, abuse prevention
5. **Operations**: Monitoring, metrics, rollback, support concerns
6. **Business**: MVP scope, user confusion, what's NOT built, plan B

## Question Style

- ❌ "What do you want?" / "Is X important?"
- ✅ "When X conflicts with Y, which wins?" / "What's the dumbest way a user could break this?" / "It's 2am and this is broken. What happened?"

## Process

1. Read topic/ticket/file
2. Ask 2-4 probing questions per round
3. Follow up on vague answers, go deeper
4. Continue until all domains explored with depth

## Completion

Done when: All 6 domains covered, edge cases enumerated, tradeoffs chosen, user says "I hadn't thought about that" 3+ times.

## Output

Write spec to: **`./.claude/specs/{topic-slug}.md`**

Sections: Overview, Requirements, Non-Requirements, Technical Design, Data Model, User Flows, Edge Cases, Security, Rollout Plan, Success Metrics, Open Questions

---

**Begin interview.** Read topic, then ask hard questions.