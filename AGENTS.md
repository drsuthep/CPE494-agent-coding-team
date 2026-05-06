# AGENTS.md

This file defines the operating rules for the CPE494 Agent Coding Lab.

## Project Summary

This project is a teaching lab for **CPE494**. Students use an AI agent team to build an ERP-style Invoice application in iterative sprints.

The lab uses two repositories under one parent folder:

```text
lab-coding-agent/
  CPE494-agent-coding-team/
  CPE494-erp-invoice-app-by-ai/
```

- `CPE494-agent-coding-team`: orchestration, prompts, specs, run logs, and generated artifacts.
- `CPE494-erp-invoice-app-by-ai`: target application code only.

## Baseline Phase (Pre-Sprint 1)

The current baseline is intentionally **pre-sprint**.

- No Sprint 1 or Sprint 2 implementation is assumed.
- Students should define sprint scope first, then run generation.
- The target app stack is **Django + SQLite + Templates + Bootstrap**.

## Source Specification Files

```text
logic_specs/erp_invoice_logic.md
ui_specs/erp_invoice_ui.md
ui_specs/ui_reference.pdf
```

Use them as:

- `logic_specs/erp_invoice_logic.md`: architecture, data, validation, transaction, and security rules.
- `ui_specs/erp_invoice_ui.md`: UI/UX rules, layout, style, and validation display behavior.
- `ui_specs/ui_reference.pdf`: visual inspiration only.

Important: the PDF may contain Thai labels. Generated application UI must be **English only**.

## Required Agent Repo Structure

```text
CPE494-agent-coding-team/
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
    ui_reference.pdf

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

## Prompt and Specification Separation

Keep these separate:

```text
prompts/      = how agents behave
logic_specs/  = what the app must do
ui_specs/     = how the app must look
```

## Main Agent Roles

### Architect Agent

Produces:

- technical manifest
- file plan
- task list
- machine-readable JSON task list

The Architect does **not** write code.

### Coder Agent

Generates source code for the specific file task.

The Coder must output only raw source code for the requested file.

### Logic Tester Agent

Audits generated files for logic, data integrity, security, and validation correctness.

### UI Auditor Agent

Audits generated UI files against visual and interaction requirements.

## Target App Technology Stack (Current)

- Django
- Python
- Django ORM
- SQLite (teaching default)
- Django Templates
- Bootstrap
- CSS

Optional later enhancements:

- richer table/grid libraries
- alternative DB providers for deployment environments

## Runtime Logging and Traceability

Every notebook run must create a unique run folder:

```text
outputs/runs/<run_id>/
```

Required runtime artifacts:

- `run_manifest.json`
- `input_snapshot/`
- `input_snapshot_manifest.json`
- `gemini_calls.jsonl`
- `architect_manifest.md`
- `tasks.json`
- `attempts/`
- `generated_files/`
- `audit_logs.jsonl`
- `workflow_result.json`
- `applied_files.json`
- `command_logs.jsonl`

## Versioning Strategy

Do not create file names like `final_final.md` or versioned copies.

Use:

- stable filenames
- Git commits
- run snapshots

## Agent Output Safety

Never overwrite the target app repo immediately.

Recommended flow:

```text
generate -> audit -> save to generated_files -> human approve -> apply to app repo
```

## Feature Bundles

Treat dependent files as a bundle.

Example:

```text
customers/views.py
templates/customers/form.html
```

Rule:

```text
Only apply a feature bundle when all required files in that bundle pass.
```

## Retry Strategy

Use:

```text
MAX_ATTEMPTS = 3
```

Retry pattern:

```text
Attempt 1: normal generation
Attempt 2: retry with audit feedback
Attempt 3: retry with audit feedback + stricter checklist + explicit final warning
```

## Delivery Mode

Work one sprint at a time.

- Keep focus on the currently approved sprint only.
- Refer to non-current work as "future sprint".

## Authentication Scope (Teaching Version)

Authentication/authorization infrastructure is out of scope by default.

- Assume users are authenticated before entering the module.
- Do not implement login/password/role-based permissions unless explicitly requested by sprint scope.

## Immediate Recommended Starting Workflow

1. Clone both repositories.
2. Prepare Django environment in target app repo.
3. Define Sprint 1 goal and detailed sprint-specific constraints.
4. Run `agent_coding_workflow.ipynb`.
5. Review generated outputs and audits.
6. Use `transfer_generated_files.ipynb` to apply approved files.
7. Run Django checks/tests.
8. Commit accepted changes in target app repo.
