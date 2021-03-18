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
한 눈에 딱 알아볼 수 있게 하기 위해서 단순히 컨테이너 박스를 만들어 글을 넣는 것이 아닌
서울시 지도를 **25개 "구"단위**로 나눠져있는 지도를 구현하고 싶었다.

![지도구현](https://zhuyuan7.github.io/assets/images/지도구현.jpg "지도구현"){: .align-center}
<center> <그림 1> 서울시 "구" 지도 시안 </center>
<br>


이러한 지도를 만들기 위해 **카카오맵 API**의 ["다각형에 이벤트 등록하기2"](https://apis.map.kakao.com/web/sample/addPolygonMouseEvent2/)를 
이용하여 **서울시 25개 "구" 지도**를 구현하였다. 
API가 제공하는 좌표주소를 통해 "구"단위를 나누는 코드를 사용하여 "구"지도를 구현하였고,
카카오맵에서 기본적으로 제공하는 좌표는 단 **8개 "구"**(용산구, 중구, 종로구, 서대문구, 동대문구,
성북구, 성동구, 마포구) 이기 때문에 
8개 "구"를 제외한 나머지 구의 좌표값은 내가 직접 구해야만 했다.

**카카오맵 API**에서 제공한 코드가 **"구"단위** 좌표값으로 되어있기 때문에 
일반적으로 사용하는 단일적인 주소의 '좌표값'을 구해 적용하기엔 부적합하다고 판단하여,
무작정 구글링해서 찾는 건 좀 무리라고 느껴졌다. 

>"구" 단위의 좌표값

```python
<script>
  // 지도에 폴리곤으로 표시할 영역데이터 배열입니다 
  var areas = [
      {
          name : '용산구',
          path : [
              new kakao.maps.LatLng(37.5548768201904, 126.96966524449994),
              new kakao.maps.LatLng(37.55308718044556, 126.97642899633566),
              new kakao.maps.LatLng(37.55522076659584, 126.97654602427454),
              new kakao.maps.LatLng(37.55320655210504, 126.97874667968763),
              new kakao.maps.LatLng(37.55368689494708, 126.98541456064552),
              new kakao.maps.LatLng(37.54722934282707, 126.995229135048),
              new kakao.maps.LatLng(37.549694559809545, 126.99832516302801),
              new kakao.maps.LatLng(37.550159406110104, 127.00436818301327),
              new kakao.maps.LatLng(37.54820235864802, 127.0061334023129),
              new kakao.maps.LatLng(37.546169758665414, 127.00499711608721),
              new kakao.maps.LatLng(37.54385947805103, 127.00727818360471),
              new kakao.maps.LatLng(37.54413326436179, 127.00898460651953),
              new kakao.maps.LatLng(37.539639030116945, 127.00959054834321),
              new kakao.maps.LatLng(37.537681185520256, 127.01726163044557),
              new kakao.maps.LatLng(37.53378887274516, 127.01719284893274),
              new kakao.maps.LatLng(37.52290225898471, 127.00614038053493),
              new kakao.maps.LatLng(37.51309192794448, 126.99070240960813),
              new kakao.maps.LatLng(37.50654651085339, 126.98553683648308),
              new kakao.maps.LatLng(37.50702053393398, 126.97524914998174),
              new kakao.maps.LatLng(37.51751820477105, 126.94988506562748),
              new kakao.maps.LatLng(37.52702918583156, 126.94987870367682),
              new kakao.maps.LatLng(37.534519656862926, 126.94481851935942),
              new kakao.maps.LatLng(37.537500243531994, 126.95335659960566),
              new kakao.maps.LatLng(37.54231338779177, 126.95817394011969),
              new kakao.maps.LatLng(37.54546318600178, 126.95790512689311),
              new kakao.maps.LatLng(37.548791603525764, 126.96371984820232),
              new kakao.maps.LatLng(37.55155543391863, 126.96233786542686),
              new kakao.maps.LatLng(37.5541513366375, 126.9657135934734),
              new kakao.maps.LatLng(37.55566236579088, 126.9691850696746),
              new kakao.maps.LatLng(37.5548768201904, 126.96966524449994)
          ]
      }
  ]
 ````
 



<br>


이 시점에 필요한 건! 뭐다~!?! **발상의 전환**이다.


먼저 지도가 제일 잘 활용되는 곳이 어딘지를 생각하였고, 그렇게해서 떠오른 것이 
**부동산 사이트의 지도**이다. 역시나 부동산 사이트가 지도를 잘 활용하고 있었다. 
부동산 사이트 이용자에게 **"구"단위**로 매물을 알아볼 수 있게 정보를 제공하는 
**네이버 부동산**에서 17개 "구"의 좌표값을 구했다.   


그 결과, 드디어 이 프로젝트의 **클라이막스**!! **서울특별시 25개 "구"**를 구현하였다.


![구지도](https://zhuyuan7.github.io/assets/images/구지도.jpg "구지도"){: .align-center}
<center> <그림 1> 서울시 25개 "구" 지도 구현 </center>
<br>


<br>

# 2. 이용 목적 테마 팝업창 만들기

지도도 만들었으니 이제 남은 건 카페 이용 목적 테마 팝업창을 만들기만 하면 된다.  

팝업창은 [**var win = window.open()**](https://www.w3schools.com/jsref/met_win_open.asp)을 
이용하여 만들었다. 

```python
    kakao.maps.event.addListener(polygon, 'click', function(mouseEvent) {
        //다음 페이지에 GET방식으로 파라미터를 전달 gu_name, gu_code를 cafe_theme에 전달햇음.
        // url?key1=value1&key2=value2
        // cafe_theme?gu_name=용산구&gu_code=YS

        var win = window.open("cafe_theme?gu_name="+area.name+"&gu_code="+area.id,
        "_blank", "toolbar=yes,oncontextmenu=no, scrollbars=yes,location=no, 
        resizable=yes,top=500,left=500,width=500,height=500");
        win.setContent(content);
        win.setPosition(mouseEvent);
        win.setMap(map);

```


![팝업창](https://zhuyuan7.github.io/assets/images/팝업창.jpg "팝업창"){: .align-center}
<center> <그림 1> 이용 목적 테마 팝업창 시안 </center>
<br>
