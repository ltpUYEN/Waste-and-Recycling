# HSY Waste Category Scraper

## Project Overview

This Python script scrapes the main waste categories listed on the HSY (Helsinki Region Environmental Services Authority) Waste Guide category page: 
`https://www.hsy.fi/en/waste-and-recycling/waste-guide/waste-category/`

It extracts the sorting order (based on sequence) and the category name for each item found on that page and saves the results into a CSV file named `hsy_waste_categories.csv`, suitable for opening in Excel. This dataset was compiled to serve as a basis for linking HSY waste categories to available external data sources regarding waste management in the Helsinki region (a project I will do in a future).

## Code
```python
import requests
from bs4 import BeautifulSoup
import re 
import csv 
from urllib.parse import urljoin 

URL = "https://www.hsy.fi/en/waste-and-recycling/waste-guide/waste-category/"
OUTPUT_CSV_FILE = "hsy_waste_categories.csv"

def scrape_and_load_hsy_categories_minimal():
    category_list = [] 
    order_counter = 0 
    try:
        headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3'}
        response = requests.get(URL, headers=headers, timeout=10)
        response.raise_for_status() 
        

        soup = BeautifulSoup(response.text, 'html.parser')
        
        main_content = soup.find('main', id='main') or soup 
        category_elements = main_content.select('a.link-box') 

        for element in category_elements:
            category_name = element.get_text(strip=True)
            if category_name:
                 order_counter += 1
                 category_list.append({
                     'order': order_counter,          
                     'category': category_name,    
                 })

    except requests.exceptions.RequestException:
        return None
    except Exception:
        return None

    return category_list

def save_data_as_csv(data_list):
    """Saves the list of dictionaries (Order, Category) as a CSV file."""
    if not data_list: 
        return 

    print(f"Saving data to CSV file: {OUTPUT_CSV_FILE}") 
    try:
        header = ['order', 'category'] 
        with open(OUTPUT_CSV_FILE, 'w', newline='', encoding='utf-8') as csvfile:
            writer = csv.DictWriter(csvfile, fieldnames=header)
            writer.writeheader()
            writer.writerows(data_list) 
        print(f"Data successfully saved to {OUTPUT_CSV_FILE}") 
    except IOError:
        pass
    except Exception:
        pass

if __name__ == "__main__":
    waste_data = scrape_and_load_hsy_categories_minimal()
    if waste_data: 
        save_data_as_csv(waste_data)
```

```csv
order,category
1,Asbestos
2,Biowaste
3,Brushwood
4,Car battery
5,Carton packages
6,Construction waste
7,End-of-life textiles
8,End-of-life vehicles
9,Garden waste
10,Glass
11,Gypsum waste
12,Hazardous waste
13,Impregnated wood
14,Insulation wool
15,Large objects
16,Metal
17,Mineral material
18,Mixed waste
19,Non-combustible waste
20,Other waste
21,Paper
22,Pharmaceutical waste (hazardous waste)
23,Plastic
24,Plastic packaging waste
25,Reusable items
26,Sekajäte kiinteistöllä
27,Small metal items
28,Soil
29,Tyres
30,Waste electric and electronic equipment (WEEE)
31,Waste that requires special handling
32,Wood waste
```

## Features

* Fetches HTML content from the specified HSY URL.
* Parses the HTML using BeautifulSoup.
* Selects category links based on the CSS selector `a.link-box`.
* Extracts the category name and assigns a sequential sorting order.
* Saves the extracted data (`order`, `category`) into a CSV file.
