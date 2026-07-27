---
name: keep-scientific-code-simple
description: Keep scientific and data-processing code simple by reviewing unsupported defensive branches, excessive function extraction, premature abstraction, and other over-engineering that obscures the calculation. Use when reviewing or simplifying MATLAB, Python, R, Julia, Fortran, or similar research code. Follow a plan-to-code-to-scan-to-review-to-fix workflow, run relevant code to investigate actual behavior, and discuss one important decision at a time. Ask permission before searching the repository for upstream coding style or researching community implementations.
---

# Keep Scientific Code Simple

Review the calculation before its scaffolding. Prefer direct, evidence-based code over speculative safeguards and abstractions.

## Workflow

Follow these phases in order:

1. **Set scope.** Identify the relevant plan, target code, data range, expected outputs, and allowed execution environment. Read only the artifacts the user identified unless later permission expands the scope.
2. **Study the plan.** Extract the core calculation, data order, units, grids, masks, averaging rules, missing-data policy, and required outputs. Keep this focused on facts needed to judge simplicity.
3. **Study and run the code.** Restate the implementation in 3–7 ordered steps. Run static analysis and the smallest representative execution that can reveal actual behavior. Inspect runtime errors, warnings, branches reached, output shapes, missing values, and intermediate results when useful.
4. **Scan systematically.** Inventory every failure branch, fallback, early return, `continue`, default value, empty result, deliberate `NaN`, single-use function, wrapper, dynamic field system, status protocol, and speculative extension point.
5. **Report one decision at a time.** Start with the issue having the greatest effect on correctness or comprehension. Present evidence from the plan, code, data, and execution. Let the user decide before continuing. Do not modify code, plans, or issue trackers during this discussion.
6. **Consolidate and fix.** After all decisions are confirmed, summarize them and modify only the approved items. Prefer surgical edits that preserve the scientific calculation and existing structure.
7. **Run and verify.** Execute the relevant checks again. Confirm the calculation still runs, approved simplifications are effective, outputs retain the intended meaning, and no new fallback hides a failure. Report commands or tools used and the observed result.

Do not skip execution merely because the code looks correct. If execution is impossible because inputs, software, hardware, or access are unavailable, state the missing evidence instead of guessing.

## Run Code to Learn Actual Behavior

- Treat relevant code execution as an authorized part of review and verification.
- Start with a safe, small, representative case when a full run is expensive or writes outputs.
- Use the language-appropriate execution and testing tools. For MATLAB, use MATLAB static analysis, evaluation, file execution, and test tools rather than invoking MATLAB through a shell.
- Run large scientific computations on the configured HPC environment instead of locally.
- Inspect the real failure condition whenever practical. Do not create a fallback based only on reading the code.
- Avoid destructive, production, or materially expensive runs unless they are clearly within scope; obtain additional authority when required.
- Preserve raw errors and diagnostic evidence long enough to understand the cause. Do not suppress an error just to make a test pass.

## Require Evidence Before Defense

Do not turn uncertainty into a fallback. For every failure path:

1. State exactly what must happen for the branch to run.
2. Investigate whether it occurs in the relevant data range, upstream output, or documented input contract. Inspect the full relevant inventory when feasible; do not infer decades of data behavior from assumptions or a small sample.
3. Report the evidence, including affected dates, files, regions, records, or frequency when a failure exists.
4. Determine the scientifically valid response with the user before retaining or adding the branch.

Apply these outcomes:

- If the condition is verified not to occur, remove the fallback and add a concise comment stating the verified input invariant when that fact is not obvious.
- If the condition occurs, explain why and how often. Retain a branch only after its behavior is scientifically justified.
- If the condition is only hypothetical, do not add speculative recovery logic.
- If the condition cannot be investigated with the available scope, ask the user. Do not silently substitute `continue`, zero, an empty array, a default value, or `NaN` merely to let the program finish.
- Use a hard error only when evidence or an explicit contract shows that continuing would make the result invalid. Do not use error handling to avoid deciding how real missing data should be treated.

Comments must record a verified invariant or scientific reason, not assert an untested assumption. Prefer references to upstream quality control or a data inventory when available.

## Apply KISS

- Prefer the simplest direct implementation that satisfies the current scientific requirement.
- Treat complexity supported by data evidence, correctness, or measured performance as necessary complexity.
- Flag wrappers, dynamic field-name systems, status protocols, configuration layers, speculative extensibility, and repeated conversions that do not reduce total complexity.
- Do not use function length, nesting depth, or call count as automatic verdicts.
- Do not design for hypothetical future inputs or reuse.

## Decide Whether to Keep a Function

Keep a function only when it provides concrete value by doing at least one of the following:

- names a meaningful scientific or data-processing operation;
- hides substantial detail and makes the caller easier to understand;
- removes real duplication;
- has multiple real consumers or a stable external contract;
- isolates a useful boundary such as reading, interpolation, geometry construction, or validation.

Recommend inlining a single-use function when it merely moves values, renames variables, constructs a trivial struct, wraps a short expression, or forces the reader to jump elsewhere without lowering cognitive complexity. A single call site alone is not sufficient reason to inline.

## Use the Plan as Supporting Evidence

Use the plan, equations, units, grids, masks, averaging rules, and required outputs to determine whether complexity or failure handling is justified. Plan conformance is secondary to the simplicity review; do not turn the review into a general requirements audit.

## Search Only With Permission

Ask before searching the repository for existing style or upstream conventions. After permission, inspect the smallest relevant scope and prioritize upstream stages in the same data-processing chain. Learn data order, dimensions, units, missing-value handling, grid and time conventions, and intermediate-result organization.

Ask before researching community functions. Recommend a community implementation only when it matches the scientific definition, is mature, has acceptable dependency cost, and makes the code simpler without hiding a critical formula.

## Output One Decision at a Time

For each decision, provide:

1. The branch, abstraction, or function under review.
2. Evidence from the code and data.
3. Whether it is justified, unsupported, or still unknown.
4. The smallest recommendation.
5. A clear choice for the user to accept, reject, or adjust.

Avoid generic checklists, quality scores, style nitpicks, and unrelated refactors. Cite file and line references when available.
