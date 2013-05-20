---
layout: post
title: ! 'Selenium Webdriver 2 with Python : a simple example'
tags:
- functional
- python
- qa
- quality
- selenium
- Test
- testing
- webdriver
status: publish
type: post
published: true
meta:
  _publicize_pending: '1'
  jabber_published: '1355760633'
  _edit_last: '9835614'
  original_post_id: '13'
  _wp_old_slug: '13'
---

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
    browser.get(&quot;http://www.google.com&quot;)

    #verify title using python internal assert
    assert &quot;Google&quot; in browser.title

    #type cheese in the search area then submit
    elem = browser.find_element_by_name(&quot;q&quot;)
    elem.send_keys(&quot;cheese&quot;)
    elem.submit()

    #wait until we get the search results
    #see http://seleniumhq.org/docs/04_webdriver_advanced.html for details
    wait.until(lambda d : d.title.lower().startswith(&quot;cheese&quot;))

    assert &quot;cheese&quot; in browser.title

    #goto to translate tools
    browser.find_element_by_link_text(&quot;Plus&quot;).click()

    #wait until the menu is displayed (french)
    #ExpectedConditions.elementsToBeClickable is not available in python
    #instead we can use is_displayed()
    #see http://selenium-python.readthedocs.org/en/latest/api.html
    wait.until(lambda d : d.find_element_by_link_text(&quot;Traduction&quot;).is_displayed())

    #translate from english to french
    browser.find_element_by_link_text(&quot;Traduction&quot;).click()
    assert &quot;fromage&quot; in browser.find_element_by_id(&quot;result_box&quot;).text
