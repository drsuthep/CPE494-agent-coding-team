# Sprint 01.1 - Fix Sprint 1 Test Failures

> Sprint 01.1 is a narrow defect-fix sprint. It does not add features. It fixes the errors found after Sprint 1 artifacts were transferred into the Django app repository.

## 1. Sprint Goal

Make the Sprint 1 Django app open normally in the browser and pass the automated test suite.

Students do not need to know the final code fix before starting this sprint. The job is to give the agentic team a clear failure report:

- What command was run.
- What page was opened.
- What error appeared.
- What behavior was expected.
- How to prove the fix worked.

## 2. What Was Tried

The instructor ran:

```text
python manage.py test
```

Django reported:

```text
Found 24 test(s).
FAILED (failures=1, errors=7)
```

The instructor also opened:

```text
http://127.0.0.1:8000/products/
```

The page crashed instead of rendering the Products placeholder page.

## 3. Defect 1 - Pages Crash With NoReverseMatch

### 3.1 Observed Behavior

Opening `/products/` shows a Django error page:

```text
NoReverseMatch at /products/
Reverse for 'landing' not found. 'landing' is not a valid view function or pattern name.
```

The browser error page points to:

```text
templates/placeholders/products_list.html, line 14
```

The same root problem also appears in the test output for several tests.

### 3.2 Expected Behavior

These pages should render normally:

```text
/
/customers/
/customers/new/
/products/
/products/new/
/invoices/
/invoices/new/
```

The Close / X buttons on list placeholder pages should return the user to the landing page:

```text
/
```

### 3.3 Guidance for the Agent

Look for template or test code that uses:

```django
{% url 'landing' %}
```

or Python code that uses:

```python
reverse('landing')
```

Sprint 0 defines the landing route under the `core` namespace. The correct route name is:

```text
core:landing
```

Acceptable fixes include:

```django
{% url 'core:landing' %}
```

or a plain root link:

```html
href="/"
```

For Python tests, acceptable fixes include:

```python
reverse('core:landing')
```

or:

```python
self.client.get("/")
```

The implementation should choose one consistent pattern and avoid introducing a second landing route only to hide the mismatch.

## 4. Defect 2 - Required Asterisk Test Is Too Strict

### 4.1 Observed Behavior

The test named `test_customer_form_required_asterisks` fails.

The rendered customer form already shows the required red asterisk. Example rendered HTML:

```html
<label for="id_code" class="form-label">Code <span class="text-danger">*</span></label>
```

But the test expects a more exact label shape, such as:

```html
<label for="id_code">Code <span class="text-danger">*</span></label>
```

That means the UI meaning is correct, but the test is too brittle about Bootstrap label markup.

### 4.2 Expected Behavior

The test should pass as long as:

- the Code field label contains a red required asterisk;
- the Name field label contains a red required asterisk;
- Bootstrap label styling such as `class="form-label"` is allowed.

### 4.3 Guidance for the Agent

Do not remove useful Bootstrap styling only to satisfy a brittle test.

Prefer updating the test so it checks the intended behavior:

- Code has a label;
- Name has a label;
- both labels include `<span class="text-danger">*</span>`.

The test should verify the contract, not require an exact byte-for-byte HTML shape when the UI is already correct.

## 5. File Bundle

The architect should produce a small fix plan. Expected edited files are likely limited to:

- affected templates that link to the landing page;
- `customers/tests.py`, if test assertions use the wrong route name or brittle label matching.

Do not edit unrelated files.

Do not change:

- Customer model fields;
- Customer form fields;
- URL paths;
- visual design beyond fixing broken links;
- Sprint 1 feature scope.

## 6. Acceptance Criteria

1. `python manage.py check` exits 0.
2. `python manage.py test` exits 0.
3. These HTTP GETs return 200:
   - `/`
   - `/customers/`
   - `/customers/new/`
   - `/products/`
   - `/products/new/`
   - `/invoices/`
   - `/invoices/new/`
4. `/products/` no longer crashes with `NoReverseMatch`.
5. `/invoices/` no longer crashes with `NoReverseMatch`.
6. `/customers/` no longer crashes with `NoReverseMatch`.
7. Close / X buttons on list placeholder pages return to `/`.
8. The Customer form still shows red required asterisks for Code and Name.
9. The test for required asterisks allows Bootstrap label classes such as `class="form-label"`.
10. No new features are added.
11. No model fields are changed.
12. No Thai characters are introduced.

## 7. Test Cases

Update or keep existing tests so the final suite proves:

1. `test_navbar_has_master_dropdown` can request the landing page successfully.
2. `test_navbar_has_transactions_dropdown` can request the landing page successfully.
3. `test_customer_list_placeholder_renders` returns 200.
4. `test_placeholder_pages_render` returns 200 for `/products/`, `/products/new/`, `/invoices/`, and `/invoices/new/`.
5. `test_no_thai_rendered_pages` can check all Sprint 1 pages without crashing.
6. `test_customer_form_required_asterisks` passes when Code and Name labels include visible red asterisks, even if the labels also have Bootstrap classes.

## 8. Smoke Run

Commands:

```text
python manage.py check
python manage.py test
```

HTTP GETs (must return 200):

```text
/
/customers/
/customers/new/
/products/
/products/new/
/invoices/
/invoices/new/
```

## 9. Definition of Done

Sprint 01.1 is done when:

```text
python manage.py test
```

finishes with:

```text
OK
```

and all seven smoke URLs return HTTP 200.

## 10. Out of Scope

- New Product functionality.
- New Invoice functionality.
- Real Customer list table.
- Search, sort, filter, or pagination.
- Shared list or form modules.
- New models or migrations.
- Authentication.
- UI redesign.
