---
layout: single
title: Dockerfile CMD와 ENTRYPOINT의 차이
date: 2023-10-04 11:18
category: docker
author: Bure5kzam
tags: [docker]
summary: Dockerfile CMD와 ENTRYPOINT의 차이
---

## Overview

Dockerfile의 지시어(Instruction)는 문장 서두에서 문장의 동작을 정의합니다.

지시어는 명령어(Command)와 함께 사용하는데, 두 가지 형식이 있습니다.

```bash
<지시어> <커맨드> "파라미터 1" "파라미터 2"
<지시어> ["실행 가능한 것", "파라미터1", "파라미터 2"]
```

이 글에서 다룰 `CMD`와 `ENTRYPOINT`도 지시어이며, 도커 이미지가 실행된 뒤에 실행할 명령어를 지정할 때 사용합니다. (execute)

## CMD와 ENTRYPOINT의 유사점

단독으로 썼을 때 동작은 같습니다.

```bash
FROM ubuntu:22.04 as base

CMD ["echo", "hello"]     

# 실행되는 명령어는 echo hello가 됩니다.
# 출력 결과는 hello cmd 입니다.
```

```bash
FROM ubuntu:22.04 as base

ENTRYPOINT ["echo", "hello"]

# 실행되는 명령어는 똑같이 echo hello가 됩니다.
# 출력 결과는 hello entrypoint 입니다.
```

따로 쓸땐 둘 다 명령어를 실행하지만, 같이 쓰면 쓰면 동작이 달라집니다.

서로 상호 작용이 있으며, 이는 둘의 존재 목적이 다르기 때문입니다.

```bash
FROM ubuntu:22.04 as base

ENTRYPOINT ["echo", "hello", "entrypoint\n"]

CMD ["echo", "hello", "cmd"]         

# 실행되는 명령어는 'echo hello entrypoint\n echo hello cmd'와 유사합니다. (같진 않습니다)
#
# 출력 결과는 
# hello entrypoint
# echo hello cmd
# 입니다.

```


도커 컨테이너는 실행할 때 프로세스를 실행하고, 프로세스가 종료되면 함께 컨테이너를 종료하는데, 이를 `executable`라고 표현합니다. 이는 명령어, 쉘 스크립트, 바이너리 등을 의미합니다.

`ENTRYPOINT`는 `executable`을 지정하는 지시어입니다. (exec form)

> An **ENTRYPOINT** allows you to configure a container that will run as an executable.

`CMD`는 executable로 실행할 `명령어(command)`를 지정하는 지시어입니다. 단 `ENTRYPOINT`가 없으면 `executable` 정의를 대신하며, 있으면 `executable`의 파라미터로 전달합니다.

> The CMD instruction sets the command to be executed when running a container from an image.

만약 hello 라는 문자열을 출력하는 이미지를 빌드하려면 다음과 같아져야 합니다.

```bash
FROM ubuntu:22.04 as base

# executable은 터미널인 bash나 sh가 되고 단일 명령어를 실행하는 -c 옵션을 붙여줍니다.
ENTRYPOINT ["/bin/sh", "-c"]    

# executable인 쉘에 전달할 커맨드는 문자열이 되어야 합니다.
CMD ["echo hello" ] 

# 실제 명령어는 /bin/sh -c "echo hello" 가 됩니다.
```

존재 목적은 서로 다르지만, 기능적으로는 유사하기 때문에 아래 내용들도 출력 결과는 같습니다.


```bash
FROM ubuntu:22.04 as base

CMD ["echo", "hello"]     

# 실행되는 명령어는 echo hello가 됩니다.
# 출력 결과는 hello cmd 입니다.
```

```bash
FROM ubuntu:22.04 as base

ENTRYPOINT ["echo", "hello"]

# 실행되는 명령어는 똑같이 echo hello가 됩니다.
# 출력 결과는 hello entrypoint 입니다.
```

따라서 `ENTRYPOINT`와 `CMD`는 존재의의는 다르지만 동작이 중첩됩니다.

오히려 Dockerfile에서 두 지시어를 구분하기보다, 동작할 때 `executable`과 `command`의 차이점을 아는 것이 중요합니다.


## executable과 command

도커 컨테이너는 `executable`을 시작 프로세스로 실행하면서 시작하며, `executable`을 종료하면서 컨테이너를 종료합니다. `executable`은 PID가 1인 `init process`가 됩니다.

도커 컨테이너에 보내는 시그널은 `init process`에 전달됩니다. 따라서 init process의 자식 프로세스나 다른 프로세스에는 전달되지 않을 수 있습니다.

예를 들어

Dockerfile에 1초에 한번 문자열 출력을 20번 하는 `hello.sh`를 생성하는 base 스테이지가 있습니다.
`SIGINT` 이벤트를 등록해뒀기 때문에 외부 인터럽트를 수신할 수 있습니다.

```bash
FROM ubuntu:22.04 as base


# 1초에 한번 hello 출력을 20번 반복하는 스크립트를 생성
RUN echo '#!/bin/bash\n\
        trap '\''echo "SIGINT received"; exit 1'\'' INT\n\
        for i in $(seq 1 20);\
                do\n\
                        echo "hello $@"\n\
                sleep 1\n\
        done' > /hello.sh

# 실행 권한 부여
RUN chmod +x /hello.sh
```

그리고 `hello.sh`를 실행시키는 스테이지 case1이 있습니다.

```bash
FROM base as case1
# CMD에서 shell form으로  /hello.sh를 실행한 경우

CMD /hello.sh cmd

```

CMD가 shell form이므로 `/bin/sh -c "/hello.sh"`가 되어 `executable`이 `/bin/sh` , `command`가 `/hello.sh` 가 됩니다.

이 스테이지는 실행 중 인터럽트를 시도하면 init process인 `/bin/sh`로 시그널을 보내지만, `/hello.sh`가 실행 중이기 때문에 종료되지 않습니다.

```bash
bure@burepool:~/test/docker_cmd_and_entrypoint$ docker build --target case1 -t test4 -f ./init_check_init_process.Dockerfile .  && docker run --rm test4
> hello cmd
> hello cmd
> ...
> ^C hello cmd         # 인터럽트 시도
> hello cmd
> hello cmd
> ...
bure@burepool:~/test/docker_cmd_and_entrypoint$
```

하지만 exec form으로 실행하는 스테이지는 인터럽트가 가능합니다.

init process가 /hello.sh 이므로 `SIGINT` 시그널이 전달됩니다.

```bash
FROM base as case2

# Case 2-------------------------------
# CMD를 exec form 으로 작성한 경우

CMD [ "/hello.sh", "cmd with exec"]

```

```bash
# Output 2
> hello cmd with exec
> hello cmd with exec
> hello cmd with exec
> ...
> hello cmd with exec
> ^CSIGINT received
bure@burepool:~/test/docker_cmd_and_entrypoint$
```
