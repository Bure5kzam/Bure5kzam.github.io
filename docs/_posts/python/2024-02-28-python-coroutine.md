---
layout: single
title: python 코루틴이란
date: 2024-02-28 00:13
category: python
author: Bure5kzam
tags: [concept]
summary: 
---

# 코루틴이란? 

[파이썬 단어집](https://docs.python.org/3/glossary.html#term-coroutine)에 있는 코루틴 설명을 보면


> 코루틴은 일반화된 서브루틴이다. 서브루틴이 한 곳에서 진입해서 다른 곳에서 종료되는 한편, 코루틴은 여러 곳에서 진입, 종료, 재개될 수 있다. 

라고 되어있습니다.

그리고 코루틴 함수 설명을 보면

> 코루틴 객체를 반환하는 함수. 코루틴 함수는 주로 sync def 구문으로 정의되고 `await`, `async for`, `async with` 키워드를 포함한다. (PEP492)

여기서 나타나는 단어과 코루틴이 뭔지 알아보겠습니다.

# 서브루틴

파이썬에서 서브루틴은 이름을 정의한 코드블럭을 뜻합니다.

- 호출할 수 있음
- 보통 이름을 정의 <-> 무명함수, lambda
- 인자를 정의할 수 있음
- 값을 반환할 수 있음

예를들면 `function` 과 `method`가 있습니다.

```python
def this_is_function():
    print("im function")

class TestClass():
    def this_is_method(self):
        print( f"hello, I'm method in {self.__class__}")

if __name__ == "__main__":
    this_is_function()
    TestClass().this_is_method()
```

```bash
bure@burepool:~/test$  cd 
im function
hello, I'm method in <class '__main__.TestClass'>
```

# Generator

코루틴의 `async/await` 구문에 대한 제안이 정리되어있는 [PEP 492](https://peps.python.org/pep-0492/#new-coroutine-declaration-syntax) 를 보면 코루틴 개발과정에 `Generator`에 대한 내용이 나옵니다.

`Generator`는 `generator iterator` 를 반환하는 함수입니다. 일반 함수처럼 보이지만 for 반복문에서 사용 가능한 값 스리즈를 생성하는 yield 표현식이 있고 next() 함수와 함께 한번에 하나씩 조회할 수 있습니다.



# 코루틴

> `Native coroutine function`을 설명합니다.
{: .notice--info}

서브루틴은 실행되면 마지막 라인에 도달하거나 값을 return 하기 전까지 실행권이 반환되지 않습니다.

하지만 코루틴은 




# Reference

[python peps-0492](https://peps.python.org/pep-0492/)

[superfast pyton](https://superfastpython.com/python-coroutine/)