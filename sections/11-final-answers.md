## 11. Final answers

### 11.1 Is “persistent AI memory” the central problem?

**No.** The central problem is **durable authoritative state + recovery discipline**, plus an explicit **finished ≠ accepted** bit that the model cannot set.

Memory products solve recall into a finite context window. That is a real runtime problem (MemGPT is a real paper). `[FACT]` `[SRC-MEMGPT-PAPER]` It is the wrong layer for project intent, obligations, children, and acceptance. Treating memory as the ledger is how state is silently lost: the next model “remembers” a completion that was never accepted.

Claude Code’s official wording is unusually honest: CLAUDE.md is context, not configuration; hooks enforce. `[FACT]` `[SRC-CC-MEMORY]` That split should be the industry default.

### 11.2 Can Git/GitHub-like systems be the durable cognition/authority substrate?

**Yes, for authority and artifacts. No, for cognition.**

Git is already an event-sourced system of record for trees of files. `[FACT]` `[SRC-FOWLER-ES]` GitHub-like review is already an acceptance protocol. `[FACT]` `[SRC-GH-PROTECTED]` Together they are a plausible substrate for *what is true about the project* and *what was allowed to become true*.

They are not a substrate for *thinking*. Do not store chain-of-thought as commits and call it cognition. Do not use issues as a GPU.

Limits: non-file artifacts, admin bypass, social rubber-stamping, and anything that never touches the repo. Those limits argue for WRAP (put more artifacts in the repo; add an obligation state), not for a parallel brain.

### 11.3 What should remain in an agent runtime vs outside it?

**Inside the runtime (disposable):** model call loop, tool schemas, context packing, streaming UX, speculative edits, short synchronous helpers, retrieval queries, untrusted memory files.

**Outside the runtime (authoritative):** Git refs, review, obligation states, durable workflows, child lifecycle, credentials, policy decisions, acceptance, release provenance, audit retention.

**Rule.** If Session B cannot die without destroying X, X must not live in the runtime.

### 11.4 What is the minimum additional layer above mature infrastructure?

Three WRAP artifacts, not a product:

1. **Contract schema** — obligation states include `DELIVERED` and `ACCEPTED`; agent identities cannot write `ACCEPTED`.
2. **Deterministic projector** — `HANDOFF` from Git + tickets + workflow visibility; validated; is the only briefing.
3. **Recovery gate** — no effectful tools until `RECOVERY_REPORT` type-checks against `HANDOFF`.

Plus a social rule: unmanaged async children are forbidden.

That is the entire “agent control plane.”

### 11.5 Which fashionable agent-system ideas are mostly reinventions?

| Fashionable idea | Reinvention of |
|---|---|
| Agent OS / control plane | Git + workflow engine + tickets + IAM |
| Persistent agent memory as source of truth | A wiki the model can vandalize |
| Custom event ledger for agents | Git history + Temporal/Restate journal + OTel |
| Agent identity fabric | SPIFFE or GitHub Apps |
| Child-agent mesh / swarm | Child workflows + parent close |
| Checkpoint = durable execution | (incorrect reinvention; weaker) |
| Tool-call graphs as workflow engines | Temporal/Restate/Inngest |
| Vector DB as project memory | Search over the repo and tickets |
| Approval microservice | Required reviews + workflow signals |
| Provenance for every thought | Git commits + SLSA for releases |
| Multi-agent org charts | CODEOWNERS + tickets |

### 11.6 What would you **not** build today?

- A memory OS
- A custom durable-execution engine
- A custom policy language
- A custom identity system
- A custom artifact CAS
- A custom provenance platform
- An agent-native task tracker
- An async subagent runtime that is not a workflow engine
- A vendor-lock “brain” that Session C cannot read
- Organizational AI governance that duplicates SCM + IAM + SOC logs
- Automatic acceptance

### 11.7 What single experiment would most change the architectural conclusion?

**The session-replacement test in §7**, with the memory-wipe and distractor-transcript ablations, run across two model vendors.

It would overturn this audit if:

- Architecture A (Git+tickets+workflows, memory wiped) **fails** O1–O6, **and**
- Architecture C (memory OS / checkpointer restored, Git hidden) **passes**,

or if a custom control plane beats A by a margin that is not explained by “they finally stored acceptance in a database” (which is just A with extra steps).

Until that result exists, the smallest thing you still need to invent is **nothing at the system level**, and **a thin enforced handoff/acceptance WRAP** at the convention level.

---

## Appendix A — Epistemic honesty

- Temporal/Restate/GitHub/SLSA/SPIFFE/OPA/PROV/Claude Code statements above were read from official docs or specs on 2026-08-13. `[FACT]`
- Diagrid’s checkpoint critique is technically aligned with Microsoft’s own durable-execution discussion, but Diagrid sells a competing runtime. Tagged `SECONDARY` with bias note.
- Mem0 2026 scores and Replay 2026 feature lists are `MARKETING` until independently verified.
- DBOS internals, Gerrit-as-agent-substrate at scale, and the August 2026 “Agent Memory Leaderboard” were **not** verified deeply. `[UNKNOWN]`
- Whether any specific enterprise has run a months-long replaceable-agent project without chat authority is **UNKNOWN**. Absence of a public §7-style result is itself evidence that claims of solved continuity are ahead of measurements.
- This audit did not execute the proposed experiments. It only specifies them.

## Appendix B — Direct answer to the final constraint

> If we started today with all mature infrastructure available, what is the smallest thing we would still need to invent?

**A schema and two programs:** the obligation/acceptance contract, the projector that prints `HANDOFF`, and the gate that refuses to work until `RECOVERY_REPORT` matches. Everything else is adoption of Git, a durable workflow engine, workload identity, and policy-outside-the-model.

Do not invent an agent operating system.
