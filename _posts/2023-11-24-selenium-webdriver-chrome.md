---
layout : post
title : "Selenium WebDriver uses to control Chrome"
categories : [selenium]
published : true
---

### Installing Google Chrome Using the Official PPA

**Add the GPG Key of the Repository**
```shell
$ wget -q -O - https://dl.google.com/linux/linux_signing_key.pub | sudo gpg --dearmour -o /usr/share/keyrings/chrome-keyring.gpg 
```

**Adding the Chrome PPA**
```shell
$ sudo sh -c 'echo "deb [arch=amd64 signed-by=/usr/share/keyrings/chrome-keyring.gpg] http://dl.google.com/linux/chrome/deb/ stable main" > /etc/apt/sources.list.d/google.list' 
```

**Update Package Lists and Install**
```shell
$ sudo apt update 

$ sudo apt install google-chrome-stable 
```

### Install the [`webdriver-manager`](https://pypi.org/project/webdriver-manager/) module

```shell
$ pip3 install webdriver-manager
```
To solve the Selenium error **"WebDriverException: Message: 'chromedriver' executable needs to be in PATH"**

```python
from selenium.webdriver.chrome.service import Service as ChromeService
from webdriver_manager.chrome import ChromeDriverManager

driver = webdriver.Chrome(service=ChromeService(ChromeDriverManager().install()), chrome_options=chromeOptions)
```

### Reference
* [Installing Google Chrome Using the Official PPA](https://tecadmin.net/how-to-install-google-chrome-on-ubuntu-22-04/)

* ['chromedriver' executable needs to be in PATH](https://bobbyhadz.com/blog/python-message-chromedriver-executable-needs-to-be-in-path)