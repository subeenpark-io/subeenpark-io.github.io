---
layout: post
title: "반응형 프로그래밍(Reactive Programming)과 Rx"
description: >
  반응형 프로그래밍(Reactive Programming)과 Rx에 대한 고찰: Rx 공부하려고 시작했는데 궁금한거 다 찾아보다 보니 Reactive Programming에 대한 공부로 끝나버린 기술 스터디 발표 자료
sitemap: true
hide_last_modified: true
tags: [design-pattern]
image:
  path:    /assets/img/blog/2021-09-02-Reactive-Programming/zclickstream.png
---



UIKit와 SwiftUI를 와리가리 하며 개발하다보니 UIKit를 쓸 때는 SwiftUI에서 되는 것들이 아쉽고, SwiftUI를 쓸 때는 UIKit에서 되는 것들이 아쉽다. 그 중에서도 UIKit에서 가장 불편한 것은 data(model)에 변동 사항이 생길 때마다 수동으로 UI를 업데이트를 해줘야 한다는 것... 아쉬운 대로 Rx로 그 허전함을 달래보자는 마음으로 Rx에 대한 기술 스터디 발표를 준비하기 시작했는데 막상 하다 보니 궁금한게 너무 많아져서 결국 Reactive Programming 공부로 끝나버렸다. 언젠간 하겠지... Rx 공부...
{:.message}

*주의 사항: 기술 스터디를 위한 발표 자료이다 보니 평소 글에 비해 설명이 부족한 느낌이 다소 있을 수 있지만....... 보충은 더 잘 알게된 이후에!😉* 


* this is unordered seed list will be replaced by the toc
{:toc}

# 비동기 작업

---

## 비동기적인 작업이 필요한 이유는?

비동기 작업이 필요한 이유는 가장 단순하게 말해서 더 나은 user experience를 위해서!

메인 스레드를 쾌적하게 유지하기 위해서는 무겁고 시간이 오래 걸리는 작업들이나 네트워킹 작업들을 background thread에서 처리하고 main thread로 들고오는 일이 필수적이다. 

## The Evaluation Matrix

비동기 작업을 처리하는 라이브러리가 고려해야 할 4가지 측면들

![The Evaluation Matrix]( /assets/img/blog/2021-09-02-Reactive-Programming/1_EvaluationMatrix.png)

**Explicit execution**

- 새로운 스레드에서 작업을 시작한 후 컨트롤 할 수 있어야함

**Easy thread management**

- 한 스레드(주로 background thread)에서 다른 스레드(주로 main thread)로 작업을 넘기거나 스레드를 교체(switch)하는 일이 쉬워야함
    - 예. background에서 그려진 UI로 main thread의 UI를 대체

**Easily Composable**

- 한 스레드의 작업이 다른 스레드의 작업으로부터 독립적이라면 좋겠지만 실제로는 그렇지 않은 경우가 많음. 대부분의 경우에 스레드들은 서로 interdependent하기 때문에 asynchonous library의 경우 easily composable 하고 에러가 날 여지가 적어야 함
    - 예. UI 업데이트, DB transaction

**Minimum side effects**

- 한 스레드가 다른 스레드에 미치는 side effect가 최소화되어야 함.

# 반응형 프로그래밍

## 반응형 프로그래밍의 정의를 찾아서

---

~~사실 아직도 모르겠음... 도대체 뭘까? 쓰라면 쓰겠는데 정의는 못하겠어요 엉엉~~

### 위키피디아

> Reactive programming is a **programming paradigm** oriented around **data flows and the propagation of change**. This means that it should be possible to express static or dynamic data flows with ease in the programming languages used, and that the underlying execution model will automatically propagate changes through the data flow.

![Definition of Reactive Programming from Reactive Manifesto]( /assets/img/blog/2021-09-02-Reactive-Programming/2_Wikipedia.png)

