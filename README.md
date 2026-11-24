# Google Sheets to PostgreSQL ETL

Solution of the Jedha exercise *Automate Google Sheets with Love* (Data Ops module).

`etl.py` reads one tab of a Google Sheet with a service account, then loads it into a PostgreSQL table. The workflow `.github/workflows/gsheet-to-postgres.yml` runs it on GitHub Actions: open the **Actions** tab, select *Run ETL workflow*, then **Run workflow**. Uncomment the `schedule` block to run it every day.

## Before you start

1. A Google Cloud service account, its JSON key, and the **Google Sheets API** enabled in the same project.
2. Your copy of the sheet **shared with the service account**: paste the `client_email` of the JSON key in the Share dialog, role Viewer. Without this step the script stops with `PermissionError`.
3. A PostgreSQL database reachable from the internet, for example AWS RDS: a GitHub runner cannot reach a database running on your laptop.

## Environment variables

| Variable | Value |
|---|---|
| `SHEET_ID` | ID of your copy: the part of its URL between `/d/` and `/edit` |
| `WORKSHEET` | name of the tab, for example `Sheet1` |
| `GOOGLE_SA_KEY_JSON` | the **full content** of the JSON key file |
| `PG_USER`, `PG_PASSWORD` | user and password of your database |
| `PG_HOST`, `PG_PORT` | endpoint and port (`5432`) of your database |
| `PG_DB` | name of the database |
| `PG_TABLE` | table to create or replace, for example `pnl` |

In GitHub, store each one as a repository secret: *Settings → Secrets and variables → Actions*. Never commit the JSON key: `.gitignore` already excludes `*.json` files.

## Run it locally

```bash
pip install -r requirements.txt
export GOOGLE_SA_KEY_JSON="$(cat service_account.json)"
export SHEET_ID=your_sheet_id WORKSHEET=Sheet1 PG_TABLE=pnl
export PG_USER=postgres PG_PASSWORD=postgres PG_HOST=localhost PG_PORT=5432 PG_DB=finance
python etl.py
```
