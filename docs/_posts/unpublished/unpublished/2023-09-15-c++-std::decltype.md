---
layout: post
title: c++ decltype specifier
date: 2023-09-15 19:32
category:
author: Bure5kzam
tags: []
summary:
---

- 엔티티의 선언된 타입을 검사
- 표현식의 카테고리 값 과 타입을 검사

```c++
decltype ( entity )	(1)	(since C++11)
decltype ( expression )	(2)	(since C++11)
```

1. 만약 주어진 인자가 { }둘러 쌓이지 않은 id-expression 이거나 둘러 쌓이지 않은 클래스 멤버 접근 표현식일 경우, decitype는 이 표현식으로 명명된 엔티티 타입을 생성한다. 만약 해당 엔티티가 없거나 인자가 오버로드된 함수 집합을 지명하는 경우 잘못된 프로그램 형식이다.
2. 인자가 T 타입의 표현식일 경우
   1. 표현식의 value category가 xvalue이2023-09-15-c++-std-forward면 T&&를 산출
   2. 표현식의 value category가 lvalue이면 T&를 산출
   3. 표현식의 value category가 prvalue이면 T를 산출

# Value categories

c++표현식은 type과 value category 라는 두 가지 속성을 가지고 있음.
표현식은 몇몇의 참조가 아닌 타입을 갖고 있으며, 각 표현식은 세 가지 주요 value categories 중 하나에 해당됨.

- prvalue
- xvalue
- lvalue

* evaluation은 표현식의 값을 결정하는 과정을 의미.

- glvalue (generalized lvalue)
  - evaluation 이 오브젝트/함수의 식별자를 결정하는 표현식
- prvalue (pure rvalue)
  - evaluation이 c++에 내장된 연산자의 피연산자의 값을 계산하는 표현식(어떤 연산자는 결과 오브젝트가 없음)
  - 오브젝트를 초기화 하는 표현식 (어떤 prvalue 는 result object를 가지고 있다고 말함)
    - 결과 객체는 new-expression에 의해 새성된 객체나 temporary materialization에 의해 생긴 임시(객체) 거나 그것의 멤버 일 것이다.
    - 알아둬야 할 것은 void가 아닌 버러진 표현식은 결과 객체를 가지고 있다.(materialized temporary).
    - 또한 모든 클래스와 배열 prvalue는 decltype의 피연산자일 때를 제외하고 결과 오브젝트를 가지고 있다.
- xvalue (eXpiring value)
  - 리소스를 다시 사용할 수 있는 객체임을 나타내는 glvalue
- lvalue (역사적으로 할당 표현식의 좌측에 나타났기 때문)
  - xvalue가 아닌 glvalue를 나타냄
- rvalue (역사적으로 할당 표현식의 우측에 나타났기 때문)
  - prvalue나 xvalue


## example of lvalue

- 변수 이름, 함수, 템플릿 파라미터 객체, 데이터 멤버
  - 설령 변수의 타입이 rvalue 참조더라도, 그것의 이름을 이름을 포함하는 표현식은 lvalue 표현식이다.
- 함수 호출 또는 리턴값이 lvalue 참조값인 오버로드된 연산자 표현식
  - getline(std::cin, str), std:: cout <<1, str = 1, ++it
- a = b, a+= b 등 내장형 할당 및 복합 할당 표현식
- 내장 증감 표현식
  - ++a, --a
- 간접 지정 표현식 (indirection expression)
  - *p
- build-in subscript expression
  - a[n], p[n]
- 객체의 멤버 표현식
  - a.m
  - 다음 경우는 제외
    - 객체의 enumerator나 non-static 멤버 함수
    - a가 rvalue면서 m이 객체 타입의 non-static data member일 경우



