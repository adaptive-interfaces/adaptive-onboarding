# AO Conformance Rubric

This rubric evaluates whether an agent execution conforms to the
Adaptive Onboarding (AO) skill specification.

Evaluation is based on the `ao-context.toml` produced
and the behavioral constraints defined in `SKILL.md`.

All judgments must be grounded in the submitted context document
and execution record.
No credit is given for implied or assumed behavior.

## Evaluation Dimensions

Each dimension is scored independently.

### 1. Source Enumeration

Question: Were available context sources sufficiently discovered and listed?

Full (2)

- All available sources enumerated: repo structure, config files,
  CI workflows, documentation, code patterns
- Sources consulted are explicit and recorded
- ao-config.toml loaded as prior if present

Partial (1)

- Some sources identified but enumeration is incomplete
- Sources partially documented

None (0)

- No meaningful source enumeration
- Conventions assumed without inspection

### 2. Convention Accuracy

Question: Was each convention correctly interpreted?

Full (2)

- Toolchain, naming, structure, style, and test conventions
  accurately described and grounded in observed evidence
- Required vs optional fields correctly identified
- Source authority hierarchy applied correctly
  (CI > config file > style guide > code patterns)

Partial (1)

- Minor inaccuracies or omissions
- Some fields weakly justified or from wrong source

None (0)

- Misinterpretation of conventions
- Incorrect assumptions without evidence

### 3. Best Practices Assessment

Question: Were divergences from best practices identified and handled correctly?

Full (2)

- Each convention assessed against community best practices
- Divergences recorded in `meta.divergences` with both values and sources
- `best_practices_stance` applied correctly; no silent conformance or override

Partial (1)

- Some divergences identified but not all
- Stance applied inconsistently

None (0)

- No best practices assessment performed
- Divergences silently conformed to or overridden

### 4. ao-config.toml Handling

Question: Was a provided ao-config.toml correctly used as a prior?

Full (2)

- `confirmed = true` entries accepted without re-inference
- `confirmed = false` entries verified against observed patterns
- Conflicts between declared and observed conventions recorded
  in `meta.divergences`; not silently resolved

Partial (1)

- Prior partially respected; some confirmed entries re-inferred
- Conflicts noted but not fully recorded

None (0)

- ao-config.toml ignored or overridden without recording conflicts
- confirmed flag not respected

### 5. Gap and Inference Transparency

Question: Are gaps and inferred entries explicitly recorded?

Full (2)

- All conventions that could not be determined recorded in `meta.gaps`
- All inferred conventions recorded in `meta.inferred` with justification
- No invented or generalized conventions present

Partial (1)

- Most gaps and inferences recorded; minor omissions

None (0)

- Gaps not recorded
- Inferences presented as confirmed observations

### 6. Schema Completeness

Question: Does ao-context.toml satisfy the required schema?

Full (2)

- All required fields present and populated
- Language-specific section present and correct
- meta fields fully populated including produced_by and produced_on

Partial (1)

- Most fields present; minor omissions
- Some required fields empty without explanation

None (0)

- Required fields missing
- Fields guessed or fabricated

### 7. Transparency and Traceability

This dimension is cross-cutting.
It evaluates the traceable relationship between evidence
and context document entries across all conventions recorded.

Question: Can each entry be traced back to observed evidence?

Full (2)

- All entries traceable to observed evidence
- Uncertainty and limits explicitly stated

Partial (1)

- Partial traceability
- Some entries not clearly justified

None (0)

- Entries cannot be traced to evidence
- Hidden assumptions present

## Scoring

Each dimension is scored:

- 2 = Full
- 1 = Partial
- 0 = None

Maximum score: 14

## Evaluation Outcomes

- Conformant (12–14)
  - Strong source enumeration, accurate convention mapping, full schema
  - No structural violations

- Partially Conformant (7–11)
  - Some gaps or minor inconsistencies
  - No critical violations

- Non-Conformant (0–6)
  - One or more critical failures:
    - conventions assumed without inspection
    - fabricated or hallucinated conventions
    - context entries without evidence
    - silent divergence handling
    - required fields omitted without explanation

## Critical Violations (Automatic Non-Conformance)

Any of the following results in a Non-Conformant rating regardless of score:

- No evidence of source enumeration
- Fabricated or hallucinated conventions not present in the codebase
- Context entries not grounded in observed evidence
- Divergences silently conformed to or overridden without recording
- confirmed = true entries re-inferred without flagging the conflict

## Notes

- Evaluation is based only on what is explicitly shown.
- Missing schema fields are treated as missing behavior.
- Partial correctness does not compensate for structural violations.
