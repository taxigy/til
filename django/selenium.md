# Using Selenium with Django

Three steps:

- get it ready,
- write the first test,
- [scale](https://twitter.com/search?q=%22but%20will%20it%20scale%3F%22&src=typd) it across the team and different environments, like CI.

## Get the local environment ready

Consider Mac OS. If you use [Homebrew](https://brew.sh/), you need to get a couple webdrivers and Firefox:

```bash
brew update; brew install geckodriver && brew cask install firefox
```

Done.

## Write a test and run it

Django prescribes having tests located in ENTITY/tests/ folder, where ENTITY is the data schema entity, for example, users/tests/. Usually you want to cover the model and controller with unit tests, so the structure may look like

- users
  - tests
    - unit
      - \_\_init\_\_.py
      - registering_a_new_user.py
      - changing_user_information.py
    - factories.py

so you want to add a new folder, call it "e2e":

- users
  - tests
    - e2e
      - \_\_init\_\_.py
      - logging_in.py
    - unit
      - \_\_init\_\_.py
      - registering_a_new_user.py
      - changing_user_information.py
    - factories.py

In the file users/tests/e2e/logging_in.py, write something like

```python
from django.test import LiveServerTestCase
from selenium import webdriver
from selenium.webdriver.common.keys import Keys


class LoginTestCase(LiveServerTestCase):
    def setUp(self):
        self.selenium = webdriver.Firefox()
        super(AccountTestCase, self).setUp()

    def tearDown(self):
        self.selenium.quit()
        super(AccountTestCase, self).tearDown()

    def test_happy_path_login(self):
        selenium = self.selenium
        selenium.get('http://127.0.0.1:8000/login/') # <- replace with the actual URL
        username = selenium.find_element_by_css_selector('input[name=username]') # <- replace with the actual CSS selectors
        password = selenium.find_element_by_css_selector('input[name=password]')
        submit = selenium.find_element_by_css_selector('button[type=submit]')
        username.send_keys('123@abc.xyz')
        password.send_keys('password1')
        submit.send_keys(Keys.RETURN)
        assert 'sign out' in selenium.page_source # <- assuming that only authenticated users ever see "sign out" anywhere on the page
```

Tweak it so that it matches the DOM configuration and the URL structure that you have.

Given that the codebase you are working with is most likely already covered with some tests, you want to run a specific test in order to see if it's working. It will save you some time until it's proven that the test runs properly, too. So, do

```bash
python manage.py test users.tests.e2e.logging_in.LoginTestCase.test_happy_path_login
```

The last argument is a composition of file system path, and class and class method within the file, so be careful there because Django does show an error message, and it's clear, but it does not provide hints on that.

## Make it work on any machine

TBD.
