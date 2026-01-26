# EU FSF Cron Trigger

This repository acts as a **cron-based scheduler** for the EU FSF Sanctions Scraper.

Due to known limitations and inconsistencies with GitHub Actions `schedule` events on some repositories, this project is intentionally separated into a **dedicated cron worker** that reliably triggers the main scraper repository.

---

## 🧠 Architecture Overview


EU-FSF-CRON (this repo)<br />
└── GitHub Actions (schedule)<br />
└── GitHub API (repository_dispatch)<br />
└── eu_fsf_sanc (worker repo)<br />
&nbsp;&nbsp;├── Scrapy spider<br />
&nbsp;&nbsp;├── Validation & pipelines<br />
&nbsp;&nbsp;├── JSON output generation<br />
&nbsp;&nbsp;└── Auto commit & push<br />


This design ensures:
- Reliable scheduled execution
- Full isolation of cron logic
- Cleaner CI/CD responsibilities
- Easier debugging and monitoring

---

## ⏰ Schedule

The workflow is triggered automatically via GitHub Actions:

- **Hourly execution** (configurable)
- Can also be triggered manually (`workflow_dispatch`) for testing

---

## 🔔 What This Repo Does

- Runs on a schedule
- Calls GitHub API using `repository_dispatch`
- Triggers the worker repository workflow
- Does **not** scrape or process data itself

This repo is **control-plane only**.

---

## 🔐 Required Secrets

The following repository secret must be configured:

| Secret Name | Description |
|------------|-------------|
| `WORKER_REPO_TOKEN` | GitHub Personal Access Token with `repo` permissions |

> ⚠️ The token must belong to a user that has **write access** to the worker repository.

---

## 📡 Triggered Repository

- **Worker repo:** `eu-fsf-sanctions`
- Responsible for:
  - Downloading EU FSF data
  - Validating schema
  - Generating JSON output
  - Committing updates automatically

---

## ✅ Why This Approach?

GitHub Actions `schedule` events:
- May silently stop triggering
- Are not always visible in the UI
- Can behave inconsistently across repositories

This external cron-trigger pattern is a **known, battle-tested workaround** used in production environments.

---

## 🛠 Manual Trigger

You can manually trigger the cron workflow from the **Actions** tab for testing purposes.

---

## 📄 License

MIT
