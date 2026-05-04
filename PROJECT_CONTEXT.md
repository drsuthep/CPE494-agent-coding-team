# Project Context for Codex

This file is a session handoff for `CPE494-agent-coding-team`.
Use it together with `AGENTS.md` when resuming work.

## Repos and Purpose

Two sibling repos under one parent folder:

```text
lab-coding-agent/
  CPE494-agent-coding-team/
  CPE494-erp-invoice-app-by-ai/
```

- `CPE494-agent-coding-team`: orchestration, prompts, specs, run artifacts.
- `CPE494-erp-invoice-app-by-ai`: generated ASP.NET Core app code only.

## Source-of-Truth Specs

```text
logic_specs/erp_invoice_logic.md
ui_specs/erp_invoice_ui.md
ui_specs/ui_reference.pdf
```

## Current Workflow Status

- Main orchestration file: `notebooks/agent_coding_workflow.ipynb`.
- Sprint 1 run referenced in recent work:
  - `outputs/runs/2026-05-04_141246_sprint_01/`
  - result: 7 pass, 1 fail (`Pages/Login.cshtml` failed UI audit).

## Important Recent Change

Step 8 apply logic was updated from all-or-nothing to per-file selection.

Updated functions in notebook:

```text
load_workflow_results()
latest_attempt_code_path()
build_apply_candidates()
prompt_apply_candidate()
apply_generated_files_to_target()
```

Behavior now:

1. Reads file statuses from `workflow_result.json` when available.
2. Prompts one file at a time for apply decision.
3. Default selection is:
   - `y` for `PASS`
   - `n` for `FAIL`
4. If a file passed, apply source is `generated_files/<path>`.
5. If a file failed, fallback source is latest attempt code in:
   `attempts/<file_key>/attempt_XX_code.txt`
6. Writes both applied and skipped decisions to `applied_files.json`:
   - `files`
   - `skipped_files`

Note: older attempts are retained for traceability but are not shown as separate choices.

## How to Resume Quickly Next Session

When user says "Please read AGENTS.md and other docs to get caught up":

1. Read `AGENTS.md`.
2. Read this file `PROJECT_CONTEXT.md`.
3. Check latest run folder from `outputs/latest_run.txt`.
4. Review these files in that run:
   - `workflow_result.json`
   - `audit_logs.jsonl`
   - `applied_files.json` (if apply step was run)
5. Continue from next sprint goal (currently Sprint 1.1 login placeholder focus).

## Current Practical Next Step

- Re-run Login work as Sprint 1.1.
- Keep bundle mindset for:
  - `Pages/Login.cshtml`
  - `Pages/Login.cshtml.cs`
- Use per-file apply prompt defaults (PASS => `y`, FAIL => `n`) when porting to app repo.
