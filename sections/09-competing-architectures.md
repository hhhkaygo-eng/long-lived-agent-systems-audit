## 9. Competing architectures

Three strongest. One is not primarily an AI-agent framework.

### 9.1 A — Git + tickets + durable workflows + thin WRAP

**Not an agent framework.** This is the recommended architecture (§6).

- **Strengths.** Authority is older than LLMs. Session replacement is a query. Children and HITL already exist. Model swap is cheap. Audit is native. Deletes the control plane.
- **Hidden assumptions.** The org will actually turn on branch protection and put effects in activities. Someone writes the projector and the ACCEPTED state. Humans accept via review. Agents work primarily by changing repo-resident artifacts.
- **Failure modes.** Non-code work left in Slack. Unmanaged subagents. Admin bypass. Continue-As-New losing child pointers if obligation records are sloppy. Humans rubber-stamp PRs.
- **Replacement cost.** Low. Components are independently replaceable (GitHub→GitLab, Temporal→Restate).
- **Falsifier.** A controlled §7 test where Session B systematically cannot recover accepted/proposed/unfinished from Git+tickets+workflows, but *can* recover from a memory-OS or control plane. Or: evidence that required-review acceptance is unworkable for the artifact types you actually produce.

### 9.2 B — Agent-framework graphs with checkpointers (LangGraph / MS Agent Framework / similar)

- **Strengths.** Fast to prototype HITL graphs, time-travel, thread memory. Good DX. Officially supports persistence and HITL. `[SRC-LANGGRAPH-PERS]` `[SRC-MS-AF-CP]`
- **Hidden assumptions.** Declared graph state is complete. Side effects are inside nodes that checkpoint atomically (often false). The checkpointer blob is readable by the next model/vendor. “Fault tolerance” in the docs equals production durability.
- **Failure modes.** Atomicity gap (effect happened, checkpoint did not, or vice versa). `[SRC-DIAGRID-CP]` `[SRC-MS-AF-DISC]` Blob lock-in. Accepted vs working collapsed into one state dict. Children as nested graphs without parent-close. Session B opens the thread and trusts the summary field the previous model wrote.
- **Replacement cost.** High if effects and authority live in the graph state. Low if the graph is only a UI on top of A.
- **Falsifier.** A production incident study showing checkpointed graphs have the same recovery properties as Temporal/Restate on kill-9 mid-tool, *and* a §7 pass where the checkpointer alone (no Git authority) suffices. Until then, use as runtime, not substrate.

Microsoft’s Durable Extension is a migration path from B toward A. `[INFERENCE]` `[SRC-MS-DURABLE-EXT]`

### 9.3 C — Stateful memory runtime (Letta/MemGPT and descendants)

- **Strengths.** Honest about finite context. Self-editing memory is inspectable in principle (blocks with ids). `[SRC-LETTA-BLOCKS]` Best-studied paging design. `[SRC-MEMGPT-PAPER]`
- **Hidden assumptions.** What the agent remembers is what the project is. Persistence of blocks equals persistence of obligations. Evaluations of chat-QA transfer to multi-week software/ops work.
- **Failure modes.** Memory drift becomes “truth.” Different model cannot read the store. No acceptance bit. Children forgotten because they are not memory objects. Vendor benchmark inflation. `[SRC-MEM0-2026]` vs LoCoMo’s own finding that systems still lag humans, especially temporally. `[SRC-LOCOMO]`
- **Replacement cost.** High if the memory DB is the project.
- **Falsifier.** §7 pass with memory wiped and only the memory DB restored — if that beats architecture A, C has a claim. Current published benches do not run that test.

### Honorable mention (not top three)

Claude Code’s file-based memory + hooks is a **partial A** for a single developer: accepted state is still Git, notes are files, enforcement is hooks. It is the least-wrong agent product posture. It is not a multi-week unattended supervisor (subagent lifecycle gaps). `[SRC-CC-MEMORY]` `[SRC-CC-SUBAGENTS]`

---
