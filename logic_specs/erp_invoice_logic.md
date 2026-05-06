# ERP Invoice App — Logic Specification (Baseline)

## 1. Purpose

Broad logic principles for the ERP Invoice app. **Detailed entity contracts come per sprint** in `sprints/sprint_NN_scope.md`. This file defines rules that apply across every sprint, every entity (Customer, Product, Invoice, and any future entity).

## 2. Tech Stack (Authoritative)

- Django 5.x
- Python 3.11+
- Django ORM (no raw SQL unless parameterized AND justified in `risk_notes`)
- SQLite (teaching default, file `db.sqlite3` at project root)
- Django Templates
- Bootstrap 5 + Font Awesome 6 (loaded via CDN)

## 3. Language Rule

All code, identifiers, comments, UI strings, and error messages must be **English only**. Reject Thai output, even when the visual reference PDF contains Thai labels.

## 4. Naming Standards

- `snake_case` for Python variables, function names, model fields, JSON keys, form field names, URL path names.
- `UPPER_SNAKE_CASE` for module-level constants.
- `PascalCase` for Django model class names, form class names, view class names.

## 5. Primary Key + Display Code Convention

Every persistent entity has two identifiers:

- **`id`** — Django default auto-increment primary key. **Hidden from the UI** in every screen (list, form, LOV picker). It is the stable referential value used in foreign keys and APIs.
- **Display code** — a short, human-friendly identifier shown to users in lists, forms, and LOV pickers. Field names by entity:
  - `code` for Customer
  - `code` for Product
  - `invoice_no` for Invoice
  - Future entities declare their display code field in their sprint scope file.

The display code must be unique within the entity (`unique=True`).

When a foreign key field is rendered in a form, the form stores the related record's `id` in a hidden input but visibly displays the related record's display code in a read-only input. The LOV button next to the input opens the related list view in select-mode for the user to change the selection. Detailed UI flow lives in `ui_specs/form_workflow.md`.

## 6. Entity Standards

Every persistent entity has:

- `id` — auto PK (Django default).
- The entity's display code field (per Section 5), `unique=True`.
- `updated_at` — `models.DateTimeField(auto_now=True)`. Lightweight last-modified field.
- `__str__(self)` returning the display code (e.g., `return self.code`) or `f"{self.code} — {self.name}"` if the entity has a name distinct from the code.
- `class Meta: ordering = [...]` where order matters in list views.

When the active sprint scope declares a need for optimistic concurrency (typically when multiple users may write the same record concurrently), implement an explicit version field (`version = models.IntegerField(default=0)`). Otherwise, `updated_at` is sufficient.

## 7. Foreign Key Standards

Use the entity name as the field name (`customer`, `product`, `invoice`). Django automatically appends `_id` at the column level. In code, refer to `invoice.customer`, not `invoice.customer_id`, unless explicitly handling raw IDs.

`ForeignKey` declarations must include `on_delete=` explicitly (no default). Typical choices: `models.PROTECT` (block deletion when referenced), `models.CASCADE` (cascade deletion).

## 8. Atomic Transaction Principle

Any save that touches more than one table must be wrapped in `transaction.atomic`. Single-entity saves do not require explicit transaction wrappers.

## 9. Server-Side Validation Mandate

Server-side validation is required for all persisted writes, even if client-side validation exists. All form input goes through Django `Form` or `ModelForm` — never read `request.POST` directly into the ORM.

Validation categories:

- Required field checks (driven by model field constraints)
- String length limits (model `max_length`)
- Numeric range checks (form `clean_<field>` methods)
- Unique-code checks (model `unique=True`)
- Foreign key existence (Django ORM enforces via `ForeignKey`)
- Optimistic-concurrency checks (when the active sprint scope requires)
- Server-side recalculation of monetary totals (when the active sprint scope introduces line items or totals)

## 10. Security Rules

- Authentication is upstream. Do not implement login/role checks unless the active sprint scope says otherwise.
- Use Django ORM. No raw SQL unless parameterized AND justified in `risk_notes`.
- All form input goes through Django `Form` or `ModelForm`.
- CSRF protection is on (Django default); every `<form method="post">` includes `{% csrf_token %}`.
- Never use `mark_safe` on user-supplied input.

## 11. Code Quality Rules

Every generated file must:

- Run without `python manage.py check` errors.
- Have all imports at the top of the file.
- Have no unused imports.
- Have docstrings on public functions, classes, and views.
- Avoid hardcoded business data in templates — pass through view context.
- Return appropriate HTTP responses (`HttpResponse`, `redirect`, etc.).
- Respect the Reuse Architecture (Section 13) — no per-entity duplication of logic that a shared module already covers.

## 12. Test Standards (TDD Layer)

- Tests live in each app's `tests.py` (Django convention). No pytest-django.
- One `TestCase` class per scenario group; methods named `test_<entity>_<scenario>`.
- Every acceptance criterion in a sprint scope file maps to **at least one** test method in `tests.py`.
- Tests must be runnable with `python manage.py test` and pass before the sprint bundle is considered done.
- At least one unhappy-path test (validation failure) per form.

## 13. Reuse Architecture (Logic Side)

All entities share the same code modules:

- **Shared list view module** — a single Django view function/class parameterized by model. It supports two modes:
  - **Browse mode** — entered from the navbar; clicking a row opens the form view for that record.
  - **Select mode** — entered from a parent form's LOV button; clicking a row returns the selected id+display code to the parent form. Detailed in `ui_specs/form_workflow.md`.
  Both modes share the same sorting, searching, filtering, and pagination capabilities.
- **Shared form view module** — a single Django view function/class parameterized by model and form class. Handles dirty-bit rendering hints, Save/Close/Delete dispatch, save-flash messaging, and FK rendering as id-hidden + code-visible + LOV.
- **Shared template partials** — `templates/_partials/list_table.html` and `templates/_partials/form_layout.html` for the visual side. Logic-side templates (e.g., the LOV select-mode renderer) may also live in `_partials/`.

The architect plans whether the active sprint extracts these modules from per-entity code or reuses already-existing shared modules. Once a shared module exists, no per-entity duplication is allowed — the logic tester FAILs duplicated code that a shared module already covers.

## 14. Sprint-Layer Detail Comes Per Sprint

This file does NOT enumerate fields, validations, or behaviors for individual entities. Those live in the active sprint scope file. The agents must read both layers.
