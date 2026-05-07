# Sprint 01 — Customer + App Skeleton

> Sprint 1 builds the full app menu skeleton (Master / Transactions dropdowns) with placeholder pages for Product and Invoice, plus one fully-functional Customer module. After this sprint, every menu item is reachable; only Customer has real CRUD.

## 1. Sprint Goal

Extend the bootstrapped Sprint-0 project with:

- A navbar carrying two dropdowns — **Master** (Product, Customer) and **Transactions** (Invoice).
- A real `customers` app: model + ModelForm + four views (list-placeholder, create, edit, delete) + tests.
- Real templates for the customer list-placeholder and customer form.
- Placeholder templates for `/products/`, `/products/new/`, `/invoices/`, `/invoices/new/` rendered via Django's `TemplateView` directly from project URLs (no separate apps yet).

The Customer form is fully functional: create a record, save (PRG + 2-second flash, redirect into edit mode), edit, delete, close. The customer list view is a placeholder card with X (close → `/`) and + (create new) buttons; the real list ships in a future sprint.

## 2. Entity Contract

### Customer

| Field | Type | Required | Notes |
|---|---|---|---|
| `id` | AutoField | auto | Django default PK; hidden from UI |
| `code` | CharField(max_length=32) | yes, `unique=True` | display code (alternate key) |
| `name` | CharField(max_length=200) | yes | display name |
| `address_line_1` | CharField(max_length=200) | no, `blank=True` | |
| `address_line_2` | CharField(max_length=200) | no, `blank=True` | |
| `city` | CharField(max_length=100) | no, `blank=True` | |
| `province` | CharField(max_length=100) | no, `blank=True` | state / province / region |
| `country` | CharField(max_length=100) | no, `blank=True` | |
| `postal_code` | CharField(max_length=20) | no, `blank=True` | international varies |
| `phone` | CharField(max_length=40) | no, `blank=True` | |
| `email` | EmailField | no, `blank=True` | |
| `notes` | TextField | no, `blank=True` | rendered as textarea |
| `updated_at` | DateTimeField(`auto_now=True`) | auto | baseline rule |

`__str__(self)` returns `f"{self.code} — {self.name}"`.
`Meta.ordering = ["name"]`.

### 2.1 Form layout density

**2** (the baseline default). 11 user-editable fields → ~6 rows on md+. Exception: `notes` textarea spans full width (`col-12`) regardless of density.

### 2.2 List view page size

N/A — Sprint 1's customer list view is a placeholder. The page-size rule applies once the real list ships.

## 3. File Bundle

### bundle_customers_app (8 files)

- `customers/__init__.py` — empty.
- `customers/apps.py` — `CustomersConfig(AppConfig)` with `default_auto_field='django.db.models.BigAutoField'` and `name='customers'`.
- `customers/models.py` — `Customer` per Section 2 above.
- `customers/forms.py` — `CustomerForm(forms.ModelForm)`. `Meta.fields` is the explicit list of all 11 user-editable fields. Bootstrap classes applied via `widgets={...}`; `notes` uses `forms.Textarea(attrs={"class": "form-control", "rows": 3})`.
- `customers/views.py` — four function-based views, all docstring'd:
  - `list_placeholder(request)` renders `customers/list_placeholder.html`.
  - `customer_create(request)` — GET renders empty `CustomerForm`. POST: validate; on success save, then `messages.success(request, "Saving... Save successful")` and `redirect('customers:edit', id=obj.id)`. On failure re-render with bound form.
  - `customer_edit(request, id)` — `get_object_or_404(Customer, pk=id)`. GET renders bound form. POST same as create but `redirect('customers:edit', id=obj.id)`.
  - `customer_delete(request, id)` — POST-only (`@require_POST`); deletes; redirects to `customers:list`.
- `customers/urls.py` — `app_name = 'customers'`. Routes:
  - `path('', views.list_placeholder, name='list')`
  - `path('new/', views.customer_create, name='create')`
  - `path('<int:id>/edit/', views.customer_edit, name='edit')`
  - `path('<int:id>/delete/', views.customer_delete, name='delete')`
- `customers/admin.py` — register Customer with `list_display = ('code', 'name', 'email', 'city', 'country')`.
- `customers/tests.py` — implements every test in Section 5.

