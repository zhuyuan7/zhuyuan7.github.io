---
title:  "(4) 서울 카페 추천 웹페이지 만들기 - 카페 이용후기에 대한 자연어 처리 Okt"
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
  - 웹페이지
  - 자연어처리
  - Okt
  - 빈도수
#{{ page.last_modified_at | date: '%Y:%B:%A:%d:%S:%R' }}
---
<br>
**2020-10-21**부터 **2020-12-28**까지 진행했던 **[서울시 25개구 카페추천 웹페이지 만들기](https://zhuyuan7.github.io/project/)** 팀프로젝트 개발에 대한 포스팅이다.
이 글에서는 카페이용 후기에 대해 자연어 처리를 통한 빈도수 분석 과정을 소개한다.
<br>

-----

<br>

# 1. 카페이용 후기 텍스트 마이닝

지루했던 텍스트 수집 단계를 마치고, 드디어 내가 기다리고 기다리던 자연어 처리 단계까지 도달했다!! 평일 오전 9시분터 오후 7시까지 학원에서 열심히 프로젝트를 준비하다가
텍스트 마이닝 관련 서적을 보러 강남역의 수많은 인파들을 뚫고 매일 교보문고에 갔던 기억이 새록새록 난다.  

교보문고에서 매일 자연어 처리 관련 서적을 찾아보면서 대부분이 영어 자연어 처리를 다룬 서적들이 주를 이뤘는 데, 
그 중에서 한국어 텍스트 마이닝을 중점으로 다룬 윤태일, 이수안 저자의 
[『파이썬으로 텍스트 분석하기』](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788965550792&orderClick=JAj)서적을 발견했다. 
자연어 처리를 처음 접해보는 나같은 사람들에겐 진짜 이해하기 쉽게 잘 정리된 서적이다.  

주의!!! 하지만 이 서적에서 다루는 한국어 형태소 분석기 [은전한닢](http://eunjeon.blogspot.com/)은 현재 윈도우에서 지원되지 않는다.
(예제가 있지만 따라할 수가 없는 현실...)
<br>
### 1.1. 자연어 처리 (Natural Language Processing)

앞서 진행했던 **카카오맵 상의 카페정보 수집**과 **구글맵 상의 카페이용후기 수집**과정을 거쳐 
서울특별시 25개 구의 카페 추천 웹페이지를 만들기 위한 데이터들을 확보하였다.
이렇게 확보한 데이터를 기반으로 자연어 처리와 빈도수 분석을 하여 각 카페에 대한 특성을 추출하였다. 

이번 과정의 절차는 다음과 같이 이루어졌다.

1. [KoNLPy](https://konlpy.org/en/latest/) 라이브러리의 [Okt](https://github.com/open-korean-text/open-korean-text) 형태소 분석기를 활용하여 명사 단위로 형태소 분석  
2. 자연어처리를 마친 데이터를 두고 댓글에 대한 빈도수 결과 도출  

이 과정을 토대로 텍스트 마이닝을 진행하였다.
<br>

> **자연어 처리(Natural Language Processing)**란?  
 - 일상적인 자연어를 분석이 가능하도록 정제하는 과정.

> **코엔엘파이(KoNLPy)**란?  
 - 한국어 자연어 처리를 위한 파이썬 패키지는 [코엔엘파이(KoNLPy)](https://konlpy.org/en/latest/) 라이브러리다. 
 - 코엔엘파인(KoNLPy)는 한나눔(Hannanum), 꼬꼬마(KKMA), 코모란(Komoran), 은전한닢(Mecab), Okt(Open-korean-text)의 
 5가지 형태소 분석기를 제공한다.  
    - 출처 : 윤태일, 이수안, 『파이썬으로 텍스트 분석하기』, 늘봄(2018), 142.
<br>


<br>
### 1.2. 왜 Okt 형태소 분석기를 선택했는 가?

먼저 프로젝트에 적용할 형태소 분석기를 선택하기 전에 KoNLPy라이브러리의 5가지 형태소 분석기의 로딩시간과 실행시간을 비교해보았다.
- 로딩시간 : 사전 로딩을 포함하여 클래스를 로딩하는 시간.
- 실행시간 : 10만 문자의 문서를 대상으로 각 클래스의 pos 메소드를 실행하는데 소요되는 시간.


<표 1> 형태소 분석기 로딩시간과 실행시간

| 형태소 분석기 | 로딩시간/초 | 실행시간/초 |
|:-:|:-:|:-:|
| 은전한닢 | 0.0007 | 0.2838 |
| Okt | 1.4870 | 2.4714 |
| 한나눔 | 0.6591 | 8.8251 |
| 코모란 | 5.4866 | 25.6008 |
| 꼬꼬마 | 5.6988 | 35.7163 |


<br>
또한 각 형태소 분석기의 문자 개수를 늘려감에 따라 모든 클래스의 실행 시간은 기하급수적으로 증가하는 것을 알 수 있다.
  ![코엔엘성능비교](https://zhuyuan7.github.io/assets/images/코엔엘성능비교.jpg "코엔엘성능비교"){: .align-center}<center> <그림 1> 형태소 분석기 실행시간 </center>  
     - 출처 : [KoNLPy](https://konlpy.org/ko/v0.4.3/morph/#pos-tagging-with-konlpy)


본래 적용하려던 형태소 분석기는 **은전한닢**으로, 은전한닢이 로딩시간과 실행시간에서 제일 짧아 적용하려 하였으나, 
원도우에서 지원이 되지않아 사용하지 못했다.  

은전한닢 다음으로 성능이 좋은 **Okt**를 적용하여 자연어 처리를 진행하기로 
최종결정하였다.


```python
from konlpy.tag import Okt
import numpy as np
import pandas as pd

from konlpy.tag import Twitter
from collections import Counter
 

okt= Okt()

df=pd.read_csv('/content/drive/MyDrive/Colab Notebooks/Count_cafe/25.JL .csv', encoding='utf-8', sep="|")
print(df)
```

-----

<br>

## 2. 핵심어 빈도분석
카페의 특성을 파악하기 위해, 카페 이용후기 자연어 처리 과정을 거쳐
명사만 추출하고, 그 빈도를 계산한 후에 최빈도수 상위 20개 단어를 추출하였다.


### 2.1. twitter.nouns()
빈도수 분석을 하기에 앞서, Okt를 적용하여 명사단위로 분리하고 추출했으며,

```python
def get_tags(text, ntags=10):
    twitter = Twitter()
    # konlpy의 Twitter객체
    nouns = twitter.nouns(text)
    # nouns 함수를 통해서 text에서 명사만 분리/추출
    count = Counter(nouns)
    # Counter객체를 생성하고 참조변수 nouns할당
    return_list = []  # 명사 빈도수 저장할 변수
    for n, c in count.most_common(ntags):
        temp = {'tag': n, 'count': c}
        return_list.append(temp)
    # most_common 메소드는 정수를 입력받아 객체 안의 명사중 빈도수
    # 큰 명사부터 순서대로 입력받은 정수 갯수만큼 저장되어있는 객체 반환
    # 명사와 사용된 갯수를 return_list에 저장합니다.
    return return_list
```
<br>

<br>

### 2.2. 빈도수
텍스트 데이터에서 출현 빈도가 높은 상위 20개 단어를 추출한다.


```python
def main():
    noun_count = 20
    # 최대 많은 빈도수 부터 20개 명사 추출
    output_file_name = "/content/drive/MyDrive/Colab Notebooks/RESULT/today_test.csv"
    open_output_file = open(output_file_name, 'w',-1,"utf-8")


    for i in set(df['id']):
      print(set(df['id']))      
      text = df[df['id'] == i] # 각 후기 빈도수 에 
      text_2 = df[df['id'] == i]
      text = text.where(df['id'] != 'NaN') 
      text = text.fillna('nan') 
      text = text['review']
      text = list(text)
      text = list(filter(lambda x: x != 'nan', text)) 
      full_text = ''
      for each in text:
        full_text += each
      print(full_text)

      tags = get_tags(full_text, noun_count)
      # get_tags 함수 실행;명사 분리/추출 후, 빈도 계산하는 함수 get_tags
      temp_tag_list = []

      for tag in tags:
        gu = list(text_2['gu'])[0]
        star_mean = np.mean(text_2['star'])
        noun = tag['tag']
        count = tag['count']
        open_output_file.write('{}|{}|{:.1f}|{}|{}\n'.format(gu, i, star_mean, noun, count))
      
    
    # 결과 저장
    open_output_file.close() 
    
if __name__ == '__main__':
    main()
```
<br>


### 2.3. 워드클라우드
서울시 25개 구의 카페 이용후기에 대한 자연어 처리와 빈도수 분석을 통해 
카페의 특성을 알 수 있는 데이터를 확보하였고, 이 데이터의 결과를 직관적으로 
파악할 수 있게 워드클라우드로 표현하였다.

> **워드클라우드**란?  
  - 텍스트에 담겨 있는 여러 단어들의 등장 빈도를 가장 직관적으로 시각화하는 방법이다.
  - 빈도수 높을 수록 큰 글자, 낮을 수록 작은 글자로 표현된다.
<br>


![워드클라우드](https://zhuyuan7.github.io/assets/images/워드클라우드.jpg "워드클라우드"){: .align-center}<center> <그림 2> 이용리뷰 워드클라우드 </center> 
<br>


----


<br>
## 3. 자연어 처리 및 빈도수 코드
```python
from konlpy.tag import Okt
import numpy as np
import pandas as pd

from konlpy.tag import Twitter
from collections import Counter
 

okt= Okt()

df=pd.read_csv('/content/drive/MyDrive/Colab Notebooks/Count_cafe/25.JL .csv', encoding='utf-8', sep="|")
print(df)

def get_tags(text, ntags=10):
    twitter = Twitter()
    # konlpy의 Twitter객체
    nouns = twitter.nouns(text)
    # nouns 함수를 통해서 text에서 명사만 분리/추출
    count = Counter(nouns)
    # Counter객체를 생성하고 참조변수 nouns할당
    return_list = []  # 명사 빈도수 저장할 변수
    for n, c in count.most_common(ntags):
        temp = {'tag': n, 'count': c}
        return_list.append(temp)
    # most_common 메소드는 정수를 입력받아 객체 안의 명사중 빈도수
    # 큰 명사부터 순서대로 입력받은 정수 갯수만큼 저장되어있는 객체 반환
    # 명사와 사용된 갯수를 return_list에 저장합니다.
    return return_list


def main():
    noun_count = 20
    # 최대 많은 빈도수 부터 20개 명사 추출
    output_file_name = "/content/drive/MyDrive/Colab Notebooks/RESULT/today_test.csv"
    open_output_file = open(output_file_name, 'w',-1,"utf-8")


    for i in set(df['id']):
      print(set(df['id']))      
      text = df[df['id'] == i] # 각 후기 빈도수 에 
      text_2 = df[df['id'] == i]
      text = text.where(df['id'] != 'NaN') 
      text = text.fillna('nan') 
      text = text['review']
      text = list(text)
      text = list(filter(lambda x: x != 'nan', text)) 
      full_text = ''
      for each in text:
        full_text += each
      print(full_text)

      tags = get_tags(full_text, noun_count)
      # get_tags 함수 실행;명사 분리/추출 후, 빈도 계산하는 함수 get_tags
      temp_tag_list = []

      for tag in tags:
        gu = list(text_2['gu'])[0]
        star_mean = np.mean(text_2['star'])
        noun = tag['tag']
        count = tag['count']
        open_output_file.write('{}|{}|{:.1f}|{}|{}\n'.format(gu, i, star_mean, noun, count))
      
    
    # 결과 저장
    open_output_file.close() 
    
if __name__ == '__main__':
    main()
```
<br>


