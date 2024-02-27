---
layout: single
title: python, metaclass에 대해
date: 2024-02-18 16:10
category: 
author: Bure5kzam
tags: [concept]
summary: 파이썬 클래스의 클래스, metaclass에 대해
---

파이썬은 모든것이 `object`고, `class`도 그렇다.

인스턴스는 당연히 타입이 있는데, 이상하게 `class`도 타입이 있다.


```python

>>> class Foo:
...     pass

>>> x = Foo()

>>> type(x)
<class '__main__.Foo'>

```

x 인스턴스의 타입은 Foo 클래스다.

```python

>>> type(Foo)
<class 'type'>

```

Foo 클래스의 타입은 `type` 클래스다.

이는 built-in class들도 마찬가지다.

```python
>>> for t in int, float, dict, list, tuple:
...     print(type(t))
...
<class 'type'>
<class 'type'>
<class 'type'>
<class 'type'>
<class 'type'>
```

앞서 본대로 class의 타입은 `type`이다.

그럼 type은 뭘까


## type class에 대해

`type` 클래스에 대해 알아보자. (오브젝트의 타입 말고 클래스 이름이 `type`이다.)

type 클래스는 클래스 자체가 인스턴스인 `metaclass` 이다.

일반적인 객체(object)가 class의 instance인 것처럼, 클래스들은 type 메타클레스의 인스턴스이다. (* new-style class가 적용된 python 3부터)



(+추가) 부연 설명을 조금 붙인다. 베이스 지식이 C++ 기반이라 디테일이 틀릴 수 있다.

**Instance**는 메모리에 존재하는 값이나 객체를 말한다.

```python
a = 3
# a에 매핑되는 메모리 주소에 3이라는 값이 할당되었다.
```

**인스턴스화**는 값을 저장할 메모리 공간을 할당 받는 행위다.

int, double 같은 내장된 자료형들은 필요한 메모리 주소가 정해져 있지만, 사용자 정의 객체는 메모리 크기가 매번 다르다.

그래서 class는 내부 필드들의 자료형을 명시해서 컴파일러에게 인스턴스화에 필요한 메모리 공간 크기를 알려준다.

여기까지는 빌드는 거치는 컴파일 언어의 이야기고, 파이썬은 소스코드를 즉시 실행하는 **인터프리터 언어**다.

그래서인지 class 마저도 인스턴스화해서 실행하고 객체 생성에 참고하는 클래스를 `metaclass`라고 하는 것 같다.


정리하면

```
instance : class를 기반으로 생성한 메모리에 존재하는 값 또는 객체

class : instance화 할때 참고하는 자료형. 하지만 파이썬에서는 이 마저도 객체.

metataclass : class를 인스턴스화 할 때 참고하는 자료형
```


## type 메타클래스로 클래스 생성하기

type 클래스는 두 가지 용도가 있다.

1. 오브젝트의 자료형 반환
2. 동적으로 클래스 생성 (클래스의 인스턴스 생성이 아님)

제공하는 아규먼트에 따라 동작이 다르다.

```python

# 1의 경우, 자료형 확인하기
>>> type(3)
<class 'int'>

# 2의 경우, 동적으로 클래스 생성하기
>>> Foo = type('Foo', (), {})
>>> x = Foo()
>>> x
<__main__.Foo object at 0x7f84540ca770>

```

`type(class_name, parent_class, field)`으로 클래스를 생성할 수 있다.
- class_name는 `str`이고 생성할 클래스의 이름을 정의한다. 이는 `__name__` 속성이 된다.
- parent_class 는 `tuple` 이고 상속할 클래스를 정의한다. 없으면 `()`. 이는 `__bases__` 속성이 된다.
- field는 `dict`고 클래스의 필드를 정의한다. 이는 `__dict__` 속성이 된다.

세부적인 예제는 레퍼런스 참고.

# Reference

[realpython.com](https://realpython.com/python-metaclasses/)