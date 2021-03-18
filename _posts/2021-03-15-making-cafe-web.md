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
### 1.1. 테이블 생성

카페 정보 테이블은 **info_tbl**로, 카페 이용 후기 핵심어 최빈도수 테이블은 **review_tbl**로 명명하였다.

**info_tbl**의 **'gu','id'**를 primary키로 설정하여 review_tbl에서 해당하는 데이터를 찾을 수 있도록 하였고,
 

![정보데이터](https://zhuyuan7.github.io/assets/images/정보데이터.jpg "정보데이터"){: .align-center}
<center> <그림 1> 카페 정보 테이블 속성 </center>
<br>


<br>
**review_tbl**은 info_tbl의 'gu'와 'id'의 primary키와 충돌되지 않도록 **'seq'**를 Auto-Increment로 추가하여 
primary키로 설정하였다.

![빈도수데이터](https://zhuyuan7.github.io/assets/images/빈도수데이터.jpg "빈도수데이터"){: align=center}
<center> <그림 2> 핵심어 최빈도수 테이블 속성 </center>

<br>


<br>
### 1. 2. 테이블 데이터 Import

이제 생성한 테이블에 카페 정보 데이터.csv 파일과 핵심어 최빈도수 데이터.csv파일을 
MySQL DB에 적재했다.

![테이블](https://zhuyuan7.github.io/assets/images/테이블.jpg "테이블"){: .align-center}
<center> <그림 3> 테이블 데이터 적재 </center>
<br>


<br>
다음과 같은 순으로 테이블 데이터를 적재하였다.

![적재](https://zhuyuan7.github.io/assets/images/적재.jpg "적재"){: .align-center}
<center> <그림 4>  데이터 적재 순서 </center>
<br>


<br>
위 순서대로 테이블 데이터 적재 작업을 진행하면 다음과 같이 컴퓨터에 보유하고 있던 
csv 파일을 적재할 수 있다.

![임폴트](https://zhuyuan7.github.io/assets/images/임폴트.jpg "임폴트"){: .align-center}
<center> <그림 5> 임폴트 결과 </center>

<br>


<br>
## 2. 기존 데이터베이스 Django에 연동

이용자가 웹페이지 상에서 서울시 특정**'구'**를 선택하고 10가지 카페 이용 목적 **'테마'**를 선택하면 
이 조건에 해당되는 데이터들을 제공하기 위해, **기존 데이터베이스**를 **Django**에 연동시켰다.


이 작업을 수행하기 위해, 정말 많이 헤맸는 데... 비트교육센터 **배선생님**께서 항상 말씀하시던 **"모르겠으면
Documentation을 참고해라!"**라고 하셨는 데!!!

역시 해당 프로그램의 [Documentation](https://docs.djangoproject.com/en/3.1/)을 찾아보면 
거기에 모든 사용설명서가 다있다. 


내가 참고했던 문서는 ["레거시 데이터베이스에 장고 연동하기"](https://docs.djangoproject.com/ko/3.1/howto/legacy-databases/) 부분인데

```pyhon
python manage.py inspectdb

```

이 한줄이면 MySQL에 적재한 모든 데이터가 한번에 Django안으로 쏙~! 들어간다.
진짜 신기했다!!!


<br>
```python

(myweb_JW) C:\Users\ehcho\Desktop\myweb2>python manage.py inspectdb
# This is an auto-generated Django model module.
# You'll have to do the following manually to clean this up:
#   * Rearrange models' order
#   * Make sure each model has one field with primary_key=True
#   * Make sure each ForeignKey and OneToOneField has `on_delete` set to the desired behavior
#   * Remove `managed = False` lines if you wish to allow Django to create, modify, and delete the table
# Feel free to rename the models, but don't rename db_table values or field names.
from django.db import models


=============================중간 생략==============================

class InfoTbl(models.Model):
    gu = models.CharField(primary_key=True, max_length=100)
    id = models.IntegerField()
    name = models.CharField(max_length=100, blank=True, null=True)
    addr = models.CharField(max_length=300, blank=True, null=True)
    hour = models.CharField(max_length=300, blank=True, null=True)
    tel = models.CharField(max_length=300, blank=True, null=True)
    photo = models.CharField(max_length=300, blank=True, null=True)

    class Meta:
        managed = False
        db_table = 'info_tbl'
        unique_together = (('gu', 'id'),)



class ReviewTbl(models.Model):
    seq = models.IntegerField(primary_key=True)
    gu = models.CharField(max_length=100, blank=True, null=True)
    id = models.IntegerField(blank=True, null=True)
    star = models.IntegerField(blank=True, null=True)
    keyword = models.CharField(max_length=300, blank=True, null=True)
    tf = models.IntegerField(blank=True, null=True)

    class Meta:
        managed = False
        db_table = 'review_tbl'


=============================중간 생략==============================


```

<br>


<br>
## 3. 적재결과 확인

데이터들이 다 잘 적재되었는 지 확인하기 위해 **admin**(http://127.0.0.1:8000/admin/) 으로 들어가서 
Django info_tbl과 review_tbl을 확인하였고, 
다음과 같이 해당 테이블에 모든 데이터가 잘 적재되있는 것을 확인 할 수 있었다.


![정보어드민](https://zhuyuan7.github.io/assets/images/정보어드민.jpg "정보어드민"){: .align-center}
<center> <그림 6> Django Info tbls  </center>
<br>

<br>
![리뷰어드민](https://zhuyuan7.github.io/assets/images/리뷰어드민.jpg "리뷰어드민"){: .align-center}
<center> <그림 7> Django Review tbls  </center>
<br>


<br>
## 4. 카페 테마 SQL 쿼리문 작성

앞서 [(5) 서울 카페 추천 웹사이트 만들기 - 결과값에 대한 카페 요소 분석 및 이용 목적 테마 분류 ](https://zhuyuan7.github.io/blog/making-cafe-web-05/)에서 
설정한 **10가지 카페 이용목적 테마**를 선택하면 그 해당하는 값의 데이터가 제공되도록 SQL 쿼리문을 작성하였다.



![쿼리](https://zhuyuan7.github.io/assets/images/쿼리.jpg "쿼리"){: .align-center}
<center> <그림 8> 10가지 카페 이용목적 테마  </center>
<br>


<br>
서울특별시 25개 **"구"**와 10가지 카페 이용목적 **"테마"**를 데이터를 기반으로 하여 
쿼리문을 설정하였다. 

만약 이용자가 **"강남구"**+**"디저트카페"**를 선택한다면 다음과 같은 쿼리문으로 작성할 수 있다.

![쿼리문](https://zhuyuan7.github.io/assets/images/쿼리문.jpg "쿼리문"){: .align-center}
<center> <그림 9> "구"+"테마" 쿼리문  </center>
<br>


<br>
이렇게 쿼리문을 작성하여 이용자가 **"구"**와 **"테마"**를 선택하기만 하면 해당 테마에 대한 
카페 정보를 제공할 수 있도록 Django와 MySQL에서의 기본 설정을 완료하였다.
 

