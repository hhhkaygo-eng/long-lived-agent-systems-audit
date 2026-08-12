## 6. Long-lived continuity architecture

Thinnest architecture recommended **today**.

### 6.1 Authoritative state

- **Accepted artifacts:** the protected Git ref (usually `main`), plus signed release tags for deployables.
- **Accepted obligations:** ticket/PR items in state `ACCEPTED` or `REJECTED`, changeable only by a human identity or an explicit human signal. Agent identities may move items to `PROPOSED`, `IN_PROGRESS`, `DELIVERED`.
- **Accepted decisions:** ADR/RFC files merged via the same review gate.
- **Runtime facts that are authoritative for process (not product):** workflow execution current state (running children, pending approvals, last successful activity).

These four are the only things Session B is allowed to trust.

### 6.2 Working state

- Topic branches, worktrees, draft PRs, unmerged ADRs, `DELIVERED` tickets, local scratch, checkpointer blobs, chat transcripts.
- Working state may be lost. If it mattered, it should have been a workflow step result or a commit on a branch that is still referenced by a ticket.

### 6.3 Runtime

- A disposable model session (Claude Code, Codex, OpenHands, Agents SDK, raw API).
- Loads: projector output + relevant accepted files + current ticket.
- Must not load prior chat as authority. Transcripts are evidence, optional, suspect.

### 6.4 Execution

- One durable workflow per *obligation* (not per chat).
- Activities wrap every effectful tool (edit is just a git commit on a working branch; deploy, email, payment, issue-close-as-accepted are effects).
- Idempotency keys on effects.
- Children are child workflows with an explicit Parent Close Policy. Default `TERMINATE` unless the child is intentionally detached (`ABANDON`) *and* re-parented in the obligation record.
- HITL is `signal` / awakeable / wait-for-event, not “the model will remember to ask.”

### 6.5 Evidence

- Workflow event history (what ran).
- Git history (what changed).
- OTel traces (how the model thought, best-effort).
- CI logs (what was checked).
- Evidence is reconstructive, not authoritative for acceptance.

### 6.6 Memory

- `INTENT.md` / ADRs in repo (reviewed).
- Optional auto-notes in a gitignored or clearly marked `memory/` that Session B may read as *hints*.
- Optional retrieval over issues and ADRs.
- Memory writes by the model never change accepted state.

Claude Code’s own split is the right one: files as context, hooks as law. `[FACT]` `[SRC-CC-MEMORY]`

### 6.7 Policy

Invariants, all evaluated outside the model:

1. Agent identity cannot merge to protected refs.
2. Agent identity cannot set `ACCEPTED`.
3. Agent identity cannot mint credentials broader than the current obligation’s scope.
4. Effectful tools unavailable until recovery report validates against projector.
5. Children must have a workflow id recorded on the obligation before spawn is considered successful.
6. Closing a parent obligation while children are `RUNNING` is denied unless policy is `ABANDON` and a new parent id is written.

Implementation: GitHub rulesets + OPA/Cedar on the API gateway or hooks + workflow assertions.

### 6.8 Identity

- Humans: IdP users.
- Agents: GitHub App installation (or SPIFFE SVID in multi-service). Per-obligation scoped tokens. `[SRC-GH-SCOPED-TOKENS]`
- Each session gets a workflow-id, not a sacred agent UUID.
- Git commits: `Agent-Name[bot]` with `Co-authored-by` if a human prompted.

### 6.9 Human authority

- CODEOWNERS + required reviews for artifact acceptance.
- Explicit accept/reject on delivered obligations (can be the PR merge itself when the deliverable is the PR).
- Break-glass: admin bypass exists on GitHub and must be logged and rare. `[FACT]` `[SRC-GH-PROTECTED]`

### 6.10 Handoff / resume path

```
Session A dies
  → workflow still running or sitting on a timer/signal
  → Git refs and tickets unchanged
Session B starts
  → client runs projector (no model yet)
  → projector emits HANDOFF {accepted_sha, open_proposals[], obligations[], running_children[], pending_approvals[], authorized_next[]}
  → schema validate; if fail, stop
  → model receives only HANDOFF + the files it needs
  → model must emit RECOVERY_REPORT {echo accepted_sha, list unfinished, propose next}
  → mechanical diff against HANDOFF; if mismatch, stop or restrict to read-only
  → work continues as activities
Session C replaces B the same way
```

Owner intervention target: **zero** for resume; **one** per acceptance.

### 6.11 What must not be in the architecture

- Chat transcript as source of truth
- Vector memory as source of truth
- A second project database that can disagree with Git
- Agent-owned “mark accepted”
- Unmanaged subagents for unattended work
- Custom event store, identity fabric, artifact CAS, or policy language
- Framework checkpoints as the durability mechanism for effects
- Per-model proprietary memory that Session C (different vendor) cannot read
- Organizational “AI governance platforms” whose only job is to duplicate CODEOWNERS and audit logs

---
