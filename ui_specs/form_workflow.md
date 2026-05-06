# Form Workflow Specification (Baseline)

This document defines the **behavior** of forms across the entire ERP app. Every entity's form (Customer, Product, Invoice, and any future entity) follows these rules. Visual rules (theme, typography, color tokens) live in `erp_invoice_ui.md`.

## 1. Form Mode

There is exactly one form mode: **editable**. A form is always editable when open. There is no separate "view mode" or "read-only mode."

Implications:

- Clicking a row in a list view opens the record in this single editable form mode (not a separate "view" page).
- Forms do **not** have an "Edit" button  -  the form is already editable. Edit semantics are handled at the list view level (clicking a row IS the edit action).

## 2. Two Views per Model

Every entity has exactly two screens:

- **List View**  -  entry point from the navbar; also reused as the LOV picker when invoked from a parent form's FK field.
- **Form View**  -  editable form for create or update.

## 3. Navigation Hierarchy

Form -> List -> Landing.

- From a Form, **Close** -> entity's List View.
- From a List View, **Close** -> Landing page (`/`).
- The user can also navigate via the navbar at any time. Navbar links carry the same dirty-bit etiquette as Close buttons (confirm before discarding unsaved changes  -  typically via a JS `beforeunload` listener).

## 4. Canonical Action Button Vocabulary

Every form (and every list view) draws from this fixed vocabulary. No new buttons may be invented in any sprint without updating this spec.

| Button | Where | Icon | Variant | Action | Confirm? |
|---|---|---|---|---|---|
| **+ New** | List view top-right | `fa-plus` | primary | Open form in create mode |  -  |
| Row link / **Edit** | List row | `fa-pen` (icon-only ok) | link / icon button | Open the row's record in form view |  -  |
| **Delete** | List row OR form action row (when editing existing) | `fa-trash` | danger | Delete record -> return to list view | yes  -  modal "Are you sure?" |
| **Save** | Form action row | `fa-floppy-disk` | primary | Persist record + 2-second success flash; stay on form |  -  |
| **Close** | Form action row | `fa-xmark` | secondary | Go to entity's list view; if dirty, confirm | if dirty |
| **Close** | List view top-right | `fa-xmark` | secondary | Go to landing page (`/`) |  -  |
| **LOV** (`...`) | Adjacent to FK input | `fa-list` | small btn | Open shared list view in select-mode |  -  |

All action buttons include English `title=` tooltips and Font Awesome icons.

The form action row does **not** have a separate "List" button  -  Close serves that purpose (Close from form goes to the list view).

## 5. Dirty Bit

A form is "dirty" when any input has changed since the last successful save (or since the form was opened, for a new record).

- Set by JS `input` / `change` event listeners on form inputs, including hidden FK id fields after an LOV selection.
- Cleared after a successful save (server returns 302 redirect or success response that re-renders with a fresh form state).
- Consulted before any navigation away from the form (Close, navbar click, browser back, browser refresh)  -  confirm before discarding.

Implementation hint  -  small `<script>` in `base.html` or in a shared form partial:

```html
<script>
  (function () {
    let formDirty = false;
    document.addEventListener('input', function (e) {
      if (e.target.matches('form input, form select, form textarea')) {
        formDirty = true;
      }
    }, true);
    window.formDirty = () => formDirty;
    window.clearDirty = () => { formDirty = false; };
    window.addEventListener('beforeunload', function (e) {
      if (formDirty) { e.preventDefault(); e.returnValue = ''; }
    });
  })();
</script>
```

## 6. Save Behavior + Save Feedback

After Save:

- Form data is validated server-side via the ModelForm.
- On validation failure: re-render the form with field errors. Dirty bit remains set. No flash message.
- On success: persist via Django ORM, then redirect to the SAME form for the saved record (PRG pattern). The redirected GET shows the saved data + a Django `messages.success("Saving... Save successful")` notification. A small JS-driven toast/banner shows the message for ~2 seconds and then fades out. Dirty bit clears.
- The form does NOT navigate to the list view on save. The user stays on the saved record. To leave, the user clicks Close.

Implementation pattern:

```python
# views.py
from django.contrib import messages
from django.shortcuts import render, redirect, get_object_or_404

def customer_edit(request, id):
    obj = get_object_or_404(Customer, pk=id)
    if request.method == 'POST':
        form = CustomerForm(request.POST, instance=obj)
        if form.is_valid():
            form.save()
            messages.success(request, "Saving... Save successful")
            return redirect('customers:edit', id=obj.id)
    else:
        form = CustomerForm(instance=obj)
    return render(request, 'customers/form.html', {'form': form, 'obj': obj})
```

```html
<!-- base.html (excerpt) -->
{% if messages %}
  <div id="flash-stack" aria-live="polite">
    {% for m in messages %}
      <div class="alert alert-{{ m.tags|default:'success' }} flash-msg" role="alert">{{ m }}</div>
    {% endfor %}
  </div>
  <script>
    setTimeout(function () {
      document.querySelectorAll('.flash-msg').forEach(function (el) {
        el.style.transition = 'opacity 0.5s';
        el.style.opacity = '0';
        setTimeout(function () { el.remove(); }, 500);
      });
      if (window.clearDirty) window.clearDirty();
    }, 2000);
  </script>
{% endif %}
```

