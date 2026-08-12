# Long-Lived Agent Systems — Independent Audit

Agent-readable architecture / prior-art audit.

Not an implementation plan. Not a product. Not an agent OS.

**Canonical report:** [`REPORT.md`](./REPORT.md)

**Machine projections:** [`data/`](./data/)

**Load contract:** [`MANIFEST.yaml`](./MANIFEST.yaml)

## Agent load order

1. `MANIFEST.yaml` — schema, closed enums, headline verdict
2. `REPORT.md` — canonical 11-section report
3. `data/sources.yaml` — `SRC-*` registry
4. other `data/*.yaml` — structured projections

If YAML and `REPORT.md` disagree, **`REPORT.md` wins**.

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

## Tree

```
MANIFEST.yaml
REPORT.md
README.md
data/
  enums.yaml
  sources.yaml
  requirements.yaml              # §2
  commodity-map.yaml             # §3
  delete-80.yaml                 # §4
  irreducible.yaml               # §5
  architecture.yaml              # §6
  session-replacement-test.yaml  # §7
  child-supervision-test.yaml    # §8
  competing-architectures.yaml   # §9
  gap-register.yaml              # §10
  final-answers.yaml             # §11
```

## What this is not

- Not a startup pitch
- Not a product roadmap
- Not code
- Not an endorsement of Temporal, GitHub, or any vendor
