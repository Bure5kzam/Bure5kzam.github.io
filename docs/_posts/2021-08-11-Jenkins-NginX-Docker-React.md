---
title: CI구축 (Jenkins, NginX, Docker, React)
categories: [TIL, devops]
toc : true
comments : true
math : true
mermaid: true
---

# CI구축 (Jenkins, NginX, Docker, React)

## 동작 단계

1. git에 push하면 webhook을 통해 jenkins가 이벤트를 받는다
2. 트리거가 동작된 jenkins가 파이프라인을 동작시킨다
3. 빌드 결과를 전송하고  성공시 도커 허브에 이미지를 배포한다.
4. 테스트서버에 3.에서 배포한 도커 이미지를 pull하고 새 컨테이너를 실행한다.

# docker 세팅

```bash
sudo docker pull nginx
sudo docker pull mongodb/server
```



## 리액트 프로젝트 생성

```bash
# react 프로젝트가 따로 없는경우 생성
npm create-react-app myapp

cd myapp
```

## dockerfile 생성
```bash
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

## nginx 설정파일 (conf/confd.d/default.conf) 작성
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


## docker 제어를 위한 스크립트 작성

jenkins와 docker compose에 좀더 익숙했더라면 더 세련되게 할 수 있었을텐데 아쉽습니다

```bash
!/bin/bash
  
cd S05P13A109/frontend/
sudo docker stop sfens-nginx
sudo docker rmi sfens-nginx
sudo docker rm sfens-nginx

git pull origin develop

docker build -t sfens-nginx .
docker run -d -name sfens-nginx -p 80:80 sfens-nginx
```

# jenkins 설정

| 링크                                                                   | 내용                  |
| ---------------------------------------------------------------------- | --------------------- |
| [개발자는 코딩만 CI 구축](https://crowdreport-defender.tistory.com/21) | 관련 내용 리뷰        |
| [오픈소스 컬설팅](https://tech.osci.kr/2020/01/16/86039236/)           | gitlab + jenkins      |
| [](https://medium.com/hgmin/jenkins-github-webhook-3dc13efd2437)       | github + webhook push |

