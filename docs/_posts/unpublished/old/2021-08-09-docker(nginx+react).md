---
title: nginx + react 도커 환경에서 호스팅하기
date: 2021-08-2 20:00:00 +/-TTTT
categories: [TIL, devops]
toc : true
comments : true
math : true
mermaid: true
---
# What is nginx?

[nginX란](https://www.nginx.com/resources/glossary/nginx/)

> **NGINX** 는 웹 서비스, 역방향 프록시, 캐싱, 로드 밸런싱, 미디어 스트리밍 등을 위한 오픈 소스 소프트웨어입니다. 최대 성능과 안정성을 위ㅇㄴ해 설계된 웹 서버로 시작했습니다. HTTP 서버 기능 외에도 NGINX는 이메일(IMAP, POP3 및 SMTP)용 프록시 서버와 HTTP, TCP 및 UDP 서버용 리버스 프록시 및 로드 밸런서로도 사용할 수 있습니다.

nginX를 프록시 서버로 사용하면 웹서버와 웹 브라우저 사이에서 캐싱, 로드밸런싱, 스트리밍 기능이 가능하다고 한다.

### 

트래픽이 많은 웹사이트를 위해 확장성을 위해 설계한 비동기 이벤트 기반구조의 웹서버 소프트웨어입니다.

# docker nginX 이미지에 react 호스팅하기

## react 빌드 파일 생성

```bash
#root로 react 프로젝트 루트폴더로 이동
$ cd /myapp

#react build 파일 생성
$ npm run build


```

## dockerfile 생성

Docker 이미지 생성시 초기 설정을 담은 Dockerfile 파일을 생성

```bash

$ vi Dockerfile

//Dockerfile

# nginx 이미지를 사용합니다. 뒤에 tag가 없으면 latest 를 사용합니다.
FROM nginx

# root 에 app 폴더를 생성
RUN mkdir /app

# work dir 고정
WORKDIR /app

# work dir 에 build 폴더 생성 /app/build
RUN mkdir ./build

# host pc의 현재경로의 build 폴더를 workdir 의 build 폴더로 복사
ADD ./build ./build

# nginx 의 default.conf 를 삭제
RUN rm /etc/nginx/conf.d/default.conf

# host pc 의 nginx.conf 를 아래 경로에 복사
COPY ./nginx.conf /etc/nginx/conf.d

# 80 포트 오픈
EXPOSE 80

# container 실행 시 자동으로 실행할 command. nginx 시작함
CMD ["nginx", "-g", "daemon off;"]
```

## nginx.conf 파일 생성

docker 내 컨테이너에서 실행되는 nginx의 설정을 명시하는 파일

```bash
$ vim nginx.config
```

```bash
#nginx.config

server {
    listen 80;
    location / {
        root    /app/build;
        index   index.html;
        try_files $uri $uri/ /index.html;
    }
}
```



## docker image 생성

```bash
# docker images 생성
$ docker build -t sfens-nginx .

# docker run
$ sudo docker run -d --name sfens-nginx -p 80:80 sfens-nginx
```



# react 페이지 갱신하기

```bash
#최신 이미지 받아오기
$ git pull origin 

#react파일이 반영된 docker image 생성
$ docker build -t sfens-nginx .

#컨테이너 재생성
$ sudo docker run -d --name sfens-nginx -p 80:80 sfens-nginx
```



| 링크                                                                                             | 내용                                                        |
| ------------------------------------------------------------------------------------------------ | ----------------------------------------------------------- |
| [Dockerfile official documents](https://docs.docker.com/engine/reference/builder/)               | dockerfile 작성 공식문서                                    |
| [Docker(nginx + react)](https://velog.io/@esc/React-Docker)                                      | docker로 nginx를 실행하고 react 파일을 옮겨 빌드하는 내용   |
| [nginx로 react 배포하기](https://www.hanumoka.net/2019/12/29/react-20191229-react-nginx-deploy/) |                                                             |
| [nginx.org](https://www.nginx.com/resources/glossary/nginx/)                                     |                                                             |
| https://nginx.org/en/docs/                                                                       | nginX documents                                             |
| https://lynlab.co.kr/blog/89                                                                     | [ Golang] Docker 멀티 스테이지 빌드로 한 큐에 이미지 만들기 |

