## 1. Executive verdict

### Already solved (commodity, if you actually use it)

- **Durable process resume after crash** is a solved distributed-systems problem. Temporal, Restate, Inngest, Cloudflare Workflows, and Azure Durable Task all persist step results and resume after failure. `[FACT]` `[SRC-TEMPORAL-UNDERSTAND]` `[SRC-RESTATE-AGENTS]` `[SRC-INNGEST-DOCS]` `[SRC-CF-AGENTS]` `[SRC-AZURE-DTS-AGENTS]`
- **Child process lifecycle, including parent-close behavior**, is solved in durable workflow systems: Temporal Parent Close Policy is `TERMINATE` (default), `REQUEST_CANCEL`, or `ABANDON`. `[FACT]` `[SRC-TEMPORAL-PCP]` `[SRC-TEMPORAL-CHILD]`
- **Accepted code vs proposed code** is solved by Git + protected branches + required reviews + CODEOWNERS. Merge to the protected branch is the acceptance event. `[FACT]` `[SRC-GH-PROTECTED]` `[SRC-GH-CODEOWNERS]`
- **Append-only history of what changed** is solved by Git objects and by workflow event histories. Fowler’s own analogy: the commit log is the event store; the working tree is derived state. `[FACT]` `[SRC-FOWLER-ES]`
- **Least-privilege short-lived machine identity** is solved at two layers: GitHub App installation tokens (optionally scoped) and SPIFFE/SPIRE SVIDs. `[FACT]` `[SRC-GH-APP-TOKEN]` `[SRC-GH-SCOPED-TOKENS]` `[SRC-SPIFFE-CONCEPTS]`
- **Policy enforcement outside the model** is solved: OPA (CNCF graduated), Cedar, GitHub rulesets, Claude Code hooks (`permissions.deny`, PreToolUse). Claude Code official docs state CLAUDE.md is context, not enforced configuration; hooks are the enforcement path. `[FACT]` `[SRC-OPA]` `[SRC-CEDAR]` `[SRC-CC-MEMORY]`
- **Artifact build provenance** is solved for software artifacts by SLSA provenance expressed as in-toto attestations. It records where/when/how something was built. It does not record why, nor that a human accepted it. `[FACT]` `[SRC-SLSA-PROV]` `[SRC-INTOTO-ATT]`
- **Observability of model/tool/agent spans** is a commodity (OpenTelemetry + GenAI conventions + OpenInference). GenAI conventions were still **Development** status as of mid-2026. Traces are evidence of execution, not acceptance. `[FACT]` `[SRC-OTEL-TRACES]` `[SRC-OTEL-GENAI-ATTR]` `[SRC-OTEL-STABILITY-SEC]`
- **Advisory cross-session notes** are solved as files: CLAUDE.md scopes, auto-memory directories, ADRs, issue comments. They persist. They are not authority. `[FACT]` `[SRC-CC-MEMORY]`

### Partially solved

