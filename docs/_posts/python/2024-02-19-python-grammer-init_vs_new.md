---
layout: post
title: python __init__과 __new__ 차이
date: 2024-02-19 00:00
category: python
author: Bure5kzam
tags: [grammer]
summary: 파이썬 클래스 메소드 __init__과 __new__의 차이
---

인터프리터가 클래스 호출구문을 만나면 클래스의 `__call__` 내부에서 `__new__`와 `__init__`가 실행된다.

둘의 차이는 뭘까

---

`__new__`는 스태틱 메서드로, 호출 시에 클래스의 인스턴스를 반환한다.

`__init__`은 인스턴스 메서드로, `__new__`가 반환한 인스턴스를 초기화 하는 로직을 작성한다.

이런 관계 때문에 `__new__`는 파라미터로 `cls`가 있지만 `__init__`은 `self`를 가 있다. 만약 `__new__`가 cls의 인스턴스를 반환하지 않으면 `__init__`은 호출되지 않는다.


---

python 공식 문서에는 다음과 같은 글이 있다. ([python 3.12.2](https://docs.python.org/ko/3/reference/datamodel.html))

> Class는 호출할 수 있습니다. 이 오브젝트들은 일반적으로 새 인스턴스를 위한 factories method 처럼 동작할 수 있지만, `__new__()` 의 재정의 여부에 따라 다양하게 바뀔 수 있습니다. 호출 인자는 `__new__()`의 매개변수로 전달되며, 보통은 새 인스턴스를 초기화 하기 위해 `__init__()`으로 전달됩니다.

# Reference

(파이썬 공식 문서, v3)[https://docs.python.org/ko/3/reference/datamodel.html]
