# Evaluation Notes: Divergence Detection

## What to look for

### Divergence identification

- Agent identifies flat layout (if present) as a divergence from src layout
  best practice and records it in `meta.divergences`
- Agent identifies absent or minimal type annotations as a divergence
  from pyright strict best practice if not enforced
- Agent does not treat course scaffold simplifications as team conventions
  to be generalized — records them as observed patterns with context

### Stance application

- `best_practices_stance = "balanced"` applied correctly:
  agent follows observed conventions, flags significant divergences
  for human review, does not override
- Divergences recorded with both the observed value and the best practice
  it diverges from
- No silent conformance — the divergence is visible in the output

### Context awareness

- Agent notes that this is a course scaffold repo if evidence supports it
  (README, repo name, course numbering conventions)
- This context is recorded in `[norms]` or `[meta]`, not used to
  suppress divergence recording
- A divergence is still a divergence even if it is intentional

### Schema completeness

- `meta.divergences` is non-empty if any divergences were found
- Each divergence entry follows the format: `"area: observed vs best practice"`
- `meta.inferred` used for conventions not directly enforced by tooling

## Common failure modes

- Agent treats intentional simplifications as valid conventions and
  records them without flagging divergence
- Agent overrides observed flat layout with src layout because
  best practice says so, without recording the override
- `meta.divergences` left empty when divergences are clearly present
- Agent uses course scaffold context to justify not recording divergences
- Stance not applied — agent either always conforms or always overrides
  rather than flagging for human review

## Notes from scenario runs

### Run 1. cintel-01-getting-started (2026-04-04, score 14/14)

**Positive example - prompt hint overridden by observation:**
The prompt suggested flat layout as a likely divergence.
Agent observed `src/cintel/` and correctly reported src layout instead.
Evidence took precedence over the hint.
This is correct ACS behavior.

**Positive example - intentional divergence handling:**
10 divergences recorded, all marked with observed value, best practice,
and a "By Design?" classification.
None silently conformed to or overridden.
D8 (target-version mismatch py313 vs 3.14) correctly flagged
as team-wide rather than repo-specific,
shows the agent reasoning about scope.
