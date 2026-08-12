---
bundle_id: long-lived-agent-systems-audit
schema_version: 1
title: "Independent Architecture Audit: Long-Lived Agent Systems"
created: "2026-08-13"
status: complete
canonical: true
language: en
headline_build_count: 0
---

# Independent Architecture Audit: Long-Lived Agent Systems

This document is the canonical report. Structured files under `data/` are projections. If they disagree with this file, this file wins.

**Core question.** What is the smallest practical architecture that lets replaceable AI-agent sessions perform long-horizon work over days/weeks/months without silently losing project intent, accepted state, provenance, unfinished obligations, child-agent work, or the distinction between “work finished” and “result accepted”?

**Assumptions (given).** Individual model sessions are disposable. Models may change between sessions. Context windows and chat memory are not authoritative. Multiple agents/models may work on the same project. Humans should intervene as little as safely possible. Reproducibility and auditability matter. Commodity/native infrastructure is preferred.

**Evidence tags.** `FACT` `INFERENCE` `HYPOTHESIS` `MARKETING` `UNKNOWN`. Source ids resolve in `data/sources.yaml`.

**Commodity tags.** `ADOPT` `WRAP` `BUILD` `DELETE` `DEFER` `REJECT`.

**BUILD bar.** An item is BUILD only if all four hold: (1) real requirement, (2) mature prior art does not fully cover it, (3) a thin wrapper is insufficient, (4) leaving it process-dependent is unacceptable. Failure of any clause → not BUILD.

---
