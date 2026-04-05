# Scenario: Multi-Repository, authoritative_for Scoping

Apply the Adaptive Onboarding (AO) skill to generate a Team Context Document
using multiple source repositories with explicit authority scoping.

No target repository is specified.
Generate a context document that captures org-level conventions
from the sources defined below.

Use the following sources:

```toml
[[sources.repos]]
url = "https://github.com/denisecase/datafun-toolkit"
authoritative_for = ["packaging", "pyproject", "ci"]

[[sources.repos]]
url = "https://github.com/civic-interconnect/multidimensional-evaluation-engine"
authoritative_for = ["testing", "type_annotations", "docstrings"]

[[sources.repos]]
url = "https://github.com/denisecase/pro-analytics-02"
authoritative_for = ["documentation", "conventions"]
```

Produce a context document at `.agent/ao-context.toml`.
For each convention, record which source it came from.
Where sources conflict on a convention outside their declared scope,
record the conflict in `meta.divergences` and apply the priority rule:
the source with the most specific `authoritative_for` scope wins.

After completing the context document:

1. Copy `ao-context.toml` to `evaluation/scenarios/multi-repo-authoritative-for/ao-context.toml`
2. Write a summary of the AO run to `evaluation/scenarios/multi-repo-authoritative-for/response.txt`
3. Score the output against `evaluation/rubric.md` and include the score table
   in `response.txt`
