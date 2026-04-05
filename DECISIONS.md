# DECISIONS.md

Design history and rationale.
Records why the skill is the way it is for
contributors, future maintainers, and dependent skills.

## Decision 1. Scope: team context and conventions only

AO covers discovery, mapping, and persistent storage of team conventions,
ownership, definition of done, and norms for a specific codebase or project.
It does not cover tool capability mapping, observation and conformance
discipline, or post-onboarding verification of agent understanding.

### Rationale 1

These are distinct concerns with distinct workflows.
Observation and conformance discipline is the responsibility of ACS,
which AO invokes as its foundational layer.
Tool capability mapping is the responsibility of ATD.
AO's scope must stay narrow enough to be reusable across any team
or codebase without requiring knowledge of specific tools or protocols.

## Decision 2. Two-file design: ao-config.toml and ao-context.toml

AO uses two distinct files with explicit roles:

- `.agent/ao-config.toml` — team-provided input configuration;
  maintained by the developer or team; declares known conventions,
  preferences, sources, and alignment stance
- `.agent/ao-context.toml` — generated output; produced and refreshed
  by the agent; contains the full observed team context document

### Rationale 2

A single file conflates what the team knows and asserts with what the
agent has observed and inferred.
Separating input from output makes it clear which entries are
team-authoritative and which are agent-generated.
It also allows the team to update preferences without overwriting
agent-generated observations, and allows the agent to refresh
the context document without modifying team-declared conventions.

Both files use the same schema and are equally valid inputs to an agent session.
An agent session may load one or both; `ao-config.toml` takes precedence
on any field where the two conflict.

A useful property of the two-file design is that the files are diffable.
Where `ao-config.toml` and `ao-context.toml` disagree on a convention,
that disagreement surfaces a gap between team intent and observed practice.
This is itself valuable independent of any automation:
teams filling in `ao-config.toml` for the first time often discover
their conventions are less consistent than they believed.

## Decision 3. confirmed field in ao-config.toml

Input configuration entries carry a `confirmed` flag.
Entries marked `confirmed = true` are accepted by the agent
without re-inference.
Entries marked `confirmed = false` are verified against observed
code patterns before being accepted.

### Rationale 3

Not all declared conventions are equally stable.
Toolchain entries (package manager, formatter, linter) are typically
stable across all of a team's repos and do not need re-inference.
Style entries (function size, docstring coverage) may vary
by repo and should be verified rather than assumed.
The `confirmed` flag gives the team explicit control over
which entries the agent treats as ground truth
versus which it treats as a prior to be checked.

## Decision 4. best_practices_stance as a first-class field

AO requires an explicit `best_practices_stance` in `ao-config.toml`
with three defined values: `prefer_team`, `balanced`, `prefer_standard`.
Agent behavior when team conventions diverge from best practices
is determined by this field, not by agent discretion.

### Rationale 4

An agent that silently conforms to a divergent team convention
conceals the divergence from the team.
An agent that silently overrides a team convention in favor of
best practice produces output the team did not ask for and
may not recognize as their own.
Both behaviors erode trust.
Making the stance explicit and team-declared ensures the agent's
behavior is predictable, auditable, and aligned with team intent.
Divergences are recorded in `ao-context.toml` regardless of stance
so the team can review them independently of what the agent did.

## Decision 5. ⭐ Convention authority is concern-specific, not repo-specific

A single repo is not uniformly authoritative across all conventions.
One repo may be the best example of packaging conventions
(e.g., a PyPI-published library), another of test setup,
another of CI workflow design.
AO's `[sources]` configuration supports `authoritative_for`
as a list of concerns rather than a single repo-level role.

### Rationale 5

During design, the naive approach of designating repos as
globally `authoritative` or `reference` was found to be insufficient.
A developer maintaining hundreds of repos with consistent toolchain
conventions but varying test maturity cannot point to a single repo
as authoritative for all concerns.
External repos (e.g., a well-maintained OSS project) may be
authoritative for a specific convention (e.g., `pyproject.toml`
structure) without being relevant to team norms or style.
The `authoritative_for` field makes the scope of authority explicit
and prevents a strong example in one area from incorrectly
overriding conventions in another.

