---
title: "selenium 사용법"
category:
  - python
tag:
  - python
  - requests
  - BeautifulSoup
toc: true
toc_sticky: true
---

참고 사이트 : https://www.selenium.dev/documentation/en/

# 시작

```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys

driver = webdriver.Chrome('C:/chromedriver')
#driver = webdriver.PhantomJS('C:/phantomjs-2.1.1-macosx/bin/phantomjs')

# 암묵적으로 웹 자원 로드를 위해 3초까지 기다려 준다.
#driver.implicitly_wait(3)

# url에 접근한다.
driver.get('https://byeonggwonchae.github.io/')
```

# 요소 검색

페이지의 단일 element에 접근하기

```python
find_element_by_name('HTML_name')
find_element_by_id('HTML_id')
find_element_by_xpath('/html/body/some/xpath')
```

페이지의 여러 elements에 접근하기

```python
find_element_by_css_selector('#css > div.selector')
find_element_by_class_name('some_class_name')
find_element_by_tag_name('h1')
```

BeautifulSoup를 사용해서 접근하기

```python
from bs4 import BeautifulSoup as bs
soup = bs(driver.page_source,'html.parser')
```

# 액션

- input에 텍스트 입력

```python
driver.find_element_by_name("name").send_keys("Charles")
```

- input에서 텍스트 삭제

```python
driver.find_element_by_name("name").send_keys("Charles").clear()
```

- 클릭

```python
driver.find_element_by_css_selector("input[type='submit']").click()
```

- 키보드 반응 keyDown() & keyUp()

```python
# 텍스트 입력 및 엔터키 누르기
driver.find_element(By.NAME, "q").send_keys("webdriver" + Keys.ENTER)

# ctrl + A 
webdriver.ActionChains(driver).key_down(Keys.CONTROL).send_keys("a").perform()
```

- 드래그&드롭

```python
source = driver.find_element_by_id("source")
target = driver.find_element_by_id("target")
webdriver.ActionChains(driver).drag_and_drop(source, target).perform()
```

# 브라우저 조작

```python
driver.get("https://selenium.dev")  # 이동

driver.current_url  # 현재 URL 확인
driver.title  # 현재 페이지 제목 확인

driver.back()  # 브라우저의 'back' 버튼 클릭
driver.forward()  # 브라우저의 'forward' 버튼 클릭

driver.refresh()  # 현재 페이지 새로고침
```

# 윈도우&탭 생성/이동/종료

```python
driver.current_window_handle  # 현재 윈도우 핸들 가져오기
driver.window_handles         # 모든 윈도우 핸들 리스트 가져오기

for window_handle in driver.window_handles:
    if window_handle != original_window:
        driver.switch_to.window(window_handle)  # 현재 윈도우 이동
        break

driver.switch_to.new_window('tab')     # 새로운 탭 생성
driver.switch_to.new_window('window')  # 새로운 윈도우 생성

driver.close()  # 탭or윈도우 종료

driver.quit()  # 브라우저 종료 (모든게 종료된다)
```

# 윈도우 관리

```python
# 윈도우 크기 확인
size = driver.get_window_size()
width1 = size.get("width")
height1 = size.get("height")

# 윈도우 크기 설정
driver.manage().window().setSize(new Dimension(1024, 768));

# 윈도우 위치 확인
Point position = driver.manage().window().getPosition();
int x1 = position.getX();
int y1 = position.getY();

# 윈도우 위치 설정
driver.manage().window().setPosition(new Point(0, 0));

# 윈도우 최대화/최소화/전체화면
driver.manage().window().maximize();
driver.manage().window().minimize();
driver.manage().window().fullscreen();
```
