# AGENTS.md

CPE494 Agent Coding Lab — Operating Manual.

This file tells the four AI agents in the team **how** to work. It does not say **what** to build in any current sprint — that comes from the active sprint scope file in `sprints/`.

## 1. Project Goal

The lab uses a multi-agent AI team to incrementally build a teaching ERP application that serves as a **development template**. The first three core entities are **Customer**, **Product**, and **Invoice** (with line items). Additional entities will be added later. Every entity follows the same UI/UX patterns and reuses the same list view code, form view code, and template partials. Investments made in any of those shared components benefit every existing and future entity at once.

## 2. Two-Layer Spec-Driven / Test-Driven Architecture

The lab follows Spec-Driven Development (SDD) and Test-Driven Development (TDD) using two layers of contract.

**Layer 1 — Baseline (this folder):** broad, stable rules.

- `AGENTS.md` — this file (operating manual)
- `logic_specs/erp_invoice_logic.md` — broad logic principles
- `ui_specs/erp_invoice_ui.md` — broad UI / theme principles
- `ui_specs/form_workflow.md` — form behavior (modes, buttons, dirty bit, save feedback, LOV, navigation)
- `ui_specs/ui_reference.pdf` — visual inspiration only (Thai labels: ignore — output English only)
- `prompts/role_*.txt` — agent behavior contracts (one per agent)
- `prompts/ui_checklist_for_coder.txt` — broad UI checklist

**Layer 2 — Sprint scope (per sprint):** detailed contract.

- `sprints/sprint_NN_scope.md` — entity fields, file bundle, acceptance criteria, test cases, smoke-run, definition of done. Authored fresh per sprint.

**Every agent run loads BOTH layers.** The current sprint scope file is the authoritative contract for what to build in this sprint. Layer 1 answers "how do we build"; Layer 2 answers "what are we building right now."

## 3. Two Repositories

```text
lab-coding-agent/
  CPE494-agent-coding-team/        # orchestration: prompts, specs, sprints, notebooks, run logs
  CPE494-erp-invoice-app-by-ai/    # target Django app code only
```

## 4. Folder Structure

```text
CPE494-agent-coding-team/
  AGENTS.md
  notebooks/
    agent_coding_workflow.ipynb
    transfer_generated_files.ipynb
  prompts/
    role_architect.txt
    role_coder.txt
    role_logic_tester.txt
    role_ui_auditor.txt
    ui_checklist_for_coder.txt
  logic_specs/
    erp_invoice_logic.md
  ui_specs/
    erp_invoice_ui.md
    form_workflow.md
    ui_reference.pdf
  sprints/
    sprint_template.md
    sprint_NN_scope.md         # authored per sprint by the instructor
  outputs/
    runs/
      <run_id>/
        run_manifest.json
        input_snapshot/
        input_snapshot_manifest.json
        gemini_calls.jsonl
        architect_manifest.md
        tasks.json
        attempts/
        generated_files/
        audit_logs.jsonl
        workflow_result.json
        applied_files.json
        command_logs.jsonl
    latest_run.txt
```

## 5. Tech Stack

- Django 5.x
- Python 3.11+
- Django ORM + SQLite (teaching default)
- Django Templates
- Bootstrap 5 + Font Awesome 6 (CDN)

## 6. Agent Team

Four agents, one role each. The team size is intentionally fixed at four to keep the lab tractable.

### Architect Agent

- Reads: baseline + active sprint scope.
- Produces: human-readable manifest + JSON task list.
- Each task carries: `feature_group`, `spec_reference`, `acceptance_criteria`, `test_cases`, `verification_methods`, `dependencies`, `done_definition`.
- Does NOT write code.

### Coder Agent

- Reads: baseline + active sprint scope + assigned task contract + existing target file (if any) + previous audit feedback (if any).
- Produces: ONE source file at a time as raw source — no markdown fences, no prose.
- For tasks that include test cases, the bundle includes a `tests.py` task; the coder writes those concrete assertions as Django `TestCase` methods.

### Logic Tester Agent

- Reads: baseline + active sprint scope + generated file + task contract.
- Produces: `PASS` or `FAIL` (first line) followed by concise bullets mapped to acceptance criteria, verification methods, or direct defects.
- Audits Django wiring, test coverage of acceptance criteria, and conformity to baseline architectural rules.

### UI Auditor Agent

- Reads: baseline + active sprint scope + generated UI file + ui_reference.pdf.
- Produces: `PASS` or `FAIL` (first line) followed by concise bullets.
- Audits theme, English-only output, canonical button vocabulary, navigation hierarchy, LOV pattern compliance.

## 7. Hybrid File Bundle Protocol

