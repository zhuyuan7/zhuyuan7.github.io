---
title:  "(6) 서울 카페 추천 웹사이트 만들기 - Django 이용"
excerpt: "Django 이용 웹페이지 구축"
permalink: "/blog/making-cafe-web-06/"
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
  - 웹페이지
  - Django
  - 정보
#{{ page.last_modified_at | date: '%Y:%B:%A:%d:%S:%R' }}
---
<br>
**2020-10-21**부터 **2020-12-28**까지 진행했던 **[서울시 25개구 카페추천 웹페이지 만들기](https://zhuyuan7.github.io/project/)** 팀프로젝트 개발에 대한 포스팅이다.
이 글에서는 Django Web framework와 MySQL Database Service를 활용한 웹페이지 구축 과정을 소개한다.
<br>

-----

<br>

# 1. Django Web framework  

길고 긴 데이터 마이닝과 텍스트 마이닝 과정을 완료하고 이제 진짜 마지막 단계인 웹페이지 구축단계가 시작됐다.
(내가 하다하다 Django까지 손을 댈 줄이야... ) 
이번 프로젝트에서 웹페이지 구축 시 Django 3.1.4를 활용했는 데, 그 이유는 100% 파이썬으로 작성되어
쉽고 빠르다는 점에서 Django를 선택하게 되었다. 

Django는 한번도 접해본 적이 없어서 유튜브의 [BIPA SORI](https://www.youtube.com/channel/UCtCiMH6QQxGU80r4SczFCRg)
강의를 참고하여 만들어보았다.(현재 내가 참고했던 장고강의는 삭제되었다.)
<br>


<br>
![장고](https://zhuyuan7.github.io/assets/images/장고.jpg "장고"){: .align-center}<center> <그림 1> Django </center>

> Django 란?  
  Django는 빠른 개발과 깔끔하고 실용적인 디자인을 장려하는 고수준 Python 웹 프레임 워크입니다.
  숙련 된 개발자가 구축 한이 앱은 웹 개발의 번거 로움을 상당 부분 처리하므로 바퀴를 다시 
  만들 필요없이 앱 작성에 집중할 수 있습니다. 무료이며 오픈 소스입니다.
  - 출처:[Django](https://www.djangoproject.com/)
<br>


<br>
```python
Microsoft Windows [Version 10.0.19041.867]
(c) 2020 Microsoft Corporation. All rights reserved.
(myweb_JW) C:\Users\ehcho\Desktop\BIG\PROJECT\프젝\myweb2>python manage.py runserver
Watching for file changes with StatReloader
Performing system checks...

2021-03-17 22:33:57.042673: W tensorflow/stream_executor/platform/default/dso_loader.cc:59] Could not load dynamic library 'cudart64_101.dll'; dlerror:
cudart64_101.dll not found
2021-03-17 22:33:57.044329: I tensorflow/stream_executor/cuda/cudart_stub.cc:29] Ignore above cudart dlerror if you do not have a GPU set up on your mac
hine.
System check identified no issues (0 silenced).
March 17, 2021 - 22:34:03
Django version 3.1.4, using settings 'config.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CTRL-BREAK.

```

# 2. MySQL Database Service 

> MySQL Database Service란?  
  개발자가 세계에서 가장 널리 사용되는 오픈 소스 데이터베이스를 사용하여 
  안전한 클라우드 네이티브 애플리케이션을 빠르게 개발하고 배포할 수 있도록
  하는 완전 관리형 데이터베이스 서비스입니다.  
  - 출처:[오라클](https://www.oracle.com/kr/mysql/)