### bundle_customer_templates (2 files)

- `templates/customers/list_placeholder.html` — extends `base.html`.
  - `{% block title %}Customers{% endblock %}`
  - Card with header "Customers". Header right side has + (create new, links to `customers:create`, `fa-plus`, `title="Create new customer"`) and X (close, links to `/`, `fa-xmark`, `title="Close"`).
  - Body: a muted note "Customer list view will be implemented in a future sprint."
  - No table, no records.
- `templates/customers/form.html` — extends `base.html`.
  - `{% block title %}{% if obj %}Edit Customer{% else %}New Customer{% endif %}{% endblock %}`
  - Card with header "New Customer" or "Edit Customer".
  - `<form method="post">` with `{% csrf_token %}`.
  - Density-2 grid (`row g-3` + each field wrapper `col-12 col-md-6`). Required fields (`code`, `name`) have red asterisk via `<span class="text-danger">*</span>`. The `notes` textarea wrapper uses `col-12`.
  - Field-level errors rendered as `<div class="invalid-feedback d-block">` below each field.
  - Action row at bottom of card:
    - Save (primary, `type="submit"`, `fa-floppy-disk`, `title="Save"`)
    - Delete (danger, `fa-trash`, `title="Delete"`) — rendered only when editing existing (`{% if obj %}`)
    - Close (secondary, `fa-xmark`, `title="Close"`, links to `customers:list`, with JS dirty-bit confirm)
  - Delete button uses a confirm modal pattern (`data-action="delete"`, `data-display="{{ obj.code }}"`, `data-url="{% url 'customers:delete' obj.id %}"`).

### bundle_other_placeholders (4 files)

- `templates/placeholders/products_list.html` — extends `base.html`. Card titled "Products". Body: muted note "Product module not yet implemented." Header has + (links to `/products/new/`) and X (links to `/`).
- `templates/placeholders/products_form.html` — extends `base.html`. Card titled "Product Form — Under Construction". Body: muted note "This module is not yet implemented." Action row: only Close button (links to `/products/`, `fa-xmark`, `title="Close"`).
- `templates/placeholders/invoices_list.html` — same shape as products_list but titled "Invoices" and routing to `/invoices/`.
- `templates/placeholders/invoices_form.html` — same shape as products_form but titled "Invoice Form — Under Construction" and Close routes to `/invoices/`.

### bundle_skeleton_edits (3 edits)

- `erp_invoice/settings.py` — add `'customers'` to `INSTALLED_APPS`.
- `erp_invoice/urls.py` — add (preserve existing admin and core routes):
  - `from django.views.generic import TemplateView`
  - `path('customers/', include('customers.urls'))`
  - `path('products/', TemplateView.as_view(template_name='placeholders/products_list.html'), name='products_list')`
  - `path('products/new/', TemplateView.as_view(template_name='placeholders/products_form.html'), name='products_create')`
  - `path('invoices/', TemplateView.as_view(template_name='placeholders/invoices_list.html'), name='invoices_list')`
  - `path('invoices/new/', TemplateView.as_view(template_name='placeholders/invoices_form.html'), name='invoices_create')`
- `templates/base.html` — replace the brand-only navbar with a Bootstrap navbar containing two dropdowns:
  - **Master** dropdown items: Product (`/products/`), Customer (`/customers/`)
  - **Transactions** dropdown item: Invoice (`/invoices/`)
  - Use Bootstrap 5 dropdown markup (`<li class="nav-item dropdown">...<ul class="dropdown-menu">`).

## 4. Acceptance Criteria

