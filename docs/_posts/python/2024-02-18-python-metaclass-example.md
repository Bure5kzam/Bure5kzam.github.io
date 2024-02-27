---
layout: single
title: python metaclass 실습
date: 2024-02-18 18:14
category: python
author: Bure5kzam
tags: [metaclass]
summary: 클래스를 생성하는 클래스인 `metaclass` 작성해보기
---

> python3 부터 적용됩니다.
{: .notice--info}

---

인터프리터가 사용자 정의 클래스 생상자를 만나면 다음과 같은 프로세스가 진행됩니다.

1. 부모 클래스인 type의 `__call__()`가 호출됨
2. `__call__()`에서 다음을 호출함.
   1. `__new__()`
   2. `__init__()`
   
클래스의 `__new__()` 와 `__init__()`를 재정의해서 인스턴스가 생성될 때마다 수행할 동작을 바꿀 수 있다.

```python
>>> def new(cls):
...     x = object.__new__(cls)
...     x.attr = 100
...     return x
...
>>> Foo.__new__ = new

>>> f = Foo()
>>> f.attr
100

>>> g = Foo()
>>> g.attr
100
```

이처럼 메타클래스도 메소드를 재정의 할 수 있습니다.

이는 메타클래스로부터 생성되는 클래스의 필드, 메소드를 일괄 변경할 수 있습니다.


```python
>>> class Meta(type):
...     def __new__(cls, name, bases, dct):
...         x = super().__new__(cls, name, bases, dct)
...         x.attr = 100
...         return x
```


# Reference

[realpython.com](https://realpython.com/python-metaclasses/)