# ERP Invoice App UI Specifications

## 1. Purpose

This document defines the UI, UX, visual style, and interaction rules for the ERP Invoice application.

The UI is based on the provided `ui_specs/ui_reference.pdf`. The PDF contains some Thai labels, but the target application must use English only.

## 2. Strict Language Rule

All generated UI text must be English only, including:

- page titles
- menu labels
- button labels
- tooltips
- validation messages
- grid headers
- modal dialog text
- placeholder text

Thai text in the reference PDF is visual guidance only and must not be copied into the application.

## 3. Zen Green Theme

The application must use a green enterprise theme inspired by the reference screens.

Required visual rules:

- Main title strips and form headers must use dark green, preferably `#004d40`, with white text.
- Accents must use light green or mint shades.
- Large content areas should use very light green backgrounds.
- Forms and panels must use rounded corners.
- Editable fields and non-editable fields must have visibly different background shading.
- Theme tokens should be defined centrally in CSS variables so the theme can later be changed.

Suggested CSS tokens:

```css
:root {
  --zen-primary: #004d40;
  --zen-primary-soft: #2e7d64;
  --zen-accent: #a5d6a7;
  --zen-surface: #e8f5e9;
  --zen-surface-strong: #c8e6c9;
  --zen-border: #81c784;
  --zen-error: #c62828;
  --zen-readonly: #eef5f0;
  --zen-editable: #fffde7;
}
```

## 4. App Shell and Navigation

Reference: page 3 of the PDF shows the app shell and control menu.

Required behavior:

- Top navigation bar with module menus.
- Visible database selector placeholder.
- Visible current user indicator placeholder.
- Landing page with brief English welcome text.
- Menu structure should support at least:
  - Master Data
  - Transactions
  - Reports
  - Control

All menu labels must be English.

## 5. Login Screen

Reference: page 2 of the PDF shows a login screen with database selection.

Required fields:

- Database
- Username
- Password
- Remember me
- Sign in button

Required behavior:

- Database selection should be represented as a dropdown placeholder in Sprint 1.
- Full authentication may be implemented in a later sprint.

## 6. List Views

References:

- Product list view on page 4
- Invoice list view on page 6
- User list view on page 15
- Role list view on page 20

Required behavior:

- Use a consistent green list container.
- Use shaded grid headers.
- Support filtering/search rows where practical.
- Support pagination.
- Support sortable columns where practical.
- Use Tabulator for rich grid behavior when appropriate.
- Numeric and currency columns must be right-aligned.
- Text columns must be left-aligned.

## 7. Form Views

References:

- Product form view on page 5
- Invoice form view on pages 7 and 8
- User form views on pages 16 and 17
- Role form views on pages 21 and 22

Required behavior:

- Use a dark green form header strip.
- Use a row of compact action buttons.
- Every action button must include a descriptive English tooltip.
- Required fields must show a red asterisk next to the label.
- Form sections must be visually grouped.
- View mode and edit mode should be visually distinguishable.

## 8. Header Attribute-Value Layouts

Reference: pages 7, 10, and 14 show two styles of header fields.

The application must support two header field styles:

### Regular Attribute-Value Fields

- Label above or beside input.
- Responsive layout supports 1, 2, 3, or 4 fields per row depending on screen width.
- Fields should be evenly spaced.

### Tabular Attribute-Value Fields

- Field label appears like a green table heading.
- Value appears below the shaded label.
- Developer can specify width per field.
- Useful for totals such as subtotal, VAT rate, VAT amount, and grand total.

## 9. Invoice Form Layout

References: pages 7, 8, and 14.

Required header fields:

- Running ID
- Invoice No.
- Invoice Date
- Customer
- Customer Name
- Address Line 1
- Address Line 2
- Address Line 3
- Country
- Subtotal
- VAT Rate
- VAT Amount
- Grand Total

Required line item fields:

- Line No.
- Product Code
- Product Name
- Quantity
- Unit Price
- Unit
- Line Total

Required behavior:

- Line item grid must support horizontal scrolling on small screens.
- Header fields must wrap responsively.
- Totals must be visually separated using tabular attribute-value fields.
- Numeric and currency values must be right-aligned.

## 10. Validation UX

Reference: page 9 of the PDF.

Required behavior:

- Header field validation messages must appear as a separate row of red text immediately below the affected field.
- Line-item/grid validation messages must appear as tooltip-style floating message boxes anchored near the affected cell.
- A general validation summary or modal may be used for whole-form validation.
- Required fields must be marked with a red asterisk.

## 11. Dirty Bit Check

Reference: page 10 of the PDF.

Required behavior:

- If a user attempts to leave a form with unsaved changes, show a confirmation prompt.
- The prompt text must be English.
- This may be implemented after Sprint 1 if needed.

## 12. Date Selector

Reference: page 10 of the PDF.

Required behavior:

- Date fields should provide a date selector button or browser-supported date input.
- Date selector controls must match the Zen Green style where practical.

## 13. Buttons, Icons, and Tooltips

Reference: page 10 of the PDF shows tooltips on buttons.

Required behavior:

- Use Font Awesome icons for common actions.
- Every action button must have a descriptive English tooltip.
- Tooltip hover state should use a dark background with light text.

Suggested actions:

- Save
- Edit
- Delete
- Cancel
- Search
- Select
- Add Line
- Remove Line
- Open List of Values

## 14. List of Values (LoV)

References: pages 12 and 13.

Required behavior:

- LoV should reuse the same list-view pattern used by normal model list pages.
- LoV should support selection mode for foreign key lookup.
- LoV should support sorting.
- LoV should support filtering by text.
- LoV should support range filtering for date and numeric fields where practical.
- LoV should support pagination.
- LoV may provide a Create New button where appropriate.
- Clicking a row should select the record in selection mode.

## 15. Responsiveness

Reference: page 14 of the PDF.

Required behavior:

- Header fields must adjust from multi-column layout to single-column layout on small screens.
- Line item grids must support horizontal scrolling.
- Form header and main action area should remain usable on small screens.

## 16. Central Subforms

References: pages 23 and 24.

The app may eventually include common subforms for:

- Change Log
- Notes
- Attachments

These are optional for early sprints and may be placeholders until later implementation.

## 17. Auditor Checklist for UI Auditor

The UI Auditor must reject code if it violates any of these rules:

- Thai text appears in UI output.
- Dark green header/title strip is missing.
- Required fields lack a red asterisk.
- Numeric or currency fields are not right-aligned.
- Action buttons lack English tooltips.
- Form panels lack rounded corners.
- Validation messages do not follow the required header vs. line-item behavior.
- Responsive behavior is missing for header fields or line item grids.
- Tabulator grid styling does not follow the green enterprise look where Tabulator is used.
