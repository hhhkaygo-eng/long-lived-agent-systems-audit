## 4. “Delete 80%” test

Hypothetical proposal: a custom long-horizon **agent operating system / control plane** with its own project truth, event ledger, task tracker, durable execution, scheduler, child registry, policy engine, credential broker, telemetry, provenance, agent identity, memory, artifact store, approval service, and org governance.

Try to delete each piece.

| Proposed custom piece | Replace with | Delete? | Residual |
|---|---|---|---|
| Project truth | Git protected ref (+ issue/PR for obligations) | **DELETE** custom DB of “the project” | Need an explicit ACCEPTED state for non-mergeables |
| Event ledger | Git history + workflow event history + OTel | **DELETE** custom ledger | Maybe a tiny projection table, not a ledger product |
| Task tracker | Issues/PRs/Linear/Jira | **DELETE** | WRAP state machine so CLOSED_BY_AGENT ≠ ACCEPTED |
| Durable execution | Temporal / Restate / Inngest / Durable Task | **DELETE** custom engine | None if you actually put side effects in activities |
| Scheduler | Workflow timers + CI cron + OS cron | **DELETE** | None |
| Child registry | Child workflows + parent close policy + visibility API | **DELETE** | Agent UIs that spawn unmanaged threads must stop doing that |
| Policy engine | OPA / Cedar / platform rulesets / client hooks | **DELETE** | A few agent-specific predicates (cannot self-accept) |
| Credential broker | GitHub App tokens + SPIFFE + cloud workload identity | **DELETE** | Thin mint-and-scope helper is WRAP, not a product |
| Telemetry | OpenTelemetry | **DELETE** | GenAI attribute mapper (WRAP) |
| Provenance | SLSA/in-toto for releases; git for text; PROV vocab if needed | **DELETE** custom provenance platform | Optional predicate for “agent session produced blob X” |
| Agent identity | Workload identity + per-session workflow id + git author `agent[bot]` | **DELETE** “agent UUID fabric” | Session id is a workflow id |
| Memory | Files in repo + optional retrieval | **DELETE** memory OS | Optional |
| Artifact store | Git + object store + OCI | **DELETE** | None |
| Human approvals | Required reviews + workflow signals/awakeables | **DELETE** approval microservice | A single “accept” event type in the ticket/PR |
| Org governance | Existing IdP, CODEOWNERS, SOC2 log sinks | **DELETE** | None for this problem |

**How much of the control plane dies?** All of the *systems*. What remains is policy-as-data and a projector.

**Adversarial check.** If 80% can be deleted, can 100% of *custom services* be deleted? Yes, if the organization already has Git hosting, a ticket system, and one durable workflow engine. If it has none of those, install those — do not invent agent-native substitutes.

**Common false “we must build this” items.**

- “Agents need their own event store because LLM traces are special.” No. OTel + workflow history. Special attributes, not a special store. `[INFERENCE]`
- “Agents need a memory OS because context windows are small.” Context management is a runtime concern. Project truth does not belong in the paging system. `[INFERENCE]` `[SRC-CC-MEMORY]`
- “Agents need a child registry because Temporal doesn’t know about agents.” Then make children workflows. Do not make Temporal know about agents. `[INFERENCE]` `[SRC-TEMPORAL-CHILD]`
- “Checkpoints are durable execution.” They are not, or at least the burden of proof is on the framework, and two independent sources dispute it. `[SRC-DIAGRID-CP]` `[SRC-MS-AF-DISC]`

---
