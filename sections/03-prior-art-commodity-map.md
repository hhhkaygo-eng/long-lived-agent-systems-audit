## 3. Prior-art / commodity map

For every requirement, the strongest existing mechanism. Not limited to AI-agent frameworks.

### 3.1 Git / version control / code review

| Candidate | Mechanism | Guarantees | Does not guarantee | Maturity | Cost | Verdict |
|---|---|---|---|---|---|---|
| Git objects + refs | Content-addressed blobs/trees/commits; refs name tips | Bit integrity of history; rewind/audit; working tree ≠ committed | Semantic correctness; which ref is “accepted”; intent | Extremely high | Low | **ADOPT** as artifact history and working/accepted split |
| Protected branch + required reviews | Merge into protected ref only via approved PR | Collaborators cannot push accepted state without review (unless admin bypass) `[SRC-GH-PROTECTED]` | Admins can bypass; reviewers can be wrong; non-code artifacts not covered unless they live in the repo | High | Low | **ADOPT** as acceptance event for repo-resident artifacts |
| CODEOWNERS | Path → required reviewers | Path-scoped human authority `[SRC-GH-CODEOWNERS]` | “Any one owner suffices”; CODEOWNERS file itself needs an owner | High | Low | **ADOPT** |
| GitHub Issues / PR state | Tickets, labels, reviews, checks | Visible obligations if used | Agent-closed ≠ accepted; no native ACCEPTED state distinct from CLOSED | High | Low | **WRAP** with an explicit state machine |
| Gerrit | Review + submit as a gate | Stronger “submitted == accepted” culture than GitHub in some orgs | Same non-code gap; operationally heavier | High in specialist orgs | Medium | **ADOPT** if already present; do not introduce for agents |
| ADRs / RFC files in repo | Dated, reviewable intent | Intent is versioned next to code | Compliance; completeness | High (process) | Low | **WRAP** as intent store |
| Git worktrees / branches per session | Isolated working state | Session dirt does not mutate accepted ref | Orphan branches accumulate | High | Low | **ADOPT** |
| jj / Jujutsu | Alternative VCS UX on Git backend | UNKNOWN as agent substrate at scale | UNKNOWN | Growing | Low–med | **DEFER** |

Fowler, 2017: “The log of all the commits is the event store and the working copy of the source tree is the system state.” `[FACT]` `[SRC-FOWLER-ES]`

This is the correct mental model. Chat is not an event store.

### 3.2 Durable workflow / execution engines

| Candidate | Mechanism | Guarantees | Does not guarantee | Maturity | Cost | Verdict |
|---|---|---|---|---|---|---|
| Temporal | Event history; workflow replay; activities; signals/queries/updates; child workflows; parent close policy | Crash-proof *code* execution; timers; child lifecycle; days–years `[SRC-TEMPORAL-UNDERSTAND]` `[SRC-TEMPORAL-CHILD]` `[SRC-TEMPORAL-PCP]` | Semantic correctness; human acceptance; artifact meaning; Continue-As-New drops child pointers from the new parent instance `[FACT]` | High | Medium (ops) or Cloud | **ADOPT** for long-running process, children, HITL waits |
| Restate | Journal of `ctx.run` / durable model calls; virtual objects; awakeables | Replay of journaled steps; LLM responses not re-fetched if wrapped `[SRC-RESTATE-AGENTS]` | Unwrapped steps; acceptance; intent | High and rising | Lower ops than self-hosted Temporal | **ADOPT** as alternative to Temporal |
| Inngest | `step.run` memoization; wait-for-event | Step-level retry/resume `[SRC-INNGEST-DOCS]` | Same non-semantic limits | High for jobs | Low–med | **ADOPT** for lighter workflows |
| Cloudflare Workflows | Steps on Workers | Interruption survival for multi-step jobs `[SRC-CF-AGENTS]` | Same | GA (vendor claim 2025–26) `[SRC-INNGEST-AGENTS]` treat date as SECONDARY | Low if already on CF | **ADOPT** if platform-aligned |
| Azure Durable Task / Durable Functions | Checkpoint every state transition | Same class as Temporal `[SRC-AZURE-DTS-AGENTS]` | Same | High on Azure | Medium | **ADOPT** if Azure-aligned |
| Dapr Workflows | Durable workflow runtime | Same class (vendor claims) | Same | Medium–high | Medium | **ADOPT** if already on Dapr |
| Kubernetes Jobs / CronJobs / Argo Workflows | Batch/DAG | Start/complete/retry of *pods* | Fine-grained LLM-step journal; HITL; parent-close semantics for agent children | High | Medium | **WRAP** for coarse jobs; **REJECT** as the only executor |
| DBOS | Workflows in DB transactions | UNKNOWN at the same bar as Temporal without deeper source read this pass | UNKNOWN | Emerging | UNKNOWN | **DEFER** |

