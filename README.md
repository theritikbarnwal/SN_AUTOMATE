# SN\_AUTOMATE

## Overview

`SN_AUTOMATE` is an automated web scraping solution built to extract job posting data from ServiceNow's careers page. It supports two independent scraping mechanisms:

* A **JavaScript-based scraper** using **Playwright** (`index.js`)
* A **Python-based scraper** using **Playwright (sync API)** (`pyscript/scrape.py`)

Both scripts are integrated with **GitHub Actions** CI/CD pipelines and save results in structured JSON files.

---

## Repository Structure

```
SN_AUTOMATE/
│
├── .github/
│   └── workflows/
│       ├── main.yml         # GitHub Actions for Python scraper
│       └── scrape.yml       # GitHub Actions for JavaScript scraper
│
├── index.js                # JavaScript-based Playwright scraper
├── package.json            # Node.js dependencies and scripts
├── README.md               # Project documentation
│
├── pyscript/
│   ├── scrape.py           # Python-based Playwright scraper
│   └── requirements.txt    # Python dependencies
│
├── JS_jobs.json            # JavaScript scraped data json file
├── PY_jobs.json            # Python scraped data jsoon file
```

---

## Features

* **Dual Scraping Options**: JS and Python versions for flexibility.
* **Headless Browser Support**: Can run both headless and non-headless.
* **Automated GitHub Workflows**: CI/CD pipelines push scraped results automatically.
* **Structured JSON Output**: Easily consumable and analyzable data.

---

## JavaScript-Based Scraper (`index.js`)

### Pros:

* Lightweight and natively integrates with Node.js-based automation.
* Easy integration with existing front-end projects.

### Known Challenge:

**Headless Mode Bug**

* Fails to consistently scrape the **experience** section.
* Works when run with `headless: false`.

### Cause:

* Dynamic JavaScript rendering delays.
* Experience fields are possibly loaded asynchronously.

### Solutions:

1. **Explicit Waits**

   ```js
   await page.waitForSelector('.experience-class', { timeout: 5000 });
   ```

2. **Increased Timeout**

3. **Larger Viewport in Headless Mode**

   ```js
   await browser.newContext({ viewport: { width: 1280, height: 1024 } });
   ```

4. **Enable Debug Logs**

   ```bash
   DEBUG=pw:browser* node index.js
   ```

### Output:

Creates JSON file with structure:

```json
{
  "Job": "Job Title",
  "Location": "City, Country",
  "Experience": "not_mentioned",
  "Job Description": "Job URL",
  "ServiceNow Page": 1
}
```
❌ Experience is marked as "not_mentioned" due to:

    JavaScript scraper running in headless mode: "true"

    Dynamic content not rendering before scraping

---

## Python-Based Scraper (`pyscript/scrape.py`)

### Script Summary:

Uses Playwright's **sync API** to scrape job title, location, description link, and experience (using regex).

### Key Features:

* Scrapes **multiple pages**.
* Opens job detail pages and extracts text for **experience pattern matching**.
* Stores result in timestamped JSON under `PY_jobs/`.

### Experience Extraction:

Uses regex to match phrases like:

* "2+ years", "minimum 5 years", "at least 3 years"

### Output:

Creates JSON file with structure:

```json
{
  "Job": "Job Title",
  "Location": "City, Country",
  "Experience": "2+ years, minimum 5 years",
  "Job Description": "Job URL",
  "ServiceNow Page": 1
}
```

---

## GitHub Actions Workflows

### 1. `scrape.yml` (JavaScript Scraper)

* Trigger: Push to `main` or on schedule.
* Installs Node.js dependencies.
* Runs `index.js`.
* Commits `JS_jobs-*.json` back to repo.

### 2. `main.yml` (Python Scraper)

* Trigger: Weekly (Mon, 4:30 AM UTC) or manual dispatch.
* Installs Python & Playwright.
* Runs `pyscript/scrape.py`.
* Commits `PY_jobs-*.json` back to repo.

---

## When to Use Which Scraper

| Situation                        | Use JS Script   | Use Python Script  |
| -------------------------------- | --------------- | ------------------ |
| CI/CD Integration                | ✓               | ✓                  |
| More Reliable Experience Parsing | ❌ (in headless) | ✓ (regex matching) |
| Faster Setup for Web Dev         | ✓               | ❌                  |
| Multi-page Scraping              | ❌               | ✓                  |

> ✅ **Python script offers better reliability when extracting dynamically loaded content like experience fields.**

---

## Final Notes

* The Python scraper is more robust for detailed extraction and can handle edge cases better.
* The JavaScript scraper is lightweight and simpler to set up in front-end CI environments.
* You can run both scrapers in **parallel** in **different branches** to avoid Git push conflicts.

Contributions and issue reports are welcome!
