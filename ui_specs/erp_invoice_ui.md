# ERP Invoice App UI Specifications

## 1. Purpose

This document defines UI/UX and visual behavior for the ERP Invoice application.

The `ui_reference.pdf` is visual guidance only. Output UI must be English only.

## 2. Language Rule

All UI text must be English:

- page titles
- menu labels
- button labels
- tooltips
- validation messages
- table headers

## 3. Zen Green Theme

Use a green enterprise theme:

- dark green title strips (prefer `#004d40`) with white text
- light green/mint accents
- clean spacing
- rounded corners

Recommended CSS tokens:

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

## 4. App Shell

Required shell behavior:

- top navigation with module groups
- user placeholder area
- landing page with brief English welcome text

## 5. List Views

Required behavior:

- consistent container style
- sortable/filterable columns where practical
- pagination where practical
- numeric/currency columns right-aligned

In the baseline, use Bootstrap tables first. Rich grid replacement can be a future sprint item.

## 6. Form Views

Required behavior:

- dark green section headers
- required fields marked with red asterisk
- clear validation messages near affected fields
- action buttons with descriptive English tooltips

## 7. Validation UX

- field-level errors: red text below field
- table/line-item errors: compact inline or tooltip-style message near affected input
- optional summary block for form-level errors

## 8. Responsiveness

- header fields adapt from multi-column to single column on small screens
- list/table views support horizontal scrolling where needed

## 9. Icons and Buttons

- use Font Awesome icons where appropriate
- keep icon use consistent
- every action button must include an English tooltip/title

## 10. Scope Rule

This is a pre-sprint baseline. Sprint documents should refine component-level rules as needed.
