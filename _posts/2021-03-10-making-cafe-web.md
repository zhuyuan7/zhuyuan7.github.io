---
title:  "(2) 서울 카페 추천 웹페이지 만들기 - 카카오맵 카페 정보 크롤링"
excerpt: "카카오맵 이용 카페 정보 크롤링"
permalink: "/blog/making-cafe-web-02/"
author_profile: true
toc: true
toc_sticky: true
# toc_label: "페이지 주요 목차"  --toc의 기본값은 "on this page"

categories:
  - 카페 추천
  - 크롤링
tags:
  - 카페 
  - 맞춤
  - 추천
  - 웹사이트
  - 카카오맵
  - 크롤링
  - 정보
#{{ page.last_modified_at | date: '%Y:%B:%A:%d:%S:%R' }}
---
<br>

**2020-10-21**부터 **2020-12-28**까지 진행했던 **[서울시 25개구 카페추천 웹페이지 만들기](https://zhuyuan7.github.io/project/)** 팀프로젝트 개발에 대한 포스팅이다.

이 글에서는 카카오맵 상의 카페정보 크롤링 과정을 소개하였다.
<br>
<br>
## 1. 카카오맵의 카페정보 수집
### 1.1. 왜 카카오맵을 사용하여 카페 정보를 수집했는 가?

한국인이 사용하는 대표적인 포털 사이트는 크게 네이버, 다음, 구글을 꼽을 수 있다.

각 포털 사이트의 지도 서비스가 제공하는 카페 정보의 양이 각기 다르기 때문에, 
"제공하는 **정보의 양**"을 카페정보 수집  대상사이트의 선정 요건으로 정했다.  
아무래도 데이터가 많아야 더많은 정보를 제공할 수 있기 때문에 많으면 많을 수록 좋다는 
생각으로 "정보의 양"이 카페정보 수집단계의 관건이라고 생각했다. 

카페 정보 웹크롤링을 하기 위해, Selenium 프레임워크의 webdriver를 사용할거기 때문에 크롬 환경에서
네이버 지도, 카카오맵, 구글맵에서 제공하는 정보의 양을 조사하였다.
<br>

> **Selenium**은 WebDriver의 사용을 통해 대부분의 모든 브라우저의 자동화를 지원합니다.  
**WebDriver**는 웹 브라우저의 동작을 제어하기 위한 언어 중립 인터페이스를 정의하는 API와 프로토콜 입니다.  
출처: [selenium.dev] (https://www.selenium.dev/documentation/ko/getting_started_with_webdriver/)
<br>

강남구에 카페가 유동인구가 제일 많기 때문에 "강남구 카페" 검색어로 검색한 결과 다음과 같은 결과를 도출했다.
<br>

![포털지도선정](https://zhuyuan7.github.io/assets/images/포털지도선정.jpg "포털지도선정"){: .align-center}
<br>

먼저, 카페의 정보를 수집하기 위해 카카오맵 API를 사용 시도를 해보았다. 하지만
정보제공 횟수의 제한이 있어 25개 구의 모든 카페들을 





```python
# 25개구 for문으로 돌려서 카페 정보 크롤링하기

import os
from time import sleep
import time
import re
from bs4 import BeautifulSoup
from selenium import webdriver
from selenium.common.exceptions import ElementNotInteractableException
from selenium.common.exceptions import NoSuchElementException
from selenium.webdriver.common.keys import Keys
from bs4 import BeautifulSoup

##########################################################################
##################### variable related selenium ##########################
##########################################################################
# 서울 특별시 구 리스트
gu_list = ['마포구','서대문구','은평구','종로구','중구','용산구','성동구','광진구',
           '동대문구','성북구','강북구','도봉구','노원구','중랑구','강동구','송파구',
           '강남구','서초구','관악구','동작구','영등포구','금천구','구로구','양천구','강서구']


# csv 파일에 헤더 만들어 주기
for index, gu_name in enumerate(gu_list):
    fileName = 'test.csv' # index.__str__() + '_' + gu_name + '.'+'csv'
    file = open(fileName, 'w', encoding='utf-8')
    file.write("카페명" + "|" + "주소" + "|" + "영업시간" + "|" + "전화번호" + "|" + "대표사진주소" + "\n")
    file.close()                                    # 처음에 csv파일에 칼럼명 만들어주기
    options = webdriver.ChromeOptions()

    # options.add_argument('headless')
    options.add_argument("Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.198 Safari/537.36   ")
    options.add_argument('lang=ko_KR')
    chromedriver_path = "D:\workspace\pythonProject2\chromedriver.exe"
    driver = webdriver.Chrome(os.path.join(os.getcwd(), chromedriver_path), options=options)  # chromedriver 열기
    driver.get('https://map.kakao.com/')  # 주소 가져오기
    search_area = driver.find_element_by_xpath('//*[@id="search.keyword.query"]') # 검색 창
    search_area.send_keys(gu_name + ' 카페')  # 검색어 입력
    driver.find_element_by_xpath('//*[@id="search.keyword.submit"]').send_keys(Keys.ENTER)  # Enter로 검색
    driver.implicitly_wait(3) # 기다려 주자
    more_page = driver.find_element_by_id("info.search.place.more")
    # more_page.click()
    more_page.send_keys(Keys.ENTER) # 더보기 누르고
    # 첫 번째 검색 페이지 끝
    # driver.implicitly_wait(5) # 기다려 주자
    time.sleep(1)

    # next 사용 가능?
    next_btn = driver.find_element_by_id("info.search.page.next")
    has_next = "disabled" not in next_btn.get_attribute("class").split(" ")
    Page = 1
    while has_next: # 다음 페이지가 있으면 loop
    # for i in range(2, 6): # 2, 3, 4, 5
        file = open(fileName, 'a', encoding='utf-8')
        time.sleep(1)
        # place_lists = driver.find_elements_by_css_selector('#info\.search\.place\.list > li:nth-child(1)')
        # 페이지 루프
        #info\.search\.page\.no1 ~ .no5
        page_links = driver.find_elements_by_css_selector("#info\.search\.page a")
        pages = [link for link in page_links if "HIDDEN" not in link.get_attribute("class").split(" ")]
        # print(len(pages), "개의 페이지 있음")
        # pages를 하나씩 클릭하면서
        for i in range(1, 6):
            xPath = '//*[@id="info.search.page.no' + str(i) + '"]'
            try:
                page = driver.find_element_by_xpath(xPath)
                page.send_keys(Keys.ENTER)
            except ElementNotInteractableException:
                print('End of Page')
                break;
            sleep(3)
            place_lists = driver.find_elements_by_css_selector('#info\.search\.place\.list > li')
            for p in place_lists: # WebElement
                # print(p.get_attribute('innerHTML'))
                # print("type of p:", type(p))
                store_html = p.get_attribute('innerHTML')
                store_info = BeautifulSoup(store_html, "html.parser")
                # BS -> 분석
                #
                place_name = store_info.select('.head_item > .tit_name > .link_name')
                # place_address = store_info.select('.info_item > .addr > p')
                # place_hour = store_info.select('.info_item > .openhour > p > a')
                # place_tel = store_info.select('.info_item > .contact > span')
                 # print("length:", len(place_name))
                if len(place_name) == 0:
                    continue # 광고
                place_name = store_info.select('.head_item > .tit_name > .link_name')[0].text
                place_address = store_info.select('.info_item > .addr > p')[0].text
                place_hour = store_info.select('.info_item > .openhour > p > a')[0].text
                place_tel = store_info.select('.info_item > .contact > span')[0].text


                # 사진url 수집
                detail = p.find_element_by_css_selector('div.info_item > div.contact > a.moreview')
                detail.send_keys(Keys.ENTER)

                driver.switch_to.window(driver.window_handles[-1])

                place_photo = ""
                try:
                    photo = driver.find_element_by_css_selector('span.bg_present')
                    photo_url = photo.get_attribute('style')
                    m = re.search('"(.+?)"', photo_url)
                    if m:
                        place_photo = m.group(1)
                    else:
                        place_photo = ""
                except:
                    place_photo = ""
                driver.close()
                driver.switch_to.window(driver.window_handles[0])
                print(place_name, place_photo)

                file.write(place_name + "|" + place_address + "|" + place_hour + "|" + place_tel + "|" + place_photo + "\n")
            print(i, ' of', ' [ ' , Page, ' ] ')
        next_btn = driver.find_element_by_id("info.search.page.next")
        has_next = "disabled" not in next_btn.get_attribute("class").split(" ")
        if not has_next:
            print('Arrow is Disabled')
            driver.close()
            file.close()
            break # 다음 페이지 없으니까 종료
        else: # 다음 페이지 있으면
            Page += 1
            next_btn.send_keys(Keys.ENTER)
    print("End of Crawl")

```

