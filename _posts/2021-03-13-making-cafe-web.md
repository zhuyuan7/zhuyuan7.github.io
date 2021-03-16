---
title:  "(5) 서울 카페 추천 웹사이트 만들기 - 결과값에 대한 카페 요소 분석 및 고객 취향 분류"
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

빈도수 데이터 기반 카페 요소 및 고객 이용 목적 분류 




# 2. 핵심어 빈도분석

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
# 3. 자연어 처리 및 빈도수 코드
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

