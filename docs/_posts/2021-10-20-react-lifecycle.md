---
date: 2021-10-20 20:00:00 +/-TTTT
title : React-lifecycle
categories: [TIL, react]
tags: [React]     # TAG names should always be lowercase
toc : true
comments : true
math : true
mermaid: true
---

[컴포넌트 생명주기](https://ko.reactjs.org/docs/react-component.html)

>모든 컴포넌트는 여러 종류의 “생명주기 메서드”를 가지며, 이 메서드를 오버라이딩하여 특정 시점에 코드가 실행되도록 설정할 수 있습니다.

## Mount
### constructor()
메서드 바인딩과 state 초기화를 담당.
선두에 super(props)를 호출해야 버그의 우려가 없음.
state에 직접 값을 할당할 수 있는 유일한 메서드로, 그외에는 this.setState()를 사용해야함.

this.methode = this.method.bind(this)

### componentDidMount()
트리에 삽입된 후 호출됨. 네트워크 요청 후 처리하기 적절한 메소드

> 이 메서드는 데이터 구독을 설정하기 좋은 위치입니다. 데이터 구독이 이루어졌다면, componentWillUnmount()에서 구독 해제 작업을 반드시 수행하기 바랍니다.

### componentDidUpdate()
컴포넌트가 갱신된 후 호출. vue에서 updated()와 유사한 역할 인 것 같다.

>componentDidUpdate()에서 setState()를 즉시 호출할 수도 있지만, 조건문으로 감싸지 않으면 무한 반복이 발생할 수 있다는 점에 주의하세요. 


### 라이프사이클 정리
Velaport님이 강의에서 보기 좋게 정리해두신 자료가 있다.

단 공식 document에서 componentWillmonut는 레거시 메소드로 언급했음을 유의해야 한다.

![Image](./../assets/post/2021-10-20-react-lifecycle/lifeCycle.png)