This is a known hard problem.
The current design is a practical middle ground.
A full convention registry with per-concern provenance tracking
would require a knowledge graph rather than a config file
and is deferred to a future decision.

## Decision 6. Persistent context document as primary output

AO produces a persistent team context document stored at a stable
location (`.agent/ao-context.toml`), not a session-local record.
The document is owned by the team whose project it describes,
not by the AO repo.

### Rationale 6

Session-local context must be re-established on every agent session.
For a developer maintaining dozens or hundreds of repos,
re-inferring the same stable conventions repeatedly is wasteful
and produces inconsistent results across sessions.
A persistent document means discovery runs once per repo,
is verified once, and is loaded by all subsequent agent sessions.
Storing the document in the repo keeps it versioned alongside
the conventions it describes.

## Decision 7. Refresh via scheduled GitHub Actions

The context document is refreshed on a schedule (e.g., weekly)
via a GitHub Actions workflow with manual trigger support.
Small changes (new norm, ownership update) auto-commit.
Large changes (definition of done, alignment stance, major restructure)
open a PR for human review.

### Rationale 7

Conventions change infrequently but do change.
A scheduled action catches drift without requiring manual intervention.
The small/large change branching ensures humans review significant
changes before they propagate to all agent sessions,
while keeping minor updates from creating unnecessary review burden.
This mirrors the same decision made for ATD registry refresh (Decision 4).

## Decision 8. License: MIT

MIT across all `adaptive-interfaces` repos.

### Rationale 8

Organizations need to be able to use and adapt skills internally
without legal friction.
MIT is OSI-approved, universally recognized by legal teams,
and imposes no obligations on derivative works.

## Decision 9. MANIFEST.toml: adaptive-interfaces schema

Uses `schema = "adaptive-interfaces-manifest-1"`, defined in
[adaptive-interfaces-manifest-1.md](https://github.com/adaptive-interfaces/.github/blob/main/schemas/adaptive-interfaces-manifest-1.md).

### Rationale 9

Consistent manifest schema across all `adaptive-interfaces` repos
allows tooling to index and compare repos without repo-specific parsing.
The schema is shared with ATD and ACS for the same reason.

## Decision 10. ⭐ Schema is the primary artifact; SKILL.md is optional automation

The AO schema — the TOML structure shared by `ao-config.toml` and
`ao-context.toml` — is the primary contribution of this project.
SKILL.md is one way to generate `ao-context.toml` automatically
from a set of repositories. It is not required.

A hand-maintained `ao-config.toml` is equally valid as a session input
and is often more accurate than an agent-generated file,
because the team knows their own conventions better than any scan can infer.

The schema serves three readers simultaneously:
a new human team member learning how the team works,
an agent loading context before generating code,
and a team auditing whether their declared conventions match their practice.
None of these use cases requires automation.

### Rationale 10

An earlier design treated SKILL.md as the primary artifact,
with the schema defined inside it.
This framing was rejected because it implied that automation was required
to use AO, which raised the barrier to adoption unnecessarily
and misrepresented where the intellectual contribution lies.

The schema defines what must be captured during onboarding
to enable an agent to code like a team member.
That question — what does an agent need to know? — is the hard problem.
How the file gets populated is a secondary concern.
Centering the schema makes the contribution legible
to teams that will never run SKILL.md
and to researchers building on top of the format.

The schema intentionally excludes project-specific domain knowledge
such as integration endpoints, data formats, and known failure modes
(e.g., [integrations], [gotchas]).
These are not team conventions; they are project context that varies
per codebase and cannot be generalized into a reusable schema.
If a team needs to capture this information in an agent-readable form,
a separate ao-domain.toml file is the appropriate location.
That format is not defined here and is deferred to future work.

## See Also

- [Markdown Architectural Decision Records](https://adr.github.io/madr/)
- [Adaptive Conformance Specification](https://github.com/adaptive-interfaces/adaptive-conformance-specification)
- [Adaptive Tool Discovery](https://github.com/adaptive-interfaces/adaptive-tool-discovery)