**Disagreement (checkpoints vs durable execution).** LangGraph official docs: checkpointers persist thread state and enable fault tolerance, HITL, time travel. `[FACT]` `[SRC-LANGGRAPH-PERS]` Diagrid (Dapr vendor) argues checkpoint-after-node leaves an atomicity gap between side effect and checkpoint, unlike journaled durable execution. `[SECONDARY]` `[SRC-DIAGRID-CP]` Microsoft maintainers discuss the same gap in Agent Framework #1092. `[FACT]` `[SRC-MS-AF-DISC]` Microsoft now ships a separate Durable Extension / Durable Task path, which is an implicit admission that framework checkpoints were not enough. `[INFERENCE]` `[SRC-MS-DURABLE-EXT]` `[SRC-AZURE-DTS-AGENTS]`

Verdict on LangGraph/MS AF checkpoints: **REJECT as the durability layer**. **WRAP** as UI/dev state if a real workflow engine owns production effects.

### 3.3 Agent runtimes and frameworks

| Candidate | Mechanism | Guarantees | Does not guarantee | Maturity | Cost | Verdict |
|---|---|---|---|---|---|---|
| Claude Code memory + hooks | CLAUDE.md scopes; auto-memory files; hooks; permissions | Files persist; hooks can deny tools; official: memory is not enforcement `[SRC-CC-MEMORY]` | Compliance; cross-machine authority; child orphans `[SRC-CC-SUBAGENTS]` | High in coding workflows | Low | **ADOPT** files+hooks as advisory+enforcement; **REJECT** memory as truth |
| Claude Code subagents | Isolated context; optional memory dir; session-scoped; transcripts retained then deleted | Isolation; result returned to parent | Parent-close; orphans; cross-session child resume (docs silent) `[SRC-CC-SUBAGENTS]` | Product-mature, spec-thin on lifecycle | Low | **WRAP** only if children are also registered in a durable workflow; else **REJECT** for long-horizon unattended work |
| OpenHands TaskToolSet | Parent blocks; TaskManager creates/resumes sub-agent conversation; returns result `[SRC-OPENHANDS-TASK]` | Synchronous completion or parent waits | Native async orphan policy; acceptance | Research + product | Medium | **WRAP** same as above |
| OpenAI Agents SDK | Sessions, handoffs, tracing, HITL pause `[SRC-OAI-AGENTS]` `[SRC-OAI-AGENTS-GUIDE]` | In-run loop, traces, approval pauses | Durable multi-day process; accepted project state | High as SDK | Low | **ADOPT** as in-session runtime; **REJECT** as authority store |
| Letta / MemGPT | OS-like memory tiers; self-editing blocks persisted in DB `[SRC-MEMGPT-PAPER]` `[SRC-LETTA-GH]` | Recall across turns if the product works as advertised | That recall is true; that it is accepted; portability | Research-famous; productizing | Medium | **DEFER** as optional recall; **REJECT** as project truth |
| LangGraph + store | Thread checkpoints + cross-thread KV `[SRC-LANGGRAPH-PERS]` | Snapshot of *declared* state | Durable effects; acceptance | High in LLM apps | Low–med | **REJECT** as control plane |
| “Agent OS / control plane” products | Usually all of the above plus a UI | Marketing continuity | Independently verified months-long authority | Mixed | High | **DELETE** until a specific gap survives this audit |

### 3.4 Policy, identity, provenance, observability, stores

