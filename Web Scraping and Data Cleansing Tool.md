# Web Scraping and Data Cleansing Tool

This project is a Python-based web scraping and data cleansing tool designed to extract and clean data from licensed liquor premises and suppliers in Hong Kong.

---

## **📌 Code Installation**
Before running the script, install the required dependencies:

```bash
pip install requests beautifulsoup4 selenium pandas openpyxl

---
### ***📌 Main Section***
✅ Automated data extraction using Python, BeautifulSoup, and Selenium.
✅ Data cleaning and preprocessing using Pandas.
✅ Handles over 50+ cuisine types & multiple district IDs for comprehensive data collection.
✅ Multi-threaded execution to optimize web scraping performance.
✅ Data export to Excel (.xlsx) format.

## **Main Section**

### **📝 Web Scraping Code**
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

## **📝 Web Scraping Code**
# Initialize Selenium WebDriver
def get_driver():
    options = Options()
    options.add_argument('--headless')
    options.add_argument('--no-sandbox')
    options.add_argument('--disable-dev-shm-usage')
    options.add_argument('user-agent=Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36')
    return webdriver.Chrome(options=options)

cuisines = [
    "粵菜-廣東", "潮州菜", "客家菜", "川菜-四川", "滬菜-上海", "京菜-官府菜", "京川滬", "台灣菜", "順德菜",
    "滇菜-雲南", "湘菜-湖南", "桂菜-廣西", "東北菜", "鲁菜-山東", "新疆菜", "農家菜", "閩菜-福建", "江浙菜",
]

district_ids = [
    1008, -35243, -35244, -35242, 1001, -9006, 1003, -9007, 1011, 1022, 1019, 1026, 1004, 1023, 1014, 1009, 1018, 1024,
]
