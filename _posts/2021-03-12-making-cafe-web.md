---
title:  "(4) 서울 카페 추천 웹사이트 만들기 - 카페 이용후기에 대한 자연어 처리 Okt"
excerpt: "Okt 이용 카페 이용후기 빈도수 분석"
permalink: "/blog/making-cafe-web-04/"
author_profile: true
toc: true
toc_sticky: true
# toc_label: "페이지 주요 목차"  --toc의 기본값은 "on this page"

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

이 글에서는 카페 이용후기에 대한 Okt활용 자연어 처리  과정을 소개한다.
<br>

-----

<br>

자연어처리/ okt/ 

<br>

### 2.4. 후기 데이터 결과물
웹크롤링을 통해, 총 473,475개의 서울시 25개 구 카페 이용후기 데이터를 확보하였다.
- 별점만 등록한 후기는 후기 칸을 빈칸으로 남겨두었음.

![후기결과물](https://zhuyuan7.github.io/assets/images/후기결과물.jpg "후기결과물"){: .align-center}
<center> <그림5> 서울시 25개 구 카페 이용후기 수집 결과물 </center>


----