| Candidate | Mechanism | Guarantees | Does not | Maturity | Cost | Verdict |
|---|---|---|---|---|---|---|
| OPA / Rego | General policy engine, CNCF graduated `[SRC-OPA]` | Deterministic allow/deny on structured input | That you call it on every effect | High | Medium | **ADOPT** for org-wide invariants |
| Cedar | Authorization language + optional formal analysis `[SRC-CEDAR]` | Deterministic authz | Same | High (AWS-backed) | Low–med | **ADOPT** if authz-shaped |
| GitHub rulesets / branch protection | Platform policy | Merge gates `[SRC-GH-PROTECTED]` | Non-GitHub effects | High | Low | **ADOPT** |
| Client hooks (Claude Code PreToolUse etc.) | Local intercept | Deny regardless of model `[SRC-CC-MEMORY]` | Remote/unhooked executors | High | Low | **ADOPT** |
| SPIFFE/SPIRE | Workload identity, short-lived SVIDs `[SRC-SPIFFE-CONCEPTS]` `[SRC-SPIRE-CONCEPTS]` | Cryptographic workload ID | Authorization policy (pair with OPA) | CNCF graduated | Medium | **ADOPT** for multi-service; overkill for a single GitHub App |
| GitHub App installation tokens | 1-hour tokens; optional extra scope-down `[SRC-GH-APP-TOKEN]` `[SRC-GH-SCOPED-TOKENS]` | Non-user machine identity; least privilege if scoped | Identity outside GitHub | High | Low | **ADOPT** as default coding-agent identity |
| SLSA + in-toto + Sigstore | Signed provenance for builds `[SRC-SLSA-PROV]` | Where/when/how built, by which builder | Intent, acceptance, semantic correctness `[FACT]` | High for releases | Medium | **ADOPT** for release artifacts; **DEFER** for every agent file write |
| W3C PROV-DM (2013) | Entity / Activity / Agent model `[SRC-PROV-DM]` | Interchange vocabulary | An implementation; signatures | W3C Rec | Low as vocabulary | **WRAP** if you need interop language; do not build a PROV platform |
| OpenTelemetry + GenAI semconv | Traces/spans; `invoke_agent`, `execute_tool`, `chat` `[SRC-OTEL-GENAI-ATTR]` | Portable telemetry if emitted | Stability (Development as of 2026) `[SRC-OTEL-STABILITY-SEC]`; not authority | High (OTel); GenAI still developing | Low–med | **ADOPT** OTel; treat GenAI names as moving |
| OpenInference | AI span conventions on OTel `[SRC-OPENINFERENCE]` | Practical instrumentors | Vendor-led (Arize); not a source of truth | High in LLM ops | Low | **WRAP** |
| Object store + hashes (S3/OCI) | Content-addressed artifacts | Bit integrity | Meaning, acceptance | High | Low | **ADOPT** for large blobs; Git for text/code |
| Vector DB / Mem0 / Zep | Retrieval | Probabilistic recall | Truth | Mixed | Low–med | **DEFER**; **REJECT** as authority |
| LangSmith / Langfuse | Vendor traces + evals | Debugging | Completeness; legal hold | High | Medium | **ADOPT** optionally on top of OTel |

### 3.5 Scientific evaluation of “long-term memory/continuity”

| Candidate | What it actually measures | What it does not | Verdict |
|---|---|---|---|
| MemGPT (2023) `[SRC-MEMGPT-PAPER]` | Document analysis beyond context; multi-session *chat* persona consistency | Project authority, acceptance, child supervision | Useful paper; not a continuity architecture |
| LoCoMo `[SRC-LOCOMO]` | Very long conversational memory QA; humans still far ahead on temporal reasoning; long-context models hallucinate on adversarial items | Session replacement without transcript; accepted vs proposed | **ADOPT** as a recall bench; **REJECT** as a continuity bench |
| LongMemEval / LME-V2 `[SRC-LME-V2]` | Long-term agent memory from trajectories; LME-V2 best reported 72.5% vs RAG 48.5%; coding-agent-as-memory-controller is competitive | Authority recovery; months-long project intent | Same as LoCoMo |
| Mem0 2026 blog `[SRC-MEM0-2026]` | Vendor: 92.5 LoCoMo / 94.4 LongMemEval | Independent reproduction | **MARKETING** until replicated |
| TheAgentCompany `[SRC-TAC-HTML]` `[SRC-TAC-NIPS]` | Multi-hour workplace tasks with tools and coworkers; best full completion ~24–30% | Session B with wiped chat; acceptance vs finished | **ADOPT** as capability ceiling evidence |
| Agent Memory Leaderboard (Aug 2026 social) | Rankings of memory products | Unverified here | **UNKNOWN** |

**Conclusion of §3.** There is no missing platform. There is a missing *convention + enforcement* that reuses Git, tickets, durable workflows, and policy engines, and that refuses to treat memory products as ledgers.

---
