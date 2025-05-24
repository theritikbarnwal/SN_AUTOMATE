# SN\_AUTOMATE

## Overview

`SN_AUTOMATE` is an automated web scraping tool built with **Playwright** to extract **job posting** data from **ServiceNow's** careers page. This repository also contains an automated CI/CD pipeline that allows scheduled or manual scraping using **GitHub Actions**. The scraped data is stored in a structured **JSON** format, providing useful insights like job titles, descriptions, and experience requirements.

This tool is designed to automate the scraping process, eliminate manual effort, and integrate into your workflow efficiently.

## File Structure

The repository contains the following files and directories:

```
SN_AUTOMATE/
│
├── .github/
│   └── workflows/
│       └── scrape.yml        # GitHub Actions configuration for automated scraping
│
├── index.html               # Basic HTML structure for displaying or verifying scraped data
├── package.json             # Node.js package manager file with all necessary dependencies
└── data.json                # JSON file containing the latest scraped job data
```

### File Descriptions:

* **index.html**: A simple static HTML file that can be used to display or validate the scraped job data.
* **scrape.yml**: A GitHub Actions workflow configuration file that automates the scraping process, defining when and how the scraping should be triggered.
* **package.json**: Contains the project's dependencies (e.g., Playwright) and scripts for running the scraper.
* **data.json**: The output file that stores all the extracted job postings from the ServiceNow careers website in JSON format.

## Features

* **Web Scraping with Playwright**: Uses Playwright to programmatically navigate through the ServiceNow careers page and scrape job postings data.
* **Headless Browser Mode**: Can run in both headless (no GUI) and non-headless mode for flexibility.
* **Automated CI/CD with GitHub Actions**: Automates scraping using GitHub Actions for scheduled or push-triggered runs.
* **Data Storage in JSON Format**: Scraped data is stored in a well-structured JSON format for easy access and further analysis.

## Installation & Setup

### 1. Clone the Repository

Clone the repository to your local machine:

```bash
git clone https://github.com/theritikbarnwal/SN_AUTOMATE.git
cd SN_AUTOMATE
```

### 2. Install Dependencies

Install the necessary Node.js dependencies by running the following command:

```bash
npm install
```

### 3. Configure GitHub Actions

GitHub Actions are configured through the `scrape.yml` file. This file will automate the scraping process. You can set it to run on a schedule (e.g., daily) or on a push to the `main` branch.

### 4. Run the Scraper Locally

To test or run the scraper locally, execute the following command:

```bash
node index.js
```

Make sure the dependencies are installed correctly before running this command.

## Known Issues

### 1. Issue with Headless Mode

When running the scraper in **headless mode (`headless: true`)**, it fails to scrape the **experience** section of the job postings correctly. However, it works perfectly when the browser is run with **headless: false** (non-headless mode).

#### Problem Description:

In headless mode, Playwright sometimes fails to interact with dynamic content on the page or waits too long for elements to load, especially those rendered with JavaScript after the initial HTML is loaded. As a result, the experience field is not properly scraped.

#### Root Cause:

* **Dynamic Content Loading**: The experience section is dynamically loaded, possibly with JavaScript after the initial page load. In non-headless mode, the browser window is displayed, which may allow the content to load correctly before the scraper tries to access it.

* **Timing Issues**: Headless browsers are optimized for speed, and elements might not be fully rendered by the time the script tries to scrape them. Playwright, in headless mode, might miss out on waiting for specific elements to appear on the page.

#### Proposed Solutions:

1. **Explicit Waits**: Use Playwright's `waitForSelector()` to ensure that the experience section is loaded before attempting to scrape it.

   ```js
   await page.waitForSelector('.experience-class', { timeout: 5000 });
   ```

2. **Increase Timeout**: Sometimes the page takes longer to load in headless mode. Increasing the timeout value might resolve the issue.

3. **Viewport Adjustments**: In some cases, increasing the browser's viewport size in headless mode can resolve rendering issues by ensuring all elements are visible.

   ```js
   await browser.newContext({
     viewport: { width: 1280, height: 1024 }
   });
   ```

4. **Enable Debug Mode**: For debugging, you can enable Playwright's debug logs in headless mode to get insights into why the content is not loading.

   ```bash
   DEBUG=pw:browser* node index.js
   ```

By implementing these fixes, the script should work consistently in both headless and non-headless modes.

## CI/CD Integration with GitHub Actions

The `.github/workflows/scrape.yml` file automates the scraping process using **GitHub Actions**. Below is a brief overview of the workflow:

### Workflow Steps:

1. **Trigger**: The workflow is triggered on either a push to the `main` branch or a scheduled time (e.g., daily at midnight).
2. **Checkout Repository**: The workflow first checks out the code from the repository.
3. **Install Dependencies**: It installs the necessary Node.js dependencies (like Playwright).
4. **Run Scraping Script**: Executes the scraping script to extract job data.
5. **Commit Scraped Data**: After scraping, the data is committed back to the repository (or can be uploaded to an external database if desired).

## Conclusion

`SN_AUTOMATE` offers an efficient solution for scraping job postings from ServiceNow, automating the process with **Playwright** and **GitHub Actions**. While there are known issues with scraping in headless mode, these can be addressed with appropriate configuration adjustments. This project is a great example of leveraging modern web scraping and CI/CD techniques for automated data extraction.

Feel free to contribute, suggest improvements, or open an issue if you encounter any problems.

---
