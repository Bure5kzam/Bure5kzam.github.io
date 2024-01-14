---
layout: post
title: 
date: 2023-12-16 23:56
category: 
author: 
tags: []
summary: 
---

1.1 Outcome semantics
각 smach 컨테이너에 포함된 `state` 인터페이스는은 `state outcomes`로 정의된다. state instacne의 속성들은 `state`의 잠재적인 결과가 되며, 실행되기 전에 미리 정의되어 있어야한다. 만약 SMACH 계획을 직접 작성한다면 모든 잠재적인 결과들은 construction에서 선언되며 state 전환시의 일관성을 실행 하지 않고도 쉽게 확인할 수 있다.

`state outcome` 은 다른 타입의 컨테이너 안에서 다른 것을 유발한다. 하지만 outcome 이 방출된 뒤에 무슨 일이 발생할지는 state의 관점에서는 아무 관련이 없다. 이 맥락에서, `outcome` 은 주어진 state에서 지역적으로 고려된다. 

예를 들어, `state`는 `succeeded`, `aborted`, `preempted` 같은 `outcome` 같은 `outcome`을 제공하며, `state`에 관한 한 task-level state flow와 상호작용 하는 방법이다. 상태 머신의 맥락에서 이 `outcome`들은 다른 `state` 들과 연관되어 `transition`(전환)을 형성한다. 그러나 다른 컨테이너의 맥락에서, 그들은 다르게 취급받는다. `outcome`들은 `state`들과 `container`들 사이에서 일반적인 인터페이스처럼 심플하게 동작한다.

1.2 User data

SMACH container 들은 다른 `state` 사이에서 데이터를 조정하고 전달하는데 사용하는 `flat database`를 갖고있다. 이는 `state`들이 어떤 결과를 계산하거나 센서 정보를 반환할 때 유용하다. 이는 데이터 같은 것들이 실행 단계에서 잡혀 있을 수 있게 해주며 다른 `task`나 `procedure`에서 사용할 수 있게 한다.

`outcome`과 유사하게 각 `state`를 설정하고 복구하는 유저 데이터 키는 `state`의 `input key`와 `output key`로 표현된다. 이들은 `state` 인스턴스의 속성이며, 실행되기 전에 선언되어야 한다. 이 모든 상호작용의 선언은 에러를 방지하고  SMACH 계획이 복잡해졌을 때 디버깅을 지원한다.

1.3 Preemption

선점 전달은 SMACH 안에 내장되어있다. `State` 베이스 클레스에는 컨테이너와 포함된 상태 사이에서 선점 요청을 조정하는 인터페이스가 포함되어있다. 각 컨테이너 타입은 선점 요청에 응답하기 위한 잘 정의된 `behavior`들을 소유하고 있다. 이는 시스템이 엔드 유저로 부터 온 종료 신호에 문제없이 응답하고 `high-level executive` 에 의한 취소 요청을 받을 수 있게 해준다.

1.4 Introspection
SMACH 컨테이너들은 개발자가 SMACH tree의 모든 초기 state를 설정할 수 있는 디버깅 인터페이스를 제공할 수 있다. SMACH View에서 시각화된다. 이는 각 레벨의 유저 데이터 구조의 컨텐츠와 각 컨테이너의 초기 state 라벨도 지원한다. 

2.State

`State` 는 맥락에 따라 해석이 달라진다. SMACH 에서 state는 `execution`의 지역 state거나 동등하게 어떤 task를 수행하는 시스템에 대응된다. 이는 각 state가 시스템이 뭘 하는지를 묘사하는 일반적인 상태 머신과 달리 주어진 시스템 설정을 묘사한다. 이는 유저가 시스템이 실행하는 것과 실행의 결과에 집중할 수 있게 해준다. SMACH 의 `state`는 구조화된 프로그래밍의 상태와 일치한다.

3. 컨테이너

SMACH 라이브러리에서 제공하는 몇가지 컨테이너 타입이 있다. 다른 컨테이너는 다른 실행 구문을 제공하지만, 다른 컨테이너 안에서는 state로 취급받는다. 컨테이너는 포함된 state로 전환하는 고유한 `transition`을 지정하는 방법을 갖고 있다. `transition`의 의미는 맥락에 따라 달라진다.

모든 SMACK 컨테이너는 다음 속성을 갖고 있다.
- 그들은 smach를 구현하는 객체에 대한 state의 dictionary를 포함한다.
- 그들은 자식들이 엑세스할 수 있는 유저 데이터 구조를 포함한다.
- 컨테이너의 구조를 수정하기 위해, 개방되어 있어야한다.

3.1 생성자에서 컨테이너 개방하기

컨테이너 객체에서 파이썬의 `with` 이나 `open(), close()` 으로 열 수 있다. `with` 키워드는 컨테이너 스스로나 컨테이너의 개방된 메소드에서 사용할 수 있다.

3.2 컨테이너에 상태를 추가하기

SMACH 컨테이너는 자식을 추가하는 정적 메소드를 제공한다. 이 메소드들은 현재 열린 컨테이너에 접근하고 순서대로 state를 추가한다

