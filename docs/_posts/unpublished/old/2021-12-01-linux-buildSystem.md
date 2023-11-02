---
layout: single
title: linux Build System
date: 2021-12-01 20:20
category: [TIL, linux]
author: bure5kzam
tags: [linux]
summary: 
---
빌드 프로세스를 설정해주고 그대로 수행하는 프로그램을 뜻한다.

# make

- Build System
- 수정된 파일만 재 컴파일
- 의존성 잇는 파일만 재컴파일
- apt install make
- makefile을 만들어야함

# 샘플

target과 스크립트 실행부로 구성되어있음.
```bash
HI:
    echo "hello"
```
bash에서 
make HI => 지정된 타겟만 실행
make => 가장 맨 위에 있는 Target만 수행

# 의존성

```bash
HI: one two
    echo "hello"
one:
    echo "is One"
two:
    echo "is TWO"
```

결과
```bash
(base) bure@burePool:~/work$ make
echo "is One";
is One
echo "is TWO";
is TWO
echo "hello";
hello
```

# 매크로 변수 추가

```bash
A = "AAA"
B = "BBB"
HI: one two
    echo $(A)
one:
    echo $(B)
two:
    echo $(C)
```

결과
```bash
echo "BBB"
BBB
echo 

echo "AAA"
AAA

```

# 매크로 변수 대입 기호

## +=
```bash
A = "bu"
A += "re"
A += "5kzam"

who:
    echo $(A);
```

## =와 :=

Simple Equel(=) 는 전처리 단계에서 값을 넣는다 

Reculsive Equel (=) 
최종 변수 결과를 집어 넣는다

```bash
NAME = "OH"

SIMPLE := $(NAME)
RESULT = $(NAME)

NAME += "AA"
NAME += "BB"

who:
    echo $(SIMPLE)
    echo $(RESULT)
```

결과

```bash
echo "OH"
OH
echo "OH" "AA" "BB"
OH AA BB
```

## Target 변수

```bash
OK :
    echo "THIS IS [ $@ ] ";
```