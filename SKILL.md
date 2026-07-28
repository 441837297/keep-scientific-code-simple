---
name: keep-scientific-code-simple
description: Simplify scientific and data-processing code by removing redundant defensive handling, duplicate work, unused state, thin single-use wrappers, and misleading scaffolding while preserving the intended scientific calculation. Use only when the user explicitly invokes $keep-scientific-code-simple.
---

# Keep Scientific Code Simple

Keep the scientific calculation direct. Review serves simplification: do not turn every normal branch into an audit target.

## Workflow

Follow these phases in order:

1. **Set scope and reconstruct the calculation.** Read only the user-identified code and the minimum necessary plan or upstream contract. State the core calculation in 3-7 steps: inputs, transformations, masks or averaging, sample policy, and outputs.
2. **Scan for direct simplifications.** Prioritize unused variables and calls, duplicate filtering or reporting, repeated conversions, empty sections, thin single-use wrappers, unnecessary state, and speculative failure handling. Do not enumerate every ordinary `if`, `continue`, `return`, or `assert`.
3. **Investigate only result-changing exception handling.** Gather data evidence only for an abnormal branch that changes formal numerical results, the formal sample set, or missing-data semantics. Examples include filling or replacing missing values, excluding samples, geometry fallbacks, threshold rules, and silently skipping records. For workflow branches such as test mode, progress logs, output naming, established grouping, and ordinary empty-period `NaN` semantics, confirm their purpose and calculation equivalence without a full data audit.
4. **Compare plan, code, and comments.** Report only material mismatches and comments that misstate the calculation or conceal removable complexity. Do not perform a general requirements or style audit.
5. **Present focused recommendations.** Group purely mechanical cleanups. Discuss scientific choices separately only when they change results, samples, or missing-data meaning. For every recommendation, state the code location, smallest change, scientific effect, and evidence when required.
6. **Edit and verify after approval.** Provide an exact edit plan, wait for explicit execution authority, make only the approved changes, inspect the diff, and run proportionate verification.

## Keep Review Read-Only

- Treat requests to review, scan, audit, or suggest simplifications as read-only.
- Do not edit until the user has seen a complete exact edit plan and explicitly authorizes modification.
- Do not create backups or snapshots unless the user asks. Inspect Git status and existing diffs first; preserve pre-existing user changes.
- Do not submit a large HPC job merely to audit code. Use a small relevant test only after the user authorizes an execution that writes outputs.

## Require an Exact Edit Plan

Before requesting execution authority, list every file and code block to modify, the exact deletion/replacement/addition, comment/log/output changes, scientific effect, and planned verification. Then ask exactly: "是否按上述精确方案修改？"

After authorization, apply only that plan. Inspect the final diff and report changed lines, unexecuted recommendations, verification results, and any unapproved difference.

## Evidence for Exception Handling

Do not retain or add an exception branch merely because a failure is conceivable. For a branch that changes results, samples, or missing-data meaning:

1. State its trigger and scientific consequence.
2. Check whether it occurs in the relevant data or an explicit upstream contract.
3. Report affected dates, records, regions, or frequency when available.
4. Decide with the user whether to keep, remove, or replace it.

Use these outcomes:

- Verified and scientifically justified: retain it with a concise reason.
- Verified but unnecessary: recommend the smallest removal or consolidation.
- Hypothetical only: do not add a fallback.
- Unknown and consequential: ask before choosing a fallback, deletion, zero, empty result, or `NaN`.

Input-file assertions and test-input assertions are contracts, not missing-data policy. Keep them unless they duplicate a stronger upstream contract or obstruct the requested workflow.

## Simplification Priorities

Prefer these changes when they reduce total cognitive load without changing the calculation:

- delete unused variables, calls, logs, output files, and empty sections;
- merge exactly duplicated filtering, loading, or reporting paths;
- count source-code call sites for every local function; a function with one call site is an inline candidate by default, even when that call sits in a loop or `parfor`;
- retain a local function only when it has multiple source-code call sites or an explicit external interface; repeated runtime execution does not count as reuse;
- remove state whose only purpose was a deleted diagnostic or fallback;
- use the existing data model and helpers instead of adding a new abstraction;

Do not refactor for hypothetical reuse. Do not replace direct, readable scientific code with configuration layers, dynamic protocols, or abstractions that make the calculation harder to follow.

## Calculation and Comments

Derive the intended calculation independently of comments. Check headers and key comments only where they affect interpretation: units, grids, time basis, masks, averaging, or missing-data policy. Remove stale claims and comments that merely narrate obvious code; retain concise explanations of scientific reasons and verified exceptions.

For MATLAB scripts, use a simple header only when it helps identify purpose, inputs, core calculation, and outputs. Do not add `clear`, `clc`, `close all`, local functions, or empty sections merely to match a template.

## Verification

Use language-appropriate static checks after editing. Run the smallest representative execution that tests the changed behavior, not an unrelated full pipeline. For MATLAB, use MATLAB tools for local checks and the configured HPC workflow for large calculations. Preserve raw errors long enough to understand a real failure; do not suppress an error just to pass a test.

## Report

Lead with the simplifications and their scientific effect. Mention retained result-changing exception branches only when they were considered. Keep the report focused on the requested code and avoid generic quality scores or exhaustive branch inventories.
