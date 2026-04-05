# Evaluation Notes: ao-config.toml as Prior

## What to look for

### confirmed = true handling

- Agent does not re-scan for `package_manager`, `layout`, `build_backend`,
  `formatter`, `linter`, `type_checker`, `test_runner`, or `ci`
- These fields appear in `ao-context.toml` with the declared values
- No mention of re-inferring or verifying these in the execution record

### confirmed = false handling

- Agent scans observed function lengths in `src/` and compares to
  `max_function_lines = 30`
- Agent reads actual docstrings and verifies `docstring_style = "google"`
- Agent checks whether `docstring_required_on` matches observed coverage
- Agent checks whether `type_annotations` declaration matches
  `pyrightconfig.json` or CI enforcement

### Conflict recording

- If observed `max_function_lines` differs from declared 30,
  conflict recorded in `meta.divergences` with both values and sources
- If observed docstring coverage is lower than declared,
  recorded as a gap or divergence, not silently accepted
- No conflicts silently resolved in either direction

### Schema completeness

- `ao-context.toml` includes all fields from `ao-config.toml`
  plus any additional conventions discovered during scanning
- `meta.produced_by` records that ao-config.toml was used as prior
- Fields not covered by ao-config.toml are filled from observation
  and marked in `meta.inferred` if not directly enforced

## Common failure modes

- Agent re-infers `confirmed = true` entries and records them as
  inferred rather than accepting the declared values
- Agent accepts `confirmed = false` entries without verifying them
  against observed patterns
- Conflicts between declared and observed values silently resolved
  in favor of the declared value (silent conformance to config)
- Conflicts silently resolved in favor of observed value
  (silent override of config)
- `meta.divergences` empty when `confirmed = false` entries
  could not be fully verified

## Notes from scenario runs

### Run 1 — datafun-toolkit with config prior (2026-04-04, score 14/14)

**Positive example — confirmed=true conflict correctly handled:**
`build_backend = "hatchling"` was declared confirmed=true but observed as
`setuptools + setuptools_scm`. Agent retained the declared value per spec
and recorded the conflict in `meta.divergences` for human review.
This is the intended behavior — confirmed entries are not silently overridden
even when evidence contradicts them.

**Positive example — max_function_lines ambiguous match:**
Observed body lines ≤15 but total lines 31–33. Agent correctly classified
this as an ambiguous match rather than a clean pass or fail, and recorded
it in `meta.inferred`. Nuanced handling of a genuine measurement ambiguity.
