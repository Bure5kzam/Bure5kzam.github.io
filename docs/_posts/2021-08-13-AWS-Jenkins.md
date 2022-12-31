---
title: AWS에서 jenkins 환경 구축하기
date: 2021-08-2 20:00:00 +/-TTTT
categories: [TIL, devops]
toc : true
comments : true
math : true
mermaid: true
---
# Jenkins



숱한 커밋 충돌과 브랜치 통합,, pull후 해일처럼 밀려오는 에러로그를 보고 있으면 CI/CD의 필요성을 절실하게 느끼게 되는 것 같다.

Jenkins를 이용하면 저장소의 특정 브랜치에 push 했을 때 서버에 배포가 자동으로 되도록 설정할 수 있다.



## CI / CD 

CI / CD는 continuous Integration / continuous Delivery 

지속 통합, 시속 배포를 의미한다.



전통적인 SW 개발방식인 Waterfall 모델을 보자

```
요구사항 수집 => 분석 => 설계 => 개발 => 단위 테스트 => 통합 시스템 테스트 => 릴리즈
```

 만약 각 단계를 성공적으로 거쳐서 릴리즈를 완료하면 어플리케이션 사용이 가능하지만, 현실은 그렇게 이상적이지 않다. 중간 과정에서 놓친 실수가 있거나 클라이언트의 요구사항의 변동이 생기면 이를 수용하기 어렵다.



시작과 끝이 있는 Waterfall 모델과 달리 (One time), Agile 개발 방법론은 시작과 끝을 끊임없이 반복하며 소프트웨어의 질을 개선해 나간다.

```
요구사항 분석 => 설계 => 개발 => QA (반복)
```

 Agile 에서는 위 활동을 주기적으로 반복하게 되는데, 이를 스프린트라고 한다.

 스프린트를 반복하고 배포하는 행위는 Waterfall를 계속해서 반복하는 것과 유사하기 때문에 업무 양이 상당히 많아진다.

 하지만 태스크를 잘 살펴보면 반복되는 행위가 많기 때문에, 이를 자동화해 성공적인 Agile 모델을 적용할 수 있다.

Jenkins는 CI/CD 파이프라인 제공하는 툴로, 애플리케이션 개발/통합 과정을 자동화해준다.



# 사용

## 설치

### 베어 인스톨

jenkins는 자바 기반으로 제작되어있다. 하지만 버전 JRE 8과 11만 지원한다고 한다.

### 컨테이너 활용

https://hub.docker.com/_/jenkins

Jenkins는 공유저장소를 모니터링하다가 커밋이 수행되면 동작한다.

1. 커밋된 코드를 빌드해본다.
2. 커밋된 코드에서 오류발생 시 커밋을 취소하고 알린다.
3. 에러가 없으면 사용자가 작성한 script에 따라 단위 테스트를 실행한다.
4. 단위 테스트에 성공하면 커밋을 통과시킨다.
5. ​


#### Jenkins 이미지 불러오기

docker-hub에 업로드 되어있는 jenkins 이미지를 가져와 docker로 컨테이너화 시킬 수 있다.

https://hub.docker.com/_/jenkins

```bash
$ docker pull jenkins

$ docker volume create sfens-jenkins

$ docker run -d --name sfens-jenkins -p 8000:8080 -v sfens-jenkins:/var/jenkins_home jenkins/jenkins

$ docker logs sfens-jenkins

```

브라우저로 8080에 접속한 후 docker logs에서 발급된 키를 복사하면 jenkins를 구동할 수 있다.

이로서 jenkins 컨테이너 환경을 구성했다.



#### Jenkins 이미지 불러오기 2

위 방법은 jenkins 이미지만 불러오는 방법이다.

컨테이너를 빌드하면 dist 폴더를 전송해 EC2에서 바로 실행할 수 있는데, 이를 위해 하나의 컨테이너 환경에 jenkins외에도 다른 미들웨어들을 포함하려면 다른 방법을 활용해야한다.



```bash
#Dockerfile
# 로직 1. 메인 컨테이너로 활용할 jenkins 컨테이너 불러오기
From jenkins/jenkins

# 로직 2. 추후 EC2에서 사용할 수 있게 AWS 인증파일을 미리 심어둔다.
COPY [].pem /var/jenkins_home #AWS 접근을 위해 인증파일을 복사
USER root #! root 권한으로 바꾸는 명령어
RUN chmod 600 /var/jenkins_home/[].pem # 인증파일 권한 변경

# 로직 3. npt 저장소 업데이트
RUN apt-get update 
RUN apt-get install -y python
RUN apt-get install -y npm



```

```bash
#shell

# dockerfile에 작성된 내용대로 컨테이너 이미지를 생성
docker build -t jinkins:1.0

# 생성된 이미지를 확인
docker images

#docker 이미지를 컨테이너로 실행, jenkins에 접속할 8080 포트와 원격 접속 백도어 50000을 개방
docker run -d --name jenkins -p 8080:8080 -p 50000:50000 jenkins:1.0
```



이제 이 이미지를 EC2 환경에서 컨테이너로 실행해보자.

credential : 타 시스템에 접속하는데 활용하는 계정들을 가지고 있다.

