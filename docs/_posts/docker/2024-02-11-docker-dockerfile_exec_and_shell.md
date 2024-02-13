---
layout: single
title: Dockerfile shell form, exec form
date: 2024-02-11 11:18
category: docker
author: Bure5kzam
tags: []
summary: 
published : false
---


# Overview

Dockerfile 레퍼런스의 `CMD`와 `ENTRYPOINT` 섹션에는 작성 문법이 `shell form`과 `exec form` 두 종류가 있다는 내용이 있습니다.

```bash
# exec from
INSTRUCTION ["executable", "param1", "param2"] 

# shell form
INSTRUCTION command param1 param2
```

용어의 차이가 중요한데, `command` 는 쉘에서 제공하는 내장 명령어를 의미한다. ls, wc, mv 같은 것들이다.

`executable`은 실행 가능한 것들이다. `command` 뿐만 아니라 바이너리(프로그램), 쉘 스크립트 등을 포함한다.

`exec form` 은 실행 가능한 모든 것들을 실행하는 폼이고, `shell form`은 입력된 커맨드를 쉘(`/bin/bash`) 에서 실행하는 폼이다.

따라서 shell form의 경우 터미널을 새로 생성해서 실행하기 때문에 쉘에서 사용할 수 있는 환경 변수, 줄 바꿈 등이 가능하고 `.bashrc`를 적용할 수 있지만 PID가 1이 아니게 된다.

- exec form
    - JSON 배열 형식
    - PID가 1이 됨(init)

- shell form
    - 단순 스트링 형식
    - 줄 바꿈 이스케이프 가능 ("\")
    - 환경 변수 사용 가능
    - PID 1이 아님

<!-- exec form은 스트링 문자열의 변형을 피할 수 있고, 커맨드 쉴이나 다른 실행가능한 것들로 지정해서 커맨드를 호출한다.

이는 각 JSON 배열 문법을 사용하며 각 요소는 command, flag, argumnet다.

shell form은 더 유연하며, 편의성, 가독성, 유연성을 강조한다. `shell form`은 `exec form`과 다르게 `command shell`을 사용한다. -->


## Exec form

`exec form`은 JSON 요소로 나타내기 때문에 각 요소를 쌍따움표로 감싸져야 한다. (따움표가 아님)

exec form이 사용되는 좋은 예는 `ENTRYPOINT`다.

`ENTRYPOINT`는 `CMD`와 결합되며 런타임에 재정의 될 수 있는 기본 인수를 지정할 때 사용한다.



### Variable substitution

exec form은 자동으로 커맨드 쉘을 호출하지 않는다. **이는 변수 치환 같은 일반적인 쉘 프로세싱이 발생하지 않는다는 뜻이다.**

예를 들어 RUN ["echo", "$HOME"] 은 실제 환경변수 $HOME으로 치환되지 않는다.

exec form으로 쉘을 직접적으로 실행하는 경우는 (`RUN ["bash", "-c", "echo", "hello"]`) `shell form` 처럼 환경변수를 실제 값으로 치환한다. 

(docker builder가 처리하는게 아니다.)

<!-- ### Baskslashes(/)

`exec form`에서 backslash 문자를 사용하려면 escape 처리를 해야 한다. 이는 backslash를 경로 구분 문자로 사용하는 window에서 유용하다.

다음 라인은  -->

## Shell form

shell form으로 표현한 지시어는 항상 command shell을 사용한다. 

```bash
RUN source $HOME/.bashrc && \
echo $HOME

RUN source $HOME/.bashrc && echo $HOME
```

shell form 문자열은 `escape character`로 개행을 지원한다. 

### Different shell

기본 쉘을 사용하려면 `SHELL`을 사용한다.


```bash
SHELL ["/bin/bash", "-c"]
RUN echo hello
```


# Reference

[Docker docs, Dockerfile reference](https://docs.docker.com/engine/reference/builder/)