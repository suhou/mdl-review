---
name: mdl-review
description: >-
  Perform a read-only code review using the Minimum Description Length
  principle. Identify over-modeling, under-modeling, duplicated business rules,
  growing conditional branches, caller-side knowledge, and displaced
  complexity. Intended only for explicit invocation on a diff or defined code
  scope. Do not modify code or replace correctness, security, performance,
  concurrency, or compatibility review.
---

# MDL Review

Review code only through the lens of total description cost:

`total description cost = model cost + residual cost`

- Model cost: abstractions, interfaces, dependencies, configuration,
  indirection, and required concepts.
- Residual cost: duplicated rules, branches, caller conventions, exceptions,
  and case-by-case tests.

Do not assume that fewer lines are simpler or that more abstractions are worse.

## Stay read-only

Do not edit files or implement refactors.

Focus on the requested diff and its direct impact. Inspect callers, tests, and
adjacent modules only as needed to understand the change.

Do not report unrelated historical debt unless the current change materially
worsens it.

## Workflow

### 1. Understand the behavior

Read the change, its callers, tests, and relevant project patterns.

Determine what the code is trying to achieve before evaluating how it expresses
that behavior.

### 2. Write the shortest honest description

Describe the resulting behavior in one to three sentences.

Frequent use of "except," "but," or "this entry point differs" may indicate
under-modeling.

A description that first requires many frameworks, custom concepts, or layers
may indicate over-modeling.

### 3. Inspect model cost

Look for:

- interfaces, factories, or strategy layers with one real use
- configuration without a demonstrated dimension of change
- wrappers that duplicate language, platform, or project capabilities
- extension points built only for speculative requirements
- simple rules that require navigating several files
- abstractions that relocate complexity without eliminating it

Report these only when they create concrete maintenance cost.

### 4. Inspect residual cost

Look for:

- one business rule implemented in several entry points
- conditional branches that grow with each new case
- internal conventions every caller must remember
- fixes that must be repeated in several locations
- tests that enumerate cases without expressing the shared rule
- unified interfaces backed by repeated type checks
- short code that hides essential rules in configuration or implicit behavior

Do not combine rules that differ for genuine domain reasons.

### 5. Validate each finding

Every finding must identify:

- concrete evidence
- whether it increases model cost or residual cost
- the resulting defect risk or maintenance burden
- a smaller and more honest replacement model

Style preferences, naming tastes, and hypothetical future benefits are not
findings.

### 6. Keep recommendations proportional

Prefer the smallest adjustment that reduces total description cost.

Do not recommend rewriting an entire module merely to make it uniform.

When the current design is already a reasonable balance, say so instead of
manufacturing findings.

## Severity

- High: the same rule can diverge across production paths, or the model is
  likely to cause recurring defects.
- Medium: abstractions or exceptions materially increase change scope and are
  likely to worsen.
- Low: a localized simplification opportunity with limited current risk.

Do not assign high severity to style concerns.

## Scope limitations

This skill does not claim to cover:

- functional correctness
- security
- performance
- concurrency
- data consistency
- API compatibility

Mention such issues when discovered, but label them as general engineering
review findings rather than MDL conclusions.

## Output

Lead with findings ordered by severity. For each finding, include:

- severity and a concise title
- file and line
- concrete evidence
- the added description cost
- the smallest viable improvement

Then summarize:

- the shortest honest description of the change
- the overall MDL assessment
- review dimensions or test risks not verified

When no actionable MDL issue exists, say so clearly and identify any remaining
review gaps.
