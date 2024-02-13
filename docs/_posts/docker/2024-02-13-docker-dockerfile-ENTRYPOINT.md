---
layout: single
title: Dockerfile ENTRYPOINT 지시어
date: 2024-02-13 14:13
category: docker
author: Bure5kzam
---



# ENTRYPOINT

Dockerfile의 `ENTRYPOINT`는 이미지를 실행할 때 실행할 `executable`을 지정하는 지시어입니다.

`ENTRYPOINT`는 `shell form`인지 `exec form` 인지에 따라 동작이 다릅니다.

| - |shell form| exec form|
|--- | --- | ---|
| CMD 사용 가능 여부| 사용 불가능| 사용 가능|
| executable | `/bin/sh` 또는 `SHELL`| `ENTRYPOINT` 값| 
| command | `ENTRYPOINT` 값| `CMD` 값


## shell form

```bash
ENTRYPOINT command param1 param2
```

`shell form`으로 작성하면 `ENTRYPOINT ["/bin/sh", "-c", "command", "param1", "param"]` 처럼 동작합니다.

이 경우 `CMD`를 정의해도 `ENTRYPOINT`의 파라미터로 사용하지 않습니다.

```bash
# Dockerfile
FROM ubuntu:22.04 as base


FROM base as case3
# Case 3-------------------------------
# ENTRYPOINT는 shell form, CMD는 exec form 으로 작성한 경우

ENTRYPOINT echo hello entrypoint

CMD [ "echo", "hello cmd"]

# Output 3
# > hello entrypoint
#
# ENTRYPOINT가 shell form이어서 CMD가 파라미터로 전달되지 않음


FROM base as case4
# Case 4-------------------------------
# ENTRYPOINT는 shell form, CMD는 shell form 으로 작성한 경우

ENTRYPOINT echo hello entrypoint

CMD echo hello cmd

# Output 4
# > hello entrypoint
#
# 3과 마찬가지로 ENTRYPOINT가 shell form이어서 CMD가 파라미터로 전달되지 않음

```

실행 결과

```bash
bure@burepool:~/test/docker_cmd_and_entrypoint$ docker build --target case3 -t test4 -f ./compare_shell_and_exec.Dockerfile .  && docker run --rm test4

...

hello entrypoint
bure@burepool:~/test/docker_cmd_and_entrypoint$ docker build --target case4 -t test4 -f ./compare_shell_and_exec.Dockerfile .  && docker run --rm test4

...

hello entrypoint
bure@burepool:~/tes
```



그리고 init process가 `/bin/sh`가 되고 `command`는 `subcommand`가 됩니다.

시그널이 전달되지 않기 때문에 `docker stop`을 해도 `SIGTERM` 시그널을 받지 못합니다.

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

FROM base as case1
# CMD에서 shell form으로  /hello.sh를 실행한 경우

CMD /hello.sh cmd

```

```bash
bure@burepool:~/test/docker_cmd_and_entrypoint$ docker build --target case1 -t test4 -f ./init_check_init_process.Dockerfile .  && docker run --rm test4
> hello cmd
> hello cmd
> ...
> ^C hello cmd         # 인터럽트 시도, 실패
> hello cmd
> hello cmd
> ...
bure@burepool:~/test/docker_cmd_and_entrypoint$
```


## exec form

```bash
ENTRYPOINT [ "executable", "param1", "param2" ]
```

반면 `exec form`로 작성하면 컨테이너 실행 시에 `init process`로 실행합니다.

단 쉘에서 실행하는 것이 아니기 떄문에, 환경변수나 사용자 정의 설정(.bashrc)는 적용이 안됩니다.

예를 들어 `ENTRYPOINT ["echo", "$HOME"]` 를 하면 `$HOME`이 유저 디렉토리 경로로 치환되지 않고 `string`으로 들어갑니다.

그래서 전처리가 필요하다면 `ENTRYPOINT ["/bin/bash", "-c", "echo", "$HOME"]` 처럼 내용을 쉘의 인자로 넘겨주거나 `shell form`로 작성해야 합니다.


대신 `exec form`으로 작성하면 `CMD`가 `ENTRYPOINT`의 파라미터로 추가됩니다.

```bash
# 도커 파일

FROM ubuntu:22.04 as base

FROM base as case2
# Case 2-------------------------------
# ENTRYPOINT는 exec form, CMD도 exec form 으로 작성한 경우

ENTRYPOINT ["echo", " hello entrypoint"]

CMD [ "echo", "hello cmd"]

# Output 2
# >  hello entrypoint echo hello cmd
#
# ENTRYPOINT가 exec form이어서 CMD 내용이 파라미터로 추가됨
```

```bash
# 이미지 실행 결과
bure@burepool:~/test/docker_cmd_and_entrypoint$ docker build --target case2 -t test4 -f ./compare_shell_and_exec.Dockerfile .  && docker run --rm test4

...

hello entrypoint echo hello cmd

```

# 기타

`docker run ` 시에 `--entrypoint`로 재정의할 수 있습니다.