---
name: keep-scientific-code-simple
description: Review scientific and data-processing code primarily for unsupported defensive branches and unnecessary complexity. Reconstruct the core algorithm, compare it with the identified plan, and check whether file-header and key comments accurately document the calculation. Use only when the user explicitly invokes $keep-scientific-code-simple. Do not trigger automatically based on the presence of MATLAB, Python, R, Julia, Fortran, scientific code, code review, or simplification tasks.
---

# Keep Scientific Code Simple

Understand and document the calculation before reviewing its scaffolding. Keep unsupported defenses as the primary target, using plan alignment and accurate comments as necessary evidence. Prefer direct, evidence-based code over speculative safeguards and abstractions.

## Workflow

Follow these phases in order:

1. **Set scope.** Identify the relevant plan, target code, data range, expected outputs, and allowed execution environment. Read only the artifacts the user identified unless later permission expands the scope.
2. **Study the plan.** Extract the core calculation, data order, units, grids, masks, averaging rules, missing-data policy, and required outputs. Restate the planned calculation in 3–7 ordered steps.
3. **Reconstruct the code.** Without assuming its comments are correct, restate the implementation in 3–7 ordered steps. Identify the actual inputs, outputs, scientific conventions, and major transformations.
4. **Compare plan, code, and comments.** Map the planned steps to the implemented steps. Note only differences that affect the calculation, its interpretation, or the evidence needed to judge defensive code. Check whether the file header and section comments accurately describe the implementation.
5. **Study actual behavior.** Run static analysis and the smallest representative execution that can reveal actual behavior. Inspect runtime errors, warnings, branches reached, output shapes, missing values, and intermediate results when useful.
6. **Scan defensive complexity systematically.** Inventory every failure branch, fallback, early return, `continue`, default value, empty result, deliberate `NaN`, single-use function, wrapper, dynamic field system, status protocol, and speculative extension point.
7. **Gate A: accept review decisions.** Report one important decision at a time, starting with the issue having the greatest effect on correctness or comprehension. Present evidence from the plan, code, comments, data, and execution. Let the user accept, reject, or adjust the conclusion, record that decision, and continue without modifying files.
8. **Gate B: approve the exact edit plan.** After all decisions are confirmed, consolidate them into one final edit plan. List every file and code location, the exact deletion, replacement, or addition, all effects on comments, logs, variable names, section numbering, and outputs, whether scientific results will change, and the planned verification. Then ask exactly: “是否按上述精确方案修改？”
9. **Gate C: execute the approved plan.** Edit only after the user explicitly replies with an instruction equivalent to execute or modify. Save a snapshot first, apply only the approved differences, inspect the actual diff, restore any unapproved difference, and run the approved verification.
10. **Report the actual result.** State the lines actually changed, recommendations not executed, verification results, and whether any unapproved difference remains.

Do not skip execution merely because the code looks correct. If execution is impossible because inputs, software, hardware, or access are unavailable, state the missing evidence instead of guessing.

The three gates are independent. Acceptance at Gate A does not authorize Gate B or Gate C. Approval of the plan at Gate B does not authorize any work outside that exact plan.

## Keep Review Read-Only

- Treat requests to review, scan, audit, or suggest simplifications as read-only. Read files, run safe read-only diagnostics, and report findings, but do not modify target code, plans, or issue trackers.
- Treat confirmation of a conclusion, such as agreeing that a branch can be deleted, only as acceptance of that decision. Record it; do not edit.
- Treat “continue scanning,” “tell me what else can be cleaned up,” “confirm this issue,” and equivalent language only as authority to continue reviewing and reporting.
- Do not edit until the user has seen the complete exact edit plan and then separately gives an explicit instruction to execute or modify.

## Require an Exact Edit Plan

Before requesting execution authority, list:

1. every file to modify;
2. each specific code block or line location;
3. the exact content to delete, replace, or add;
4. whether comments, logs, variable names, section numbering, or outputs will change;
5. whether scientific calculation results will change;
6. the verification to run after editing.

Do not use summaries such as “simplify this section” in place of an exact plan. Include every necessary companion edit as a separate approved item.

## Apply Only the Approved Diff

