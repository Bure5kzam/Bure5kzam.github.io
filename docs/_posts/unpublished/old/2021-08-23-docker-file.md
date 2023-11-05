---
title: Dockerfile로 컨테이너 빌드하기
date: 2021-08-2 20:00:00 +/-TTTT
categories: [TIL, docker]
toc : true
comments : true
math : true
mermaid: true
---
# Dockerfile
  dockerfile은 Docker 이미지를 생성하는 docker build 명령어에 사용되는 파일이다.
   
# Docker build
docker build는 Dockerfile이나 context로부터 이미지를 빌드하는 명령어다. (context는 build할 대상이 있는 루트 폴더)

  - PATH 나 URL
  ```bash
  # build 시 기본적으로 병시된 path에 있는 dockerfile을 이용하지만 -f 옵션으로 명시하지 않고 사용 가능.
  docker build -f /path/to/a/Dockerfile .

 # build 시 명시된 패쓰로 이미지를 바로 생성하는 명령어
  docker build -t image path
  ```
 ```bash
  # build 시 기본적으로 병시된 path에 있는 dockerfile을 이용하지만 -f 옵션으로 명시하지 않고 사용 가능.
  docker build -f /path/to/a/Dockerfile .

 # build 시 명시된 패쓰로 이미지를 바로 생성하는 명령어
  docker build -t image path
  ```

  # Dockerfile 작성법
- FROM
  - 이미지를 빌드할 상위 이미지를 명시.
  - 도커파일은 FROM에서 부터 시작해야한다.
- Parser   
  - Parser는 Dockerfile의 최상단에 # nave=value 식으로 작성된다.
  - Dockerfile이 진행될 때 사용할 설정값을 지정해준다.
  - [오류의 여지가 다분하다 ](https://docs.docker.com/engine/reference/builder/)
  - # escape=`
- Environment replacement
  - 환경변수를 사용할 때 기본값을 주는 문법
  - ${variable:-word} : variable이 없으면 word를 리턴
  - ${variable:+word} " variable이 있으면 word를 리턴, 없으면 빈 string값