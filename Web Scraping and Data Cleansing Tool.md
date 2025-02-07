# Web Scraping and Data Cleansing Tool
This project is a Python-based web scraping and data cleansing tool designed to extract and clean data from licensed liquor premises and suppliers in Hong Kong.

---
## 📌 Code Installation  
Before running the script, install the required dependencies:
pip install requests beautifulsoup4 selenium pandas openpyxl
## 🚀 Features
✅ Automated data extraction using Python, BeautifulSoup, and Selenium.
✅ Data cleaning and preprocessing using Pandas.
✅ Handles over 50+ cuisine types & multiple district IDs for comprehensive data collection.
✅ Multi-threaded execution to optimize web scraping performance.
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