![Definition of Reactive Programming from Wikipedia](/assets/img/blog/2021-09-02-Reactive-Programming/2_Screen Shot 2021-08-29 at 2.00.25 AM.png)

→ MS에서 Rx를 만들던 조직에 있던 에릭 메이허가 2014 리액트 컨퍼런스에서 발표한 자료. 위키피디아 말대로라면 아래 있는 코드도 반응형 아니냐고 하는데... 또 틀린 말은 아님. 이외에 다른 사람들도 기존에 MV* 형태의 아키텍쳐에서 사용해오던 것과 뭐가 다르냐는 의견이 대다수. 

### Reactive Manifesto



![Definition of Reactive Programming from Reactive Manifesto]( /assets/img/blog/2021-09-02-Reactive-Programming/3_ReactiveManifesto.png)

> **Responsive** (응답성)가능한 한 즉각적으로 응답하는 것을 말합니다. 사용자의 입장에서 가장 중요한 항목이 되겠지요. 소프트웨어는 당연히 사용자가 필요로 할 때 빠르게 응답해야 합니다. 응답성은 Reactive Manifesto의 4가지 속성 중 가장 중요하고, 나머지 아래 3가지 요소는 응답성을 위하여 존재하는 하위 요소입니다.

> **Resilient** (탄력성): 시스템이 장애에 직면하더라도 응답성을 유지하는 것입니다. Fault Tolerence(장애 허용)와 밀접한 개념이기도 합니다. 부분적인 장애나 고장이 시스템 전체를 망가뜨리지 않습니다. 시스템 고장을 예외가 아닌 시스템 기능의 일부로 받아들입니다.

> **Elastic** (유연성)시스템의 작업량이 변하더라도 응답성을 유지하는 것입니다. 리액티브 시스템은 트래픽에 따라 Scale Up과 Scala Down이 쉬워야 합니다. 리액티브 선언의 초기 버전에는 Scalable 이라는 말을 썼지만, 이것은 Scale Up에 해당하는 용어라, Scale Up과 Down을 동시에 표현하는 Elastic 이라는 용어로 바뀌었습니다.

> **Message Driven** (메시지 구동): 소프트웨어 내부에서 의사소통을 하는 방식이 메시지를 전달하는 방식으로 구성되어 있습니다. 소프트웨어를 구성하는 각 부분들은 비동기적으로 메시지를 주고받습니다. 메시지를 주고받을 때는 보내고 잊는(fire-and-forget) 방식을 이용합니다.

