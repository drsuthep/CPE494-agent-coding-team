# Sprint 00 — Hello World (Smoke Test)

> Sprint 0 is a smoke test of the four-agent pipeline. It bootstraps an empty target repo into a runnable Django project with one route that renders "Hello, world!" and one passing test. **No business entities** are introduced in Sprint 0 — that's deliberate. The job is to prove the orchestration works before any feature scope is on the line.

## 1. Sprint Goal

Take the empty `CPE494-erp-invoice-app-by-ai/` repo and produce:

- A complete, bootstrapped Django 5 project (`erp_invoice/` package + `manage.py`).
- A minimal `core` app holding the landing view and tests.
- A `base.html` template carrying every baseline UI scaffold (Zen Green CSS tokens, Bootstrap 5 + Font Awesome 6 via CDN, dirty-bit listener, messages flash) — these are unused in Sprint 0 but must be in place so Sprint 1+ can use them without revising `base.html`.
- A `landing.html` template that renders the literal text `Hello, world!` inside a Bootstrap card.
- A `tests.py` with assertions that prove the route works and the baseline scaffolds are present.

If this sprint produces a project where `python manage.py runserver` starts, `python manage.py test` passes, and `http://127.0.0.1:8000/` shows `Hello, world!` — the agent team is verifiably working end-to-end. Subsequent sprints can then introduce real features on this foundation.

## 2. Entity Contract

**None.** Sprint 0 introduces no business entities. No models, no forms, no foreign keys. The `core` app is purely an orchestration shell holding the landing view + tests.

### 2.1 Form layout density

N/A — Sprint 0 has no forms.

### 2.2 List view page size

N/A — Sprint 0 has no list views.

## 3. File Bundle

### bundle_project_skeleton

- `manage.py` — standard Django manage.py pointing at `erp_invoice.settings`.
- `erp_invoice/__init__.py` — empty.
- `erp_invoice/settings.py` — DEBUG=True, ALLOWED_HOSTS=['*'], SQLite, INSTALLED_APPS includes the standard Django built-ins (`django.contrib.admin`, `auth`, `contenttypes`, `sessions`, `messages`, `staticfiles`) plus `core`, TEMPLATES dirs include `BASE_DIR / "templates"`, LANGUAGE_CODE='en-us', TIME_ZONE='UTC', SECRET_KEY clearly marked TEACHING, DEFAULT_AUTO_FIELD='django.db.models.BigAutoField'.
- `erp_invoice/urls.py` — `path('admin/', admin.site.urls)` AND `path('', include('core.urls'))`.
- `erp_invoice/wsgi.py` — standard WSGI module.
- `erp_invoice/asgi.py` — standard ASGI module.
- `requirements.txt` — `Django>=5.0,<6.0`.
- `.gitignore` — Python/Django ignores: `__pycache__/`, `*.pyc`, `db.sqlite3`, `.env`, `.venv/`, `venv/`, `staticfiles/`, `media/`, `.idea/`, `.vscode/`.

### bundle_core_app

- `core/__init__.py` — empty.
- `core/apps.py` — `CoreConfig(AppConfig)` with `default_auto_field = 'django.db.models.BigAutoField'` and `name = 'core'`.
- `core/views.py` — `landing_view(request)` function with docstring; returns `render(request, 'landing.html')`. Imports `from django.shortcuts import render`.
- `core/urls.py` — `app_name = 'core'`. One route: `path('', views.landing_view, name='landing')`.
- `core/tests.py` — `TestCase` subclass implementing the four test cases in Section 5.

### bundle_templates

