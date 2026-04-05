# Scenario: Divergence Detection

Apply the Adaptive Onboarding (AO) skill to generate a Team Context Document
for a repository that contains known divergences from community best practices.

The target repository is:
<https://github.com/denisecase/cintel-01-getting-started>

This is a course scaffold repository.
It may use simplified project structure by design.

No `ao-config.toml` is provided.
Use `best_practices_stance = "balanced"`.

Produce a context document at `.agent/ao-context.toml`.
For each convention that diverges from community best practices:

- Record the observed convention
- Note the best practice it diverges from
- Record the entry in `meta.divergences`
- Do not silently conform to the divergent practice
- Do not silently override it in favor of the best practice

After completing the context document:

1. Copy `ao-context.toml` to `evaluation/scenarios/divergence-detection/ao-context.toml`
2. Write a summary of the AO run to `evaluation/scenarios/divergence-detection/response.txt`
3. Score the output against `evaluation/rubric.md` and include the score table
   in `response.txt`
