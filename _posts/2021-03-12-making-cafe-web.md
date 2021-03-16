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
텍스트 마이닝 관련 서적을 보러 강남역의 수많은 인파들을 뚫고 매일 교보문고로 향하던 기억이 난다.  

교보문고에서 매일 자연어 처리 관련 서적을 찾아보면서 대부분이 영어 자연어 처리를 다룬 서적들이 주를 이뤘는 데, 
그 중에서 한국어 텍스트 마이닝을 중점으로 다룬 윤태일, 이수안 저자의 
[『파이썬으로 텍스트 분석하기』](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788965550792&orderClick=JAj)서적을 발견했다. 
자연어 처리를 처음 접해보는 나같은 사람들에겐 진짜 이해하기 쉽게 잘 정리된 서적이다.  

- 주의!!! 하지만 이 서적에서 다루는 한국어 형태소 분석기 [은전한닢](http://eunjeon.blogspot.com/)은 현재 윈도우에서 지원되지 않는다.
(예제가 있지만 따라할 수가 없는 현실...)
<br>


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


<br>
본래 적용하려던 형태소 분석기는 **은전한닢**으로, 은전한닢의 로딩시간과 실행시간이 가장 효율적이라 적용하려 하였으나, 
윈도우에서 지원이 되지않아 사용하지 못했다.  

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
빈도수 분석을 하기에 앞서, Okt를 적용하여 명사단위로 분리하고 추출하였다.

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
명사 단위로 분리해 추출한 서울시 25개 구 카페 이용후기 데이터에서 
출현 빈도가 높은 상위 20개 단어를 추출하여 총 98,053개의 빈도수 데이터를 확보하였다.  


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


물론, 이 경우도 마찬가지로 하나의 '핵심어'에 식별자('GU'+'ID')를 부여하여
어떤 카페의 핵심어인지 판별할 수 있게 설정하였다.

![빈도수결과](https://zhuyuan7.github.io/assets/images/빈도수결과.jpg "빈도수결과"){: .align-center}<center> <그림 2> 자연어처리 및 빈도수 분석 결과 </center>


<br>


### 2.3. 워드클라우드
서울시 25개 구의 카페 이용후기에 대한 자연어 처리와 빈도수 분석을 통해 
카페의 특성을 알 수 있는 데이터를 확보하였고, 이 데이터의 결과를 직관적으로 
파악할 수 있게 워드클라우드로 표현하였다.

> **워드클라우드**란?  
  - 텍스트에 담겨 있는 여러 단어들의 등장 빈도를 가장 직관적으로 시각화하는 방법이다.
  - 빈도수 높을 수록 큰 글자, 낮을 수록 작은 글자로 표현된다.
<br>


![워드클라우드](https://zhuyuan7.github.io/assets/images/워드클라우드.jpg "워드클라우드"){: .align-center}<center> <그림 3> 이용리뷰 워드클라우드 </center> 
<br>


<br> 

## 3. 자연어 처리 보완점

### 3.1. 복합 단어 인식 불가  
자연어 처리를 하면서 **복합 단어** 인식이 불가한 점을 찾아냈다. 
예를 들어 "밀크티"의 경우, "밀크"+"티"로 이루어져 있어 "밀크티"로 
분석되기 보다는 **"밀크"+"티"**로 분석되며,
**"가성비"**의 경우, **"가성"+"비"**로 이루어진 각각의 다른 단어로 인식이 된다. 

![밀크티](https://zhuyuan7.github.io/assets/images/밀크티.jpg "밀크티"){: .align-center}<center> <그림 4> 복합단어 처리 결과-밀크티 </center> 


![가성비](https://zhuyuan7.github.io/assets/images/가성비.jpg "가성비"){: .align-center}<center> <그림 5> 복합단어 처리 결과-가성비 </center> 
<br>


<br>
### 3.2. 띄어쓰기에 취약 
같은 단어도 **띄어쓰기**에 따라 인식을 달리한다. 이용후기 데이터 중 GR(구로)269번 카페의
이용후기를 살펴보면, "리뉴얼" 단어가 나오는 데, "리뉴얼"은 완벽하게 **"리뉴얼"**로 인식하지만
"리뉴얼후에"라고 띄어쓰기를 생략해버리면 **"리뉴"+"얼후"**로 분리하였다.

![리뉴얼전](https://zhuyuan7.github.io/assets/images/리뉴얼전.jpg "리뉴얼전"){: .align-center}<center> <그림 6> 띄어쓰기 처리 결과-리뉴얼 </center> 
<br>


<br>
### 3.3 명사형 전성 어미 '-(으)ㅁ' 인식 불가
> **명사형 전성 어미 '-(으)ㅁ'**이란?
 - 어미로서 동사, 형용사, 서술격 조사의 어간에 붙어서 명사형을 만들고, 
 명사형 전성 어미 '-(으)ㅁ'에 의해 만들어진 명사형은 기본 어미 '-다'가 붙은 
 형태로만 사전에 실린다.
 - 명사형 전성 어미의 경우 어간 끝에 받침이 없으면 '달림', '떡임'처럼 '-ㅁ'만 붙고, 
 어간 끝에 'ㄹ'이 아닌 받침이 있으면 '먹음'처럼 그 앞에 매개모음 '-으-'도 붙는다. 
 자음 충돌을 방지하기 위한 것. 
 - 명사형 전성 어미 '-ㅁ'은 관형사형 전성 어미 '-ㄴ, -ㄹ'과 달리 어간 'ㄹ' 받침의 탈락 조건에 
 속하지 않으므로 'ㄹ' 받침일 때는 '돎, 얾'처럼 'ㄹ'을 빼지 않고 'ㄻ'으로 합친다.
 한국어에서 겹받침을 문법적으로 만들어낼 수 있는 사례 가운데 하나이다
  - 출처 : [나무위키](https://namu.wiki/w/%E3%85%81)
<br>


<br>
동사, 형용사, 서술격 조사의 어간에 명사형 전성 어미'-(으)ㅁ'이 붙는 형태의 단어는 인식을 
못한다. GR(구로)-311번 카페의 이용후기 중 3가지의 자연어 처리 결과를 보면 다음과 같은 형태로 분리하였다.
명사형 전성 어미에 대한 지식은 아직 훈련이 부족한 상태인 것 같다.

1. 냄새남 -> "냄새"+"남"
2. 더비쌈 -> "더비"+"쌈"
3. 비추임 -> "임"  ("비추" 인식 못함)

![명사](https://zhuyuan7.github.io/assets/images/명사.jpg "명사"){: .align-center}<center> <그림 7> 명사형 전성 어미 '-(으)ㅁ' </center> 
<br>


<br>
### 3. 4. 자연어 처리 작업 후기
자연어 처리 작업을 진행하기 전에도 한국어가 어렵다는 것은 이미 알고 있었지만
이정도로 복잡하고 다양한 형태로 변형이 가능한지 인지하지 못했다.

내가 작업한 자연어 처리는 대화형이 아니어서 그나마 수월하게 작업했지만, 
한국어 자연어를 이해하고 생성하는 로봇을 상용화시키려면 평생을 바쳐 연구해야 
그나마 좀 인간답게 말할 수 있는 로봇을 개발 할 수 있지 않을까 싶다.
<br>


자연어 처리를 진행하면서 한국어 자연어처리 발전을 위해 기본적으로 
신경써야할 점들을 크게 3가지로 나눠보았다.
 
1.한국어는 꼭 끝까지 들어봐야 한다.  

주어나 목적어를 생략하고 말을 하는 경우가 있고, "개떡같이 말해도 찰떡같이 알아듣는 DNA"가 있어
한국어 원어민이 아닌 이상 개떡같이 말하면 외국인들은 알아듣기가 참 힘들 것이다.


![한국어](https://zhuyuan7.github.io/assets/images/한국어.jpg "한국어"){: .align-center}<center> <그림 8> 끝까지 들어야 되는 한국어 </center> 
  - 출처 : [국립국어원](https://www.korean.go.kr/front/onlineQna/onlineQnaView.do?mn_id=216&qna_seq=32532)
<br>

2.한국어 띄어쓰기의 중요성  

한국어는 영어와 중국어와는 다르게 띄어쓰기에 따라 의미가 와전될 수 있다. 
유명한 예로 "아버지가 방에 들어가신다."라는 말이 있지 않는가.
![가방에](https://zhuyuan7.github.io/assets/images/가방에.jpg "가방에"){: .align-center}<center> <그림 9> 아버지가방에들어가신다 </center> 
  - 출처 : [엠빅뉴스](https://m.post.naver.com/viewer/postView.nhn?volumeNo=10576842&memberNo=28995784&vType=VERTICAL)
<br>


<br>
3.한국어의 신조어, 은어, 비속어  

창의적인 감정표현에 능한 민족이라 신조어, 은어, 비속어의 발달 속도가 빠르다. 
따라서 컴퓨터에게 훈련시켜야 되는 언어들이 지속적으로 생겨난다.  


![개이득](https://zhuyuan7.github.io/assets/images/개이득.jpg "개이득"){: .align-center}<center> <그림 10> 개이득 은어 </center> 
  - 출처 : MBC 무한도전
<br>


----


<br>

**자연어 처리**를 처음 접했을 누군가들은 분명 [네이버 영화후기 분석](https://wikidocs.net/44249)을 
해봤을 것이다. 나 또한 그 작업을 수행하면서 언제부터 컴퓨터가 한국어텍스트를 이해하고 
이게 명사인지 동사인지 형용사인지를 분석할 수 있는 경지에 올라왔는 지!
참...그 때 느꼈던 그 감정은 개인적으로 굉장히 흥미를 느낀 것 이상으로
말로는 형용할 수 없을 정도였다.

현재의 한국어 자연어 처리 성능을 갖기까지 엄청난 훈련을 했다는 말인데,
마치 어학당에서 영어가 모국어인 외국인 유학생에게 한국말을 가르치듯이 
기본 언어가 영어로 구성된 컴퓨터에게 한국어를 가르치는 것과 같은 상황이라고
하면 이해가 쉽게 되려나...

이렇게 자연어 처리 연구가 지속적으로 발전된다면, 미리 대본을 입력하지 않아도 
한국어를 **"이해"**하고 **"생성"**하는 그야말로 사람과 자유자재로 대화하는 
한국판 AI로봇 **소피아** 개발이 머지않았다는 신호가 아닌가!!


![한국소피아](https://zhuyuan7.github.io/assets/images/한국소피아.jpg "한국소피아"){: .align-center}<center> <그림 11> 인공지능 로봇 소피아</center> 
  - 출처 : [인공지능신문](http://www.aitimes.kr)
<br>


<br> 
소피아 얘기가 나와서 하는 말인데... 내가 자연어 처리를 연구하고 싶은 이유가 바로 여기에 있다.  
**한국인과 자유자재로 대화**하는 한국판 AI로봇 소피아를 만들어 
고령화 사회에, 1인가구의 증가라는 사회적 문제로부터 야기되어진 
누군가와의 대화가 그리운 미래의 혼자 생활하거나 혼자 남겨진 사람들에게 
친구가 되어줄 **한국말 잘하는 마음 따뜻한 존재**를 만들어 
사람들의 마음을 행복하게 만드는 것이 나의 큰 꿈이다. 



----


<br>
## 4. 자연어 처리 및 빈도수 코드
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


