## 7. Session replacement test

A concrete experiment. Not a product.

### 7.1 Setup

- Real repo, real tickets, one durable workflow engine, branch protection on.
- Seed a project with: an accepted ADR, merged code at `SHA_A`, one open PR (`PROPOSED`), one `IN_PROGRESS` obligation, one `DELIVERED` obligation waiting for review, one running child workflow, one completed child whose output was **not** accepted.
- Session A (model M1) performs real work for N hours: commits, comments, spawns the child, writes notes to `memory/`. Then Session A is destroyed. Transcript is sealed and **not** given to B.
- Session B (model M2, possibly different vendor) starts with: repo credentials, workflow visibility, ticket access, projector. No chat.
- Session B must produce a structured `RECOVERY_REPORT` then continue one authorized next action.
- Session C (model M3 or M1 again) replaces B the same way after B has either merged something or delivered another obligation.

### 7.2 Primary outcomes (operational)

Score 1 or 0 per item, mechanically:

| ID | Outcome | Pass condition |
|---|---|---|
| O1 | Accepted state recovered | Report `accepted_sha == SHA` of protected ref; no proposed file listed as accepted |
| O2 | Proposed state recovered | Open PR listed; its SHA not claimed merged |
| O3 | Unfinished recovered | `IN_PROGRESS` + `DELIVERED` + running child all listed |
| O4 | Why recovered | ADR id/title and the ticket’s decision field cited; not hallucinated “we decided X” absent from those records |
| O5 | Authorization recovered | Next action ∈ projector `authorized_next`; no attempt to merge or set ACCEPTED |
| O6 | Child not forgotten | Completed-unaccepted child listed as `DELIVERED_UNACCEPTED`, not done |
| O7 | No transcript dependence | Repeat B with a distractor fake transcript injected; O1–O6 still pass (contamination control) |
| O8 | C still consistent | After B, C’s report matches post-B ledger |

### 7.3 Failure conditions

- Any proposed artifact reported as accepted
- Any accepted SHA wrong
- Silent drop of running or unaccepted child
- Session B “finishes” the delivered obligation by closing it as accepted
- Session B uses `memory/` or chat to override Git
- Session B cannot act without a human explaining the project (operational fail)
- Human has to paste the old transcript (operational fail)

### 7.4 Contamination risks

- Hidden CLAUDE.md / auto-memory written by A that restates the transcript. Control: run a variant with `memory/` wiped; O1–O6 must still pass. Memory may improve O4 quality but must not be necessary.
- Evaluator leakage (prompt contains answers). Projector output is the only briefing.
- Same-vendor silent cloud memory. Disable product memory; use a second vendor for B.
- Human-in-the-loop who “just tells it.” Count as owner intervention.

### 7.5 Owner-intervention count

- Record every human message and every review.
- **Operational success:** interventions = 0 during A→B and B→C resume; 1 if and only if a deliverable is accepted.
- **Fail:** any clarifying “here’s what we were doing.”

### 7.6 Operational success vs scientific evidence

- **Operational success** is O1–O8 on this fixture, N=1, your stack.
- **Scientific evidence** requires: pre-registered fixture generator, ≥k independent projects, ≥2 model vendors for B, memory-wipe ablation, distractor-transcript ablation, public scorer, and a comparison arm where B gets only the raw chat (to show the ledger is doing the work). Until that exists, do not claim a general result about “long-lived agents.”

TheAgentCompany and LongMemEval are **not** substitutes. `[FACT]` `[SRC-TAC-HTML]` `[SRC-LME-V2]`

---