→ ([원문](https://www.reactivemanifesto.org/))([위 글 긁어온 미디엄](https://medium.com/@ahaljh/the-reactive-manifesto-16ed38106103)) 반응형의 정의에 꽤 자주 등장하는 글인 것 같은데... ~~니 매니저 가서 보여줘라~~ 라던가 듣기 좋은 말만 써놨다 라는 평이 블로그/컨퍼런스에 자주 등장하는 것으로 보아 모두가 동의하는 정의와는 거리가 먼 것 같음. 전부 틀렸다고 말하기는 어렵겠지만 미사여구가 많고 빛좋은 개살구 같은 정의... 그래도 반응형 프로그래밍의 정의를 목적으로 두지 않고 현대 프로그램이 갖춰야 할 정의 정도로 생각하면 읽어볼 만 하다. 

### RedHat Developer ++

> Reactive, what an overloaded word. Many things turn out to become magically Reactive these days. In this post, we are going to talk about **Reactive Programming**, i.e. a **development model structured around asynchronous data streams**.

![4 Fundamental Side Effects]( /assets/img/blog/2021-09-02-Reactive-Programming/4_DefinitionMatrix.png)


- 개인적으로 가장 공감이 갔던 정의. 결국 비동기 데이터 스트림을 어떻게 대할 것인지에 대한 방법론 중 하나가 반응형 프로그래밍인 것 같음.
- 더해서 이전에도 비동기 이벤트 스트림을 관찰하고 사이트 이펙트를 실행하는 방식이 없었던 것은 아니지만 처리 방식에 대한 아이디어 중 손에 꼽게 강력하다는 것!!

### 주섬주섬 합쳐보자면

![Reactive Programming]( /assets/img/blog/2021-09-02-Reactive-Programming/5_Summary.png)

- 리액티브 시스템

    > Reactive systems… are repeatedly prompted by the outside world and their role is to continusouly respond external inputs

    > 리액티브 시스템이란 외부에서 들어오는 요청에 계속해서 응답하는 시스템이다.

    - David Harel, Amir Pnueil, <On the development of reactive systems>

- 리액티브 프로그래밍
    - 리액티브 시스템을 구현하는 데 적합한 프로그래밍 방법론
    - 외부에 계속해서 응답한다 == 반응한다 → 리액티브 프로그래밍의 목적은 외부에서 들어온 자극에 반응하는 구조를 만드는 데 있음.
    - 반응의 경우 (1) 자극이 밖에서 안으로 흘러야 하고, (2) 자극이 있어야만 반응하는 수동성을 가져야 함
    - 결국 기존에 데이터 생산자와 데이터 소비자가 상호작용 하는 방식을 거꾸로 뒤집어서 데이터 소비자의 수동적 반응성을 획득하는 일!
    - 이해가 되지 않을 수도 있겠지만 뒤에 Rx 파트에서 좀 더 자세히..

## 반응형 프로그래밍의 특징

---

![Click Stream]( /assets/img/blog/2021-09-02-Reactive-Programming/6_Clickstream.png)

- 반응형 프로그래밍을 흔히 **비동기적인 데이터 스트림에 관한 것**이라고 많이 부르는데, 데이터 스트림이 우리가 일반적으로 생각하는 것과는 조금 다르다.

    → **events, messages, calls, failures를 전부 포함**

    → 클릭 이벤트, HTTP 요청, ingested message, 알림, 변수의 변화, 캐시 이벤트, 센서의 측정 같은 것들이 데이터 스트림에 주로 들어가게 된다. 

    → 사실 데이터 스트림에 들어가는 원소들이 이런 것들이다보니 필연적으로 비동기적일 수 밖에 없음. 

- 함수형과 궁합이 아~주 좋음
    - 반응형은 결국 **stream을 가지고 무엇을 할 것인가**에 대한 문제이면서 동시에 **비동기적인 동작을 어떻게 다룰 것인가**에 대한 문제
        - Stream을 가지고 어떻게 할 것인가: 스트림이 다른 스트림의 input이 될 수도 있고, 두 스트림을 merge 하거나 기존 스트림을 filter 하거나 map 하거나 이러쿵 저러쿵 해서 어쨌든 또 다른 스트림을 만든다.

            → 함수형에서 많이 보던 형태!

        - 비동기적인 동작을 수행한다는 건 결국 여러 스레드를 건드리겠다는 것... 기왕이면 함수형을 쓰는게 에러가 조금이라도 덜 일어나지 않을까
        - 

## 반응형 프로그래밍을 예시로 이해해보자

---

### 오리지널 클릭 스트림

![Marble Diagram of an Original Click Stream]( /assets/img/blog/2021-09-02-Reactive-Programming/7_MarbleDiagram.png)



```python
--a---b-c---d---X---|->

# a, b, c, d : 발생한 값
# X : 에러
# | : 완료 신호
# ---> : 타임라인
```

- Stream : 시간순으로 정렬한 이벤트의 시퀀스
- Stream에서 발생하는 세 가지 값: `값/타입`, `에러`, `완료 신호`

### 카운터 스트림

```python
clickStream: ---c----c--c----c------c--> # origianl
               vvvvv map(c becomes 1) vvvv
               ---1----1--1----1------1--> # new stream 1
               vvvvvvvvv scan(+) vvvvvvvvv
counterStream: ---1----2--3----4------5--> # new stream 2
```

- 오리지널 클릭 스트림에 반응형 라이브러리에서 제공하는 `map`, `filter`, `scan` 등을 적용 시  새로운 스트림을 반환한다.
- 오리지널 클릭 스트림을 수정하지는 않는 성질이 **불변성(immutability)**

### 더블 클릭 스트림

Stateful 한 방식으로 코딩한다고 생각하면 어질어질...

![Marble Diagram of an Double Click Stream]( /assets/img/blog/2021-09-02-Reactive-Programming/8_DoubleClickStream.png)



- 회색: 하나의 스트림을 다른 스트림으로 변형하는 함수
- throttle: 250 ms 동안 추가적인 event가 발생하지 않으면 지금까지의 클릭을 리스트로 변환
- map: 리스트 스트림을 리스트 개수 스트림으로 변환
- filter: 리스트 개수 스트림 중에서 2보다 큰 이벤트만 필터링
- 완성! subscribe 해서 데이터를 가져다 쓰면 된다

# Rx(Reactive eXtension)

---

*[자기 소개](http://reactivex.io/)에 따르면 Observer pattern, iterator pattern, functional programming의 장점만 취했다고 한다*

- Observer pattern: 옵저버패턴이란 객체의 상태 변화를 관찰하는 관찰자들, 즉 옵저버들의 목록을 객체에 등록하여 상태 변화가 있을 때마다 메서드 등을 통해 객체가 직접 목록의 각 옵저버에게 통지하도록 하는 디자인 패턴
- Iterator pattern: 접근기능과 자료구조를 분리시켜서 객체화 한 것. 컬렉션 구현 방법을 노출시키지 않으면서도 그 집합체안에 들어있는 모든 항목에 접근할 수 있게 해 주는 방법을 제공해 주는 패턴
- Functional Programming: 순수 함수(pure functions) 작성과 공유 상태(shared state), 변경 가능한 데이터(mutable date)와 side-effects 피하기로 소프트웨어를 구축하는 프로세스.

## Rx란?

---

- Reactive Programming Principle의 구현체
- 비동기적이고 이벤트 기반으로 작동하는 프로그램을 작성하는 것에 있어서 `Observable sequence` 를 사용

## Rx의 등장배경

---

- Promise의 한계
    - 마우스 움직임, 파일 스트림 바이트 시퀀스 처럼 둘 이상의 값을 생성하는 데이터 소스를 처리할 수 없었음
    - 에러 발생 시 재실행 하는 기능도 없고, 불변이라서 취소할 수 없었음.
        - 예. XmlHttpRequest 객체 기반의 HTTP 호출은 중단할 수 있지만 프라미스 인터페이스 등을 통해서는 사용할 수 없었음.
    - 콜백, 프라미스, 이벤트 이미터를 잘 사용하면 구현이야 할 수 있겠지만 코드가 너무 복잡
- 이외에 비동기 프로그래밍이 당시 공통적으로 가지던 문제들
    - 비동기 함수의 제어문은 제대로 동작하지 않음. 반복 사이의 지연 시간을 인식하지 못하기 때문
    - 콜백 내에서 try/catch 블록이 중첩되면 에러 처리 전략이 빠른 속도로 복잡해지고, 재시도 로직 구현도 어려움 + 콜백헬
    - 이벤트 취소가 어려움
    - 슬슬 자바스크립트 코드에서 메모리 문제가 발생하기 시작했고, 이벤트 리스너가 메모리 누수의 원인인 경우가 많았음.
    - Throttling과 Debouncing 같은 것들의 구현 방법이 딱히 없었음.
        - 막간을 이용한 얼탱이 없는 [웹사이트](https://cse.snu.ac.kr/search/node/%EC%9E%A5%ED%95%99%20OR%20%EA%B5%90%ED%99%98%ED%95%99%EC%83%9D)..를 보여드리려고 했는데 그새 버그를 고쳤네용. 아숩. 약간 이런거였어요

        ![Pull and Push]( /assets/img/blog/2021-09-02-Reactive-Programming/9_PullandPush.png)
        
        

    - 서버 사이드에선 concurrency를 보장할 방법이 필요했음. RxJava도 그래서 넷플릭스에서 만들었다고 한다

### Reactive Framework의 역사(?)

- 2009.11 — Rx의 첫 등장
    - NET을 위한 Reactive Extension의 첫 릴리즈, 이 당시까지만 해도 오픈 소스가 아니었음.
- 2010.03 — RxJs의 첫 릴리즈
    - 웹 UI가 화려해지고 AJAX 기술의 비중이 높아지던 시점.
    - 사실은 Promise/A+(2012.12 등장)보다더 먼저 나왔음
    - 근데 그 당시에는 큰 인기는 없었다고 한다
    - Node.js가 인기가 많아지면서 콜백 지옥이 화두에 떠올라서 덩달아 인기가 많아졌다고 한다.
- 2012.11 — Rx .Net, RxJS, Rx++를 오픈 소스로 공개

## Rx의 세 요소: Observable + Observer + Schedular

---

- **`Observable`**
    - 가장 단순하게 말해서 한 스레드에서 다른 스레드로 넘길 수 있는 데이터들을 모아놓은 **데이터 스트림의 생산자**
    - Observable의 종류에 따라서 data를 주기적으로/일시적으로 방출
    - 특정 이벤트나 컨디션에 따라서 알맞은 데이터를 방출할 수 있도록 도와주는 게 바로 **`operators`**
- **`Observer`**
    - Observable이 만들어낸 **데이터의 소비자**
    - (언어마다 다를수도!)
        - Observer들은 observable을 `subscribeOn()` 메소드를 통해 구독해 observable이 방출하는 데이터를 받아봄.
        - Observable이 데이터를 방출하면 등록된 observer들은 `onNext()` 콜백으로부터 데이터를 전달받음.
        - 그 과정에서 에러가 있을 경우에는 이를 `onError()` 로 전달받는다.
- **`Scheduler`**
    - Observable과 observer가 어느 스레드에서 동작해야 할 지 알려주는 컴포넌트.
        - Observable - `scheduleOn()`
        - Observer - `observeOn()`
- 이외에도 여러 observer 한테 한번에 값을 전해주는 `subject`, observable을 구독/구독 취소 하는 `subscribe` 등이 있는데 이거는 언어/라이브러리마다 부르는 표현이 좀 다른 것 같아서 여기까지...

## Pull vs. Push

---

- **데이터 생산자(Data producer)와 데이터 소비자(Data Consumer)가 통신하는 프로토콜의 두 가지 종류**
    - 여기서 주의해야 할 점은 Data producer/consumer은 서버-클라이언트와 같은 개념이 아님. Rx에서 말하는 데이터 생산자와 소비자는 각각 **데이터 변경을 수행하는 이벤트**와 **데이터 변경 이벤트를 처리하는 것**을 말함.



![]( /assets/img/blog/2021-09-02-Reactive-Programming/PullPushSummary.png)
![Pull and Push]( /assets/img/blog/2021-09-02-Reactive-Programming/9_PullandPush.png)

- Pull: function, iterator
    - 프로그램이 외부 환경의 명령에 의해서 원하는 결과를 획득하는 방식
    - 프로그램이 직접 제어의 흐름을 통제
    - 데이터가 언제 이동할 지 소비자가 결정.
        - 예1. 자바스크립트의 함수
            - 함수가 데이터를 생산, 함수를 호출하는 코드가 반환값을 pull
        - 예2. 제네레이터 함수, 이터레이터
            - Iterator.next()가 iterator에서 값을 pull
        - 예3. 비동기 request 던지고 response를 기다렸다가 callback으로 받아서 처리(기다린다)
- Push: Promise, observable
    - 환경이 프로그램 안으로 요청(주로 이벤트)을 밀어넣는것. 프로그램은 외부에서 응답이 오면 그 때 반응.
    - 제어의 흐름을 통제할 권한을 외부 환경에 넘김으로써 응답 대기 비용을 줄일 수 있기 때문에 비동기 처리에 유리함.
    - 데이터의 전달 시점을 생산자가 결정
        - 예1. Promise
            - Promise가 값을 생산, 소비자인 callback에 전달. 결국 callback에 값을 Push 하는 시점을 결정하는 것은 promise!
        - 예2. Observable
            - 여러 값을 생산 후 observer에게 push
        - 예3. 비동기 request를 던지고, 외부데이터 스트림에 대한 subscribe형식으로 유입에 반응하도록 처리(기다리지 않는 대신 오면 반응)

![Pull and Push Table]( /assets/img/blog/2021-09-02-Reactive-Programming/10_PullandPushTable.png)

## Observable의 종류 두 가지

많은 사람들이 놓치고 가는 부분이라고 한다. 하지만 잘못 이 부분을 간과하고 프로그램을 짰다가는 데이터가 유실될 수도 있다! ~~지난 학기 두 달 디버깅의 원인...~~

### Cold Observables

- **Lazy evaluation**
    - 다른 component가 subscribe(observing)을 시작하지 않으면 아무것도 하지 않는다.
    - 소비될 것이 확실한 순간부터 publish를 시작!
- cold stream에 의해 생산된 data는 subscribers들 사이에서 공유되지 않음

### Hot Observables

- subscription 이전에도 active
- 개별 subscriber와 데이터가 독립적
- observer는 subscription 후에만 데이터를 전달받는다
- subscriber들 사이에서 데이터가 공유

## 코드로 보는 Rx

---

![Twitter Recommendation Tab]( /assets/img/blog/2021-09-02-Reactive-Programming/11_RecommendationTab.png)





트위터 사용자 추천 탭을 만들어보자

### 작동 방법

- 시작 시, API에서 계정 데이터를 불러오고 3개의 추천을 보여줌
- “Refresh” 버튼을 클릭하면 또 다른 3개의 계정을 추천합니다
- 각 줄의 X 버튼을 누르면 해당 부분만 비운 다음 다른 계정을 보여줍니다
- 각 줄은 계정의 아바타와 페이지의 링크로 구성돼 있습니다

### Request/Response Button

1. Request를 위한 URL Stream

    ```jsx
    // --a------|->
    // a는 'https://api.github.com/users' 스트링을 의미합니다

    var requestStream = Rx.Observable.just('https://api.github.com/users');
    ```

    - Stream을 만든 직후에는 별다른 연산이 일어나지 않음. 연산을 추가해줘야 한다.
2. URL Stream 구독

    ```jsx
    // (1) jQuery Ajax 콜백을 사용했을 때
    requestStream.subscribe(function(requestUrl) {
      // 리퀘스트 실행
      jQuery.getJSON(requestUrl, function(responseData) {
        // ...
      });
    }

    // (2) Rx를 사용했을 때
    requestStream.subscribe(function(requestUrl) {
      // 리퀘스트 실행하는 곳
      var responseStream = Rx.Observable.create(function (observer) {
        jQuery.getJSON(requestUrl)
        .done(function(response) { observer.onNext(response); })
        .fail(function(jqXHR, status, error) { observer.onError(error); })
        .always(function() { observer.onCompleted(); });
      });
      
      responseStream.subscribe(function(response) {
        // 리스폰스 처리하는 곳
      });
    }
    ```

    - Rx.Observable.create()는 데이터 이벤트 onNext나 onError가 발생 시 명시적으로 옵저버를 적어서 커스텀 스트림을 만드는 데 의의가 있음.
    - (1)에서 jQuery Ajax Promise를 한 겹 감쌌는데, 사실 Promise 도 Observable이긴 하다. 단 하나의 값만 발생하는 Observable!
        - 그래서 Rx.Observable.fromPromise(promise)같은 걸로 쉽게 변환 가능
        - 단 observable은 Promise/A+를 따르지는 않는다.
3. 여전히 콜백지옥 처럼 생긴 response 코드를 고쳐보자. 

    ```jsx
    var responseMetastream = requestStream
      .map(function(requestUrl) {
        return Rx.Observable.fromPromise(jQuery.getJSON(requestUrl));
      });
    ```

    - responseStream이 requestStream에 dependent 한 부분이 문제가 되는 것. 한 스트림을 다른 스트림으로 변환하는 map을 이용해서 metaStream을 만들었다.
    - 이를 통해서 각 URL을 리스폰스가 담긴 프로미스 스트림으로 매핑할 수 있음!

![Request Stream]( /assets/img/blog/2021-09-02-Reactive-Programming/12_RequestStream.png)
    


4. JSON 객체에 대한 프로미스를 받는 대신 그냥 바로 JSON 객체를 받으려면?

```jsx
var responseStream = requestStream
  .flatMap(function(requestUrl) {
    return Rx.Observable.fromPromise(jQuery.getJSON(requestUrl));
  });
```

- Flatmap: map()의 한 종류, 메타 스트림을 flatten 시킴(단일 원소 스트림을 반환)

![Meta Request Stream]( /assets/img/blog/2021-09-02-Reactive-Programming/13_RequestStream.png)

- 드디어 리스폰스 스트림이 완성!

    ```jsx
    requestStream:  --a-----b--c------------|->
    responseStream: -----A--------B-----C---|->
    ```

1. 리스폰스 스트림에서 데이터를 렌더링하자. 

    ```jsx
    responseStream.subscribe(function(response) {
      // `response`를 우리가 원하는대로 DOM에 렌더링하면 되는 부분
    });
    ```

### Refresh Button

- 새로고침 이벤트의 클릭 스트림을 변환해보자
1. 클릭 스트림의 생성

    ```jsx
    var refreshButton = document.querySelector('.refresh');
    var refreshClickStream = Rx.Observable.fromEvent(refreshButton, 'click');
    // 이벤트 리스너에서 바로 옵저버블을 만들어줌
    ```

2. 새로고침 버튼이 클릭 OR 웹페이지가 열렸을 때 API을 호출

    ```jsx
    var requestOnRefreshStream = refreshClickStream
      .map(function() {
        var randomOffset = Math.floor(Math.random()*500);
        return 'https://api.github.com/users?since=' + randomOffset;
      });
      
    var startupRequestStream = Rx.Observable.just('https://api.github.com/users');
    ```

3. 두 스트림을 합치자! 

    ```jsx
    var requestOnRefreshStream = refreshClickStream
      .map(function() {
        var randomOffset = Math.floor(Math.random()*500);
        return 'https://api.github.com/users?since=' + randomOffset;
      });
      
    var startupRequestStream = Rx.Observable.just('https://api.github.com/users');

    var requestStream = Rx.Observable.merge(
      requestOnRefreshStream, startupRequestStream
    );
    // merge로 뚝딱 하면 이렇게
    // stream A: ---a--------e-----o----->
    // stream B: -----B---C-----D-------->
    //          vvvvvvvvv merge vvvvvvvvv
    //          ---a-B---C--e--D--o----->
    ```

    ```jsx
    // 찐고수 버전
    var requestStream = refreshClickStream
      .map(function() {
        var randomOffset = Math.floor(Math.random()*500);
        return 'https://api.github.com/users?since=' + randomOffset;
      })
      .startWith('https://api.github.com/users');
    ```

### 사용자 추천 UI에 스트림 사용하기

- 현재는 새로고침을 눌러도 현재 추천되어있는 사용자가 없어지지 않음.
- 클릭 이벤트가 발생하면 현재 추천된 사용자를 삭제

```jsx
// 최악으 코드! responseStream과 refreshClickstream의 영향을 모두 받는다.
refreshClickStream.subscribe(function() {
  // 이전에 추천했던 세 명의 사용자를 삭제합니다
});
```

```jsx
// 이렇게 두 개의 stream을 사용하는 대신 하나의 stream을 계속 쓰자
// 대신 DRY 원칙은 위배함 ㅋㅋㅋ
var suggestion1Stream = responseStream
  .map(function(listUsers) {
    // get one random user from the list
    return listUsers[Math.floor(Math.random()*listUsers.length)];
  })
  .merge(
    refreshClickStream.map(function(){ return null; })
  );
// 새로고침을 클릭하면 null인 추천 데이터를 제공, 렌더링 시에는 데이터 없음으로 처리해서 UI를 숨김
```

```jsx
// 결과물
refreshClickStream: ----------o--------o---->
     requestStream: -r--------r--------r---->
    responseStream: ----R---------R------R-->   
 suggestion1Stream: ----s-----N---s----N-s-->
 suggestion2Stream: ----q-----N---q----N-q-->
 suggestion3Stream: ----t-----N---t----N-t-->
```

[참고한 실제 코드](http://jsfiddle.net/staltz/8jFJH/48)

# 각종 여담

- iOS 때문에 공부하기 시작했는데 디지털 논리 설계의 악몽이 떠오르는 것은 왜일까
- 스위프트도 많이 썼으면 좋겠다
- RP, FP, FRP를 혼용하는 사람들이 너무 많은 것 같다.
- 찾아 보면서 공감갔던 말들

    > 개인적인 견해에 따르면 프로그래밍 패러다임은 과거의 패러다임을 폐기시키는 혁명적인 과정을 거치지 않는 것으로 보인다. 오히려 과거에 있던 패러다임의 단점을 보완하는 발전적인 과정을 거치는 것으로 보인다. 간단히 말해 프로그래밍 패러다임은 혁명적(revolutionary)이 아니라 발전적(evolutionary)이다.
    - 조영호, 『오브젝트』

    > “Your Mouse Is DataBase" - 에릭 마이어

- 결국 모든 것들은 추상화의 문제인 것 같음. 함수형으로 절차지향 + 객체지향을 한 번 더 감싸서 추상화한다던가 기존에도 가능했던 비동기 프로그래밍을 한 단계 더 추상화하듯이? 결국에는 전부 business logic만을 생각하기 위한 몸부림...
- [이 영상](https://www.youtube.com/watch?v=sTSQlYX5DU0) 을 이해하면 반응형 프로그래밍을 다 이해했다고 봐도 된다고 그러는데 저는 반공변성이 나오는 순간 포기했습니다... 강연자 자체는 유쾌하고 좋으신 분인 것 같으니 궁금하신 분은 들어보시고 이해하셨다면 꼭 공유해주시길...⭐️✨

[What is Reactive Programming?](https://medium.com/@kevalpatel2106/what-is-reactive-programming-da37c1611382)

[반응형 프로그래밍과 RxJS](https://www.howdy-mj.me/rxjs/reactive-programming-and-rxjs/)

[ReactiveX](http://reactivex.io/intro.html)

[5 Things to Know About Reactive Programming | Red Hat Developer](https://developers.redhat.com/blog/2017/06/30/5-things-to-know-about-reactive-programming)

[The introduction to Reactive Programming you've been missing](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)

[[번역] 당신이 놓친 반응형 프로그래밍에 대한 모든 것](https://pilgwon.github.io/post/introduction-to-reactive-programming)

[2부 - 자바스크립트 비동기적 프로그래밍(콜백, 프라미스)](https://code-masterjung.tistory.com/66?category=828347)

[MS는 ReactiveX를 왜 만들었을까? (feat. RxJS)](https://huns.me/development/2051)
