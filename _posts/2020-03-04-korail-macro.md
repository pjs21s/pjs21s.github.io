---
layout: post
title: "코레일 일반 홈페이지 예매 자동화해보기 (명절 페이지 X)"
tags: Python
comments: true
---
아래 코드는 명절 특수 예매 페이지에서는 동작하지 않는 코드 입니다.

명절 특수 예매 페이지에서 사용하려면 마우스 좌표 클릭과 관련한 매크로를
만들어보시는게 좋을 것 같습니다.

공부하시는데 참고가 되면 좋겠습니다.

```python
from selenium import webdriver
import time
import requests
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support.ui import Select
from urllib.request import urlopen
from bs4 import BeautifulSoup

def accept_popup():
    alert = driver.switch_to.alert
    alert.accept()

member_num ="멤버쉽 번호"
password = "비밀번호"
depature = "출발지" # 용산
arrived = "도착지" # 광주송정
month = input("월 :")
day = input("일 :")
hour = input("시간 :")

driver = webdriver.Firefox()

# Login
driver.implicitly_wait(4)
driver.get('http://www.letskorail.com/korail/com/login.do')
time.sleep(0.5)
driver.find_element_by_name('txtMember').send_keys(member_num)
time.sleep(0.5)
driver.find_element_by_name('txtPwd').send_keys(password)
driver.find_element_by_xpath("//img[@src='/images/btn_login.gif']").click()
driver.implicitly_wait(3)

# Security Alert accept
accept_popup()

# Input Destination
driver.find_element_by_name('txtGoStart').clear()
driver.find_element_by_name('txtGoStart').send_keys(depature)
driver.find_element_by_name('txtGoEnd').clear()
driver.find_element_by_name('txtGoEnd').send_keys(arrived)

# Click Search Tickets Red button
driver.find_element_by_xpath("//img[@src='/images/btn_reserve.gif']").click()

# Check Checkbox
driver.find_element_by_id("chkNotSee").click()

# Input Date
selectMonth = Select(driver.find_element_by_name('selGoMonth'))
selectMonth.select_by_value(month)
selectDay = Select(driver.find_element_by_name('selGoDay'))
selectDay.select_by_value(day)
selectHour = Select(driver.find_element_by_name('selGoHour'))
selectHour.select_by_value(hour)

# Click Search Tickets Blue button
driver.find_element_by_xpath("//img[@src='/images/btn_inq_tick.gif']").click()

# Ready Crawling & Print Train lists
trains_list = driver.find_elements_by_tag_name("tr")
results = []
for train_list in trains_list:
    result = train_list.text
    results.append(result)

for index in range(len(results)-2): # 쓸데없는 tr 자르기
    print("번호 {0}".format(index))
    print(results[index+1]) # 쓸데없는 tr 자르기

# Click Reserve button
reserveIndex = input("예매를 희망하는 열차의 번호를 입력하세요 :")
driver.find_element_by_name("btnRsv1_{0}".format(reserveIndex)).click()

# Alert accept_2
accept_popup()
driver.implicitly_wait(1)
accept_popup()

# Click to Pay
driver.find_element_by_id("btn_next").click()

# Input card info
driver.find_element_by_name('txtCardNo1').send_keys('카드 첫번째 번호')
driver.find_element_by_name('txtCardNo2').send_keys('카드 두번째 번호')
driver.find_element_by_name('txtCardNo3').send_keys('카드 세번째 번호')
driver.find_element_by_name('txtCardNo4').send_keys('카드 마지막 번호')

selectCardMonth = Select(driver.find_element_by_id('month'))
selectCardMonth.select_by_value('카드 월 번호')
selectCardYear = Select(driver.find_element_by_id('year'))
selectCardYear.select_by_value('카드 년 번호')

driver.find_element_by_name('txtCCardPwd_1').send_keys('카드 비밀번호 앞 두자리')
driver.find_element_by_name('txtJuminNo2_1').send_keys('주민번호 앞 6자리')

# finally to check
driver.find_element_by_id("chkAgree").click()

# finally TO PAY
#driver.find_element_by_id("fnIssuing").click()

```