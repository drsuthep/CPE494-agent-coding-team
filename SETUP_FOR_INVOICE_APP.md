# SETUP_FOR_INVOICE_APP.md

This guide is for setting up the generated ERP Invoice app repository:

- `CPE494-erp-invoice-app-by-ai`

It is written for Windows student machines.

## 1. What This Setup Covers

After this setup, students should be able to:

- run the ASP.NET Core app
- connect to SQL Server LocalDB
- apply database migrations
- use local Tabulator assets (no CDN)

## 2. Prerequisites (Install Once Per PC)

Install the following:

1. .NET SDK (use the version required by the app project)
2. Visual Studio 2022 with `ASP.NET and web development` workload  
   (this installs SQL Server Express LocalDB)
3. Git

Optional but useful:

- Visual Studio Code
- SQL Server Management Studio (SSMS)

## 3. Clone the Generated App Repository

```powershell
git clone <GENERATED_APP_REPO_URL>
cd CPE494-erp-invoice-app-by-ai
```

## 4. Verify LocalDB

Run:

```powershell
sqllocaldb info
```

If `MSSQLLocalDB` appears, continue.

If not, run:

```powershell
sqllocaldb create MSSQLLocalDB
sqllocaldb start MSSQLLocalDB
```

## 5. Configure Connection String

Open `appsettings.json` (or `appsettings.Development.json`) and set:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\MSSQLLocalDB;Database=CPE494_InvoiceDb;Trusted_Connection=True;MultipleActiveResultSets=true;TrustServerCertificate=True"
  }
}
```

If your app uses a different connection string key, keep that key name and only change the value.

## 6. Restore, Build, and Apply Database Migration

From the generated app folder:

```powershell
dotnet restore
dotnet build
dotnet ef database update
```

If `dotnet ef` is missing:

```powershell
dotnet tool install --global dotnet-ef
dotnet ef database update
```

## 7. Install Tabulator Locally (No CDN)

Create this folder in the generated app:

```text
wwwroot/lib/tabulator/
```

Download Tabulator release files and copy at least:

- `wwwroot/lib/tabulator/tabulator.min.css`
- `wwwroot/lib/tabulator/tabulator.min.js`

Use the official Tabulator release source.

## 8. Reference Local Tabulator Files

In `_Layout.cshtml` (or page-specific layout), include:

```html
<link rel="stylesheet" href="~/lib/tabulator/tabulator.min.css" />
<script src="~/lib/tabulator/tabulator.min.js"></script>
```

Do not use CDN links.

## 9. Run the App

```powershell
dotnet run
```

Open the localhost URL shown in the terminal.

## 10. Quick Verification Checklist

1. `sqllocaldb info` shows `MSSQLLocalDB`
2. `dotnet build` succeeds
3. `dotnet ef database update` succeeds
4. app opens in browser
5. Tabulator files load from local paths (`~/lib/tabulator/...`)

## 11. Common Troubleshooting

`sqllocaldb` command not found:
- Install Visual Studio 2022 with ASP.NET workload, then reopen terminal.

`dotnet ef` not found:
- Run `dotnet tool install --global dotnet-ef`.

Database connection errors:
- Recheck connection string server name: `(localdb)\\MSSQLLocalDB`
- Ensure LocalDB instance is running: `sqllocaldb start MSSQLLocalDB`

Static file not found for Tabulator:
- Check exact file names and paths under `wwwroot/lib/tabulator`
- Confirm `_Layout.cshtml` uses `~/lib/tabulator/...`