## 7. Close Behavior

- From a Form: if dirty -> JS confirm "Discard your unsaved changes?" -> on yes, navigate to entity's List View. Otherwise navigate immediately.
- From a List View: navigate to landing page (`/`).
- The Close button is a regular `<a>` link to the destination, with a JS click handler that consults `window.formDirty()` before allowing the navigation.

## 8. Delete Behavior

- A trash icon button appears on each list row AND in the form action row (only when editing an existing record  -  not in create mode).
- Click -> JS modal confirm "Are you sure you want to delete `<display_code>`?" -> on yes, POST to the delete URL -> redirect to the list view.
- Delete is a POST (not GET) and includes `{% csrf_token %}`.

## 9. List of Values (LOV) Pattern

Every foreign key field is rendered as three controls:

1. **Hidden input** carrying the related record's `id` (named to match the model field, e.g., `name="customer"`).
2. **Read-only visible input** showing the related record's display code.
3. **LOV button** (`...`) adjacent to the visible input.

Clicking the LOV button:

- Opens the related entity's List View in **select-mode**. Visually similar to browse-mode (sort, search, filter all available) but each row is selectable instead of openable.
- The select-mode list shows the columns declared in the active sprint scope file for that LOV (e.g., for a customer LOV in the invoice form, the scope might say "show: code, name, address, phone").
- User clicks a row -> LOV closes -> parent form's hidden id is set to that record's id, visible input is set to that record's display code -> parent form's dirty bit is set.

Implementation choice (modal vs. popup window vs. full-page route): the active sprint scope decides. Either way the parent form regains focus after selection.

## 10. Foreign Key Display Convention

The user never sees a numeric `id`. They see a display code (e.g., `"ACME-001"` for a customer). The hidden id field carries the value that ultimately persists in the FK column. This applies in:

