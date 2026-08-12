## 2. Requirements inventory

Each requirement is a distinct capability the long-lived system actually needs. Guarantee is the *required* level, not what vendors claim.

| ID | Capability | Required guarantee | Why that level | Status |
|---|---|---|---|---|
| R01 | Cross-session continuity of *accepted* project state | deterministic | Replacement session must not guess what is merged/accepted | partial (solved for code; convention for the rest) |
| R02 | Recovery after parent model/session replacement | deterministic for state reconstruction; probabilistic for subsequent work quality | State must be reconstructible without chat; next actions remain model-dependent | partial |
| R03 | Distinction: accepted vs working/proposed | deterministic | Silent promotion of drafts is the core integrity failure | partial |
| R04 | Owner intent persistence | process_dependent, with deterministic *storage* | Intent changes; it must be stored and dated, not frozen in weights | partial |
| R05 | Provenance of artifacts and decisions | deterministic for artifacts; process_dependent for decisions | Builds can be attested; “why we chose X” is a record humans/agents write | partial |
| R06 | Task/result contracts (inputs, outputs, done-when) | deterministic schema; human_dependent acceptance | Contract shape can be enforced; satisfaction often cannot | partial |
| R07 | Child/subagent lifecycle supervision | deterministic | Orphans are an operational failure, not a memory failure | partial (ADOPT Temporal; agent UIs do not) |
| R08 | Orphan/forgotten child detection | deterministic | Must be queryable independently of parent chat | partial |
| R09 | Terminal execution ≠ verified/accepted deliverable | deterministic | The most commonly collapsed distinction | unsolved as a uniform primitive; WRAP |
| R10 | Interruption, retry, resume, replay, idempotency | deterministic | Side effects without idempotency corrupt the world | solved (if tools are activities/steps) |
| R11 | Long-running workflows (days/weeks) | deterministic | Process must outlive sessions and machines | solved |
| R12 | Multi-agent concurrency and ownership | deterministic for ownership of refs/leases; process_dependent for social ownership | Git + workflow ids give mechanical ownership; org ownership is social | partial |
| R13 | Model/provider replacement | deterministic (no vendor lock on authority) | Authority artifacts must be portable | partial |
| R14 | Routing/fallback across heterogeneous executors | process_dependent | Useful, not definitional for continuity | defer |
| R15 | Memory vs authoritative project truth | deterministic separation | Mixing them is the standard design error | unsolved in most agent products; solved by policy |
| R16 | Repo vs live-production divergence | measured + process_dependent | Detectable; prevention is promotion discipline | partial |
| R17 | Human approval boundaries | deterministic gates; human_dependent decisions | The gate must not be skippable by the model | partial (config) |
| R18 | Least-privilege machine identity | deterministic | Long-lived PATs as “the agent” are unacceptable | solved |
| R19 | Audit logs and observability | deterministic retention of events; measured quality of traces | You can always log; you cannot always reconstruct meaning | partial |
| R20 | Policy / invariant enforcement | deterministic | Invariants that live in prompts are not invariants | partial |
| R21 | Artifact integrity / attestation | deterministic for bits; not for meaning | Hash + signature ≠ correct | solved for bits |
| R22 | Scientific evaluation of long-term continuity claims | measured | Claims of months-long agency need a test that is not chat-QA | unsolved / scientific |
| R23 | Handoff packet completeness | deterministic validation | Session B must fail closed if required fields are missing | wrap |
| R24 | Authorization of *next* actions after resume | deterministic | Recovered session must know what it may do, not only what happened | wrap |

Guarantee-level notes:

- **Deterministic** items are infrastructure or schema. They do not require a smarter model.
- **Probabilistic** items (R02 work quality, retrieval recall) cannot be made deterministic by another database.
- **Human-dependent** items (acceptance, some intent updates) should stay human-dependent. Automating acceptance is how silent loss happens.
- **UNKNOWN:** whether organizations will actually configure branch protection, parent-close policy, and non-self-acceptance. That is adoption, not architecture.

---