- Save a snapshot of every target file before editing.
- Make surgical edits that implement only the differences shown in the approved final plan.
- Do not opportunistically change adjacent comments, logs, formatting, names, section numbers, outputs, other scan findings, called functions, or other scripts.
- Do not make a necessary companion change unless it was separately listed and approved in advance.
- Compare the actual diff with the snapshots after editing. If the diff exceeds the approved plan, restore the unapproved parts before reporting completion.

## Establish the Calculation and Check Its Comments

- Derive the planned algorithm and the implemented algorithm independently. Do not use comments to fill gaps in the code or use the code to silently rewrite the plan.
- When a plan exists, map each core planned step to the corresponding code section. Classify material differences as aligned, changed, missing, or still unknown.
- When no plan is available, state that plan conformance cannot be judged. Do not invent the intended method.
- Treat plan alignment as evidence for the simplicity review, not as a general requirements audit.
- Check that the file header states the actual purpose, core steps, inputs, and outputs. Include units, grids, time bases, masks, or missing-data rules only when they are important to interpreting the calculation.
- Check that section comments follow the real algorithmic stages. Do not require a section merely because a template contains one.
- Prefer comments that explain scientific reasons, verified data invariants, units, non-obvious transformations, or justified exceptions. Flag comments that merely restate code only when they obscure more useful information.
- Treat stale or contradictory comments as review findings when they can mislead the calculation or the judgment of defensive logic.
- Avoid author blocks, change histories, fragile line-number references, and other boilerplate that does not help reconstruct the calculation.

For MATLAB scripts, use this as a lightweight review baseline rather than a mandatory fixed layout:

```matlab
%% script_name.m
%
% 功能:
%   用 1–3 句话说明脚本完成的科学计算及其在处理流程中的位置。
%
% 核心步骤:
%   1. 读取并统一……
%   2. 按……计算……
%   3. 根据……筛选或聚合……
%   4. 保存……
%
% 输入:
%   1. 数据或上游文件：内容，以及必要的单位或维度
%
% 输出:
%   1. 输出文件或变量：用途
%
% 关键约定:  % 仅在确有必要时保留
%   时间基准、坐标顺序、单位、掩膜或缺测处理。

%% 1. 参数与输入

%% 2. 数据读取

%% 3. 核心计算

%% 4. 筛选或统计聚合

%% 5. 保存结果
```

Remove empty sections and adapt names and numbering to the actual algorithm. Do not add `clear`, `clc`, `close all`, local functions, or other executable scaffolding merely to match the template.

## Run Code to Learn Actual Behavior

- Treat safe, read-only diagnostic execution as an authorized part of review and verification. Obtain additional authority before any run that writes outputs or materially changes state.
- Start with a safe, small, representative case when a full run is expensive or writes outputs.
- Keep diagnostic code minimal. Before creating a temporary diagnostic file, state its purpose, size, expected runtime or cost, and location. Prefer short read-only checks, do not create a large temporary script for a simple review, and remove the temporary file when finished.
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

- If the condition is verified not to occur, recommend removing the fallback and, when useful, adding a concise comment stating the verified input invariant. Apply that recommendation only through the approval gates above.
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

## Keep Plan Alignment Focused

Use the plan, equations, units, grids, masks, averaging rules, missing-data policy, and required outputs to determine whether complexity or failure handling is justified. Report plan-code differences that affect the scientific calculation or the interpretation of defensive behavior. Leave unrelated requirement, style, and documentation issues outside the review.

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

Before the first decision, briefly show the planned 3–7 steps, the implemented 3–7 steps, and any material alignment or comment gap. After the user responds, record the decision and proceed to the next decision without editing. Only after all decisions are complete should you produce the consolidated exact edit plan and request one execution authorization.

Avoid generic checklists, quality scores, style nitpicks, and unrelated refactors. Cite file and line references when available.

## Report the Actual Edit

After modification, report:

1. the lines actually changed;
2. recommendations that remain unexecuted;
3. verification performed and its results;
4. whether any unapproved difference exists.

Do not treat a passing static check as proof that the modification scope is correct. Verify the actual diff against the approved plan separately.
