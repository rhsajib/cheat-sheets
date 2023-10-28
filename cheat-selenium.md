```python
pip install selenium==4.9.0
```

### Undetected Chrome drive
```python
pip install undetected-chromedriver==3.5.2
```

```python
import undetected_chromedriver as uc


# Set ChromeOptions 
options = uc.ChromeOptions()
options.add_argument("start-maximized")  # maximize window
options.add_argument("--disable-popup-blocking")   # disable blocking to open a new blank tab
driver = uc.Chrome(options=options)
```

### Selenium webdriver

```python
import selenium
from selenium import webdriver
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
from selenium.common.exceptions import NoSuchElementException, NoSuchWindowException


# Set up path for existing chrome profile in my machine
# this profile ca store whatsapp login data

### for windows OS
# CHROME_PROFILE_PATH = 'user-data-dir=C:/Users/<PC_name>/AppData/Local/Google/Chrome/User Data/Default/'

### for mac OS
CHROME_PROFILE_PATH = 'user-data-dir=Users/sajib/Library/Application Support/Google/Chrome/Default/'  

# set up the Chrome options
options = webdriver.ChromeOptions()
options.add_argument("start-maximized")
options.add_argument(CHROME_PROFILE_PATH)

# downloaded chrome driver is located in chromedriver folder
chrome_driver_path = '/usr/local/bin/chromedriver'

# set up the Chrome driver
driver = webdriver.Chrome(executable_path = chrome_driver_path, options=options) 
```



```python 
# open the webpage in chrome driver
driver.get('https://www.google.com/')

driver.implicitly_wait(10)
wait = WebDriverWait(driver, 30)

# selecting the current window as main tab
main_tab = driver.current_window_handle

# open a blank new tab
# set options.add_argument("--disable-popup-blocking") 
# for allowing chrome to open a blank new tab
driver.execute_script("window.open(''),'_blannk';")

# switch to the new tab
driver.switch_to.window(driver.window_handles[-1])

new_tab_url = 'https://www.youtube.com/'
driver.get(url)                
time.sleep(3)


# closing current tab
driver.close()

# Switch back to the original tab (main tab)
driver.switch_to.window(main_tab)
# or
driver.switch_to.window(driver.window_handles[0])

driver.quit()

# driver.quit() will close the entire browser instance, 
# driver.close() will close the current tab without closing the browser.
```


### Whatsapp message
- undetected chromedriver can not store whatsapp login data after QR scan login
- but selenium webdiver can store login data as it uses `CHROME_PROFILE_PATH`

```python
# message.py
from selenium.webdriver.common.keys import Keys

# command for line break
L_BREAK = (Keys.SHIFT)+(Keys.ENTER)+(Keys.SHIFT)   

def seller_message(url):
    message =   'Hi, I am interested by your car posted at this ' + url + L_BREAK + L_BREAK + \
                'Could you please send me your location? '\
                'I also need the VIN number. When can I come to try it out?' + L_BREAK + L_BREAK + \
                'Thank you and have a good day!'
    return message
```

```python
# whatsapp.py

import time
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.common.by import By
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.support.ui import WebDriverWait
from selenium.common.exceptions import NoSuchElementException

def validate_phone(phn):
    # UAE phone number = country_code + last 9 digits.
    # for example +971 123456789
    # for BD +880 1234567890

    p_number = '+880' + phn[-10:] 
    return p_number
    

def create_whats_app_url(phone):
    url = 'https://web.whatsapp.com/send?phone=' + phone + '&text=&app_absent=1' 
    return url
    
    
def send_whatsapp_messages(phone, message, driver=None, status=False):
    """
    : whatsapp web will require scan option for login
    : for auto login we have to use cookies and data of our chrome browser
    : this can be done using ChromeOptions (it will load data from our previous login to chrome browser)
    """ 

    # open a blank new tab
    driver.execute_script("window.open(''),'_blannk';")

    # switch to the new tab
    driver.switch_to.window(driver.window_handles[-1])

    valid_phone = validate_phone(phone)
    whats_app_url = create_whats_app_url(valid_phone)
    
    driver.get(whats_app_url)                

    driver.implicitly_wait(20)
    wait = WebDriverWait(driver, 60)
   
    try:
         # use message box to send message
        message_box_path = '//*[@id="main"]/footer/div[1]/div/span[2]/div/div[2]/div[1]/div/div[1]/p'  # message box xpath

        driver.find_element(By.XPATH, message_box_path)
        # if there is no such element it will raise NoSuchElementException  

        message_box = wait.until(EC.presence_of_element_located((By.XPATH, message_box_path)))           
    
        # write message in message box and send it
        message_box.send_keys(message + Keys.ENTER)
        time.sleep(3)

        # hashing status for sending message
        status = True
        
        
        ### use search box to find saved contact or input old number
        search_box_path = '//*[@id="side"]/div[1]/div/div/div[2]/div/div[1]'
        # wait until the search path loaded
        search_box = wait.until(EC.presence_of_element_located((By.XPATH, search_box_path)))

        # enter phone number / contact name in search box 
        search_box.send_keys(phone_number + Keys.ENTER)
        time.sleep(2)

        # wait until the message path loaded
        message_box = wait.until(EC.presence_of_element_located((By.XPATH, message_box_path)))
                
        br = (Keys.SHIFT)+(Keys.ENTER)+(Keys.SHIFT)               
        message_to_client = 'URL of the car ad: ' + new_ad['car_ad_url'] + br + \
                            'Price: ' + str(new_ad['car_price']) + ' AED'  
            
        # write message in message box
        message_box.send_keys(message_to_client + Keys.ENTER)       
        time.sleep(3)
        
    except NoSuchElementException:       
            # after proceding to this exception, it will try to find ok button
            # if ok button does not exist, it will raise another NoSuchElementException

            try:
                ok_button_xpath = '//*[@id="app"]/div/span[2]/div/span/div/div/div/div/div/div[2]/div/button'

                # find ok button
                ok_button = driver.find_element(By.XPATH, ok_button_xpath)

                # click the 'Ok' button of popup window
                ok_button.click()
                
            except NoSuchElementException:
                pass  

    # closing current tab & Switch back to the original tab (main tab)
    driver.close()
    driver.switch_to.window(driver.window_handles[0])

    return status
    
    
seller_status = send_whatsapp_messages(phone, message, driver=driver)
data['message_status'] = seller_status
```















