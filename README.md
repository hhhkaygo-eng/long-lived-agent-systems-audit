# Long-Lived Agent Systems — Independent Audit

Public agent-readable architecture / prior-art audit.

Not an implementation plan. Not a product. Not an agent OS.

## Give this to an agent

Repo: https://github.com/hhhkaygo-eng/long-lived-agent-systems-audit

Load order:

1. `MANIFEST.yaml` — schema, closed enums, headline verdict
2. `sections/00` … `sections/11` in numeric order — canonical report
3. `data/sources.yaml` — `SRC-*` registry
4. other `data/*.yaml` — structured projections

`REPORT.md` is an index of the sections. If YAML disagrees with the sections, **the sections win**.

Do not implement from this repo. Consume it as an audit.

## Headline

Persistent AI memory is not the central problem.

The central problem is **durable authoritative state + recovery discipline**, plus an explicit **finished ≠ accepted** bit that the model cannot set.

`BUILD` count after the commodity test: **0**.

Minimum extra layer: a thin WRAP — acceptance contract, deterministic `HANDOFF` projector, recovery gate.

## Closed vocabularies

Epistemic status: `FACT | INFERENCE | HYPOTHESIS | MARKETING | UNKNOWN`

Commodity verdict: `ADOPT | WRAP | BUILD | DELETE | DEFER | REJECT`

A `BUILD` item is allowed only if all four hold: real requirement, prior art incomplete, thin wrapper insufficient, process-dependence unacceptable.

## What this is not

- Not a startup pitch
- Not a product roadmap
- Not code
- Not an endorsement of Temporal, GitHub, or any vendor
