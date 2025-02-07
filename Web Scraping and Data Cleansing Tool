!pip install requests beautifulsoup4 selenium pandas
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

# Install necessary packages for Colab
!apt-get update
!apt install chromium-chromedriver
!cp /usr/lib/chromium-browser/chromedriver /usr/bin
!pip install selenium requests beautifulsoup4 pandas openpyxl

# List of cuisines and district IDs
cuisines = [
    "粵菜-廣東", "潮州菜", "客家菜", "川菜-四川", "滬菜-上海", "京菜-官府菜", "京川滬", "台灣菜", "順德菜",
    "滇菜-雲南", "湘菜-湖南", "桂菜-廣西", "東北菜", "鲁菜-山東", "新疆菜", "農家菜", "閩菜-福建", "江浙菜",
    "黔菜-貴州", "陝菜-陝西", "蒙古菜", "晉菜-山西", "鄂菜-湖北", "淮揚菜", "日本菜", "韓國菜", "泰國菜",
    "越南菜", "印尼菜", "新加坡菜", "馬來西亞菜", "菲律賓菜", "緬甸菜", "印度菜", "尼泊爾菜", "斯里蘭卡菜",
    "中東菜", "地中海菜", "土耳其菜", "黎巴嫩菜", "摩洛哥", "埃及菜", "非洲菜", "猶太菜", "希臘菜", "意大利菜",
    "法國菜", "美國菜", "英國菜", "西班牙菜", "德國菜", "比利時菜", "澳洲菜", "葡國菜", "瑞士菜", "愛爾蘭菜",
    "東歐菜", "荷蘭菜", "奧地利菜", "西式", "墨西哥菜", "古巴菜", "阿根廷菜", "秘魯菜", "巴西菜", "港式",
    "中菜", "粵菜", "亞洲菜", "西餐", "中東-地中海菜", "中南美菜", "多國菜"
]

district_ids = [
    1008, -35243, -35244, -35242, 1001, -9006, 1003, -9007, 1011, 1022, 1019, 1026, 1004, 1023, 1014, 1009, 1018, 1024,
    1013, 1005, 1017, 1025, 1027, 1012, 1020, 1021, 1010, 1002, -9151, 1007, 1015, 1016, 2008, 2010, 2028, 2011, 2029,
    2019, 2026, 2006, 2003, 2022, 2015, 2004, 2013, 2005, 2001, 2016, 2031, 2007, 2009, 2002, 2030, 2027, 2020, 2032,
    2021, 2024, 2025, 2012, 3019, 3018, 3015, 3005, 3003, 3002, 3007, 3012, 3009, 3020, -35260, -35259, -35276, 3004,
    3008, 3001, 3014, 3006, 3013, 3017, 3010, 3022, 3021, 3011, 3016, 4009, 4002, 4004, 4001, 4006, 4005, 4010, 4003, 4011
]

thread_local = threading.local()

def get_driver():
    if not hasattr(thread_local, "driver"):
        options = webdriver.ChromeOptions()
        options.add_argument('--headless')
        options.add_argument('--no-sandbox')
        options.add_argument('--disable-dev-shm-usage')
        options.add_argument('user-agent=Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36')
        thread_local.driver = webdriver.Chrome(options=options)
    return thread_local.driver

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

def main():
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
    df.to_excel('openrice_restaurants_1.xlsx', index=False, engine='openpyxl')
    print("Data saved to openrice_restaurants_1.xlsx")

    # Close all drivers
    for thread in threading.enumerate():
        if hasattr(thread, "driver"):
            thread.driver.quit()

if __name__ == "__main__":
    main()
