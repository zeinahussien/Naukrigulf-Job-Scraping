# Naukrigulf Data Engineer Jobs Scraper

A Selenium + Python script that scrapes "Data Engineer" job listings from the first three pages of search results on [Naukrigulf](https://www.naukrigulf.com/data-engineer-jobs) and saves them to a CSV file.

## What it does

1. Opens the Naukrigulf "Data Engineer" search results in Chrome.
2. Reads every job card on the current page.
3. Clicks the **next page** arrow to move to the following page.
4. Repeats until pages 1, 2 and 3 have been scraped.
5. Exports all collected jobs to `jobs.csv`.

## Data extracted

For every job listing, the script collects:

| Column       | Description                                   |
|--------------|-----------------------------------------------|
| `Title`      | Job title                                     |
| `Company`    | Hiring company name                           |
| `Location`   | Job location (city and country)               |
| `Experience` | Required experience (e.g. `5 - 8 Years`)      |
| `Description`| Job description text shown on the results card |

> **Note:** the description is the summary shown on the search results card, not the text from each job's own detail page.

## Requirements

- Python 3.8+
- Google Chrome
- Python packages:
  - `selenium`
  - `pandas`

Selenium 4.6+ downloads a matching ChromeDriver automatically, so no manual driver setup is needed.

## Installation

```bash
git clone https://github.com/zeinahussien/Naukrigulf-Job-Scraping.git
cd Naukrigulf-Job-Scraping
pip install selenium pandas
```

## Usage

The code is in a Jupyter notebook (`app.ipynb`). Open it and run the cells in order:

1. **Cell 1:** imports and Chrome options.
2. **Cell 2:** launches the browser, scrapes pages 1 to 3 and stores the results in `jobs_list`.
3. **Cell 3:** converts the results to a DataFrame and saves `jobs.csv`.

```bash
jupyter notebook app.ipynb
```

A Chrome window will open and navigate the pages by itself. Don't close it until the scrape finishes.

## Output

`jobs.csv` is created in the same folder as the notebook. A full run collects about 90 listings (around 30 per page).

## How it works

- **Selectors:** each job card is found with the CSS selector `div.ng-box.srp-tuple`. Inside each card, the title, company, location, experience and description are read using `designation-title`, `info-org`, `li.info-loc`, `li.info-exp` and `description`.
- **Dynamic loading:** the site is rendered with JavaScript, so the script waits a few seconds after each page change before reading the cards.
- **Pagination:** the next-page arrow is clicked using the selector `.ico.bwd`. A plain `arr-wrapper` selector is avoided because, from page 2 onward, the "previous" arrow also uses that class and the script would go backwards.
- **Browser options:** options are set to reduce the chance of the site blocking the automated browser (for example `--disable-blink-features=AutomationControlled`).

## Troubleshooting

| Problem | Likely cause and fix |
|---|---|
| `NoSuchElementException` on job cards | The page hasn't finished rendering. Increase the `time.sleep` value or use `driver.implicitly_wait(20)`. |
| `StaleElementReferenceException` | The page re-rendered while reading cards. Re-find the cards after each page change and wait before reading them. |
| Script goes from page 2 back to page 1 | The wrong arrow is being clicked. Make sure the selector targets the **next** arrow only. |
| Blank page or access denied | The site may be blocking automation. Run in non-headless mode (the default here) and keep the anti-detection options. |

## Notes

- This project is for educational purposes. Check the website's terms of use before scraping it at scale, and keep the request rate low.
- The site's HTML structure can change at any time, which may break the selectors.

## Project structure

```
.
├── app.ipynb     # scraping notebook
├── jobs.csv      # scraped output
└── README.md
```
