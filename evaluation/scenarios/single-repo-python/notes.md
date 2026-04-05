# Evaluation Notes: Single Repository, Python

## What to look for

### Source enumeration

- Agent reads `pyproject.toml`, `ruff.toml` or `ruff` section in pyproject,
  CI workflow files, and README before drawing any conclusions
- Agent does not rely on training knowledge about the repo owner's conventions
- Sources consulted are explicitly listed in the execution record

### Convention accuracy

- `package_manager` correctly identified as `uv` from `pyproject.toml`
  or CI workflow (not inferred from owner identity)
- `layout` correctly identified as `src` from directory structure
- `build_backend` identified from `pyproject.toml` `[build-system]` section
- `formatter` and `linter` identified from ruff config, not assumed
- `type_checker` identified from CI workflow or `pyrightconfig.json`
- `docstring_style` inferred from observed function docstrings in `src/`

### Gap and inference handling

- `max_function_lines` and `max_cyclomatic_complexity` correctly recorded
  as inferred (not CI-enforced) if no tool config enforces them
- `review_required` recorded as gap if no CODEOWNERS or branch protection
  rules are visible
- `commit_style` recorded as inferred or gap if no commitlint config present

### Schema completeness

- `[python]` section fully populated
- `meta.produced_by` and `meta.produced_on` present
- No empty required fields left without explanation

## Common failure modes

- Agent infers conventions from owner identity rather than observed files
  (e.g., assumes `uv` because the repo owner uses it, not because
  `pyproject.toml` specifies it)
- Agent populates `max_function_lines` with a number without recording
  it as inferred
- Agent leaves `meta.gaps` empty when conventions could not be determined
- Agent writes conventions that apply to the org but not this specific repo
  without flagging the difference
- `docstring_style` set to "google" without citing observed docstrings

## Notes from scenario runs

### Run 1. denisecase/datafun-toolkit (2026-04-04, score 14/14)

**Positive example - comment prefix convention:**
Agent correctly identified `REQ:/WHY:/OBS:` as an observed comment prefix
style from source files, with no config file declaring it.
This is the intended behavior: unwritten norms surfaced from code patterns
and recorded in `[norms]`, not in `[code_style]`.

**Common divergence to watch for - build backend:**
`datafun-toolkit` uses `setuptools + setuptools_scm` rather than `hatchling`.
Repos that predate a team's adoption of hatchling will show this divergence.
Agent should record it in `meta.divergences`, not silently accept either value.
Note: After noticing this, updating repos has begun.

### Run 2. datafun-toolkit with org-wide layered prior (2026-04-04, score 14/14)

**Positive example - commit_style conflict from git history:**
Prior declared `commit_style = "conventional commits"`.
Agent checked git history directly and found informal messages
("Prep v1.0.0", "Prep v0.9.6").
Git history correctly weighted over README example.
Evidence hierarchy applied correctly.

**Observation - confirmed=false throughout triggers broader coverage:**
Org-wide prior with all entries `confirmed = false`
caused the agent to verify hygiene, CI, security, and norms -
not just toolchain.
Dependabot confirmed active from auto-PRs in commit history.
Teams building a first `ao-config.toml` should start with
`confirmed = false` throughout to maximize verification coverage
before promoting stable entries to `confirmed = true`.

**Observation - unverifiable gaps correctly recorded:**
`branch_protection` and `secret_scanning` are GitHub settings
not visible in repo files.
Agent recorded both as gaps rather than guessing or omitting.
This is correct behavior; gaps are information.
