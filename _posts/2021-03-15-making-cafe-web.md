---
title:  "(7) 서울 카페 추천 웹사이트 만들기 - MySQL 카페 데이터 파일 적재"
excerpt: "MySQL 이용 카페 데이터 파일 적재"
permalink: "/blog/making-cafe-web-07/"
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
  - Mysql
  - db
  - 정보
#{{ page.last_modified_at | date: '%Y:%B:%A:%d:%S:%R' }}
---
<br>
**2020-10-21**부터 **2020-12-28**까지 진행했던 **[서울시 25개구 카페추천 웹페이지 만들기](https://zhuyuan7.github.io/project/)** 팀프로젝트 개발에 대한 포스팅이다.
이 글에서는 MySQL 데이터 파일 적재를 활용한 웹페이지 구축 과정을 소개한다.
<br>

-----

<br>

# 1. MySQL 데이터 적재

데이터 마이닝과 텍스트 마이닝 작업을 통해 추출한 카페 정보 데이터와 최빈도수 데이터들을 
[MySQL Workbench](https://www.mysql.com/products/workbench/)를 이용하여 적재하였다.

> MySQL Workbench란?  

  MySQL 워크벤치는 데이터베이스 설계자, 개발자 및 DBA를 위한 통합된 시각적 도구입니다.
  서버 구성, 사용자 관리, 백업 등을 위한 데이터 모델링, SQL 개발 및 포괄적인 관리 도구를 제공합니다.
  Windows, Linux 및 Mac OS X에서 사용할 수 있습니다.
  - 출처 : [mysql.com](https://www.mysql.com/products/workbench/)
<br>


<br>

카페 정보 테이블을 **info_tbl**로, 카페 이용 후기 핵심어 최빈도수 테이블을 **review_tbl**로 명명하였다.

info_tbl의 **'gu','id'**를 primary키로 설정하여 review_tbl에서 해당하는 데이터를 찾을 수 있도록 하였고,
review_tbl은 info_tbl의 'gu'와 'id'의 primary키와 충돌되지 않도록 seq'를 Auto-Increment로 추가하여 
primary키로 설정하였다. 