1. `python manage.py check` exits 0.
2. `python manage.py makemigrations customers && python manage.py migrate` exits 0; `customers_customer` table exists in SQLite.
3. `python manage.py test` exits 0 with all Sprint-1 tests passing.
4. Navbar shows Master and Transactions dropdowns; Master expands to Product and Customer; Transactions expands to Invoice.
5. `GET /customers/` renders the list-placeholder card with X and + buttons (no records table, no errors).
6. `GET /customers/new/` renders the customer form with all 11 user-editable fields. `code` and `name` show red asterisks. `notes` field is a textarea spanning full width.
7. `POST /customers/new/` with valid `code` and `name` returns 302 to `/customers/<id>/edit/`. The redirected GET response contains the substring "Save successful" (Django messages flash text).
8. `POST /customers/new/` with empty `code` returns 200 (re-render) with `Customer.objects.count() == 0` and the response contains the field error class `invalid-feedback`.
9. `POST /customers/new/` with a duplicate `code` (same code as an existing record) returns 200 with the original record still present and only one record total in the database.
10. `GET /customers/<id>/edit/` (for an existing customer) renders the form pre-populated with that customer's data.
11. `POST /customers/<id>/edit/` with modified data returns 302 to the same edit URL; the database row reflects the change; the redirected GET contains "Save successful".
12. `POST /customers/<id>/delete/` returns 302 to `/customers/`; the row is gone from the database.
13. `GET /customers/<id>/delete/` (the same URL via GET) returns 405 (method not allowed) — confirms POST-only.
14. The form template's Close link points to `/customers/`. The Delete button is absent on `/customers/new/` (no `obj`) and present on `/customers/<id>/edit/`.
15. `GET /products/`, `GET /products/new/`, `GET /invoices/`, `GET /invoices/new/` all return 200 and render the placeholder cards.
16. `GET /products/new/` placeholder card has only one button (Close) linking to `/products/`. Same for `/invoices/new/`.
17. No Thai characters (Unicode U+0E00–U+0E7F) appear anywhere in any rendered page or generated file.

## 5. Test Cases

Implement in `customers/tests.py`. Every acceptance criterion above maps to at least one test below.

1. `test_customer_model_fields` — `Customer._meta.get_fields()` includes all expected fields with correct types and constraints (`code` is unique, `name` is required, `notes` is TextField).
2. `test_navbar_has_master_dropdown` — `GET /` (or any page extending base) response contains the strings "Master", "Customer", "Product".
3. `test_navbar_has_transactions_dropdown` — same response contains "Transactions" and "Invoice".
4. `test_customer_list_placeholder_renders` — `GET /customers/` returns 200 and contains the literal text "Customers" (card title) and `href="/customers/new/"`.
5. `test_customer_form_renders_all_fields` — `GET /customers/new/` returns 200 and contains `name="code"`, `name="name"`, `name="address_line_1"`, `name="address_line_2"`, `name="city"`, `name="province"`, `name="country"`, `name="postal_code"`, `name="phone"`, `name="email"`, `name="notes"`.
6. `test_customer_form_required_asterisks` — `GET /customers/new/` response contains the asterisk pattern (`text-danger`) near `code` and `name` field labels (assert occurrences).
7. `test_customer_create_valid_redirects_to_edit` — `POST /customers/new/` with `{"code": "ACME-001", "name": "Acme Inc"}` returns 302 to a URL matching `/customers/\d+/edit/`.
8. `test_customer_create_save_flash_in_messages` — after the POST in test 7, follow the redirect and assert the response contains "Save successful".
9. `test_customer_create_invalid_re_renders` — `POST /customers/new/` with empty code returns 200, `Customer.objects.count() == 0`, and response contains `invalid-feedback`.
10. `test_customer_code_uniqueness` — create one customer with `code="DUPE"`, then POST another with `code="DUPE"`; second response is 200 and `Customer.objects.filter(code="DUPE").count() == 1`.
11. `test_customer_edit_loads_existing` — create a customer programmatically; `GET /customers/<id>/edit/` response contains that customer's `code` and `name` in the form values.
12. `test_customer_edit_save_persists` — `POST /customers/<id>/edit/` with modified `name`; redirected response contains the new name.
13. `test_customer_delete_removes_record` — `POST /customers/<id>/delete/` returns 302; `Customer.objects.filter(pk=id).exists()` is False.
14. `test_customer_delete_get_returns_405` — `GET /customers/<id>/delete/` returns 405.
15. `test_customer_form_has_close_button` — `GET /customers/new/` contains an anchor with `href="/customers/"` and `title="Close"`.
16. `test_customer_edit_form_has_delete_button` — `GET /customers/<id>/edit/` contains a button with `data-action="delete"`. The same button is absent in `GET /customers/new/`.
17. `test_placeholder_pages_render` — each of `/products/`, `/products/new/`, `/invoices/`, `/invoices/new/` returns 200.
18. `test_placeholder_form_has_only_close` — `GET /products/new/` and `GET /invoices/new/` each contain exactly one anchor with `title="Close"` and no Save / Delete buttons.
19. `test_no_thai_anywhere` — `GET /`, `/customers/`, `/customers/new/`, `/products/`, `/invoices/` responses contain no Unicode chars in U+0E00–U+0E7F.

