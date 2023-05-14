---
title: "AWS Lambda 콜드 스타트 해결 과정"
date: 2023-05-13 16:00:00 -0400
summary:
toc: ture
toc_sticky: true
comments: true
tag:
    - aws
    - lambda
    - cold start
category:
    - lambda
---

람다는 AWS에서 제공하는 서버리스 컴퓨팅이다. <br>
서버를 직접 구축할 필요가 없고 가볍고 구축하기도 쉬운 여러 장점덕에 많은 곳에서 사용하는 것 같다. <br>
트래픽이 엄청나게 많은 서비스가 아니라면 비용도 저렴한 장점이 있다. <br>

**이번 주제인 콜드 스타트가 생기는 이유는 서버리스라는 특성에서 발생한다.**

<br> <br>

# 서버리스

서버리스도 사실은 서버다. <br>

편의점으로 비교해보면 일반 서버에는 직원이 24시간 항상 대기하고 있지만 서버리스는 손님이 왔을 때만 직원이 잠깐 출근해서 대응하는 방식이다. <br>
여기서 직원이 잠깐 출근할 때 드는 시간이 콜드 스타트이다. <br>
즉 람다를 실행하기 위해 AWS에서 관리하는 서버가 부팅되는 시간이다. <br>

이런 서버리스의 특징 때문에 콜드 스타트가 발생하게 된다. <br>

<br><br>

# 콜드 스타트는 어떤식으로 발생되는가

처음엔 콜드 스타트가 최초 1회만 발생된다고 생각했다. <br>
하지만 문제는 콜드 스타트 시간은 서서히 감소된다. <br>

아래는 람다 2개가 결합된 한 api를 테스트 했을 때 속도이다. <br>

-   <span style="font-size:12px;">1초 간격으로 람다 실행: 2.72s -> 1.21s -> 0.75s -> 0.1s</span>
-   <span style="font-size:12px;">10초 간격으로 람다 실행: 2.75s -> 1.81s -> 1.6s -> 1.1s -> 0.7s -> 0.4s -> 0.1s</span>

0.1s 일때가 콜드 스타트가 완전히 없고 오로지 람다 코드만 실행된 상태이다. <br>
이처럼 콜드 스타트는 한번에 없어지는게 아니고 점차 줄어드는 방식이고 요청 간격에 따라 줄어드는 속도도 다르다. <br>
참고로 지금은 아래 개선사항들을 적용하며 1~2번 요청 내 최고 속도에 도달한다.

<br><br>

# 콜드 스타트 해결하기

먼저 아래 링크는 콜드 스타트를 해결하는 5가지 방법에 대한 글이다. <br>
https://medium.com/postnl-engineering/the-5-ways-we-reduce-lambda-cold-starts-at-postnl-915e62401457

간단히 정리하면 아래와 같다. <br>

1. provisioned concurrency (동시성 구성) <br>
   : 람다를 계속해서 워밍업을 시켜주는 서비스이다. 우리 서비스의 경우 추가비용이 많이 발생될 것으로 보여 사용하지 않았다.
2. language choice (개발 언어 선택) <br>
   : 언어에 따라 람다 속도차이가 있는데 람다에 적합한 typescript를 사용했다.
3. improve performance to reduce <br>
   : 람다에 설정한 메모리 값에 따라 람다의 컴퓨팅 CPU 값이 정해진다. 비용을 고려해서 최대한 scale-up 했다.
4. initialization code (초기화 코드 최적화) <br>
   : 리팩토링을 진행하며 초기화를 위한 로직은 핸들러 외부인 초기화 코드로 적용했다.
5. package size (람다 용량) <br>
   : 모든 람다를 inline으로만 적용해서 용량은 충분히 작았다.

5가지 사항 중 2~5번은 모두 충분한 수준으로 반영했지만... 결과는 크게 나아지지 않았다.

<br>

<img src="https://github.com/Sik-Kim/Sik-Kim.github.io/assets/63498876/3e4b5b11-76aa-4341-83e6-fc91c407d23f" width="50%" height="50%">

<br>

## 실제로 효과가 있었던 3가지 방법

여전히 콜드 스타트 때문에 애를 먹고 있었기에 자체적으로 다른 방법을 찾아야했다. <br>
아래는 실제로 효과적이었던 3가지 방법이다.

    1. 람다 합치기
    2. Gateway의 Authorizer 대신 Lambda Layer 사용
    3. Event Bridge를 활용한 워밍업

### 1. 람다 합치기

람다를 합친다는건 별도로 생성된 람다들을 하나의 람다로 합친다는 의미이다. <br>
서비스에 필요한 api가 50개라면 처음엔 50개의 람다를 만들었었다. <br>
문제는 각각의 람다 모두 콜드 스타트가 적용되게 된다. <br>
1개의 람다당 3번의 콜드가 걸린다고 하면 전체로 보면 50x3=150개의 콜드 스타트를 감수해야 한다는 말이다. <br>

<br>

<img src="https://github.com/Sik-Kim/Sik-Kim.github.io/assets/63498876/ca4d931b-de08-4c41-aba3-d6233e47d87b" width="50%" height="50%">

<br>

유사한 기능을 1개의 람다로 뭉치면 50개의 람다가 5개로 줄어든다. <br>
전체로 보면 5x3=15개의 콜드 스타트가 걸리는 것이고 위의 경우보다 1/10이 줄어든다. <br>
실제로 굉장히 효과가 좋았다. <br>

(그리고 이땐 몰랐지만 aws cloud Formation의 스택 Max Resource수(500개) 이슈도 해결하는 방법이 되었다.)

물론 람다를 합칠 때 우려되는 부분들도 있다. <br>
먼저 람다 용량이 커져서 무거워지지 않을까 하는 우려이다.
합치더라도 람다 inline의 max size(3MB) 내로 여전히 용량은 작아 문제되지 않았다.

두 번째로 기존에는 모든 api를 gateway에서 라우팅을 해줬었는데 람다를 합치면서 람다 내에서 라우팅 해주는 코드를 짜야했다. event값의 resource와 method를 이용해서 라우팅을 해줬다.

### 2. Gateway의 Authorizer 대신 Lambda Layer 사용

Gateway에는 유저 인가를 위해 AWS가 제공하는 Authorizer 기능을 사용할 수 있다. <br>
이 Authorizer는 람다인데 api용 람다 + Authorizer용 람다 2개가 실행되면서 콜드도 2번 걸리는 문제가 있었다. <br>
Authorizer의 캐싱을 이용하면 이를 해결할 수 있지만 Authorizer로 커스텀을 하니 캐싱 기능을 사용할 수 없는 상황이었다. <br>
즉 인가를 위한 코드는 gateway authorizer 말고 람다 공통 모듈 Layer에 포함시켰다.

### 3. Event Bridge를 활용한 워밍업

Event Bridge를 통해 주기적으로 람다를 요청해서 계속 워밍업을 시키는 방법이다.
provisioned concurrency을 대체하는 방법으로 이 방법을 이용하면 비용을 절약할 수 있다. <br>

위 3가지 방법을 통해 콜드 스타트 문제는 거의 해결이 됐다.

<br>

<img src="https://github.com/Sik-Kim/Sik-Kim.github.io/assets/63498876/c8648284-e50e-4add-8930-62a2ec932169" width="50%" height="50%">

<br><br>

# 결론

어느 기술이나 장점이 있다면 단점도 같이 존재하는 법이다. <br>
장점은 잘 취하고 단점은 극복할 방법을 최대한 찾고 적용하는게 중요한 것 같다. <br>
웬만해서는 해결책은 있기 마련이다.
