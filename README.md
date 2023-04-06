# job_app_automator.py
A personal project to help automate my search for a front-end dev career on Dice.com

import time
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

# Set your login credentials, job search preferences, and ChromeDriver path
email = 'your_email@example.com'
password = 'your_password'
job_title = 'junior web developer'
minimum_pay = 35
chrome_driver_path = '/path/to/chromedriver'

# Initialize the Chrome webdriver
driver = webdriver.Chrome(chrome_driver_path)

# Function to log in to your Dice.com account
def login():
    driver.get('https://www.dice.com/dashboard/login')
    time.sleep(2)

    email_input = driver.find_element_by_name('email')
    password_input = driver.find_element_by_name('password')

    email_input.send_keys(email)
    password_input.send_keys(password)
    password_input.send_keys(Keys.RETURN)

# Function to search for jobs
def search_jobs():
    WebDriverWait(driver, 10).until(EC.presence_of_element_located((By.ID, 'typeaheadInput')))
    search_input = driver.find_element_by_id('typeaheadInput')
    search_input.clear()
    search_input.send_keys(job_title)
    
    # Select the remote filter
    remote_checkbox = driver.find_element_by_id('remote-jobs-checkbox')
    if not remote_checkbox.is_selected():
        remote_checkbox.click()

    search_input.send_keys(Keys.RETURN)

# Function to apply for jobs
def apply_jobs():
    job_listings = WebDriverWait(driver, 10).until(EC.presence_of_all_elements_located((By.CSS_SELECTOR, '.card-content')))
    
    for job in job_listings:
        try:
            pay = float(job.find_element_by_css_selector('.salary').text.strip('$').split('-')[0])
            if pay >= minimum_pay:
                job.find_element_by_css_selector('.job-apply-btn').click()
                time.sleep(1)
        except:
            continue

# Log in, search for jobs, and apply
login('ramez.festek@gmail.com')
password('RfproducerPLAID007!')
search_jobs('data analyst')
apply_jobs('remote', 'data analyst', '> 30/hr')

# Close the browser window
driver.quit()
