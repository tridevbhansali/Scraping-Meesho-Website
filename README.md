# Scraping Meesho Website Script
from selenium import webdriver
from selenium.webdriver.edge.service import Service
from selenium.webdriver.common.by import By
from selenium.webdriver.support.wait import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
import pandas as pd
import time

service = Service('C:\Program Files (x86)\msedgedriver.exe')
driver = webdriver.Edge(service=service)

product_name = []
price = []
mrp = []
discount_per = []  
material = []
pattern = []
manufacturer = []
impoter = []

for page in range(1,3):
    driver.get('https://meesho.com/sports-shoes-men/pl/4dxfc?page='+str(page))
    driver.maximize_window()
    elements = WebDriverWait(driver, 20).until(EC.visibility_of_all_elements_located((By.XPATH, "//div[@class='sc-gsnTZi Grid__Row-sc-4ki5nk-0 gfMTeL WphYx products']/div/a")))
    for element in elements:
        href = element.get_attribute('href')
        driver.execute_script("window.open('" +href +"');")
        driver.switch_to.window(driver.window_handles[1])
        try:
            product_names = WebDriverWait(driver, 10).until(EC.visibility_of_element_located((By.XPATH,"/html/body/div/div[3]/div/div[2]/div[1]/span")))
        except:
            driver.close()
            driver.switch_to.window(driver.window_handles[0])
            driver.execute_script("window.open('" +href +"');")
            driver.switch_to.window(driver.window_handles[1])
            product_names = WebDriverWait(driver, 20).until(EC.visibility_of_element_located((By.XPATH,"/html/body/div/div[3]/div/div[2]/div[1]/span")))    
        prices = driver.find_element(By.TAG_NAME, 'h4')
        mrps = driver.find_element(By.XPATH, '/html/body/div/div[3]/div/div[2]/div[1]/div[1]/p')
        discount_pers = driver.find_element(By.XPATH, '/html/body/div/div[3]/div/div[2]/div[1]/div[1]/span[1]')
        try:
            materials = driver.find_element(By.XPATH, "//div/div/p[text() = 'Material']") 
        except:
            material.append(None)
        else:
            material.append(materials.text[11:])
        try:
            patterns = driver.find_element(By.XPATH, "//div/div/p[text() = 'Pattern']")
        except:
            pattern.append(None)
        else:
            pattern.append(patterns.text[10:])
        product_name.append(product_names.text)
        price.append(prices.text[1:])
        mrp.append(mrps.text[1:])
        discount_per.append(discount_pers.text[:3])
        driver.find_element(By.XPATH, '/html/body/div/div[3]/div/div[2]/div[3]/div/span').click()
        manufacturers = WebDriverWait(driver, 20).until(EC.visibility_of_element_located((By.XPATH, '//div[@class="Popupstyled__PopupContent-sc-cngcu8-1 iHHCOM"]/div/div[1]/span[2]')))
        impoters = driver.find_element(By.XPATH, '//div[@class="Popupstyled__PopupContent-sc-cngcu8-1 iHHCOM"]/div/div[3]/span[2]')
        manufacturer.append(manufacturers.text)
        impoter.append(impoters.text)
        driver.close()
        driver.switch_to.window(driver.window_handles[0])
driver.quit()

Meesho = pd.DataFrame({'Name': product_name,'Price': price, 'MRP': mrp, 'Discount_percent': discount_per, 'Material': material, 'Pattern': pattern, 
                        'Manufacturer': manufacturer, 'Impoter': impoter})
print(Meesho)






## Data Scraped from Meesho website
![image](https://github.com/tridevbhansali/Scraping-Meesho-Website/blob/main/Meesho%20Data.png)
