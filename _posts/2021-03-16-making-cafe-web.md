---
title:  "(8) 서울 카페 추천 웹사이트 만들기 - 카카오맵 API이용 한 서울시 25개 구 지도 구현"
excerpt: "카카오맵 이용 카페 정보 크롤링"
permalink: "/blog/making-cafe-web-08/"
author_profile: true
toc: true
toc_sticky: true

categories:
  - 카페 추천
  - 크롤링
tags:
  - 카페 
  - 맞춤
  - 추천
  - 웹사이트
  - 카카오맵 API
  - API
  - 크롤링
  - 정보
#{{ page.last_modified_at | date: '%Y:%B:%A:%d:%S:%R' }}
---
<br>
**2020-10-21**부터 **2020-12-28**까지 진행했던 **[서울시 25개구 카페추천 웹페이지 만들기](https://zhuyuan7.github.io/project/)** 팀프로젝트 개발에 대한 포스팅이다.


이 글에서는 카카오맵 API이용 한 서울시 25개 구 지도 구현 과정을 소개한다.
<br>

-----

<br>

# 1. 카카오맵 API이용 한 서울시 25개 구 지도 구현

이용자가 카페 추천 웹페이지를 이용하면서 서울시 25개 "구"를 선택할 때, 
한 눈에 딱 알아볼 수 있게 하기 위해서 단순히 박스를 만들어 글을 넣는 것이 아닌
서울시 지도를 **25개 "구"단위**로 나눠져있는 지도를 구현하고 싶었다.

![지도구현](https://zhuyuan7.github.io/assets/images/지도구현.jpg "지도구현"){: .align-center}
<center> <그림 1> 서울시 "구" 지도 시안 </center>
<br>


<br>

카카오맵 API 이용하여 서울시 25개구 지도 구현 크롤링
