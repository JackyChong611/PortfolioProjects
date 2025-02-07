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

# List of cuisines and district IDs
cuisines = ["粵菜-廣東", "潮州菜", "客家菜", "川菜-四川", "滬菜-上海"]
district_ids = [1008, -35243, -35244, -35242, 1001]

def get_driver():
    """Initialize a Chrome WebDriver instance with proper settings."""
    options = Options()
    options.add_argument('--headless')
    options.add_argument('--no-sandbox')
    options.add_argument('--disable-dev-shm-usage')
    options.add_argument('user-agent=Mozilla/5.0 (Windows NT 10.0; Win64; x64)')

    return webdriver.Chrome(options=options)

def safe_find(element, selector, attribute=None):
    """Safely extract data from HTML elements."""
    try:
        found = element.find(**selector) if isinstance(selector, dict) else element.find(selector)
        return found.get(attribute) if found and attribute else found.text.strip() if found else ''
    except Exception:
        return ''

def find_address(info_section):
    """Extract restaurant address from info section."""
    divs = info_section.find_all('div')
    for div in divs:
        text = div.text.strip()
        if text and not text.startswith('上') and not div.get('class'):
            return text
    return ''

def scrape_openrice(cuisine, district_id, max_retries=2, delay=5):
    """Scrapes OpenRice for restaurant details based on cuisine and district."""
    encoded_cuisine = quote(cuisine)
    base_url = f'https://www.openrice.com/zh/hongkong/restaurants/cuisine/{encoded_cuisine}?sortBy=ORScoreDesc&districtId={district_id}'
    print(f"Scraping: {base_url}")

    for attempt in range(max_retries):
        try:
            with get_driver() as driver:
                driver.set_page_load_timeout(30)
                driver.get(base_url)

                # Scroll down to load all restaurants
                last_height = driver.execute_script("return document.body.scrollHeight")
                while True:
                    driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")
                    time.sleep(2)
                    new_height = driver.execute_script("return document.body.scrollHeight")
                    if new_height == last_height:
                        break
                    last_height = new_height

                print("Finished scrolling")

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
                            continue  # Skip relocated restaurants

                        name = re.sub(r'\s*\(已搬遷\)\s*', '', name_element.text.strip()) if name_element else ''
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

                        # Extract Restaurant ID
                        link = restaurant.find('a', class_='poi-list-cell-desktop-right-link-overlay')
                        restaurant_id = re.search(r'r(\d+)', link['href']).group(1) if link else ''

                        data.append({
                            'Name': name,
                            'District': district,
                            'Address': address,
                            'Cuisine': cuisine,
                            'Type': restaurant_type,
                            'Price': price,
                            'RestaurantID': restaurant_id
                        })

                time.sleep(delay)
                return data

        except (WebDriverException, TimeoutException, NoSuchElementException) as e:
            print(f"Attempt {attempt + 1} failed: {str(e)}")
            if attempt < max_retries - 1:
                print(f"Retrying in {delay} seconds...")
                time.sleep(delay)
            else:
                print("Max retries reached. Moving to next URL.")
                return []

def main():
    """Main function to scrape multiple cuisines and districts."""
    all_data = []
    with concurrent.futures.ThreadPoolExecutor(max_workers=10) as executor:
        future_to_url = {executor.submit(scrape_openrice, cuisine, district_id): (cuisine, district_id)
                         for cuisine in cuisines for district_id in district_ids}
        for future in concurrent.futures.as_completed(future_to_url):
            cuisine, district_id = future_to_url[future]
            try:
                data = future.result()
                if data:
                    all_data.extend(data)
            except Exception as exc:
                print(f'{cuisine} {district_id} generated an exception: {exc}')

    # Save to Excel
    df = pd.DataFrame(all_data)
    df.to_excel('openrice_restaurants.xlsx', index=False, engine='openpyxl')
    print("Data saved to openrice_restaurants.xlsx")

if __name__ == "__main__":
    main()
