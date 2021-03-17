---
title:  "(5) 서울 카페 추천 웹사이트 만들기 - 결과값에 대한 카페 요소 분석 및 이용 목적 테마 분류"
excerpt: "카페 댓글 빈도수 분석 데이터 기반 카페 요소 분석 및 분류"
permalink: "/blog/making-cafe-web-05/"
author_profile: true
toc: true
toc_sticky: true

categories:
  - 카페 추천
  - 자연어 처리
tags:
  - 카페 
  - 맞춤
  - 추천
  - 웹사이트
  - 자연어 처리
  - 크롤링
  - 정보
#{{ page.last_modified_at | date: '%Y:%B:%A:%d:%S:%R' }}
---
<br>
**2020-10-21**부터 **2020-12-28**까지 진행했던 **[서울시 25개구 카페추천 웹페이지 만들기](https://zhuyuan7.github.io/project/)** 팀프로젝트 개발에 대한 포스팅이다.
이 글에서는 텍스트 마이닝을 통해 추출한 최빈도수 데이터에 대한 카페 특성 요소 분석 및 이용 목적 테마 분류 과정을 소개한다.
<br>

-----

<br>

# 1. 카페 특성 요소 분석

카페의 특성을 파악하기 위해, 텍스트 마이닝을 통해 추출한
총 98,053개의 서울시 25개 구 각 카페들의 상위 20개의 최빈도수 데이터를 
분석하여 **10가지 카페 이용 목적 테마**를 나누었다.
<br>

<br>
### 1.1. 핵심어 최빈도수 중복값 데이터 합치기

먼저 총 98,053개의 서울시 25개 구 각 카페들의 상위 20개의 **최빈도수 데이터의 중복값**을 
합쳐 총 327개의 핵심어 최빈도수 데이터를 추출하였다.

![중복값](https://zhuyuan7.github.io/assets/images/중복값.jpg "중복값"){: .align-center}<center> <그림 1> 최빈도수 단어 </center> 
<br>


```python
# 구글 코랩

import pandas as pd
df = pd.read_csv(r'/content/drive/MyDrive/Colab Notebooks/cafe/all_word_count.csv', engine='python', encoding='utf-8-sig', sep=' ')
WC=df.groupby('word')['w_count'].apply(lambda grp: grp.nlargest(10000).sum()) # 중복값 합치기
# 출처: stackoverflow (https://stackoverflow.com/questions/40390634/pandas-groupby-nlargest-sum)
DSC=WC.sort_values(ascending=False)
all_sum=DSC.to_csv(r'/content/drive/MyDrive/new_sum_count.csv', encoding='utf-8-sig', sep=' ')
```
<br>


<br>
## 2. 10가지 이용목적 테마

현존하는 추천 서비스들과 차별을 두기 위해, 이용자가 많은 정보를 입력하지 않고, **이용 목적 테마**만을 
클릭하여 추천 카페 정보를 제공하는 웹페이지를 만들기 위해, 핵심어 최빈도수 데이터를 기반으로
카페 이용 목적에 따른 고객의 취향을 크게 10가지 테마로 분류하였다.


카페 특성 요소의 테마를 나누기 전에 **최빈도수 중복값** 합친 데이터에서 의미없는 단어들은 제외시키는
정제작업을 하였다.  


팀원들과 토론을 통하여 고객의 대표적인 카페 이용목적을 다음과 같이 **10가지 이용 목적 테마**로 정하였고, 

```python
1. 데이트
2. 모임
3. 모닝커피
4. 회의
5. 공부
6. 가성비
7. 키즈카페
8. 애견카페
9. 브런치카페
10. 디저트 카페
```

정제된 데이터를 분석하여 특정 테마에 속하는 **카페 특성 요소**를 해당 테마에 배정하였다.



![테마](https://zhuyuan7.github.io/assets/images/테마.jpg "테마"){: .align-center}<center> <그림 2> 10가지 카페 이용 테마 </center> 
<br>


<br>
