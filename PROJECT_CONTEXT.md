# Project Context for Codex

This file is a session handoff for `CPE494-agent-coding-team`.

Use it with `AGENTS.md` when resuming work.

## Repos and Purpose

```text
lab-coding-agent/
  CPE494-agent-coding-team/
  CPE494-erp-invoice-app-by-ai/
```

- `CPE494-agent-coding-team`: agent orchestration, prompts, specs, run artifacts.
- `CPE494-erp-invoice-app-by-ai`: target app implementation repo.

## Current Baseline State

- Baseline is pre-sprint.
- No active sprint is assumed.
- Students should define Sprint 1 scope before generation.
- Target app stack is Django + SQLite + Templates + Bootstrap.

## Source-of-Truth Specs

- `logic_specs/erp_invoice_logic.md`
- `ui_specs/erp_invoice_ui.md`
- `ui_specs/ui_reference.pdf`

## Workflow Notebooks

- Generation and audit: `notebooks/agent_coding_workflow.ipynb`
- Apply approved files: `notebooks/transfer_generated_files.ipynb`

## Quick Resume Procedure

1. Read `AGENTS.md`.
2. Read this file.
3. Check `outputs/latest_run.txt` if present.
4. Review latest run artifacts:
   - `workflow_result.json`
   - `audit_logs.jsonl`
   - `applied_files.json` (if apply step was run)
5. Continue with current sprint definition and execution.
