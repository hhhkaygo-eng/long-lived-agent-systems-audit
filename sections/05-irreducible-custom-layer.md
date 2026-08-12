## 5. Irreducible custom layer

After the commodity test, **no capability survives a strict BUILD classification.**

Candidates that were tested and **failed** the four-part bar:

### CAND-1 Obligation/acceptance contract

1. Requirement exists? **Yes** (R03, R06, R09).
2. Prior art incomplete? **Only as a uniform API.** GitHub required review is acceptance for code. Issues can represent other work. Temporal can wait on a signal named `accept`.
3. Thin wrapper insufficient? **No.** A JSON Schema / issue template + required labels + CI that rejects `accepted` set by the agent identity is a wrapper.
4. Process-dependence unacceptable? **Not if the wrapper is enforced mechanically.**

→ **WRAP**, not BUILD.

### CAND-2 Deterministic recovery projector

1. Yes (R02, R23, R24).
2. Prior art: `git log`, `gh pr status`, `gh issue list`, Temporal query/describe, OTel backends.
3. Wrapper sufficient: a script that emits a validated `HANDOFF` document from those APIs. Session B is forbidden from working until it reads that document, not the chat.
4. Process-dependence is removed by making the projector the session entrypoint (CI job, workflow query, or client bootstrap).

→ **WRAP**.

### CAND-3 Child registry

1. Yes (R07, R08).
2. Covered by Temporal child workflows + Parent Close Policy + visibility. `[SRC-TEMPORAL-CHILD]` `[SRC-TEMPORAL-PCP]`
3. Wrapper: spawn children only as workflows; agent “Task” tools become `start_child`.
4. Process-dependence is unacceptable — and that is why you **ADOPT** the engine rather than BUILD a registry.

→ **ADOPT** Temporal/Restate; **REJECT** unmanaged subagents for unattended work.

### CAND-4 Memory OS

1. Recall is useful, not the requirement in the core question.
2. Files + retrieval cover recall. MemGPT-style paging is a context tactic. `[SRC-MEMGPT-PAPER]`
3. Wrapper sufficient for recall.
4. Process-dependence of “what to remember” is acceptable because memory is not authority.

→ **DEFER** / **REJECT** as authority.

### CAND-5 Provenance platform

1. Artifact integrity is real (R05, R21).
2. SLSA/in-toto/Sigstore/git exist. `[SRC-SLSA-PROV]`
3. Wrapper: sign release artifacts; commit everything else.
4. “Every token the model uttered” does not need attestations.

→ **ADOPT** existing provenance; **DEFER** per-thought attestation.

### CAND-6 Semantic faithfulness of Session B

1. Real.
2. No infrastructure covers it.
3. Wrapper cannot make a model honest.
4. Leaving it probabilistic is **acceptable** because the alternative is pretending. Mitigation is deny-by-default tools until a structured recovery report type-checks against the projector.

→ Not BUILD. It is a **model property**. Architecture must assume it fails.

### CAND-7 Scientific continuity benchmark

1. Real for claims; not required to operate a system tomorrow.
2. No existing bench is the session-replacement test (§7).
3. A thin wrapper of TheAgentCompany is insufficient (wrong independent variable).
4. Process-dependence is acceptable until someone claims “our agent works for months.”

→ **DEFER** as product infrastructure; run as an experiment (§7, §11.7).

**BUILD list: empty.**

If a future team claims BUILD, they must produce a requirement that is not R01–R24 as satisfied above, and pass all four clauses. “We prefer one vendor UI” is not a requirement.

---
