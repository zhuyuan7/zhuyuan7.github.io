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
  - Selenium
  - REST API
#{{ page.last_modified_at | date: '%Y:%B:%A:%d:%S:%R' }}
---
<br>

**2020-10-21**부터 **2020-12-28**까지 진행했던 **[서울시 25개구 카페추천 웹페이지 만들기](https://zhuyuan7.github.io/project/)** 팀프로젝트 개발에 대한 포스팅이다.

이 글에서는 카카오맵 상의 카페정보 크롤링 과정을 소개하였다.
<br>

-----

<br>

## 1. 카페정보 수집대상 포털사이트 선정
### 1.1. 왜 카카오맵을 사용하여 카페 정보를 수집했는 가?
한국인이 사용하는 대표적인 포털 사이트는 크게 네이버, 다음, 구글을 꼽을 수 있다.

카페정보 수집에 들어가기에 앞서, 수집대상 포털사이트 선정요건으로 **"정보의 양"**에 무게를 두었다.
이는 수집한 데이터가 많으면 많을수록 "카페추천 웹페이지" 이용자에게 더 많은 정보를 제공할 수 있을 뿐만 아니라,
전처리 과정에서 불명확한 정보들을 제외시키는 경우를 대비하기 위함이다. 따라서 이 프로젝트의 관건은 "정보의 양"이라는 점을 강조하고 싶다.

각 포털 사이트의 지도 서비스가 제공하는 카페 정보의 양이 각기 다르기 때문에, 각 포털 사이트의 지도 서비스에서
특정 검색어를 검색하여 "정보의 양"을 비교해보기로 했다.대표적으로 유동인구가 많은 강남구의 카페를 예시로 검색어를 설정(검색어: **강남구 카페**)하여 다음과 같은 결과를 도출했다.
<br>

![포털지도선정](https://zhuyuan7.github.io/assets/images/포털지도선정.jpg "포털지도선정"){: .align-center}
<center> <그림 1> 네이버, 카카오맵, 구글의 검색 결과 </center>
<br>
<br>
`Selenium` 프레임워크의 `WebDriver`를 이용하여 카페 정보 웹크롤링을 진행하기 위해 크롬 환경에서
네이버 지도, 카카오맵, 구글맵이 제공하는 정보의 양을 조사하였다.
<br>

> **Selenium** 이란?  
**Selenium**은  웹브라우저를 자동화할 수 있게 돕는 일련의 도구 모음인 엄브렐라 프로젝트(umbrella project) 중 일부이며,
WebDriver의 사용을 통해 대부분의 모든 브라우저의 자동화를 지원합니다.  
**WebDriver**는 웹 브라우저의 동작을 제어하기 위한 언어 중립 인터페이스를 정의하는 API와 프로토콜 입니다.  
* 출처: [selenium.dev](https://www.selenium.dev/documentation/ko/getting_started_with_webdriver/)
<br>

네이버, 카카오맵, 구글 중 카카오맵의 정보제공 양이 **총 510개**로 압도적으로 많은 양의 정보를 제공하기 때문에
카카오맵 상에서 카페정보를 수집하기로 최종결정하였다.

<표 1> 네이버, 카카오맵, 구글의 정보 제공 수 

|  |  1 | 2 | 3 |
|:-:|:-:|:-:|:-:|
| 포털 사이트 | 네이버 | 카카오맵 | 구글 |
| 총 페이지 수 | 6 페이지 | 34 페이지 | 15 페이지 |
| 한 페이지 당 결과물 수 | 50 개 | 15 개 | 20 개 |
| 총 정보 제공 수 | 300 개 | **510** 개 | 300 개 |

<br>
<br>
본래 계획은 **[Kakao Developers> REST API](https://developers.kakao.com/tool/rest-api/open/get/v2-local-search-keyword.%7Bformat%7D)**를 이용해 카페의 정보를 수집하기로 하였으나, 서비스 사용에 대한 한도제한의 문제가 발생하였다.

![카카오제한](https://zhuyuan7.github.io/assets/images/카카오제한.jpg "카카오제한"){: .align-center}
<center> <그림 2> 카카오 API 쿼터 및 제한 </center> 
출처: [kakao developers 약관](https://developers.kakao.com/terms/latest/ko/site-policies)
<br>
<br>
카카오 REST API를 사용하여 정보수집을 시도해 보았지만, 최대 **45 page x 15 size**의 정보만을 제공하므로 정보제공 개수의 제한이 있어 방대한 양의 정보를 수집하기엔 적합하지 않다고
판단하였다. 

![카카오제한](https://zhuyuan7.github.io/assets/images/수정카카오제한그림.jpg "카카오제한"){: .align-center}
<center> <그림 3> 카카오 API 쿼터 및 제한 </center>
<br>

API 사용제한 문제을 해결하는 방법으로 직접 코드를 작성하여 크롤링을 진행하였다.
<br>
<br>


----


## 2. 카카오맵 상의 카페정보 수집

`Beautiful Soup`을 이용하여 카카오맵에서 서울시 25개 구(区）카페의 정보를 수집하였다.
 
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
```
<br>
>**Beautiful Soup이란?**  
<br>
**Beautiful Soup**은 HTML 및 XML 파일에서 데이터를 가져 오기위한 Python 라이브러리입니다. 
자주 사용하는 파서와 함께 작동하여 구문 분석 트리를 탐색, 검색 및 수정하는 관용적 인 방법을 제공합니다. 일반적으로 프로그래머의 
작업 시간 또는 며칠을 절약합니다.
* 출처:[Beautiful Soup 4.9.0 documentation](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)

<br>
### 2.1 서울 특별시 구 리스트 

먼저  크롤링할 25개 구 카페의 검색어를  **"구 이름"+"카페"**<sup>(강남구카페)</sup> 으로 설정하였고, 
for문을 사용하여 각 구의 카페 정보를 수집하기 위해서, 서울시 25개 구(gu_name）를 
리스트(gu_list) 형태로 만들었다.

```python
gu_list = ['마포구','서대문구','은평구','종로구','중구','용산구','성동구','광진구',
           '동대문구','성북구','강북구','도봉구','노원구','중랑구','강동구','송파구',
           '강남구','서초구','관악구','동작구','영등포구','금천구','구로구','양천구',
           '강서구']

```



### 2.2 "카페명" + "주소" + "영업시간" + "전화번호" + "대표사진주소" 수집

코드를 작성해 확보한 서울시 25개 구 카페의 "카페명" + "주소" + "영업시간" + "전화번호" + "대표사진주소" 에 대한
총 12,500개의 데이터를 확보했다. 
<표2>를 보면 알 수 있듯이, 각 구(gu_name)마다 500개의 데이터를 확보하였지만, 
전처리 과정에서 도봉구(424개) 데이터 파일 안에 "주소"가 "노원구"인 76개 데이터의 존재를 발견하여
해당 데이터들은 "노원구" 파일 안에 옮겨놓았다. 
<br>

<표 2> 서울시 25개 구 카페정보 데이터 개수

| 구(식별자) 	| 데이터 수 	|  구(식별자)  	| 데이터 수 	|   구(식별자)   	| 데이터 수 	|
|:----------:	|:---------:	|:------------:	|:---------:	|:--------------:	|:---------:	|
| 강남구(GN) 	|    500    	|  도봉구(DB)  	|  **424**  	|   양천구(YC)   	|    500    	|
| 강동구(GD) 	|    500    	| 동대문구(DD) 	|    500    	|  영등포구(YD)  	|    500    	|
| 강북구(GB) 	|    500    	|  동작구(DJ)  	|    500    	|   용산구(YS)   	|    500    	|
| 강서구(GS) 	|    500    	|  마포구(MP)  	|    500    	|   은평구(EP)   	|    500    	|
| 관악구(GA) 	|    500    	| 서대문구(SD) 	|    500    	|   종로구(JR)   	|    500    	|
| 광진구(GJ) 	|    500    	|  서초구(SC)  	|    500    	|    중구(JG)    	|    500    	|
| 구로구(GR) 	|    500    	|  성동구(SD)  	|    500    	|   중랑구(JL)   	|    500    	|
| 금천구(GC) 	|    500    	|  성북구(SB)  	|    500    	|                	|           	|
| 노원구(NW) 	|  **576**  	|  송파구(SP)  	|    500    	| 총 데이터 합계 	|   12500   	|


이리하여 "카페 추천 웹페이지"프로젝트에 사용할 카페정보 데이터 총 12,500개의 데이터를 확보하였다. 
<br>


### 2.3 고유 식별자 부여

확보한 "25개 구 카페 정보 데이터"들이 다음 단계인 각 카페에 대한 댓글 데이터 수집 과정에서
이 댓글이 어느 카페의 댓글인지 구별할 수 있도록 미리 각 카페에 대해 "구"와 "ID"를 부여하여 
고유 식별자를 설정하였다.
<br>

![카페정보데이터](https://zhuyuan7.github.io/assets/images/카페정보데이터.jpg "카페정보데이터"){: .align-center}
<center> <그림 4> 서울시 25개 구 카페정보 수집 결과물 </center>

<br>


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