- **Cross-session continuity** is solved for *process* (workflow resume) and *artifacts* (git) and only partially for *intent* (docs/ADRs/issues). Intent files are advisory unless a human or a gate treats them as required. `[INFERENCE]`
- **Task/result contracts** exist for code (PR + CI + review). They do not exist as a uniform primitive for non-code deliverables, decision records, or child-agent outputs. `[INFERENCE]`
- **Human approval boundaries** exist (required reviews, Temporal signals / Restate awakeables / Inngest wait-for-event). Wiring them so an agent cannot self-accept is a configuration/process problem, not a missing engine. `[INFERENCE]` `[SRC-GH-PROTECTED]` `[SRC-RESTATE-AGENTS]`
- **Model/provider replacement** is operationally easy if authority lives outside the model; it is hard if the “brain” is a vendor memory store or a checkpointer blob. `[INFERENCE]`
- **Memory systems** (MemGPT/Letta, Mem0, LangGraph store, Claude auto-memory) persist *recall*. Published evals measure conversational QA (LoCoMo, LongMemEval), not session-replacement recovery of accepted project state. Vendor scores should be treated as `MARKETING` until independently reproduced. `[FACT]` `[SRC-MEMGPT-PAPER]` `[SRC-LOCOMO]` `[SRC-LME-V2]` `[SRC-MEM0-2026]`
- **Agent-framework checkpointing** (LangGraph, Microsoft Agent Framework file/Cosmos checkpoints) snapshots graph state. That is not the same as durable execution. Vendor-adversarial analysis (Diagrid) and Microsoft’s own discussion thread both treat the distinction as real. `[FACT]` `[SRC-LANGGRAPH-PERS]` `[SRC-MS-AF-CP]` `[SRC-MS-AF-DISC]` `[SRC-DIAGRID-CP]`
- **Child-agent supervision inside agent products** is weak. Claude Code subagents are session-scoped; official docs do not specify parent-termination or orphan handling. OpenHands TaskToolSet runs children synchronously and returns a result; async orphan control is not the primitive. `[FACT]` `[SRC-CC-SUBAGENTS]` `[SRC-OPENHANDS-TASK]`
- **Long-horizon task competence** is empirically limited. TheAgentCompany (NeurIPS 2025): Claude 3.5 Sonnet completed 24.0% of workplace tasks fully (34.4% partial-credit score). Later rows in the NeurIPS PDF show higher but still minority full-completion rates (~30%). This is capability, not architecture, but it bounds how little human intervention is “safe.” `[FACT]` `[SRC-TAC-HTML]` `[SRC-TAC-NIPS]`

### Genuinely unsolved (after the scan)

- **A scientific test of session replacement.** No standard benchmark starts Session B with zero chat transcript and scores recovery of accepted / proposed / unfinished / why / authorized-next against an independent ledger. LoCoMo and LongMemEval are not this test. `[FACT]` `[SRC-LOCOMO]` `[SRC-LME-V2]`
- **Uniform “result accepted” for non-code obligations.** Git merge is acceptance for code. There is no equally mature, widely deployed primitive that makes “the agent said done” distinct from “the owner accepted” for arbitrary artifacts, and that a replacement session can query without convention. This is a *seam*, not a platform. `[INFERENCE]`
- **Binding execution-terminal to deliverable-accepted across heterogeneous executors.** Temporal can tell you the child workflow completed. It cannot tell you the output satisfied the contract unless you add an acceptance step. Agent runtimes usually collapse the two. `[INFERENCE]`
- **Guaranteed model compliance with recovered state.** Even a perfect ledger does not force a new model to believe it. Enforcement can deny tools until a structured recovery report validates; it cannot make the model faithful. This is irreducible model unreliability, not a missing database. `[INFERENCE]`
- **Repo vs live-production divergence as an agent-specific problem** is not new and not unsolved: it is GitOps/deploy-vs-HEAD, solved operationally by environment promotion and deploy attestations, unsolved socially whenever humans hotfix prod. `[INFERENCE]`

### Smallest plausible architecture

```
authority     Git protected ref + required review   = accepted state
working       branches / PRs / drafts / worktrees   = proposed state
obligations   tickets/PRs with an explicit state
              machine including ACCEPTED ≠ CLOSED   = unfinished work
execution     Temporal | Restate | equivalent       = resume, retry, children
evidence      workflow history + git log + OTel     = what happened
memory        files / optional retrieval            = advisory only
policy        rulesets + OPA/Cedar + client hooks   = cannot self-accept
identity      GitHub App / SPIFFE short-lived       = least privilege
human         CODEOWNERS / required review / signal = acceptance authority
handoff       deterministic projector of the above  = Session B briefing
```

Do **not** add: agent OS, custom ledger, custom identity, custom artifact store, memory-as-authority, child registry outside the workflow engine, or a second source of truth in chat.

**Invented surface area today:** a WRAP measured in schema + scripts + CI/workflow invariants, not a control plane.

---
