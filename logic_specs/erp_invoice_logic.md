# ERP Invoice App Logic Specifications

## 1. Purpose

This document defines business logic, architecture, data standards, and validation rules for the ERP Invoice application.

## 2. Target Stack

- Django
- Python
- Django ORM
- SQLite (teaching default)

## 3. Strict Language Rule

All generated code, UI labels, messages, comments, and hardcoded strings must be English only.

## 4. Naming Standards

Use `snake_case` for:

- Python variables and function names
- model fields
- JSON keys
- form field names where practical

Use `UPPER_SNAKE_CASE` for constants.

## 5. Database Standards

Each entity must include:

- `id` primary key
- `rowversion`-equivalent optimistic concurrency field

For Django, the concurrency field may be represented using a version counter or update timestamp strategy defined per sprint.

Foreign keys should use clear names such as:

- `customer_id`
- `product_id`
- `invoice_id`

## 6. Core Entities

Expected business entities:

- Product
- Customer
- Invoice Header
- Invoice Line

Fields and constraints should be implemented incrementally by sprint.

For simplicity we will exclude authentication and authorization.  This app is meant to serve as a demo for agentic coding, so we will stick to functional requirements for Invoice app.

## 7. Transaction Rules

Saves involving invoice header and line items must be atomic.

A full invoice save must either:

- save header + all lines, or
- roll back all changes

Use Django transaction handling (`transaction.atomic`).

## 8. Validation Rules

Server-side validation is mandatory even when client-side validation exists.

Validation categories:

- required fields
- numeric range checks
- unique code checks
- foreign key validity
- concurrency checks
- server-side total recalculation

## 9. Security Rules

- Assume authentication is handled upstream unless sprint scope says otherwise.
- Validate input before persistence.
- Use Django ORM to avoid SQL injection risk.
- Avoid raw SQL unless parameterized and justified.

## 10. Code Quality Rules

Generated code must:

- run without syntax errors
- follow clear module boundaries
- avoid unnecessary duplication
- keep logic testable
- avoid hardcoding business data in templates

## 11. Sprint Scope Rule

This baseline is pre-sprint.

Each sprint definition must explicitly state:

- files in scope
- acceptance criteria
- verification checks
- done definition