gitlab에 접속할 username과

gitlab의 패스워드,

시스템에 access할 때 사용할 ID를 입력해주면

입력한 ID로 access가 생성된다.



Jenkins의 새로운 아이템 만들기

설정할게 많은 Freestyle와 다르게 Pipeline을 활용하면 스크립트 하나로 

사용하기 쉽다.



```bash

#stage에는 하나의 steps과 하나의 post이 있음
#steps 안에는 여러개의 명령어를 줄 수 있음.

#pipeline {
  agent any
  
  stages {
    stage('prepare') {
    # 준비단계에서 수행할 동작들을 steps에 정의
      steps {
      # jenkins에서 자체적으로 가지고 있는 명령어들이다.
        git credentialsId: 'id', url: 'http...url' # git 저장소에 접근하는 명령어. jenkins credentials에 등록된 Id와 접속할 url이 입력하면 저장소의 내용들을 jenkins가 구동되고 있는 컨테이너로 pull 한다.
        
        sh 'npm install' # 컨테이너에서 실행할 쉘 스크립트 명령어이다.
      }
    }
    stage('build') {
      steps {
      # build 단계에서 수행할 동작들을 정의
        sh 'npm run build'
      }
      post {
      # steps가 성공적으로 동작했을 경우 동작할 내용들을 정의
        success {
          sh "scp -i /var/jenkins_home/[].pem \
          -q \
          -o StrictHostKeyChecking=no \
          -r ./dist/ hostname:/home/ubuntu/test" 
          #! scp -i 인증파일을 이용해 -r에 적힌 ec2 서버에 접속해서 build로 생성된 ./dist 폴더 내의 모든 파일들을 복사해줘라
          
          echo "Success"
          #성공이라고 출력해줘라
        }
      }
    }
  }
}
```

위 스크립트를 pipeline script에 복사하면 스크립트를 적용할 수 있다.

jenkins는 [여러 문법](https://www.jenkins.io/doc/book/pipeline/syntax/)을 가지고 있다. 위는 Declarative 방식의 문법이다.

Declarative에 사용되는 step 명령어들은 [여기](https://www.jenkins.io/doc/pipeline/steps/workflow-basic-steps/)서 확인할 수 있다.



사이드바의 BuildNow를 클릭하면 git으로 pull해서 컨테이너에서 dist 폴더를 복사할 것이다.

Build history에 console output을 통해 빌드 결과를 확인할 수 있다.



## Trigger 연동

위 까지 진행했으면 , build now 버튼을 눌러 스크립트 Ec2에 저장된 파일이 갱신되는 것을 확인할 수 있다.

여기서 그치지 않고 Gitlab과 hook을 연동하면, Gitlab에서 커밋이 일어날 때마다 스크립트를 실행할 수 있도록 설정할 수 있다.

 workflow를 구성하고 배포 브랜치에 커밋이 이루어지면 배포 서버에 파일이 갱신되는 방식으로 활용한다.



### ngrok 설정

git repository에서 hook하면 , jenkins 서버의 trigger가 동작하도록 하려한다.

 하지만 jenkisn서버에 접근할 수 있는 도메인 네임이 있어야 github에서 접근할 수 있는데, 이를 가능토록 만들어주기 위해 Ngrok를 활용할 것이다.



ngrok은 외부 접속 도메인을 생성해준다.



#### ngrok 설치

1. https://ngrok.com/
2. https://ko.linux-console.net/?p=701
3. https://galid1.tistory.com/743

```bash
#ngrok
http [port]
```



#### Plugin 설치하기

Trigger를 사용하기 위해 플러그인을 설치하자.

Blue Ocean은 필수는 아니나 번거로운 작업을 단축시켜주고 GUI를 제공해준다.

| 이름       | 내용                                                                                                                                                                                                                      |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Blue Ocean | [Blue ocean](https://www.jenkins.io/doc/book/blueocean/getting-started/)은 jenkins에서 공식적으로 권장하는 플러그인이다. <br />Git 자격요건만 증명하면 Git과 손쉽게 연동<br />pipeline GUI 지원<br />SCM와 pipeline 연동. |
| github     | github web을 받기 위한 플러그인                                                                                                                                                                                           |
| gitlab     | gitlab의 웹훅을 사용할 수 있도록 하는 플러그인                                                                                                                                                                            |

#### webhook 추가

Payload URL : https://{ngrok https domain}/github-webook/



#### blueoccean에 새 파이프라인 생성





# 참조 & 수정내용

1. scp 명령어

2. jenkins User 명령어. 비밀번호같은건 없나??

   ​

| 링크                                                                                                                                                      | 설명                                     |
| --------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------- |
| [Devops 2021: The best Jenkins Plugins to have in 2021](https://medium.com/devopscurry/devops-2021-the-best-jenkins-plugins-to-have-in-2021-b015189a19b5) | jenkins plugin의 동작과정, 플러그인 추천 |
| [builder](https://lynlab.co.kr/blog/89)                                                                                                                   |                                          |
| [카카오 기술 블로그](https://devblog.croquis.com/ko/2018-02-27-1-jenkins-job-styles/)                                                                     | 젠킨스 문법, blue ocean                  |

