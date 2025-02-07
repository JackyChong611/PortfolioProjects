# Web Scraping and Data Cleansing Tool
This project is a Python-based web scraping and data cleansing tool designed to extract and clean data from licensed liquor premises and suppliers in Hong Kong.

---
## 📌 Code Installation  
Before running the script, install the required dependencies:
```
pip install requests beautifulsoup4 selenium pandas openpyxl
```

## 🚀 Features
✅ Automated data extraction using Python, BeautifulSoup, and Selenium. <br/>
✅ Data cleaning and preprocessing using Pandas. <br/>
✅ Handles over 50+ cuisine types & multiple district IDs for comprehensive data collection.<br/>
✅ Multi-threaded execution to optimize web scraping performance.<br/>
✅ Exports cleaned data to Excel (.xlsx) format.
## 📌 Main Section
### 📝 Web Scraping Code
```
import requests
from bs4 import BeautifulSoup
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.common.exceptions import WebDriverException, TimeoutException, NoSuchElementException
import time
import pandas as pd
import re
from urllib.parse import quote
import concurrent.futures
import threading
```
## 🚗 Initializing WebDriver
```
thread_local = threading.local()

def get_driver():
    """Initialize a Chrome WebDriver instance with proper settings."""
    if not hasattr(thread_local, "driver"):
        options = Options()
        options.add_argument('--headless')
        options.add_argument('--no-sandbox')
        options.add_argument('--disable-dev-shm-usage')
        options.add_argument('user-agent=Mozilla/5.0 (Windows NT 10.0; Win64; x64)')
        thread_local.driver = webdriver.Chrome(options=options)
    return thread_local.driver
```
## 🔍 Scraping Data from OpenRice
```
def safe_find(element, selector, attribute=None):
    try:
        if isinstance(selector, dict):
            found = element.find(**selector)
        else:
            found = element.find(selector)
        if found and attribute:
            return found.get(attribute)
        elif found:
            return found.text.strip()
    except:
        pass
    return ''

def find_address(info_section):
    divs = info_section.find_all('div')
    for div in divs:
        text = div.text.strip()
        if text and not text.startswith('上') and not div.get('class'):
            return text
    return ''

def scrape_openrice(cuisine, district_id, max_retries=2, delay=5):
    encoded_cuisine = quote(cuisine)
    base_url = f'https://www.openrice.com/zh/hongkong/restaurants/cuisine/{encoded_cuisine}?sortBy=ORScoreDesc&districtId={district_id}'
    print(f"Scraping URL: {base_url}")

    for attempt in range(max_retries):
        try:
            driver = get_driver()
            driver.set_page_load_timeout(30)  # Set page load timeout to 30 seconds
            driver.get(base_url)

            scroll_pause_time = 2
            last_height = driver.execute_script("return document.body.scrollHeight")

            while True:
                driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")
                time.sleep(scroll_pause_time)
                new_height = driver.execute_script("return document.body.scrollHeight")
                if new_height == last_height:
                    break
                last_height = new_height

            print('Finished scrolling')

            WebDriverWait(driver, 10).until(EC.presence_of_element_located((By.CLASS_NAME, "poi-list-cell")))

            soup = BeautifulSoup(driver.page_source, 'html.parser')

            restaurants = soup.find_all('div', class_='poi-list-cell')
            print(f"Found {len(restaurants)} restaurants")

            data = []
            for restaurant in restaurants:
                # Skip closed restaurants
                if restaurant.find('section', class_='pois-closed-restaurants-container'):
                    continue

                info_section = restaurant.find('section', class_='poi-list-cell-desktop-right-top-info-section')
                if info_section:
                    name_element = info_section.find('div', class_='poi-name')
                    if name_element and '(已搬遷)' in name_element.text:
                        continue  # Skip restaurants that have relocated

                    name = name_element.text.strip() if name_element else ''
                    name = re.sub(r'\s*\(已搬遷\)\s*', '', name)  # Remove "(已搬遷)" if present

                    address = find_address(info_section)

                    line_info = info_section.find('div', class_='poi-list-cell-line-info')
                    if line_info:
                        links = line_info.find_all('a', class_='poi-list-cell-line-info-link')
                        district = links[0].text.strip() if len(links) > 0 else ''
                        cuisine = links[1].text.strip() if len(links) > 1 else ''
                        restaurant_type = links[2].text.strip() if len(links) > 2 else ''
                        price_span = line_info.find('span', class_='poi-list-cell-line-info-link')
                        price = price_span.text.strip() if price_span else ''
                    else:
                        district = cuisine = restaurant_type = price = ''

                    # Find RestaurantID
                    link = restaurant.find('a', class_='poi-list-cell-desktop-right-link-overlay')
                    restaurant_id = ''
                    if link:
                        match = re.search(r'r(\d+)', link['href'])
                        if match:
                            restaurant_id = match.group(1)

                    data.append({
                        'Name': name,
                        'District': district,
                        'Address': address,
                        'Cuisine': cuisine,
                        'Type': restaurant_type,
                        'Price': price,
                        'RestaurantID': restaurant_id
                    })

            time.sleep(delay)  # Add delay between requests
            return data

        except (WebDriverException, TimeoutException, NoSuchElementException) as e:
            print(f"Attempt {attempt + 1} failed: {str(e)}")
            if attempt < max_retries - 1:
                print(f"Retrying in {delay} seconds...")
                time.sleep(delay)
            else:
                print("Max retries reached. Moving to next URL.")
                return []
```