The architect groups related files under the same `feature_group` (a "feature bundle"). A bundle is applied atomically — either every file in a bundle passes audits or the bundle is held back at the transfer step. Each task in `tasks.json` represents **one file**; tasks in the same bundle share a `feature_group` value.

## 8. Reuse Architecture

The app is a development template. Every existing and future entity reuses the same shared modules:

- **Shared list view code** — one Django view module is parameterized by model class. Every entity's list page renders through it. Investments in sorting, searching, filtering, and pagination apply to every list at once.
- **Shared form view code** — one Django view module is parameterized by model class and form class. Every entity's create/edit page renders through it. Investments in dirty-bit handling, save feedback, validation display, and Close/Delete behavior apply everywhere.
- **Shared template partials** — `templates/_partials/list_table.html` and `templates/_partials/form_layout.html` carry the visual layout reused by every entity's templates.
- **List of Values (LOV) pattern** — every foreign key field renders as `hidden id input + visible display-code input + LOV button`. The LOV button opens the related entity's list view in select-mode. Selecting a row fills the FK and returns to the parent form. Defined in `ui_specs/form_workflow.md`.
- **Canonical UI vocabulary** — the action buttons (New, Save, Close, Edit, Delete, LOV) and their semantics are the same across every form. Defined in `ui_specs/form_workflow.md`.

The exact moment in the build progression when each shared module is *introduced* is decided per sprint. The architect uses the active sprint scope to choose between extracting a new shared module from per-entity code, or reusing existing shared code. The architect must NOT plan to duplicate code that a shared module already covers.

## 9. Run Folder Logging

Every notebook run creates `outputs/runs/<run_id>/` containing:

- `run_manifest.json`
- `input_snapshot/` (snapshot of all baseline files AND the active sprint scope used in the run)
- `input_snapshot_manifest.json`
- `gemini_calls.jsonl`
- `architect_manifest.md`
- `tasks.json`
- `attempts/<file>/` (every generation attempt with its audit)
- `generated_files/` (only audit-PASS files)
- `audit_logs.jsonl`
- `workflow_result.json`
- `applied_files.json` (after transfer step)
- `command_logs.jsonl` (after smoke run)

## 10. Retry Strategy

- `MAX_ATTEMPTS = 3` per file.
- Attempt 1: normal generation.
- Attempt 2: retry with audit feedback.
- Attempt 3: retry with audit feedback + stricter checklist + final-attempt warning.

## 11. Authentication Scope

- Out of scope by default. Assume users are authenticated upstream.
- The active sprint scope may override.

## 12. Versioning

- Stable filenames. No `_final.md` or `_v2`. Use Git commits and run snapshots.

## 13. Output Safety

- Never overwrite the target app repo directly during generation.
- Flow: **generate → audit → save to generated_files → human approve → apply to app repo → smoke run → commit.**

## 14. Smoke Run Gate

The transfer notebook MUST execute, after files are applied to the target repo:

- `python manage.py check`
- `python manage.py makemigrations`
- `python manage.py migrate`
- `python manage.py test`
- HTTP smoke GETs against URLs declared in the active sprint scope's "Smoke Run" section. The transfer notebook parses these URLs from the scope file at runtime — no sprint-specific URLs are hardcoded in the notebook.

A sprint is **not done** until the smoke run passes.

## 15. Language Rule

All UI text, code comments, identifiers, and messages must be **English only**. Reject Thai output even when the visual reference PDF contains Thai labels.

## 16. Naming Conventions

- `snake_case` for Python and DB fields.
- `UPPER_SNAKE_CASE` for module-level constants.
- `PascalCase` for Django model, form, and view-class names.
- Every entity has a hidden `id` (auto PK) AND a human-friendly **display code** field (`code` for Customer/Product, `invoice_no` for Invoice, others declared in the active sprint scope). The id is never shown in the UI; the display code is what the user sees.

## 17. One Sprint at a Time

The architect plans ONLY the active sprint. Future-sprint features must be deferred and labeled "future sprint" in `risk_notes` if relevant. The "Out of Scope" section of the active sprint scope file is binding.

## 18. Recommended Workflow

1. Author or refine `sprints/sprint_NN_scope.md` for the next sprint.
2. Set `SPRINT_SCOPE_FILE` in `notebooks/agent_coding_workflow.ipynb`.
3. Run the workflow.
4. Inspect generated_files and audit logs.
5. Run `notebooks/transfer_generated_files.ipynb` — applies files AND runs smoke gate.
6. If smoke run passes: git commit in target repo, then file feedback for the next sprint scope.
7. Author the next sprint scope file and repeat.
