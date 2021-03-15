---
title:  "(3) 서울 카페 추천 웹사이트 만들기 - 구글맵 카페 댓글 크롤링"
excerpt: "구글맵 이용 카페 댓글 크롤링"
permalink: "/blog/making-cafe-web-03/"
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
  - 구글맵
  - 크롤링
  - 정보
#{{ page.last_modified_at | date: '%Y:%B:%A:%d:%S:%R' }}
---
<br>
**2020-10-21**부터 **2020-12-28**까지 진행했던 **[서울시 25개구 카페추천 웹페이지 만들기](https://zhuyuan7.github.io/project/)** 팀프로젝트 개발에 대한 포스팅이다.
이 글에서는 구글맵 상의 카페 이용후기 데이터 크롤링 과정 을 소개한다.
<br>
-----
<br>
## 1. 카페 이용 후기 수집대상 포털사이트 선정
카카오맵에서 확보한 12,000건에 대한 카페정보 데이터를 활용해 
구글맵에서 카페 이용 후기들을 수집하였다.
### 1.1. 왜 카카오맵이 아닌 구글맵에서 이용후기를 수집했는가?
카카오맵에서 카페정보를 수집하고 수집된 카페들의 후기를 수집해보았는 데,
후기 데이터의 양도 적을 뿐더러, 댓글의 내용이 너무 짧고 구체적이지 않아 
카페 특색을 추출하기엔 부적합하다고 판단하였다. 
또한 네이버 지도는 데이터의 양은 많지만, 카카오맵과 같은 이유로 제외시켰다.
반면에 구글맵은 데이터의 양도 많고, 댓글의 내용도 구체적이라
댓글을 통해 카페 특색을 보다 정확하게 추출할 수 있어서 구글맵에서 
카페 후기 정보를 수집하기로 최종 결정하였다.
<br>
![카페후기](https://zhuyuan7.github.io/assets/images/카페후기.jpg "카페후기"){: .align-center}
<center> <그림 1> 구글맵, 카카오맵, 네이버 지도 이용 후기 </center>
<br>
<br>
### 1. 2. 구글맵 API 사용제한
앞서 살펴본 [Kakao REST API](https://developers.kakao.com/tool/rest-api/open/get/v2-local-search-keyword.%7Bformat%7D) 사용시 사용제한이 있는 것과 같이, 
[Google Maps Platform](https://developers.google.com/maps/faq#usage_apis) 에서도 
API 사용 제한이 있어 보다 원활한 정보 수집을 위해 직접 코드를 작성하여 웹크롤링을 진행하였다.
![구글제한](https://zhuyuan7.github.io/assets/images/구글제한.jpg "구글제한"){: .align-center}
<center> <그림 2> 구글맵 API 사용제한 </center>
----
<br>
<br>
## 2. 구글맵 상의 카페 이용 후기 수집
### 2.1. 서울시 25개 구 카페 이용 후기 데이터 수집
카카오맵 웹크롤링을 통해 확보한 12,500개의 25개 구 카페 정보 데이터을 기반으로, 
구글맵 상의 카페 이용 후기를 수집하였다.
카카오맵과 구글맵 상의 등록된 카페정보의 불일치를 방지하기 위해, 
검색어로 **"카페이름"+"카페주소"+카페전화번호"**로 설정하여 수집을 진행하였다.
작성한 코드는 다음과 같다.
```python
def main():
    driverPath = "D:\workspace\pythonProject2\chromedriver.exe"
    driver = webdriver.Chrome(driverPath)
    # 카페 1개의 정보 구, ID, 카페명, 주소 ...
    # df = pd.read_csv(r'C:\Users\USER\Desktop\cafe_list\result_gu.csv')
    # df['검색어'] = df['카페명'] + df['주소']
    df = pd.read_csv(r'C:\Users\USER\Desktop\cafe_list\CAFE\XIN_result_gu2.csv', encoding='utf-8-sig', sep="|")
    gu = df['구']
    cafe_id = df['ID']
    cafe_name = df['카페명']
    cafe_addr = df['주소']
    cafe_tel = df['전화번호'].astype(str)
    cafe_len = len(cafe_name)
    search_name = []
    for i in range(cafe_len):
        name = cafe_name[i] + " " + cafe_addr[i] + " " + cafe_tel[i]
        search_name.append({
            "name":name,
            "gu": gu[i],
            "cafe_id": cafe_id[i]
        })
```
<br>
<br>
### 2.2. 카페 "후기"와 "별점" 수집
각 카페의 특색을 알기 위해, 카페 이용자들이 구글맵 상의 등록한 **"후기"와 "별점"**을 
다음과 같은 코드를 통해 수집하였다. 
 
```python
def get_store_review_data(driver, cafe_info):
    while True:
        try:
            time.sleep(5)
            # 더보기 = driver.find_element_by_css_selector('#pane > div > div.widget-pane-content.scrollable-y > div > div > div:nth-child(44) > div > div > button')
            더보기 = driver.find_element_by_css_selector("button[aria-label*='리뷰 더보기']")
            더보기.send_keys(Keys.ENTER)
            # 더보기 = driver.find_element_by_xpath('//*[@id="pane"]/div/div[1]/div/div/div[41]/div/div/button')
            # time.sleep(1)
            # 더보기.click()
        except Exception as e:
            print(e)
            break
    # last_height = driver.execute_script("return document.body.scrollHeight")
    for i in range(100):
        # 스크롤 : 마지막까지
        # reviews = driver.find_elements_by_css_selector('span.section-review-text')
        # for review in reviews:
        #     print(review.text)
        # print(
        #     "========================================================================================================")
        try:
            scroll = driver.find_element_by_css_selector(
            'div.section-layout.section-scrollbox.scrollable-y.scrollable-show')
            time.sleep(1)
            driver.execute_script('arguments[0].scrollTop = arguments[0].scrollHeight', scroll)
            location1 = scroll.location_once_scrolled_into_view
            print(i, location1)
        except Exception as e:
            print(e)
            break
        time.sleep(1)
    # 스크롤 끝났으니 수집
    reviews = driver.find_elements_by_css_selector('span.section-review-text')
    stars = driver.find_elements_by_css_selector('span.section-review-stars')
    result =[[cafe_info.get("gu"), cafe_info.get("cafe_id"), re.search(r'[0-9]', star.get_attribute("aria-label")).group(), review.text.replace("\n", " ")] for
              star, review in zip(stars, reviews)]
    return result
```
<br>
<br>
또한 각 이용후기 데이터마다 식별자 **"GU"+"ID"**를 부여해 
후기 데이터의 자연어 처리시 식별자만 보고도 어떤 카페의 후기 데이터인지 판단하기 쉽게 설정하였다.
```예) 카페노티드 청담 : "GN"+"ID" --> GN1 : 카페노티드 청담```
![후기식별자](https://zhuyuan7.github.io/assets/images/후기식별자.jpg "후기식별자"){: .align-center}
<center> <그림 3> 후기 데이터 식별자 </center>
<br>
<br>
### 2.3. 수집과정 중 오류발생
카페 이용 후기 데이터의 정확도를 높이기 위해, 검색어를 `df['검색어'] = df['카페명'] + df['주소'] + df['전화번호']` 
로 설정했음에도 불구하고, 카카오맵과 구글맵의 각 등록된 카페정보가 불일치하여, 
복수 결과값(한국어표기-영문표기)과 부분 일치값(도로명 주소-지번 주소)을 도출하게 되었다.
이러한 불일치 정보는 이용 후기 데이터에서 제외시켰다.
![후기오류](https://zhuyuan7.github.io/assets/images/후기오류.jpg "후기오류"){: .align-center}
<center> <그림 4> 구글맵 후기오류 </center>
<br>
<br>
### 2.4. 후기 데이터 결과물
웹크롤링을 통해, 총 473,475개의 서울시 25개 구 카페 이용후기 데이터를 확보하였다.
- 별점만 등록한 후기는 후기 칸을 빈칸으로 남겨두었음.
![후기결과물](https://zhuyuan7.github.io/assets/images/후기결과물.jpg "후기결과물"){: .align-center}
<center> <그림5> 서울시 25개 구 카페 이용후기 수집 결과물 </center>
<br>
<br>
### 3. 카페후기 수집 코드
서울특별시 25개 구의 카페 이용후기 수집 코드는 다음과 같다.
```python
import os
import re
from time import sleep
import time
from bs4 import BeautifulSoup
from selenium import webdriver
from selenium.common.exceptions import ElementNotInteractableException
from selenium.common.exceptions import NoSuchElementException
from selenium.webdriver.common.keys import Keys
from bs4 import BeautifulSoup
import pandas as pd
import numpy as np
import requests
def start_search(driver, cafe_info):
    url = "https://www.google.com/maps/"
    # driverPath = "D:\workspace\pythonProject2\chromedriver.exe"
    # driver = webdriver.Chrome(driverPath)
    driver.get(url)
    # result = requests.get(url=url) # 응답
    # search = driver.find_element_by_css_selector("input#searchboxinput.tactile-searchbox-input")
    search = driver.find_element_by_css_selector("#searchboxinput")
    time.sleep(1)
    search.clear()
    search.send_keys(cafe_info.get("name"))
    search.send_keys(Keys.ENTER)
    # driver.implicitly_wait(3)
    return get_store_review_data(driver, cafe_info)
def get_store_review_data(driver, cafe_info):
    while True:
        try:
            time.sleep(5)
            # 더보기 = driver.find_element_by_css_selector('#pane > div > div.widget-pane-content.scrollable-y > div > div > div:nth-child(44) > div > div > button')
            더보기 = driver.find_element_by_css_selector("button[aria-label*='리뷰 더보기']")
            더보기.send_keys(Keys.ENTER)
            # 더보기 = driver.find_element_by_xpath('//*[@id="pane"]/div/div[1]/div/div/div[41]/div/div/button')
            # time.sleep(1)
            # 더보기.click()
        except Exception as e:
            print(e)
            break
    # last_height = driver.execute_script("return document.body.scrollHeight")
    for i in range(100):
        # 스크롤 : 마지막까지
        # reviews = driver.find_elements_by_css_selector('span.section-review-text')
        # for review in reviews:
        #     print(review.text)
        # print(
        #     "========================================================================================================")
        try:
            scroll = driver.find_element_by_css_selector(
            'div.section-layout.section-scrollbox.scrollable-y.scrollable-show')
            time.sleep(1)
            driver.execute_script('arguments[0].scrollTop = arguments[0].scrollHeight', scroll)
            location1 = scroll.location_once_scrolled_into_view
            print(i, location1)
        except Exception as e:
            print(e)
            break
        time.sleep(1)
    # 스크롤 끝났으니 수집
    reviews = driver.find_elements_by_css_selector('span.section-review-text')
    stars = driver.find_elements_by_css_selector('span.section-review-stars')
    result =[[cafe_info.get("gu"), cafe_info.get("cafe_id"), re.search(r'[0-9]', star.get_attribute("aria-label")).group(), review.text.replace("\n", " ")] for
              star, review in zip(stars, reviews)]
    return result
    # return (stars.get_attribute("aria-label"), reviews.text)
    # print(stars.get_attribute("aria-label"), reviews.text)
def main():
    driverPath = "D:\workspace\pythonProject2\chromedriver.exe"
    driver = webdriver.Chrome(driverPath)
    # 카페 1개의 정보 구, ID, 카페명, 주소 ...
    # df = pd.read_csv(r'C:\Users\USER\Desktop\cafe_list\result_gu.csv')
    # df['검색어'] = df['카페명'] + df['주소']
    df = pd.read_csv(r'C:\Users\USER\Desktop\cafe_list\CAFE\XIN_result_gu2.csv', encoding='utf-8-sig', sep="|")
    gu = df['구']
    cafe_id = df['ID']
    cafe_name = df['카페명']
    cafe_addr = df['주소']
    cafe_tel = df['전화번호'].astype(str)
    cafe_len = len(cafe_name)
    search_name = []
    for i in range(cafe_len):
        name = cafe_name[i] + " " + cafe_addr[i] + " " + cafe_tel[i]
        search_name.append({
            "name":name,
            "gu": gu[i],
            "cafe_id": cafe_id[i]
        })
    for i in range(cafe_len):
        result = start_search(driver, search_name[i])
        # 구, ID, stars, 리뷰
        # result = get_store_review_data(driver)
        data = pd.DataFrame(result)
        # data.columns = ['stars', 'reviews']
        # data.columns = ['stars', 'reviews']
        # data = pd.concat([search_name[i].get("gu") + search_name[i].get("cafe_id"), data], axis=1)
        # 누적
        if not os.path.exists("cafe_reviews_GA_GC.csv"):
            data.to_csv('cafe_reviews.csv', index=False, sep='|', mode="w")
        else:
            data.to_csv('cafe_reviews.csv', index=False, sep='|', mode="a", header=False)
"""
1. 카페 정보 불러오기
2. 한라인씩 읽으면 -> 해당 카페의 식별정보(구, ID)
3. 수집 -> [구, ID, stars, review]
"""
if __name__ == "__main__":
    main()
```