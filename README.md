# CPE494-agent-coding-team

This repository contains a multi-agent coding workflow for CPE494.

Students use this repo together with the companion target app repo:

- `CPE494-agent-coding-team` (this repo): orchestration, prompts, specs, runtime logs.
- `CPE494-erp-invoice-app-by-ai`: generated application code.

## Current Baseline

This baseline is pre-sprint and ready for a new implementation cycle.

- Orchestration: Python notebooks and scripts
- Target app stack: Django + SQLite + Templates + Bootstrap
- Delivery style: one sprint at a time, with audit before apply

## Core Workflow

1. Define current sprint goal and scope.
2. Run `notebooks/agent_coding_workflow.ipynb` to generate and audit outputs.
3. Review outputs in `outputs/runs/<run_id>/`.
4. Apply approved files using `notebooks/transfer_generated_files.ipynb`.
5. Run checks in target app repo and commit accepted changes.

## Source-of-Truth Specs

- `logic_specs/erp_invoice_logic.md`
- `ui_specs/erp_invoice_ui.md`
- `ui_specs/ui_reference.pdf`

## Repository Structure

```text
notebooks/      orchestration and apply flow
prompts/        role prompts for each agent
logic_specs/    business and data requirements
ui_specs/       UI/UX requirements and visual references
outputs/        run artifacts and logs
```

## Notes for Students

- Keep prompts and specs separate.
- Keep sprint scope small and testable.
- Do not apply generated files directly without review.
- Use Git commits and run snapshots for traceability.

## License

For educational use in CPE494.
