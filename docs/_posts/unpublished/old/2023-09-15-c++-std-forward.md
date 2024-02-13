---
layout: single
title:
date: 2023-09-15 11:34
category:
author:
tags: []
summary:
---

```
template< class T >
constexpr T&& forward( std::remove_reference_t<T>& t ) noexcept;
(since C++14)
```

lvalues를 전달할 때 lvaules로 전달할지 rvalues로 전달할지는 T 클래스에 달렸다.

t가 forwarding reference 이면 (cv-unqualified 함수 템플릿 매개변수 rvalue reference 함수 인자)
