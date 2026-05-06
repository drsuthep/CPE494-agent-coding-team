# SETUP_FOR_INVOICE_APP.md

This guide sets up the generated app repository:

- `CPE494-erp-invoice-app-by-ai`

Target baseline stack:

- Django
- SQLite
- Django Templates
- Bootstrap

## 1. Prerequisites

Install once per machine:

1. Python 3.11+ (or class-approved version)
2. Git

Optional:

- VS Code

## 2. Clone Target App Repo

```powershell
git clone <GENERATED_APP_REPO_URL>
cd CPE494-erp-invoice-app-by-ai
```

## 3. Create Virtual Environment

```powershell
python -m venv .venv
.venv\Scripts\activate
```

## 4. Install Dependencies

```powershell
python -m pip install --upgrade pip
pip install django
```

If a `requirements.txt` exists later, use:

```powershell
pip install -r requirements.txt
```

## 5. Django Project Initialization (If Not Yet Created)

From repo root:

```powershell
django-admin startproject config .
python manage.py startapp invoice_app
```

## 6. Migrate SQLite Database

```powershell
python manage.py migrate
```

## 7. Run Development Server

```powershell
python manage.py runserver
```

Open the URL shown in terminal, usually:

- `http://127.0.0.1:8000/`

## 8. Quick Verification Checklist

1. Virtual environment activates successfully.
2. `python manage.py migrate` succeeds.
3. `python manage.py runserver` starts normally.
4. Browser opens application home page.

## 9. Troubleshooting

`python` not found:
- Reinstall Python and enable PATH option.

Virtual env activation blocked:
- In PowerShell:
  ```powershell
  Set-ExecutionPolicy -Scope CurrentUser RemoteSigned
  ```

`django-admin` not found:
- Ensure venv is active.
- Reinstall with `pip install django`.
