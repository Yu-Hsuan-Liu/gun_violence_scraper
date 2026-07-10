# Gun Violence Archive Scraper

A Selenium-based scraper for the [Gun Violence Archive query page](https://www.gunviolencearchive.org/query). The site caps each CSV export at 2,000 records; this tool works around that limit by splitting a long study period into short date windows, submitting one query per window, and downloading each export automatically.

## What the code does

1. Splits a user-defined date range (for example, 2015-01-01 to 2021-10-31) into fixed-length intervals with `get_dates_list()`.
2. For each interval, drives a Chrome browser through the Gun Violence Archive query builder:
   - `get_gun_violence_data()` builds an exclusion query, adding one "Incident Characteristics does not contain" rule for each unwanted category (suicides, accidental shootings, officer-involved incidents, defensive gun use, and so on) before setting the From and To dates.
   - `get_gun_violence_data_any_gang()` builds an inclusion query that returns incidents matching either "Gang involvement" (incident characteristic) or "Gang vs Gang" (subject/suspect relationship) within the date window.
3. Submits the query, exports the results as CSV, and downloads the file.
4. Renames each downloaded file to `gun_vio_<start>_<end>.csv` and moves it from the browser download folder to a target data directory.
5. Concatenates all interval files into a single dataset with `combine_all_files()` and writes one combined CSV.

Helper functions include `get_days_positions()`, which converts a calendar date into the row and column position needed to click that day in the site's date-picker widget, and `get_options()`, which lists the incident characteristic values available in the query form.

## Requirements

- Python 3
- Google Chrome
- Packages: `selenium`, `webdriver-manager`, `pandas`, `numpy`, `beautifulsoup4`, `requests`

Note: the notebook was written against the Selenium 3 API (`find_element_by_xpath`). Selenium 4 users need to replace these calls with `find_element(By.XPATH, ...)`.

## Usage

Open `scrape_gun_violence.ipynb` and set the parameters near the top:

- `start_date`, `end_date`: study period in `YYYYMMDD` format
- `interval`: number of days per query window (keep each window small enough that a single export stays under 2,000 records)
- `not_contain_list`: incident characteristics to exclude (for the exclusion query)
- `new_path`: destination folder for the downloaded CSV files
- `mypath` inside the download-handling loop: your browser's download folder

Then run the cells in order. The final cells combine the per-window files into one CSV covering the full period.

## Output

- One CSV per date window, named `gun_vio_YYYYMMDD_YYYYMMDD.csv`
- One combined CSV, `total_gun_vio_<start>_<end>.csv`, with the standard Gun Violence Archive export columns

## Repository structure

```
scrape_gun_violence.ipynb   Main notebook: scraping functions and run cells
README.md                   This file
LICENSE                     MIT license
```

## Caveats

The scraper depends on hard-coded XPaths for the Gun Violence Archive query form. If the site layout changes, the XPaths need to be updated. Random sleep intervals are built in to avoid overloading the server; please keep them in place and respect the site's terms of use.
