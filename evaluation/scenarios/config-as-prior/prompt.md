# Scenario: ao-config.toml as Prior

Apply the Adaptive Onboarding (AO) skill to generate a Team Context Document
for a repository, using a provided `ao-config.toml` as a prior.

The target repository is:
<https://github.com/denisecase/datafun-toolkit>

The following `ao-config.toml` is pre-populated for this scenario.
Save it to `.agent/ao-config.toml` before running AO.

```toml
[owner]
name = "denisecase"
github = "denisecase"
best_practices_stance = "balanced"

[defaults.python]
package_manager = "uv"
layout = "src"
build_backend = "hatchling"
formatter = "ruff format"
linter = "ruff check"
type_checker = "pyright strict"
test_runner = "pytest"
ci = "github-actions"
confirmed = true

[defaults.code_style]
max_function_lines = 30
docstring_style = "google"
docstring_required_on = "all public functions and classes"
type_annotations = "required on all public functions"
confirmed = false
```

Instructions:

- Accept all `confirmed = true` entries without re-inference
- Verify all `confirmed = false` entries against observed code patterns
- If any `confirmed = false` entry conflicts with observed patterns,
  record the conflict in `meta.divergences` with both values
- Do not silently resolve any conflict in either direction

Produce a context document at `.agent/ao-context.toml`.

Note: This scenario requires an agent with file system access and the ability
to read GitHub repositories.
Claude Code is the recommended runner.
ChatGPT and similar chat interfaces cannot execute this scenario end-to-end
without manual file preparation.
See the README for an adapted workflow.

After completing the context document:

1. Copy `ao-context.toml` to `evaluation/scenarios/config-as-prior/ao-context.toml`
2. Write a summary of the AO run to `evaluation/scenarios/config-as-prior/response.txt`
3. Score the output against `evaluation/rubric.md` and include the score table
   in `response.txt`
