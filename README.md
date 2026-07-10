# Gun Violence Archive Scraper

The [Gun Violence Archive query page](https://www.gunviolencearchive.org/query) caps each CSV export at 2,000 records. This notebook works around that by splitting the study period into short date windows, submitting one query per window through Selenium, downloading each export, and combining the files at the end.

Two query builders are included: one that excludes unwanted incident types (suicides, accidental shootings, officer-involved incidents, defensive gun use, and so on), and one that pulls gang-related incidents only.

Set the date range, window length, and download folders near the top of `scrape_gun_violence.ipynb` and run the cells in order. The code was written against the Selenium 3 API, so on Selenium 4 the `find_element_by_xpath` calls need updating. XPaths are hard-coded to the current site layout; keep the built-in delays and respect the site's terms of use.
