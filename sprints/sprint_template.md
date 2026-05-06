# Sprint NN  -  <Sprint Title>

> Authoritative contract for this sprint. Every agent must read this file (Layer 2) plus the baseline files (Layer 1).

## 1. Sprint Goal

One paragraph in plain English describing what this sprint produces. Be concrete about user-visible behavior.

## 2. Entity Contract

Field-by-field specification of any entities the sprint creates or modifies. Each entity has a hidden auto `id` PK + a unique display code field + `updated_at` (per `logic_specs/erp_invoice_logic.md` Section 5). Declare the display code field name explicitly.

| Field | Type | Constraints | Notes |
|---|---|---|---|
| code | CharField(max_length=32) | unique, required | display code shown in UI |
| name | CharField(max_length=200) | required | |
| ... | | | |

Include `__str__(self)` format and `Meta.ordering`.

For FK fields, specify the LOV columns (which fields of the related entity the LOV picker shows) per `ui_specs/form_workflow.md` Section 9.

### 2.1 Form layout density

Pick **1**, **2**, **3**, or **4** columns for this entity's form view. Default is 2 if not specified. Per `ui_specs/form_workflow.md` Section 12.

- `<entity>` form density: **N** (e.g., 2)

### 2.2 List view page size

Server-side pagination size for this entity's list view. Default is 50. Override only when the entity warrants (e.g., heavy rows like Invoices may use 25). Per `form_workflow.md` Section 13.

- `<entity>` list page size: **N** (e.g., 50)

## 3. File Bundle

Exhaustive list of files this sprint creates or edits, grouped by `feature_group`.

### bundle_<name>

- `path/to/file.py`  -  what it does

(Repeat per bundle. The architect MUST emit one task per file with the matching `feature_group`. Bundles are applied atomically.)

If this sprint extracts shared modules (shared list view code, shared form view code, shared partials), declare those as their own bundles (e.g., `bundle_shared_views`, `bundle_shared_templates`). If shared modules already exist, the per-entity bundles must reuse them, not duplicate them.

## 4. Acceptance Criteria

Numbered, observable. Format: "When X happens, Y is observable."

1. ...

## 5. Test Cases

Concrete assertions the architect declares in `test_cases` and the coder implements in `tests.py`.

1. `test_<entity>_<scenario>`  -  GET /<path>/ returns 200 AND response.content contains "<expected>".
2. `test_<entity>_save_flash`  -  POST valid -> response (after redirect-follow) contains "Saving... Save successful".
3. ...

Include at least one unhappy-path test per form.

## 6. Click-Path Test (Manual)

1. Open `http://127.0.0.1:8000/`.
2. Click "<button>".
3. ...

## 7. Smoke Run

Commands the smoke gate (in `transfer_generated_files.ipynb`) executes:

```text
python manage.py check
python manage.py makemigrations
python manage.py migrate
python manage.py test
```

HTTP GETs that must return 200  -  the transfer notebook parses these URLs from this section at runtime. List one URL per bullet, either as a full URL or as a path inside backticks:

- `/`
- `/<entity>/`
- `/<entity>/new/`

(The smoke gate launches `runserver` on port 8765 to avoid colliding with the developer's own dev server on 8000. Full URLs use that port; path-only entries are auto-prefixed.)

## 8. Definition of Done

- [ ] All acceptance criteria observable.
- [ ] All test cases pass via `python manage.py test`.
- [ ] Smoke run commands all exit 0.
- [ ] Smoke HTTP GETs all return 200.
- [ ] Logic Tester PASS for every file in every bundle.
- [ ] UI Auditor PASS for every template.
- [ ] Manual click-path test succeeds in the instructor's browser.

## 9. Out of Scope (Explicit)

Things explicitly NOT in this sprint, to prevent scope creep:

- ...

## 10. Notes for Future Sprints

Anything the architect should remember when planning the next sprint.

