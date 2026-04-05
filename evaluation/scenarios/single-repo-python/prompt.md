# Scenario: Single Repository, Python

Apply the Adaptive Onboarding (AO) skill to generate a Team Context Document
for a single Python repository.

The target repository is:
<https://github.com/denisecase/datafun-toolkit>

No `ao-config.toml` is provided for this scenario.
Discover all conventions from observed evidence only.

Produce a context document at `.agent/ao-context.toml`.
Mark all inferred conventions explicitly in `meta.inferred`.
Record any conventions that could not be determined in `meta.gaps`.

After completing the context document:

1. Copy `ao-context.toml` to `evaluation/scenarios/single-repo-python/ao-context.toml`
2. Write a summary of the AO run to `evaluation/scenarios/single-repo-python/response.txt`
3. Score the output against `evaluation/rubric.md` and include the score table
   in `response.txt`