## 6. Click-Path Test (Manual)

1. `python manage.py runserver`
2. Open `http://127.0.0.1:8000/` → see Sprint-0 landing card with Master and Transactions dropdowns now in the navbar.
3. Hover Master → see Product and Customer items. Hover Transactions → see Invoice.
4. Click Master → Customer → land on `/customers/` placeholder card.
5. Click + → land on `/customers/new/` empty form.
6. Type `ACME-001` in code, `Acme Inc` in name, leave others blank. Click Save.
7. URL becomes `/customers/<id>/edit/`. Form shows the saved values. Top of page (or top-right toast) shows "Saving... Save successful" for ~2 seconds, then fades.
8. Modify `email` to `info@acme.com`. Click Save → flash again, form retains values.
9. Click Close → return to `/customers/` placeholder. (Try modifying without saving and clicking Close → confirm dialog appears.)
10. From the form (still on the saved record's edit URL), click Delete → confirm modal appears with "ACME-001" referenced. Click confirm → return to `/customers/` placeholder; the customer is gone from `python manage.py shell` queries.
11. Click Master → Product → see "Products" placeholder card. Click + → see "Product Form — Under Construction" card. Click Close → back to `/products/`.
12. Repeat for Transactions → Invoice.

## 7. Smoke Run

Commands:

```text
python manage.py check
python manage.py makemigrations
python manage.py migrate
python manage.py test
```

HTTP GETs (must return 200):
/
/customers/
/customers/new/
/products/
/products/new/
/invoices/
/invoices/new/
(Smoke gate launches runserver on port 8765 to avoid colliding with the developer's dev server on 8000.)


## 8. Definition of Done
- All 17 acceptance criteria observable.
- All 19 test cases pass via python manage.py test.
- Smoke run commands all exit 0.
- All 7 smoke HTTP GETs return 200.
- Logic Tester PASS for every file in every bundle.
- UI Auditor PASS for every template.
- Manual click-path test succeeds in the instructor's browser.

## 9. Out of Scope (Explicit)
- Real customer list view. The placeholder stays. Real list (with rows, sort, search, filter, pagination) ships in a later sprint.
- Shared list view code module. Sprint that ships the real list will introduce or use it.
- Shared form view code module. Same — extracted in a later refinement sprint.
Reusable partials (_partials/list_table.html, _partials/form_layout.html).
- Pagination, sorting, searching, filtering. No real list yet.
- LOV pattern. Customer has no foreign keys in this sprint.
- Real Product app — placeholder only.
- Real Invoice app and line items — placeholder only.
- Authentication.
- Customer admin customization beyond the basic list_display.
- Custom widgets beyond Bootstrap classes. Date pickers, country selects, etc. are out.
- Static assets beyond CDN-loaded Bootstrap and Font Awesome.
## 10. Notes for Future Sprints
- Sprint 1's list-placeholder is a deliberate stub. The next sprint that targets Customer should replace it with the shared list view (or introduce the shared module if it doesn't exist yet).
- The notes TextField rendering (full-width textarea) is the first non-trivial widget pattern; reuse the same approach for any future TextField on other entities.
- Customer code uniqueness has no per-tenant scoping in this sprint — it's globally unique. A future multi-tenant sprint may need to revisit.
- The is_active soft-disable flag is intentionally absent from this sprint. If you decide later that customers need to be deactivatable rather than deleted, add is_active and adjust the list view filter accordingly.
- Address fields (address_line_1, address_line_2, city, province, country, postal_code) are simple CharFields. If structured address validation becomes needed, consider extracting an Address model or using django-address in a later sprint.
- The Product / Invoice placeholders use TemplateView directly in erp_invoice/urls.py. When the real Product app lands, those routes move to products/urls.py and the project URL just includes it. Same for Invoice.