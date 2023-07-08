---
title: "클러스터링과 지오해시로 지도서비스 성능 최적화"
date: 2023-05-17 23:00:00 -0400
summary:
toc: ture
toc_sticky: true
comments: true
tag:
    - 지도
    - postgresql
    - typescript
    - clustering
category:
    - clustering
---

## Intro

지도 서비스에서 클러스터링과 지오해시 개념을 적용하며 사용자 불편을 해소하고 성능 개선한 경험을 정리한다.

#### 기존 방식의 지도

<img src="https://github.com/Sik-Kim/Sik-Kim.github.io/assets/63498876/54612eb3-14c0-464f-a9a6-58bf91676b5d" width="45%" height="45%">
<img src="https://github.com/Sik-Kim/Sik-Kim.github.io/assets/63498876/26bfb11c-4ae5-4a5e-87cf-ba746d957724" width="35%" height="35%">

<span style="color: grey;">**기존의 방식은 위와 같이 의원별로 마커를 불러오는 방식이다.**</span> <br>
<span style="color: grey;">**만약 불러오는 데이터에 개수 제한을 주지 않으면 오른쪽과 같이 된다. 😱**</span>

#### 클러스터링 지도

<img src="https://github.com/Sik-Kim/Sik-Kim.github.io/assets/63498876/7e63aa46-13df-4fa0-8d29-59bc38060760" width="80%" height="80%">

<span style="color: grey;">**클러스터링을 적용한 지도이다.**</span> <br>
<span style="color: grey;">**데이터를 불러오는데 개수 제한이 없다. <br> 5000개 이상의 데이터를 0.2s 내 불러오고 시각적으로도 편한 방식이다.**</span>

## 기존 방식의 문제점 및 클러스터링을 통한 해결

**문제 1. 사용자 경험이 좋지 않음** <br>
마커 개수 제한으로 표시되지 않는 데이터가 발생하면서 일부 유저분들은 본인이 원하는 의원을 못찾겠다는 피드백이 생겼다. <br>
또한 비슷한 위치의 의원들은 마커가 겹쳐서 클릭하기가 어려웠다. <br>
<span style="color: red;">**-> _클러스터링을 적용하며 모든 데이터 제공하기에 원하는 데이터를 쉽게 찾을 수 있음_**</span> <br>

**문제 2. 성능 이슈** <br>
모든 의원정보를 각각 취급하기에 db에서 불러오고 렌더링 하는 과정에서 시간이 오래 걸렸다. <br>
<span style="color: red;">**-> _클러스터링은 대량의 데이터를 그룹화해서 가져오기에 응답속도가 훨씬 빠름 <br> 기존의 방식은 데이터양에 비례해서 속도가 느려졌지만 클러스터링은 대량의 데이터에도 성능이 뛰어남_**</span> <br>
<span style="color: red;">**&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\* _200개 기준 속도 개선: 0.8s -> 0.15s_**</span> <br>
<span style="color: red;">**&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\* _5000개 기준 속도 개선: 5s -> 0.2s_**</span> <br>

**문제 3. 분포 및 밀도 데이터 불충분** <br>
개수 제한이 있다보니 지도 서비스에 필요한 의원의 공간적 분포와 밀도를 이해하기 어려웠다. <br>
<span style="color: red;">**-> _클러스터링 마커는 지도에 전체 데이터를 한눈에 보면서 공간적인 분포와 밀도 파악에 용이함_**</span> <br>

<br>

<span style="color:gray; font-size:18px;">**_그렇다면 클러스터링은 어떻게 적용해야 할까?_**</span>

<br>

## 클러스터링

클러스터링의 사전적 의미는 유사한 특성을 가진 데이터를 집단으로 새롭게 정의하는 것을 의미한다. <br>
쉽게 설명하면 수 많은 데이터를 그룹화해서 한눈에 편하게 보기 위함이다. <br>
주로 지도 서비스에서 많이 사용한다. <br>
클러스터링 데이터를 만들 때 지오해시라는 개념을 이용했다.

