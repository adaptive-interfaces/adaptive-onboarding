# Evaluation Notes: Multi-Repository, authoritative_for Scoping

## What to look for

### Source scoping

- Agent reads each source repo independently before combining results
- Packaging conventions (`package_manager`, `build_backend`, `layout`)
  come from `datafun-toolkit`, not the other repos
- Test conventions (`test_pattern`, `test_runner`) come from
  `multidimensional-evaluation-engine`, not the packaging source
- Documentation conventions come from `pro-analytics-02`
- Agent does not let a strong signal in one area bleed into another source's scope

### Conflict handling

- If two sources disagree on a convention neither owns exclusively,
  the conflict is recorded in `meta.divergences` with both values
- The resolution is stated explicitly with the priority rule applied
- No silent resolution in either direction

### Traceability

- Each entry in `ao-context.toml` identifies which source it came from
- Either inline as a comment or in `meta.inferred` with source attribution
- A reader can verify any entry by checking the cited source

### Schema completeness

- `[python]` section reflects packaging source correctly
- `[code_style]` reflects the testing/annotation source correctly
- `meta.gaps` records anything that could not be resolved across sources

## Common failure modes

- Agent averages or blends conflicting conventions rather than
  recording the conflict and applying the priority rule
- Agent uses the first repo scanned for all conventions regardless of
  `authoritative_for` scope
- Traceability entries missing — conventions recorded without citing source
- Agent requests `ao-config.toml` when none is provided in this scenario
- `meta.divergences` left empty when sources visibly disagree

## Notes from scenario runs

### Run 1. datafun-toolkit / MEE / pro-analytics-02 (2026-04-04, score 14/14)

Positive example. Balanced stance on line_length conflict:
S1 (datafun-toolkit) uses line-length = 100, S2 and S3 both use 88.
Agent correctly resolved to S1 by authority scope but flagged the conflict
for human review because 2-of-3 sources prefer 88.
This is the balanced stance working as designed.
The conflict is visible, not silently resolved.

### Observation: stale authoritative source

datafun-toolkit (S1) still had setuptools and setuptools_scm as the build
backend at time of this run, predating the hatchling migration.
The agent correctly reported what it observed,
but the conventions it recorded for [python].build_backend were already outdated.
authoritative_for sources must be current.
A stale source propagates outdated conventions
to every repo that loads this context.
Refresh ao-config.toml sources after migrating canonical repos.
