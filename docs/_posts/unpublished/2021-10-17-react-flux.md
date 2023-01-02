---
date: 2021-10-17 20:00:00 +/-TTTT
title : React-facebook의 flux 설명 해석하기
categories: [TIL, react]
tags: [React]     # TAG names should always be lowercase
toc : true
comments : true
math : true
mermaid: true
---
# Flux의 개념

Flux는 Facebook에서 나타나던 이전의 MVC 모델의 문제점을 보완하기 위해 탄생했다.
MVC 모델은 4가지 개념 Action, Controller, Model, View로 이루어져 있으며 역할은 다음과 같다.

> React에서 MVVC 패턴을 적용했을 때 얻는 이점에 대한 설명을 facebook의 [github.io](https://facebook.github.io/flux/docs/in-depth-overview)에서 찾을 수 있었지만 영상에서 MVC 모델에서의 구성요소 역할이 글로 기록되어있지는 않았으며, 아래는 사실 기반이 아닌 주관적으로 파악한 내용이다.

Action : 데이터의 변경 이벤트 트리거 동작 Instruction
Controller : 데이터를 변경하는 이벤트 콜백 함수, Action의 instruction을 수신해 실행
Model :  데이터
View : 마크업처럼 웹 브라우저에서 랜더링된 웹 오브젝트 객체. 유저의 화면에 보이게 되며 유저의 입력을 상호작용할 수있다. **Model에 직접 접근해 값을 변경한다**

![MVC_problem](/assets/post/2021-10-17-react-flux/MVC_problem.png)

> facebook의 jing Chen
> Can you tell if there's an infinite loop here where you know model triggers something in the view and view triggers different model, it goes in a cycle. it's really hard to tell just by looking at this.
>
>뷰 트리거를 동작시키고 뷰가 다른 모델을 동작시키는 무한 루프가 싸이클을 이루고 있을 때, 이것만 보고는 말하기 어렵다.
>
> if you were to look at that in code and try to figure out where do I have an infinite loop somewhere in here that's causing my app to just go in constant loops, it's really difficult
> 앱에서 이걸 코드로 보고 루프를 찾는 것은 정말 어렵다.

MVC 패턴은 양방향 데이터 바인딩으로 model를 controller와 view가 함께 변동할 수 있어 추적이 어려웠지만, MVVC 패턴에서는 단방향 흐름을 적용해 데이터 변동의 우선순위를 설정하기 쉽고 디버깅이 용이한 구조를 만들었다.