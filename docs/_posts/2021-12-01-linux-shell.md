---
layout: single
title: linux-script
date: 2021-12-01 19:50
category: [TIL, linux]
author: bure5kzam
tags: [TIL]
summary: 
---
간단한 예시
```bash
echo "HOW ARE YOU?"
read ans
echo "FINE THANKS"
```

#Born shell
```sh
#! /bin/sh
a=100 #모든 변수 값은 문자열로 취급됨
b="100" #모든 변수 값은 문자열로 취급됨
c=100
# 1. 변수 출력
echo $a #100 출력
echo $b #100 똑같이 출력

echo $1 #스크립트 실행시 함께 실행된 argument 실행

# 2. 산술연산
sum=`expr $a + $b` 
echo $sum

# 3. 조건문
if [ $a == $c ] ## -gt, -lt
then
    echo "HI"
else # elif
    echo "HELLO"
fi
```

# Bash Shell
```bash
#!/bin/bash

# 1. 변수 선언
a=$((10))
b=$((a+14))
arr=(10 20 30 40) # 배열

# 2. 산술연산
echo $(($a + $b))
echo ${arr[0]}

# 3. 조건문
if (($a == 10));then
    echo "is 10"
else
    echo "is not 10"
fi

printf " HI %d \n" $a

# 4. 반복문
for ((i = 0; i < 10; i++));do
    echo "HI"
done

# 5. 함수 만들기

abc() {
    printf "HIHI\n"
}

printf "START\n";
abc
abc
```