<br>

## 지오해시(Geohash)

지오해시는 **특정 위치 정보를 고유한 값으로 표시하는 방법 또는 값**을 의미한다. <br>
위치를 구분하는 방법과 값을 부여하는 방식은 제각각이다. <br>
일반적으론 아래와 같이 사각형으로 분리하고 위치 값으로 알파벳 또는 숫자를 사용한다.

<img src="https://github.com/Sik-Kim/Sik-Kim.github.io/assets/63498876/c2a7edc4-0010-4be9-a7f1-6b2499e116ab" width="100%" height="100%">

 <br>

## 4등분 지오해시(Geohash)

내가 적용한 방법은 정사각형으로 4등분으로 나누는 방식이다. <br>
지도를 4등분으로 나누면 4개면이 생긴다. 아래 지도에 표기처럼 각 면에 0,1,2,3을 부여한다. <br>
쪼개진 면을 또 한번 4등분 후 값을 이어서 추가한다. <br>
이런식으로 4등분을 15번 하면 지오해시 값도 15자리의 숫자가 나오게 된다. <br>
(15자리를 한 이유는 대한민국 지도를 그정도로 했을 때 적절히 분리된 클러스터링 데이터가 나왔다)

<img src="https://github.com/Sik-Kim/Sik-Kim.github.io/assets/63498876/7cb429d3-51b6-443f-b8fd-4fe1c0fbac6a" width="100%" height="100%">

 <br>

## 좌표를 지오해시 값으로 변환하기

이론적인 개념을 이해했다면 이제 실제 지오해시 값을 만들면 된다. <br>

**핵심은 좌표 데이터를 지오해시 값으로 변환해주는 것이다.** <br>
아래는 Typescript로 작성한 변환해주는 함수이다. <br>

10만개 정도의 좌표를 변환하는데 1분 이내로 실행됐다.

```typescript
function generateGeohash(lat: number, lng: number): string {
    let x1 = 124.43994112;
    let x2 = 132.17495859;
    let y1 = 31.99775289;
    let y2 = 39.73277036;

    let midY = (y1 + y2) / 2;
    let midX = (x1 + x2) / 2;

    let markerId = "";
    let i = 0;
    while (i < 15) {
        let unit = "";
        if (lat <= midY) {
            unit = "0";
            y2 = midY;
        } else {
            unit = "1";
            y1 = midY;
        }

        if (lng <= midX) {
            unit += "0";
            x2 = midX;
        } else {
            unit += "1";
            x1 = midX;
        }
        midY = (y1 + y2) / 2;
        midX = (x1 + x2) / 2;

        markerId += parseInt(unit, 2).toString();

        i += 1;
    }

    return markerId;
}
```

<br>

## 지도 축척별로 달라지는 클러스터링 원리

지도의 축척(이하 레벨)에 따라 묶이는 범위가 달라지는건 어떤 원리일까? <br>

축척별로 다른 지오해시 값을 가지고 있는걸까? <br>
만약 그렇다면 레벨이 15개 있는 지도 서비스에서는 각각의 데이터별로 15개의 지오해시 값을 가지고 있어야한다. <br>
이는 너무 비효율적이다. <br>

사실 15자리로 이뤄진 하나의 지오해시 값으로 15개 레벨의 위치 정보를 표현할 수 있다. <br>
**15자리의 지오해시 값은 15개의 위치정보를 포함한다는 의미이다.** <br>
자리수가 늘어날수록 더 좁은 범위의 위치를 표현해주는 원리이다. <br>

예를 들어보자. <br>
2개의 지오해시 값이 "012301230123012"와 "012301230123013"가 있다. <br>

2개의 지오해시는 레벨 14까지는 동일하다가 레벨 15에서 달라진다. <br>
즉 클러스터링을 할 때 두 데이터는 레벨 14까지는 함께 그룹화되고 레벨 15에서는 따로 그룹화가 되는 것이다.
