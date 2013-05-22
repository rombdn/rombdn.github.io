---
layout: post
title: "Selenium Webdriver 2 with Python example"
date: 2012-12-17 12:00
comments: true
categories: 
---

```python
    from selenium import webdriver
    from selenium.common.exceptions import NoSuchElementException
    from selenium.webdriver.common.keys import Keys
    from selenium.webdriver.support.ui import WebDriverWait
    import time, re

    #browser init
    browser = webdriver.Firefox()

    #wait template : 3 seconds
    wait = WebDriverWait(browser, 3)

    #goto google
    browser.get('http://www.google.com')

    #verify title using python internal assert
    assert 'Google' in browser.title

    #type cheese in the search area then submit
    elem = browser.find_element_by_name('q')
    elem.send_keys('cheese')
    elem.submit()

    #wait until we get the search results
    #see http://seleniumhq.org/docs/04_webdriver_advanced.html for details
    wait.until(lambda d : d.title.lower().startswith('cheese'))

    assert 'cheese' in browser.title

    #goto to translate tools
    browser.find_element_by_link_text('Plus').click()

    #wait until the menu is displayed (french)
    #ExpectedConditions.elementsToBeClickable is not available in python
    #instead we can use is_displayed()
    #see http://selenium-python.readthedocs.org/en/latest/api.html
    wait.until(lambda d : d.find_element_by_link_text('Traduction').is_displayed())

    #translate from english to french
    browser.find_element_by_link_text('Traduction').click()
    assert 'fromage' in browser.find_element_by_id('result_box').text
```