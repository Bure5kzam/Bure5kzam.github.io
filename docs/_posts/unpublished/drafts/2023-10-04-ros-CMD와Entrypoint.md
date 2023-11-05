---
layout: post
title: Dockerfile CMD와 Entrypoint
date: 2023-10-04 11:18
category:
author:
tags: []
summary:
---

CMD와 ENTRYPOINT는 Docker 컨테이너를 시작할 때 실행될 쉘 명령을 지정하는 지시어다.

지시어는 두 가지 형식이 있다.

<지시어> <커맨드>
<지시어> ["커맨드1", "커맨드2"]

CMD는 컨테이너 인스턴스 실행 중 CLI로 재정의 할 수 있는 매개변수이다. 실행 시 CLI, 도커 스크립트에서 매개변수로 CMD를 지정하면 기존의 CMD 지시어는 무시된다.

반면 ENTRYPOINT는 재정의 할 수 없는 매개변수다. 무시되지 않지만 명령의 인수로 처리되어 추가된다.

`docker run `으로 주어진 인자들은 exec 폼으로 주어진 `ENTRYPOINT` 뒤에 추가되고, CMD로 지정된 모든 인자들을 덮어쓴다. 이는 인자들이 entrypoint로 전달되게 해준다.

shell 폼은 `CMD` 나 `run` 으로 주어진 인자가 사용되는 것을 방지하지만 `ENTRYPOINT`가 `/bin/sh -c`의 서브 커맨드로 시작되는 단점이 있다.

이는 `ENTRYPOINT`로 실행된 프로세스가 PID 1이 아니게 됨을 의미하며 Unix 시그널을 수신하지 않기 때문에 docker stop을 해도 `SIGTERM` 시그널을 받지 못한다.

반면 exec 폼은 커맨드 쉘을 호출하지 않는다. 이는 일반 쉘 처리가 발생하지 않음을 의미한다. 예를들어 ENTRYPOINT ["ECHO", "$HOME"] 은 $HOME을 치환하지 않는다.만약 SHELL PROCESSING 을 원한다면 shell 폼을 이용하거나 쉘을 직접 호출해야한다. ENTRYPOINT ["sh", "-c", "echo $HOME"]. exec 폼을 사용하고 쉘을 직접 실행하면 환경변수 확장을 하는건 환경 변수이다.`  `

도커 파일에서 마지막 ENTRYPOINT 지시문만이 효과가있다.

## CMD 테스트

```bach
# Dockerfile.cmd_test
FROM ubuntu:22.04

CMD ["echo", "hello_world"]
```

```bash
FROM ubuntu:22.04

CMD ["echo", "hello cmd"]
~
```

docker run 은 cmd 매개변수 수정을 지원한다.

`Usage:  docker run [OPTIONS] IMAGE [COMMAND] [ARG...]`

쉘에서 cmd 값을 새로 지정하면 이전 내용이 수정된다.

```bash
ros@burePool:~/test$ docker run test
hello_world
ros@burePool:~/test$ docker run test echo "hello new cmd"
hello new cmd
```

## ENTRYPOINT 테스트

```bash
# Dockerfile.entrypoint_test
FROM ubuntu:22.04 as base

ENTRYPOINT ["echo", "-e",  "hello entrypoint\n"]

From base as new_entrypoint

ENTRYPOINT ["echo", "-e",  "hello new entrypoint\n"]
```

상위 스테이지에서 entrypoint를 정의하면 entrypoint 매개변수가 수정된다.

```bash
ros@burePool:~/test$ docker run test
hello new entrypoint

```

## CMD와 ENTRYPOINT 혼용시 주의사항

cmd와 entrypoint를 같이 사용하면 cmd가 entrypoint의 매개변수로 취급된다.

```bash
#Dockerfile.cmd_and_entrypoint
FROM ubuntu:22.04

CMD ["echo","-e", "hello cmd\n"]
ENTRYPOINT ["echo", "-e",  "hello entrypoint\n"]
```

```bash
ros@burePool:~/test$ docker run test
hello entrypoint
 echo -e hello cmd

ros@burePool:~/test$ docker run test hello new cmd
hello entrypoint
 hello new cmd
 ```


