# HSY Waste Category Scraper

## Project Overview

This Python script scrapes the main waste categories listed on the HSY (Helsinki Region Environmental Services Authority) Waste Guide category page: 
`https://www.hsy.fi/en/waste-and-recycling/waste-guide/waste-category/`

It extracts the sorting order (based on sequence) and the category name for each item found on that page and saves the results into a CSV file named `hsy_waste_categories.csv`, suitable for opening in Excel or other spreadsheet programs. This dataset was compiled to serve as a basis for linking HSY waste categories to available external data sources regarding waste management in the Helsinki region.

## Features

* Fetches HTML content from the specified HSY URL.
* Parses the HTML using BeautifulSoup.
* Selects category links based on the CSS selector `a.link-box`.
* Extracts the category name and assigns a sequential sorting order.
* Saves the extracted data (`order`, `category`) into a CSV file.
