---
title: 리눅스 서버 유저 컨트롤
date: 2021-08-20 20:00:00 +/-TTTT
categories: [TIL, linux]
toc : true
comments : true
math : true
mermaid: true
---

# 리눅스 유저관리

## 유저 생성

```bash
//유저 추가
$ sudo adduser kjh
$ sudo adduser yty
$ sudo adduser har
$ sudo adduser jbj
$ sudo adduser sej

//유저가 잘 추가되었는지 확인
$ vi /etc/passwd
$ cat /etc/passwd | grep bash


//그룹 추가
$ sudo addgroup backend
$ sudo addgroup frontend

//그룹들을 유저에 그룹에 추가한다
$ gpasswd -a kjh backend
$ gpasswd -a yty backend
$ gpasswd -a har frontend
$ gpasswd -a jbj frontend

//다른 방법
$ gpasswd -M kjh yty backend
$ gpasswd -M har jbj frontend

//그룹에 잘 들어갔는지 확인
$ su kjh
$ groups
$ exit
```

## 유저 권한 관리

```/etc/sudoer```에 내용 추가

```bash

# User privilege specification
root    ALL=(ALL:ALL) ALL

//여기 추가해준다.
%backend ALL=(ALL) ALL
%frontend ALL=(ALL) ALL

# Members of the admin group may gain root privileges
%admin  ALL=(ALL) ALL

# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL
```



## SSH 연결 허용

```bash
# vi /etc/ssh/sshd_config

PermitRootLogin yes
PasswordAuthentication yes
```



## 접속중인 계정 확인

```bash
$ users
$ who
```





## 계정 배포

```bash
#change password command
$ passwd
```

| 링크                                  | 내용                      |
| ------------------------------------- | ------------------------- |
| https://jhnyang.tistory.com/10        | 유저 생성                 |
| https://blog.lael.be/post/7678        | 리눅스 서버 ssh 접속 허용 |
| https://help.ubuntu.com/community/UFW | 우분투 커뮤니티           |



