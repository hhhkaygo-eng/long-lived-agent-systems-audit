## 10. Gap register

Only gaps that survived the prior-art scan. Not “we don’t have a UI.”

| ID | Uncovered behavior | Severity | Required guarantee | Smallest missing seam | Verdict | Next evidence |
|---|---|---|---|---|---|---|
| G1 | Uniform ACCEPTED ≠ FINISHED for non-code, non-PR deliverables | High | deterministic | Issue/obligation state machine + ACL (agent cannot set ACCEPTED) | **WRAP** | Deploy WRAP on 1 project; see if Session B mis-classifies delivered items |
| G2 | Session-replacement benchmark does not exist | High for claims; low for ops | measured | Public fixture + scorer as in §7 | **DEFER** (science) | Run §7 at N≥1 internally; then pre-register |
| G3 | Agent UIs spawn children that are not durable workflows | High for unattended | deterministic | Ban or adapter: Task tool → start_child | **WRAP** / **REJECT** unmanaged | Instrument one product (Claude Code / OpenHands) under T1–T6 |
| G4 | Temporal Continue-As-New drops child bindings from the new parent instance | Medium | deterministic | Obligation record holds child ids; projector uses that | **WRAP** | Confirm current Temporal behavior still as documented `[SRC-TEMPORAL-CHILD]` |
| G5 | Model may ignore recovered authority | High | probabilistic (irreducible) | Deny tools until RECOVERY_REPORT matches HANDOFF | **WRAP** | Measure mismatch rate in §7 |
| G6 | Product “memory” can contaminate recovery | Medium | deterministic disable switch | Session B with memory wiped must still pass | **WRAP** | Ablation in §7.4 |
| G7 | GenAI telemetry names still Development | Low | measured | Pin a semconv version; do not parse names as API | **DEFER** | Watch OTel GenAI stability |
| G8 | Admin/bypass paths can silently accept | Medium | process_dependent | Log + alert on bypass; cannot eliminate on GitHub | **DEFER** extra platform | Org policy review |
| G9 | Live prod vs repo HEAD | Medium | measured | Existing GitOps/deploy attestation | **ADOPT** GitOps; not agent-specific | Standard drift check |
| G10 | Independent reproduction of 2026 memory-leaderboard / Mem0 scores | Low for architecture | measured | Third-party rerun | **DEFER** | Ignore vendor scores until then |

No gap in this table is BUILD.

---
