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

![임포트](https://zhuyuan7.github.io/assets/images/임포트.jpg "임포트"){: .align-center}
<center> <그림 5> 임포트 결과 </center>

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


class AccountEmailaddress(models.Model):
    email = models.CharField(unique=True, max_length=254)
    verified = models.IntegerField()
    primary = models.IntegerField()
    user = models.ForeignKey('AuthUser', models.DO_NOTHING)

    class Meta:
        managed = False
        db_table = 'account_emailaddress'


class AccountEmailconfirmation(models.Model):
    created = models.DateTimeField()
    sent = models.DateTimeField(blank=True, null=True)
    key = models.CharField(unique=True, max_length=64)
    email_address = models.ForeignKey(AccountEmailaddress, models.DO_NOTHING)

    class Meta:
        managed = False
        db_table = 'account_emailconfirmation'


class AuthGroup(models.Model):
    name = models.CharField(unique=True, max_length=150)

    class Meta:
        managed = False
        db_table = 'auth_group'


class AuthGroupPermissions(models.Model):
    group = models.ForeignKey(AuthGroup, models.DO_NOTHING)
    permission = models.ForeignKey('AuthPermission', models.DO_NOTHING)

    class Meta:
        managed = False
        db_table = 'auth_group_permissions'
        unique_together = (('group', 'permission'),)


class AuthPermission(models.Model):
    name = models.CharField(max_length=255)
    content_type = models.ForeignKey('DjangoContentType', models.DO_NOTHING)
    codename = models.CharField(max_length=100)

    class Meta:
        managed = False
        db_table = 'auth_permission'
        unique_together = (('content_type', 'codename'),)


class AuthUser(models.Model):
    password = models.CharField(max_length=128)
    last_login = models.DateTimeField(blank=True, null=True)
    is_superuser = models.IntegerField()
    username = models.CharField(unique=True, max_length=150)
    first_name = models.CharField(max_length=150)
    last_name = models.CharField(max_length=150)
    email = models.CharField(max_length=254)
    is_staff = models.IntegerField()
    is_active = models.IntegerField()
    date_joined = models.DateTimeField()

    class Meta:
        managed = False
        db_table = 'auth_user'


class AuthUserGroups(models.Model):
    user = models.ForeignKey(AuthUser, models.DO_NOTHING)
    group = models.ForeignKey(AuthGroup, models.DO_NOTHING)

    class Meta:
        managed = False
        db_table = 'auth_user_groups'
        unique_together = (('user', 'group'),)


class AuthUserUserPermissions(models.Model):
    user = models.ForeignKey(AuthUser, models.DO_NOTHING)
    permission = models.ForeignKey(AuthPermission, models.DO_NOTHING)

    class Meta:
        managed = False
        db_table = 'auth_user_user_permissions'
        unique_together = (('user', 'permission'),)


class BoardBoard(models.Model):
    idx = models.AutoField(primary_key=True)
    writer = models.CharField(max_length=50)
    title = models.CharField(max_length=120)
    hit = models.IntegerField()
    content = models.TextField()
    post_date = models.DateTimeField()
    filename = models.CharField(max_length=500, blank=True, null=True)
    filesize = models.IntegerField()
    down = models.IntegerField()

    class Meta:
        managed = False
        db_table = 'board_board'


class BoardComment(models.Model):
    idx = models.AutoField(primary_key=True)
    board_idx = models.IntegerField()
    writer = models.CharField(max_length=50)
    content = models.TextField()
    post_date = models.DateTimeField()

    class Meta:
        managed = False
        db_table = 'board_comment'


class ChatbotChatbot(models.Model):

    class Meta:
        managed = False
        db_table = 'chatbot_chatbot'


class DjangoAdminLog(models.Model):
    action_time = models.DateTimeField()
    object_id = models.TextField(blank=True, null=True)
    object_repr = models.CharField(max_length=200)
    action_flag = models.PositiveSmallIntegerField()
    change_message = models.TextField()
    content_type = models.ForeignKey('DjangoContentType', models.DO_NOTHING, blank=True, null=True)
    user = models.ForeignKey(AuthUser, models.DO_NOTHING)

    class Meta:
        managed = False
        db_table = 'django_admin_log'


class DjangoContentType(models.Model):
    app_label = models.CharField(max_length=100)
    model = models.CharField(max_length=100)

    class Meta:
        managed = False
        db_table = 'django_content_type'
        unique_together = (('app_label', 'model'),)


class DjangoMigrations(models.Model):
    app = models.CharField(max_length=255)
    name = models.CharField(max_length=255)
    applied = models.DateTimeField()

    class Meta:
        managed = False
        db_table = 'django_migrations'


class DjangoSession(models.Model):
    session_key = models.CharField(primary_key=True, max_length=40)
    session_data = models.TextField()
    expire_date = models.DateTimeField()

    class Meta:
        managed = False
        db_table = 'django_session'


class DjangoSite(models.Model):
    domain = models.CharField(unique=True, max_length=100)
    name = models.CharField(max_length=50)

    class Meta:
        managed = False
        db_table = 'django_site'


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


class MybookMybook(models.Model):
    idx = models.AutoField(primary_key=True)
    name = models.CharField(max_length=50)
    passwd = models.CharField(max_length=50)
    content = models.TextField()
    post_date = models.DateTimeField()

    class Meta:
        managed = False
        db_table = 'mybook_mybook'


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


class SocialaccountSocialaccount(models.Model):
    provider = models.CharField(max_length=30)
    uid = models.CharField(max_length=191)
    last_login = models.DateTimeField()
    date_joined = models.DateTimeField()
    extra_data = models.TextField()
    user = models.ForeignKey(AuthUser, models.DO_NOTHING)

    class Meta:
        managed = False
        db_table = 'socialaccount_socialaccount'
        unique_together = (('provider', 'uid'),)


class SocialaccountSocialapp(models.Model):
    provider = models.CharField(max_length=30)
    name = models.CharField(max_length=40)
    client_id = models.CharField(max_length=191)
    secret = models.CharField(max_length=191)
    key = models.CharField(max_length=191)

    class Meta:
        managed = False
        db_table = 'socialaccount_socialapp'


class SocialaccountSocialappSites(models.Model):
    socialapp = models.ForeignKey(SocialaccountSocialapp, models.DO_NOTHING)
    site = models.ForeignKey(DjangoSite, models.DO_NOTHING)

    class Meta:
        managed = False
        db_table = 'socialaccount_socialapp_sites'
        unique_together = (('socialapp', 'site'),)


class SocialaccountSocialtoken(models.Model):
    token = models.TextField()
    token_secret = models.TextField()
    expires_at = models.DateTimeField(blank=True, null=True)
    account = models.ForeignKey(SocialaccountSocialaccount, models.DO_NOTHING)
    app = models.ForeignKey(SocialaccountSocialapp, models.DO_NOTHING)

    class Meta:
        managed = False
        db_table = 'socialaccount_socialtoken'
        unique_together = (('app', 'account'),)


class SurveyAnswer(models.Model):
    answer_idx = models.AutoField(primary_key=True)
    survey_idx = models.IntegerField()
    num = models.IntegerField()

    class Meta:
        managed = False
        db_table = 'survey_answer'


class SurveyInfoTbl(models.Model):
    gu = models.TextField()
    id = models.IntegerField(primary_key=True)
    name = models.TextField()
    addr = models.TextField()
    hour = models.TextField()
    tel = models.TextField()
    photo = models.TextField()

    class Meta:
        managed = False
        db_table = 'survey_info_tbl'


class SurveyReviewTbl(models.Model):
    seq = models.IntegerField(primary_key=True)
    gu = models.TextField()
    id = models.IntegerField()
    star = models.TextField()
    keyword = models.TextField()
    tf = models.IntegerField()

    class Meta:
        managed = False
        db_table = 'survey_review_tbl'


class SurveySurvey(models.Model):
    survey_idx = models.AutoField(primary_key=True)
    question = models.TextField()
    ans1 = models.TextField(blank=True, null=True)
    ans2 = models.TextField(blank=True, null=True)
    ans3 = models.TextField(blank=True, null=True)
    ans4 = models.TextField(blank=True, null=True)
    status = models.CharField(max_length=1)

    class Meta:
        managed = False
        db_table = 'survey_survey'


class SurveySurvey1(models.Model):
    idx = models.AutoField(primary_key=True)
    name = models.CharField(max_length=50)
    passwd = models.CharField(max_length=50)
    content = models.TextField()
    post_date = models.DateTimeField()

    class Meta:
        managed = False
        db_table = 'survey_survey1'
```

<br>


<br>



