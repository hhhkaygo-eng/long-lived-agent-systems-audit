## 8. Child-agent supervision test

Failure mode: a parent spawns children, continues other work, and forgets children that are still running, failed, or completed without accepted deliverables.

### 8.1 Fixture

Parent workflow P starts children C1 (long running), C2 (fails), C3 (completes and writes a PR), C4 (completes and only writes a chat summary). Parent then does unrelated work and is killed. New parent session P2 starts from projector only.

### 8.2 Tests

| ID | Question | Observability pass | Policy/enforcement pass |
|---|---|---|---|
| T1 | Awareness while children run | P can query C1–C4 status without remembering them | Spawn without a durable child id is rejected |
| T2 | Terminal vs accepted | C3 = execution complete + PR proposed; C4 = execution complete + **not** accepted | C3/C4 cannot be marked ACCEPTED by the child or parent agent |
| T3 | Orphan detection | After P dies, a list-children API still returns C1–C4 | Alert if a child has no live parent *and* policy ≠ ABANDON |
| T4 | Parent-close | On P completion, C1 is terminated or cancelled unless ABANDON was explicit `[SRC-TEMPORAL-PCP]` | Default terminate; ABANDON requires a new parent obligation id |
| T5 | Recovery after interruption | P2 lists C1 running, C2 failed, C3 delivered-unaccepted, C4 delivered-unaccepted | P2 cannot “start fresh” C3 work without seeing the existing PR |
| T6 | Continue-As-New / session rollover | If the engine drops child pointers on continue `[SRC-TEMPORAL-CHILD]`, the obligation record still has child ids | Projector reads obligation record, not only the new workflow instance |

### 8.3 Observability vs enforcement

These are different failures.

- **Observability problem:** the child ran but nobody can see it. Fix: register children in the workflow engine and surface them in the projector. Commodity.
- **Policy problem:** everybody can see C4 completed, and the agent closes the ticket. Fix: acceptance ACL. Commodity + WRAP.
- **Runtime-support problem:** Claude Code / many Task tools never register children durably. Official Claude Code docs: subagents work within a single session; parent-end and orphans are undocumented. `[FACT]` `[SRC-CC-SUBAGENTS]` OpenHands TaskToolSet: parent blocks until the child completes. `[FACT]` `[SRC-OPENHANDS-TASK]` That avoids orphans by refusing asynchrony; it does not solve unattended long children.

### 8.4 What native runtime support is actually necessary

Minimum native support (already in Temporal/Restate, **not** in typical agent UIs):

1. Durable child id at spawn time
2. Parent close policy
3. Visibility list by parent / by obligation
4. Cancellation
5. Result payload stored independently of parent chat

Not necessary natively: agent-specific “child personality,” child memory OS, or a new registry service.

**Recommendation:** treat unmanaged async subagents as unsafe for unattended long-horizon work. Either (a) make them child workflows, or (b) keep them synchronous and short.

---
