---
layout: post
title: c++ function tamplate
date: 2023-09-15 13:37
category:
author: Bure5kzam
tags: []
summary:
---

## 함수 템플릿 인스턴스화

함수 템플릿 자체는 타입도, 함수도, 어느 개체도 아니다.
템플릿 정의만 있으면 소스 파일에서 어떤 코드도 생성되지 않는다는 뜻이다.

실제 코드가 생성되려면 템플릿이 인스턴스화 되어야하며,
인스턴스화에 사용할 템플릿 인자가 결정되어야 컴파일러가 실제로 함수를 생성할 수 있다. (클래스 템플릿이라면 클래스)

* 이해를 위해 아래 표현들을 치환함
* 명시적 인스턴스화 정의 => 명시적 정의
* 아규먼트 => 인자


## 명시적 인스턴스화

```md
template return-type name < argument-list > ( parameter-list ) ;	(1)
# 명시적 인스턴스화 정의 (모든 기본값없는 템플릿 파라미터가 명백하게 정의된 경우 템플릿 인수 추론 없음)

template return-type name ( parameter-list ) ;	(2)
# 모든 파라미터에 템플릿 인자 추론을 사용한 명시적 인스턴스화 정의

extern template return-type name < argument-list > ( parameter-list ) ;	(3)	(since C++11)
extern template return-type name ( parameter-list ) ;	(4)	(since C++11)
```

명시적 인스턴스화 정의는 참조하는 함수나 멤버 함수의 인스턴스화를 강제한다. 템플릿 정의 뒤면 어디든 발생하며 주어진 인자 리스트에 대해서는 프로그램에서 한번만 나타나는 것이 허용된다.

> 명시적 인스턴스화 선언 (외부 템플릿)은 암시적 인스턴스화를 방지한다.
> 암시적 코드를 유발할 수 있는 코드는 다른 곳에서 제공된 명시적 인스턴스화 정의를 사용해야 한다.

따라오는 템플릿 인자는 지정되지 않은채 함수 템플릿 전문화의 명시적 정의에 남을 수 있다. (함수 파라미터에서 추론할 수 있다면)

```c++

템플릿 정의
template<typename T>
void f(T s)
{
    std::cout << s << '\n';
}

template void f<double>(double); // f<double>(double) 인스턴스화
template void f<>(char);         // 템플릿 인자를 추론해서 f<char>(char) 인스턴스화
template void f(int);            // 템플릿 인자를 추론해서 f<int>(int) 인스턴스화
```

함수 템플릿의 명시적 인스턴스화는 인라인이나 상수표현식을 사용할 수 없다.
만약 명시적 인스턴스화 선언이 암시적으로 선언한 특수한 멤버 함수 이름이 될 경우 프로그램 형식이 잘못된 것이다.

생성자의 명시적 인스턴스화는 템플릿 파라미터 리스트를 사용할 수 없다. 필요도 없으며 추론될 수 있기 때문이다.
인자 기본 값이 지정된 함수 템플릿의 명시적 인스턴스화 정의는 인수를 사용하지 않으며 초기화를 시도하지 않습니다.

```c++
char* p = 0;

template<class T>
T g(T x = &p) { return x; }

template int g<int>(int); // OK even though &p isn’t an int.
```

## 암시적 인스턴스화

코드가 함수 정의가 존재해야 하는 먼텍스트에서 함수를 참조하거나, 정의의 존재가 프로그램의 시멘틱에 영향을 미치는 경우, 그리고 함수가 명시적으로 인스턴스화 되지 않은 경우 암시적 인스턴스화가 발생한다.

만약 컨텍스트에서 추론할 수 있다면 템플릿 인자 목록은 제공받지 않을 것이다.

```c++
#include <iostream>

template<typename T>
void f(T s)
{
    std::cout << s << '\n';
}

int main()
{
    f<double>(1); // instantiates and calls f<double>(double)
    f<>('a');     // instantiates and calls f<char>(char)
    f(7);         // instantiates and calls f<int>(int)
    void (*pf)(std::string) = f; // instantiates f<string>(string)
    pf("∇");                     // calls f<string>(string)
}
```

표현식의 상수 평가가 요구되지 않거나 상수 표현식 평가가 정의를 사용하지 않더라 하더라도 함수가 표현식에 의해 상수 평가가 필요하다고 여겨질 때는 함수 정의의 존재가 프로그램의 시멘틱에 영향을 끼친다고 판단된다.

+
