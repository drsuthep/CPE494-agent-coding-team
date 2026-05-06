# Project Context for Codex

This file is a session handoff for `CPE494-agent-coding-team`.
Read this first, then read `AGENTS.md`.

## Repos and Purpose

```text
lab-coding-agent/
  CPE494-agent-coding-team/
  CPE494-erp-invoice-app-by-ai/
```

- `CPE494-agent-coding-team`: orchestration, prompts, baseline specs, sprint scopes, run artifacts.
- `CPE494-erp-invoice-app-by-ai`: generated Django application code.

## Current Branch State

- Both repos are currently on branch: `sprint_django_0`.
- Milestone tag exists in both repos: `pre_sprint0_student_baseline`.
  - Agent repo tag commit: `e29d082` (`Ready for Sprint 1 using Django`).
  - App repo tag commit: `5f5c8a0` (`Add Day 1 Django environment setup to README`).

## Active Sprint

- Active sprint scope file: `sprints/sprint_00_scope.md`.
- Sprint objective: Django "Hello, world!" smoke test to validate the four-agent pipeline end-to-end.

## Source-of-Truth Files (Layer 1 + Layer 2)

Layer 1 baseline:
- `AGENTS.md`
- `logic_specs/erp_invoice_logic.md`
- `ui_specs/erp_invoice_ui.md`
- `ui_specs/form_workflow.md`
- `prompts/role_*.txt`

Layer 2 sprint contract:
- `sprints/sprint_00_scope.md`

## Notebook Status

- `notebooks/agent_coding_workflow.ipynb` and `notebooks/transfer_generated_files.ipynb` were converted from ASP.NET assumptions to Django flow.
- Prior ASP.NET notebook outputs were cleared in previous cleanup.

## Latest Run Marker

- `outputs/latest_run.txt` currently points to:
  - `2026-05-06_231917_sprint_00_scope`

## Important Working-Tree Note Before Next Run

Agent repo currently has uncommitted changes on `sprint_django_0`:
- `AGENTS.md`
- `logic_specs/erp_invoice_logic.md`
- `notebooks/agent_coding_workflow.ipynb`
- `sprints/sprint_00_scope.md`
- `sprints/sprint_template.md`
- `ui_specs/erp_invoice_ui.md`
- `ui_specs/form_workflow.md`

App repo currently appears clean on `sprint_django_0`.

## Environment Notes

- Notebook kernel issue previously observed: missing/broken `.venv` kernel registration.
- Recommended recovery:
  1. Recreate/activate `.venv`
  2. Install `ipykernel` and project dependencies
  3. Register kernel with:
     - `python -m ipykernel install --user --name cpe494-agent-venv --display-name "cpe494-agent-venv"`

## Resume Checklist

1. Open `AGENTS.md`, then `sprints/sprint_00_scope.md`.
2. Ensure VS Code kernel is `cpe494-agent-venv` (or the current `.venv` interpreter).
3. Decide whether to commit current uncommitted agent-repo edits before running.
4. Run `notebooks/agent_coding_workflow.ipynb`.
5. Run `notebooks/transfer_generated_files.ipynb` to apply approved files and perform smoke gate.