- `templates/base.html` — root layout. Required content:
  - `<!DOCTYPE html>` and `<html lang="en">`.
  - Bootstrap 5 CDN link (`https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css`).
  - Font Awesome 6 CDN link (`https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css`).
  - Inline `<style>` with the Zen Green CSS tokens (`--zen-primary: #004d40`, `--zen-primary-soft: #2e7d64`, `--zen-accent: #a5d6a7`, `--zen-surface: #e8f5e9`, `--zen-border: #81c784`, `--zen-error: #c62828`).
  - Inline `<style>` with the `.list-viewport` and `.lineitem-viewport` CSS rules from `form_workflow.md` Sections 13 and 14 (declared once now so Sprint 1+ doesn't have to revise).
  - A Bootstrap `<nav class="navbar">` with the brand `ERP Invoice` linking to `/`. Background uses `--zen-primary`. No entity nav links yet (those land in feature sprints).
  - `{% block title %}ERP Invoice{% endblock %}` in `<title>`.
  - `{% block content %}{% endblock %}` inside a Bootstrap container.
  - Messages flash scaffold (per `form_workflow.md` Section 6): if `messages`, render a flash stack, with the 2-second auto-fade `<script>`.
  - Dirty-bit listener `<script>` (per `form_workflow.md` Section 5): defines `window.formDirty()`, `window.clearDirty()`, and the `beforeunload` listener.
  - Bootstrap 5 JS bundle CDN at the bottom of `<body>`.
- `templates/landing.html` — extends `base.html`. Content:
  - `{% block title %}Hello, world!{% endblock %}`.
  - `{% block content %}` containing a Bootstrap card with header `Welcome` (`--zen-primary-soft` background, white text) and body containing the literal text `Hello, world!`.

## 4. Acceptance Criteria

1. `python manage.py check` exits 0.
2. `python manage.py makemigrations` exits 0 (no custom-app migrations expected; built-in apps may produce migrations on first run — that's fine).
3. `python manage.py migrate` exits 0.
4. `python manage.py test` exits 0 with all four Sprint 0 tests passing.
5. `python manage.py runserver` starts the server without errors.
6. `GET /` returns 200 and the response body contains the literal text `Hello, world!`.
7. `GET /` response uses `templates/base.html` (assertable via Django's `assertTemplateUsed`).
8. `templates/base.html` contains the literal token `--zen-primary: #004d40` (proves Zen Green tokens are declared).
9. `templates/base.html` contains a `<link>` referencing `bootstrap` (CDN) and a `<link>` referencing `font-awesome` or `fontawesome` (CDN).
10. `templates/base.html` contains the literal token `window.formDirty` and `window.clearDirty` (proves dirty-bit scaffold is present).
11. `templates/base.html` contains the literal token `flash-stack` or `flash-msg` (proves messages-flash scaffold is present).
12. No Thai characters (Unicode U+0E00–U+0E7F) appear anywhere in any generated file.

## 5. Test Cases

Implement in `core/tests.py`. Every acceptance criterion above maps to at least one test below.

1. `test_landing_returns_200` — `GET /` returns HTTP 200.
2. `test_landing_contains_hello_world` — `GET /` response.content (decoded utf-8) contains the substring `Hello, world!`.
3. `test_landing_uses_base_template` — `GET /` uses both `templates/base.html` and `templates/landing.html` (verify with `self.assertTemplateUsed`).
4. `test_landing_no_thai_characters` — `GET /` response.content (decoded utf-8) contains no character in the Unicode range U+0E00–U+0E7F (assert via Python regex).

## 6. Click-Path Test (Manual)

1. `python manage.py runserver`
2. Open `http://127.0.0.1:8000/` in a browser.
3. Confirm the page renders with the navbar at the top (dark green Zen Green strip, "ERP Invoice" brand on the left).
4. Confirm the body shows a Bootstrap card with "Welcome" in the header and `Hello, world!` in the body.
5. Confirm there are no broken images, no console errors, and no visible Thai characters.

## 7. Smoke Run

The transfer notebook's smoke gate executes:

​```text
python manage.py check
python manage.py makemigrations
python manage.py migrate
python manage.py test
​```

HTTP GETs that must return 200 (parsed by the transfer notebook from this section at runtime):

- `/`

(That's the only URL in Sprint 0. The smoke gate launches `runserver` on port 8765 to avoid conflict with the developer's dev server on 8000.)

## 8. Definition of Done

- [ ] All 12 acceptance criteria observable.
- [ ] All 4 test cases pass via `python manage.py test`.
- [ ] Smoke run commands all exit 0.
- [ ] Smoke HTTP GET against `/` returns 200.
- [ ] Logic Tester PASS for every file in every bundle.
- [ ] UI Auditor PASS for every template.
- [ ] Manual click-path test succeeds in the instructor's browser.

## 9. Out of Scope (Explicit)

Sprint 0 deliberately includes only what's needed to prove the pipeline. Everything else waits for a feature sprint.

- Business entities — Customer, Product, Invoice, and any other future entities.
- Models, custom-app migrations, ModelForms.
- Form views (the dirty-bit and save-flash scaffolds in `base.html` are present but unused).
- List views, shared list view code, pagination, sort/search/filter.
- Foreign keys, LOV pattern, FK display rule.
- Line item grids.
- Shared template partials (`_partials/list_table.html`, `_partials/form_layout.html`) — first feature sprint that needs them extracts them.
- Form layout density rules (no forms yet).
- Edit and Delete buttons.
- Authentication.
- Admin customization (the `admin.site.urls` route is registered but no `admin.py` for `core`).
- Static asset pipeline beyond `STATIC_URL = 'static/'` in settings (no actual static files).

## 10. Notes for Sprint 1+

- Sprint 0 produces a foundation that Sprint 1+ extends. Do not revise `base.html` unnecessarily — it already carries the Zen Green tokens, CDN links, dirty-bit listener, and messages flash scaffold.
- The `core` app exists purely as an orchestration shell for the landing route. Sprint 1+ may keep it for the landing page or replace it with a feature-specific app — either is fine.
- The empty navbar in Sprint 0 is intentional; Sprint 1+ adds entity links (e.g., "Customers") to the navbar as those entities land.
- If Sprint 0 fails, debug the agent infrastructure (prompts, notebook wiring, audit gates) before retrying. A failure here points to an orchestration bug, not a feature gap.
- If Sprint 0 passes, you have evidence the four-agent pipeline can produce runnable Django code from spec — proceed to design Sprint 1 with confidence.
