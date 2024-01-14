---
title: ssh 키 생성해서 github에 등록하기
date: 2023-12-09 16:19
---

> docs.github.com : [Generating a new SSH key and adding it to the ssh-agent](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)

> docs.github.com : [Adding a new SSH key to your GitHub account](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)

## 으읅,,,ssh키가 필요하다

2021년 7월 13일 부로 패스워드 인증 방식에 대한 지원이 사라졌다고 한다...

```bash
bure@burepool:~/.cfg$ git push -u origin master
Username for 'https://github.com': bure5kzam
Password for 'https://bure5kzam@github.com': 
remote: Support for password authentication was removed on August 13, 2021.
remote: Please see https://docs.github.com/en/get-started/getting-started-with-git/about-remote-repositories#cloning-with-https-urls for information on currently recommended modes of authentication.
fatal: https://github.com/Bure5kzam/dotfile.git/에 대한 인증이 실패하였습니다

```

ohno!!!!!!!!

의도치않은 포맷도 어지러운데...


## ssh 생성

```bash
# ssh 키 생성 커맨드, 암호 알고리즘과 라벨을 지정
bure@burepool:~/.cfg$ ssh-keygen -t ed25519 -C "bure5kzam@gmail.com"

Generating public/private ed25519 key pair.
# 키 생성 경로 및 파일 이름
Enter file in which to save the key (/home/bure/.ssh/id_ed25519): /home/bure/.ssh/github_ed25519_231209
# 키 사용시 2차 암호
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
# 결과
Your identification has been saved in /home/bure/.ssh/github_ed25519_231209
Your public key has been saved in /home/bure/.ssh/github_ed25519_231209.pub
The key fingerprint is:
# 키의 fingerprint
SHA256: ... bure5kzam@gmail.com
# 키의 fingerprint 이미지
The key's randomart image is:
+--[ED25519 256]--+
|                 |
|                 |
|                 |
|                 |
|                 |
|                 |
|                 |
|                 |
|                 |
+----[SHA256]-----+


```

키 쌍에 사용할 파일 이름과 경로만 지정해줬다.

password의 경우 지정하면 도커 컨테이너 내부에서 사용할 때 제약이 있어 지정하지 않았다.

### fingerprint

fingerprint는 키를 생성할 시 함께 생성되는 해쉬 값이다.

사이트에 공개 키와 함께 등록하며, 인증을 요청할 때 같이 검사한다.

키의 지문이 다르면, 통신 과정이나 키의 생성 과정에서 위변조된 파일로 의심받을 수 있다.

통신에서 오류 검출을 위해 사용하는 `패리티 비트`와 유사하다고 생각하면 된다.

아래에 출력되는 그림은 fingerprint를 시각화한 래터링 이미지다.

### 공개키와 비밀키

생성 경로를 확인해보면 파일 두개가 추가된걸 볼 수 있다.

```bash
bure@burepool:~/.cfg$ ls -al ~/.ssh/
합계 16
drwx------  2 bure bure 4096 12월  9 16:28 .
drwxr-x--- 25 bure bure 4096 12월  9 16:46 ..
-rw-------  1 bure bure  411 12월  9 16:28 github_ed25519_231209
-rw-r--r--  1 bure bure  101 12월  9 16:28 github_ed25519_231209.pub

```

권한을 보면 `github_ed25519_231209`는 모두 내용을 확인할 수 있는 반면, `github_ed25519_231209.pub`은 생성자만 읽고 쓸 수 있음을 알 수 있다.

학교에서 교수님이 암호키 쌍을 종종 자물쇠에 비교하시곤 했다. 공개키인 `~.pub`을 자물쇠, 비밀키는 열쇠로.

지나가는 사람들에게 자물쇠를 나눠주면 아무것도 할 수 없지만 열쇠를 주면 자물쇠를 다 따버릴 것이다.

내 메세지를 누구나 공캐키로 암호화 할 수 있지만, 암호를 복호화하려면 비밀키가 필요하다.

깃허브에 내 공개키를 등록해두면 깃허브는 내 공개키를 이용해 암호화해서 데이터를 보내며, 이를 해석할 수 있는건 개인키를 가진 내가 된다.

## ssh-agent 실행

```bash
bure@burepool:~/.cfg$ eval "$(ssh-agent -s)"
Agent pid 84715

```

ssh 인증을 요청하는 ssh-agent를 실행한다.

### 부연 설명

인증 프로그램을 로컬에서 실행해 사용할 수도 있지만, 인증 서버를 두고 여러 컴퓨터가 인증 정보를 요청해서 사용하기도 한다.

`-s` 옵션은 인증에 사용할 정보를 가져오는 명령어로, 실행해보면 쉘에서 실행할 수 있는 스크립트를 반환한다.

```bash
bure@burepool:~/.cfg$ ssh-agent -s
SSH_AUTH_SOCK=/tmp/ssh-XXXXXXJLQTfA/agent.85282; export SSH_AUTH_SOCK;
SSH_AGENT_PID=85283; export SSH_AGENT_PID;
echo Agent pid 85283;

```

agent와 인증 소켓으로 사용할 환경 변수 정보가 있다.

`eval`은 명령어 실행 결과를 현재 쉘에 적용하는 명령어다.


## github에 공개키 등록하기

`https://github.com/settings/keys` 에 공개키를 등록한다.

```bash
bure@burepool:~/.cfg$ cat ~/.ssh/github_ed25519_231209.pub 
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIPAAGvUlcMIxesJM/w5HAx3OearSPSgDT4zntS52pjru bure5kzam@gmail.com
```

키로 인증이 잘 되는지 쉘에서 미리 확인해볼 수 있다.

```bash
bure@burepool:~/.cfg$ ssh -T git@github.com
The authenticity of host 'github.com (20.200.245.247)' can't be established.
ED25519 key fingerprint is SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'github.com' (ED25519) to the list of known hosts.
Hi Bure5kzam! You've successfully authenticated, but GitHub does not provide shell access.

```