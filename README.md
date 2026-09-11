# metricregistry

> Executable KPI definitions with a divergence monitor, so three teams stop quoting three different enrolment numbers.

`COMPACT` · **Business Analyst** · Intermediate · ~5 days · Education - university enrolment reporting

**Primary language:** SQL
**Tags:** `semantic-layer`, `dbt`, `metrics`, `sql`, `governance`, `data-quality`

---

> **Implementation note.** The catalogue specifies **SQL** for this
> project and that remains the target. This repository ships a runnable
> **Python** reference implementation of the core differentiator so the
> behaviour is executable and tested today; port it to SQL as step one
> of your own build.

## The problem

Finance reports 14,200 enrolled students. The registrar reports 13,850. Marketing reports 14,600. All three are correct - they use different census dates and different treatments of deferrals - but the meeting spends forty minutes arguing about the number instead of making the decision the number was for.

## ⭐ The differentiator

Definitions are **executable and continuously reconciled**: each KPI is stored with its SQL, grain, filters and owner, and a scheduled job **recomputes every definition and alerts when two nominally-identical metrics diverge beyond tolerance**. A generic metrics dictionary is a wiki page describing metrics in English, which drifts from the SQL immediately and has no mechanism to notice.

This is the sentence to lead with when someone asks you to walk through the
project. Everything else in this repo exists to make it true and to prove it.

## Data

A synthetic student-record generator with deliberately ambiguous cases - deferrals, mid-census withdrawals, dual enrolment - and **documented correct values under each definition**, so the registry can be tested rather than merely populated.

> No paid API key is required to run or demo this project. Where a paid
> service would add value it is wired as an optional enhancement behind an
> interface with an offline mock as the default implementation.

## Stack

- SQL on DuckDB / PostgreSQL
- dbt with MetricFlow-style definitions
- Python for the divergence monitor
- Streamlit
- dbt tests

## Core capabilities

- Versioned metric definitions carrying grain, filters, owner and business description
- Executable definitions producing values, not prose
- Divergence monitor comparing metrics declared equivalent and alerting on tolerance breach
- Lineage from each metric down to its source columns
- Definition-change log showing when a number moved because the *definition* moved

## Repository layout

```
metrics/
models/
monitor/
generator/
app/
tests/
```

## Build plan

1. Encode the three competing definitions exactly as they are used today. Do not harmonise them yet - the disagreement is the finding.
2. Make them executable and show the three numbers side by side with their differences named.
3. Divergence monitor, then the change log.
4. Only then propose a canonical definition.

## Testing strategy

Assert each definition returns the **documented correct value on every ambiguous case** - deferral, mid-census withdrawal, dual enrolment. Assert the divergence monitor fires when a definition is edited, which is the behaviour that keeps the registry honest over time.

Tests assert **correctness**, not merely that the code runs. A green suite on
this repo is a claim about behaviour under adversarial conditions; treat any
test that would pass against a deliberately broken implementation as a bug in
the test.

## Quality & safety layer

Every published number carries its definition version. A metric whose definition changed since the last publication is flagged in the output rather than silently restated.

## Measurable outcome

> One page showing all three enrolment numbers, the exact definitional difference between each pair, and which one policy says to use - ending a recurring forty-minute argument.

State it in these terms — business units, not technical ones — in your CV
bullet and in the first thirty seconds of describing the project.

## Interview questions this project answers

- **Three teams have three numbers. How do you resolve it?**
- **What belongs in a metric definition beyond the SQL?**
- **How do you detect that a metric definition has drifted?**

## What this deliberately is *not*

- Not a BI tool. It governs definitions; your BI tool renders them.


## Run it now

```bash
python -m unittest discover -s tests -v   # the suite
python -m src.demo                        # the 60-second artefact
```

Requires Python 3.11+. The runnable core uses **only the standard
library** (including `sqlite3`), so there is nothing to install.

## Getting started

```bash
git clone <your-fork-url> metricregistry
cd metricregistry
pip install -e .
python -m generator --students 20000
dbt build
python -m monitor check        # divergence across equivalent metrics
streamlit run app/main.py
```

Docker is supported but optional — every path above works on a plain
Windows/macOS/Linux laptop without a cloud account.

## Definition of done

- [ ] The differentiator above is implemented, and a test proves it
- [ ] The measurable outcome is produced by a command anyone can run
- [ ] `README` explains the one decision a generic version gets wrong
- [ ] CI runs the full suite on every push and is green on `main`
- [ ] A recruiter can see the headline artefact in under 60 seconds

## Licence

MIT — see [LICENSE](LICENSE).
