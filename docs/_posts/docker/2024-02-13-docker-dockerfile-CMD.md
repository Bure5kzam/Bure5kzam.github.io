---
layout: single
title: 
date: 2024-02-13 14:35
category: 
author: 
tags: []
summary: 
published : false
---


# CMD

Dockerfile 지시어 `CMD`는 이미지 실행시에 실행할 `command`를 지정합니다.

단 문법에 따라 `executable`을 지정할 수 있습니다.




CMD는 컨테이너 실행 시 기본으로 실행될 `executable`을 정의할 때 사용할 수 있지만 `ENTRYPOINT`가 존재하면 내용이 `ENTRYPOINT`의 기본 파라미터로 추가되어 동작합니다.


<!-- 만약 컨테이너의 `executable`을 정의하려 하는 거라면, ENTRYPOINT와 CMD를 함께 쓰는 것이 좋습니다. CMD 는 cli에서 `docker run` 명령어로 실행할 때 유저 지정 인수로 재정의할 수 있습니다. -->

[dockerfile, docs, cmd](https://docs.docker.com/engine/reference/builder/#cmd)

## exec form

```bash
# Dockerfile.cmd_test
FROM ubuntu:22.04

CMD ["echo", "hello_world"]
```

```bash
# After build dockerfile.cmd_test to image named test

ros@burePool:~/test$ docker run test
>   hello_world

ros@burePool:~/test$ docker run test echo "hello new cmd"
>   hello new cmd
```

## shell form

# 기타

`docker run` 은 cmd 매개변수 수정을 지원합니다.

`Usage:  docker run [OPTIONS] IMAGE [COMMAND] [ARG...]`

쉘에서 cmd 값을 새로 지정하면 기존 `CMD` 내용이 재정의 됩니다.