- Form views (FK rendered as id hidden + code visible + LOV button).
- List views (FK column displayed as the related record's display code, never id).
- LOV select-mode lists (rows show display code as the primary column).

## 11. Field-Level Errors

When server-side validation fails on Save:

- Re-render the form with the bound form (no redirect).
- Each invalid field shows a red error message below it: `<div class="invalid-feedback d-block">{{ form.<field>.errors|join:", " }}</div>`.
- The form retains the user's entered values (not the saved DB values).
- Dirty bit remains set so the user must explicitly Save or Close.

## 12. Form Layout Density (Responsive Columns)

Every form supports **1, 2, 3, or 4 columns** of attribute-value pairs at its widest viewport, configurable per form. As the viewport narrows (large desktop -> small desktop -> tablet -> phone), columns collapse smoothly:

| Density | xl >=1200 | lg >=992 | md >=768 | sm <768 |
|---|---|---|---|---|
| 1 | 1 col | 1 col | 1 col | 1 col |
| 2 | 2 col | 2 col | 2 col | 1 col |
| 3 | 3 col | 3 col | 2 col | 1 col |
| 4 | 4 col | 3 col | 2 col | 1 col |

The active sprint scope picks the density per form (declared in the entity contract section). Default density is **2** if not specified.

Bootstrap class mapping  -  apply to each field's wrapper `<div class="col-...">`:

- Density 1 -> `col-12`
- Density 2 -> `col-12 col-md-6`
- Density 3 -> `col-12 col-md-6 col-lg-4`
- Density 4 -> `col-12 col-md-6 col-lg-4 col-xl-3`

Field-row structure example (density 3):

```html
<div class="row g-3">
  <div class="col-12 col-md-6 col-lg-4">
    <label for="id_code">Code <span class="text-danger">*</span></label>
    <input type="text" id="id_code" name="code" class="form-control" required>
  </div>
  <div class="col-12 col-md-6 col-lg-4">
    <label for="id_name">Name <span class="text-danger">*</span></label>
    <input type="text" id="id_name" name="name" class="form-control" required>
  </div>
  <div class="col-12 col-md-6 col-lg-4">
    <label for="id_email">Email</label>
    <input type="email" id="id_email" name="email" class="form-control">
  </div>
</div>
```

The form_layout.html partial is parameterized by density. Templates pass the density as a context variable (e.g., `{"form_density": 3}`).

## 13. List View Scrolling and Pagination

List views handle large record counts with **two complementary mechanisms**: server-side pagination and viewport-bounded scroll.

### 13.1 Server-side pagination

The shared list view module uses Django's `Paginator` with a page size of **N records** per page. Default `N = 50`. The active sprint scope can override per entity.

Pagination controls (Previous / page-numbers / Next) appear at the bottom of the list view card, outside the scrolling viewport.

### 13.2 Viewport scrolling

The list table viewport is fixed-height  -  vertical scroll appears when rendered rows exceed the viewport, even within a single paginated page. Default `max-height: 60vh`.

CSS (declared once in base.html or a shared stylesheet):

```css
.list-viewport {
  max-height: 60vh;
  overflow-y: auto;
}
.list-viewport thead {
  position: sticky;
  top: 0;
  background: var(--zen-primary-soft);
  color: white;
}
```

Template wrapper:

```html
<div class="table-responsive list-viewport">
  <table class="table">
    <thead>
      <tr>...</tr>
    </thead>
    <tbody>
      ...
    </tbody>
  </table>
</div>
<nav class="pagination-controls" aria-label="Page navigation">
  ...
</nav>
```

The combined behavior: server returns 50 records per page; the user scrolls within those 50; clicking "Next page" fetches the next 50 from the server.

### 13.3 Sort, search, filter

These features live on the shared list view module and are toggleable per column. They operate on the full filtered queryset before pagination  -  i.e., a search returns paginated matching results, not just matches within the current page.

## 14. Line Item Grid Workflow (Master-Detail Forms)

A "line item grid" is a child table embedded inside a parent form. Invoice header -> invoice lines is the canonical case; future entities may adopt the same pattern.

### 14.1 Visual structure

- The grid renders inside the parent form as a Bootstrap `<table>` with editable cells.
- Each row corresponds to one child record.
- A "+ Add row" button appears below the table (or as an empty bottom row)  -  primary outline style, `fa-plus` icon, `title="Add row"`.
- Each row has a trash icon button (`fa-trash`, `title="Delete row"`) in a trailing actions cell.
- Inline editing: cells are `<input>`, `<select>`, or LOV inputs (for FK fields in a line) directly in the table.
- Numeric/currency cells use `text-end`.

### 14.2 Dirty bit integration

- Adding a row, removing a row, or editing any cell sets the **parent form's dirty bit**.
- Save / Close / Delete behavior of the parent form applies to the entire master-detail unit  -  there is no separate "save line" action.

### 14.3 Atomic save

- Saving the parent form persists header + all lines in a single `transaction.atomic` block (per logic spec Section 8).
- Line-level validation surfaces inline (red text below the affected cell or floating tooltip) without losing other rows' data.

### 14.4 Scrolling viewport

Long line lists must not push the parent form's action row off-screen. The grid container is fixed-height with vertical scroll and a sticky header:

```css
.lineitem-viewport {
  max-height: 50vh;
  overflow-y: auto;
}
.lineitem-viewport thead {
  position: sticky;
  top: 0;
  background: var(--zen-primary-soft);
  color: white;
  z-index: 1;
}
```

```html
<div class="table-responsive lineitem-viewport">
  <table class="table">
    <thead><tr>...</tr></thead>
    <tbody id="lineitem-rows">...</tbody>
  </table>
</div>
<button type="button" class="btn btn-outline-primary btn-sm" data-action="add-line" title="Add row">
  <i class="fa-solid fa-plus"></i> Add row
</button>
```

### 14.5 Responsive behavior

- On `<md` viewports the grid horizontally scrolls (`.table-responsive`). The grid stays tabular  -  it does not collapse to a stacked card layout. Inline editing on a phone is acknowledged as awkward; this is a deliberate tradeoff to keep the table semantics consistent.
- A future sprint MAY add a "view-only" line summary card for small viewports; that is a sprint-scope decision, not baseline.

### 14.6 What stays sprint-scope (not baseline)

- Specific line fields and their types (e.g., `quantity`, `unit_price`, `line_total`).
- Auto-recalculation rules (e.g., `line_total = quantity * unit_price`).
- Whether recalculation runs live on input change (JS) or only on save (server-side).
- Whether a footer row sums totals across lines.
- The exact implementation pattern  -  Django formset, AJAX, or htmx-equivalent  -  sprint scope picks one.

The principle: line item grids inherit ALL form-workflow rules above (modes, buttons, dirty bit, save feedback, navigation, density rules for the parent form's headers) and ADD the line-grid-specific behaviors in this section. The sprint scope that introduces master-detail declares the schema, recalculation rules, and implementation pattern.

## 15. Compliance

- The architect MUST plan for all the buttons, navigation, density, viewport, pagination, and line-grid behaviors above when scoping any task that produces a form, list, or master-detail template.
- The coder MUST emit forms with the canonical button vocabulary, dirty-bit listeners, save flash, FK rendering as described, density classes from Section 12, list viewport CSS from Section 13, and line-grid viewport CSS from Section 14.
- The logic tester MUST audit:
  - FK fields render as id-hidden + code-visible + LOV button.
  - Save feedback uses Django messages.
  - Shared list view uses Django Paginator (default page_size = 50, override per sprint).
- The UI auditor MUST audit:
  - Canonical button placement, navigation hierarchy, save flash, Delete confirmation.
  - Form density classes match the density declared in the active sprint scope (default 2).
  - List view viewport has `.list-viewport` class and pagination controls.
  - Line item grids (when present) have `.lineitem-viewport` class and sticky `<thead>`.

