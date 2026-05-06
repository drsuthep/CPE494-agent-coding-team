# ERP Invoice App  -  UI Specification (Baseline)

## 1. Purpose

Broad UI / theme principles for the ERP Invoice app. **Form behavior** (modes, buttons, dirty bit, save feedback, LOV, navigation) lives in `ui_specs/form_workflow.md`. **Detailed component layouts come per sprint** in `sprints/sprint_NN_scope.md`. The PDF reference (`ui_reference.pdf`) is visual inspiration only.

## 2. Language Rule

All UI text must be English: page titles, menu labels, button labels, tooltips, validation messages, table headers, empty-state messages.

## 3. Zen Green Theme

Green enterprise theme. Required CSS tokens (declared once in `templates/base.html` `<style>` block or a project static stylesheet):

```css
:root {
  --zen-primary: #004d40;
  --zen-primary-soft: #2e7d64;
  --zen-accent: #a5d6a7;
  --zen-surface: #e8f5e9;
  --zen-border: #81c784;
  --zen-error: #c62828;
}
```

- Top header strip / navbar: `--zen-primary` background, white text.
- Section / card headers: `--zen-primary-soft`.
- Surfaces and cards: `--zen-surface` (or default Bootstrap white).
- Borders: `--zen-border`.
- Error text: `--zen-error`.

## 4. App Shell

- Top navbar (Bootstrap 5 navbar) with module groups: at minimum **Customers**, **Products**, **Invoices**. Modules become live as their sprints land. Navbar links navigate to each entity's list view.
- User placeholder area on the right (no real auth in baseline).
- Landing page (`/`) is the rest state of the app  -  a welcome card. Closing the list view returns here.

## 5. Two Views per Model

Every entity has exactly two screens:

- **List View**  -  accessed from the navbar. Also reused as an LOV picker when invoked from a parent form's foreign key field. Lists existing records with sortable, searchable, filterable columns.
- **Form View**  -  accessed by clicking `+ New` (from list view) or by clicking a row (to edit existing). Single mode: always editable.

**Navigation hierarchy:** Form -> List -> Landing. Each Close pops up one level.

Detailed behavior  -  buttons, dirty bit, save flash, LOV pick flow, delete flow  -  lives in `ui_specs/form_workflow.md`.

## 6. Reusable Layout Pattern

Every entity uses the shared partials:

- `templates/_partials/list_table.html`  -  list view layout.
- `templates/_partials/form_layout.html`  -  form view layout.

Per-entity templates (`templates/customers/list.html`, etc.) are thin wrappers that include the partial and pass entity-specific context. The active sprint scope's file bundle indicates whether the current sprint is extracting these from per-entity code or reusing existing partials.

## 7. Canonical Action Button Vocabulary (Summary)

This is the fixed vocabulary across the entire app. Full details (icons, variants, when used, confirm behavior) live in `ui_specs/form_workflow.md`.

- **+ New** (`fa-plus`, primary)  -  list view top-right.
- **Edit** / row link (`fa-pen` optional)  -  list row.
- **Delete** (`fa-trash`, danger)  -  list row OR form action row.
- **Save** (`fa-floppy-disk`, primary)  -  form action row.
- **Close** (`fa-xmark`, secondary)  -  form action row (-> list view) AND list view top-right (-> landing).
- **LOV** (`fa-list`, small)  -  adjacent to a foreign-key input.

No new buttons may be invented without updating `form_workflow.md`.

## 8. Save Feedback (Baseline UX Rule)

After a successful save, the user stays on the form (showing the saved record) and sees a 2-second flash message `"Saving... Save successful"`. Implementation: Django `messages` framework + a small JS-driven toast in `base.html`. Pattern detailed in `ui_specs/form_workflow.md`.

## 9. Form Rules

- Required fields show a red asterisk after the label: `<span class="text-danger">*</span>`.
- Field-level errors: red text below the field, `<div class="invalid-feedback d-block">`.
- All `<form method="post">` includes `{% csrf_token %}`.

## 10. Validation Display

- Field-level errors: red text directly below the affected field.
- Form-level errors: optional summary block at top of card.
- Line-item grid errors (when present): floating tooltip-style near the affected cell. Detailed when an active sprint introduces line items.

## 11. Responsiveness, Density, and Scrolling

- **Form column density** is configurable per form: 1, 2, 3, or 4 columns at maximum viewport, collapsing smoothly as the viewport narrows (xl -> lg -> md -> sm). Default density is 2. See `form_workflow.md` Section 12 for the full breakpoint table and Bootstrap class mapping.
- **List view** uses a fixed-height viewport (`max-height: 60vh`) with vertical scroll AND server-side pagination (default page size = 50 records, configurable per entity). See `form_workflow.md` Section 13.
- **Line item grids** (master-detail forms) use a fixed-height viewport (`max-height: 50vh`) with vertical scroll and sticky `<thead>`. See `form_workflow.md` Section 14.
- All tables wrapped in `.table-responsive` for horizontal scroll on small screens.

## 12. Icons (Font Awesome 6, CDN)

CDN: `https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css`

Icon mapping is part of the canonical vocabulary in Section 7 and `form_workflow.md`. Every action button has an icon + label + English `title=` tooltip.

## 13. Numeric Alignment

Numeric and currency columns and inputs are right-aligned (`text-end` Bootstrap class).

## 14. Accessibility (Lightweight)

- Every form input has a label (via `for=` or wrapping `<label>`).
- Buttons have visible text or `aria-label`.
- Empty-state rows are visible to screen readers (no `display: none`).

## 15. List of Values (LOV) Pattern (Summary)

Foreign key fields render as `hidden id input + visible read-only display-code input + LOV button`. The LOV button opens the related entity's list view in select-mode. Full pattern, columns, and pick behavior in `ui_specs/form_workflow.md`.

## 16. Sprint-Layer Detail Comes Per Sprint

Specific page layouts, field sets, button labels for entity-specific text, and screen names are in the active sprint scope file. This file defines rules that apply across every entity.

