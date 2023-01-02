---
date: 2021-08-30 20:00:00 +/-TTTT
categories: [TIL, docker]
toc : true
comments : true
math : true
mermaid: true
---
# Docker compose


프로젝트에서 docker를 사용해보니 굉장히 편했다. 서비스를 독립적으로 구동할 수 있다는 것은 생각보다 어마어마한 편의성을 제공해주었다.
 하지만 CI/CD 과정에서 docker만 사용했을 때의 한계점을 느낄 수 있었는데, 이식을 컨테이너를 최신화하는 과정에 입력해야하는 스크립트가 생각보다 많았다. 급한대로 스크립트로 자동화 했지만 Docker compose를 사용할 줄 알았다면 더 쉽지 않았을까 싶다.

 [도커 공식 문서](https://docs.docker.com/compose/compose-file/compose-file-v3/)

# Dockerfile
 Docker-compose는 docekrfile을 제어하기 때문에 Dockerfile에 대해 먼저 알아야 하는 것 같다.
  Dockerfile은 이미지를 조합하기 위해 명령줄에서 호출할 수 있는 모든 명령을 포함하는 텍스트 문서이다.


 # Compose의 용도
 Compose는 스크립트를 작성해 서비스들의 종속성 최신화를 자동화 시킬 수 있다.

  만약 서버를 다른 인프라로 옮겨야 할 때, 스크립트만 있다면 docker-compose up 으로 간단하게 환경을 구현할 수 있다.

 # Compose script
 Docker compose는 사전에 유저가 작성한 docker-compose.yml을 기반으로 동작한다. 공식문서를 읽으며 docker-compose에 사용되는 단어들을 번역해 기록해보았다.

## Compose 스크립트의 기본 형태
도커 스크립트는 아래의 기본 구성을 가지고 있다. 일반 리눅스 쉘 스크립트와 같은 문법으로 환경변수도 사용할 수 있다.

[Dockerfile document](https://docs.docker.com/compose/compose-file/compose-file-v3/#context)

 ```bash
 version: "3.9" #스크립트 버전
 services: #서비스를 구동하겠다. 
    webapp: #구동할 서비스 이름
 ```

docker 이미지를 생성하는데에는 두가지 방식이 있다.

### build

- 이미지를 구동할 build 파일의 디렉토리 경로이다.
- build를 이용하면 파일을 기반으로 이미지를 컨테이너로 생성한다.
- https://docs.docker.com/compose/gettingstarted/

```bash
#docker-compose

version: "3.9"
services:
#web 서비스는 dockerfile에서 이미지를 가져와 host-machine의 포트 5000과 컨테이터의 5000포트를 바운드 시킨다.
  web: # 사용할 서비스 이름
    build: . # 로컬 파일을 컨테이너화 하는데 필요한 dockerfile의 경로
    ports:
      - "5000:5000"
#redis 서비스는 docker-hub에서 가져온 redis 프로그램의 이미지이다.
  redis:
    image: "redis:alpine" #docker-hub에서 가져올 이미지명:버전
```

아래의 명령어를 docker-compose.yml가 있는 경로에서 실행해볼 수 있다.

```bash
#이미지를 생성하고 컨테이너화 하는 명령어 (docker-compose.yml)
docker compose up

#데몬 백그라운드 실행
docker compose up -d

```

 - Context
   - 두가지 의미를 가질 수 있습니다.
     - Dockerfile이 포함된 디렉토리 경로
     - git 원격저장소의 URL
   - context에 명시된 경로는 Docker 데몬으로 전송되는 빌드 컨텍스트라고하는데 아직 의미를 모르겠다.
   - Compose는 생성된 이름으로 빌드, 태그를 지정하고 해당 이미지를 사용한다고 한다.
 - dockerfile
   - 도커파일의 이름을 명시해줍니다. 
   - context가 있으면 해당 폴더를 기반으로 찾는다.
```bash
build:
    context: .
    dockerfile: Dockerfile-alternate
```

### image

- 빌드해서 docker image화 시킬 이름입니다.
- image를 이용하면 이전에 docker images에 존재하던 이미지를 컨테이너로 생성한다.

```bash
version: "3.9"
services:
  jenkins:
  # build 디렉토리의 파일을 빌드해서 해당 이름의 iamge로 만들어라.
    ports:
      - "8081:8080"
    image: "jenkins/jenkins:latest"
```

