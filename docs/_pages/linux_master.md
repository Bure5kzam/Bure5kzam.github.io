---
title: 리눅스 마스터 1급 실기 리뷰
layout: single
permalink: /linux-master/
collection: linux-master
entries_layout: grid
author_profile: true
toc : true
date: 2022-07-10 11:20
summary: 
author: Bure5kzam
classes: wide
---


2022년 12월 12일, 1년 6개월의 여정 끝에 리눅스 마스터 1급을 취득했습니다.

처음에 준비기간을 1년 잡았는데 중간에 취업하고 연애하다보니 생각보다 오래 걸렸네요. 

필기 : 21.06.10 ~ 9.11 (약 3개월)

실기 : 21.04.25 ~ 11.10 (약 6개월)

<!-- # 준비 방법

필기

커트라인 : 100문제 중 60문제

OMR 마킹 4지선다 유형으로 진행됩니다.

리눅스 경험자라면 4지선다라 암기하기 쉽고 기출 변형이 적기 때문에, 문제은행 10년치만 외워도 충분하다고 생각합니다.

공부 목적일 경우 출제 영역이 넓기 때문에, 아래에 설명한 출제 영역과 본인의 사전 지식 정도를 잘 비교해서 진행하시길 바랍니다.

(참고로 제가 공부할 때 구했던 pdf 설명 + 기출 요약본이 63 페이지였습니다) -->


<!-- # 학습 방법

(추천) 
[https://hellocbt.com/](https://hellocbt.com/)에서 기출문제를 참고하며 먼저 문제 동향을 파악했습니다.

커맨드나 서비스 사용법을 찾아볼 때는 주로 아래와 같이 학습했습니다. 짧은 기간에 자격증 취득을 위해서는 적합하지 않지만, 공신력 있는 정보를 보고싶었습니다.

- man pages
- Linux Enterpirse 서비스를 제공하는 Red Hat사의 매뉴얼 참고
- MIT[Red Hat 교육자료](https://web.mit.edu/rhel-doc/4/RH-DOCS/rhel-rg-en-4/) 참고
- MIT [오픈 코스 강의](https://ocw.mit.edu/) 참고
- 모교 교육자료 참고
- geekforgeeks, w3schools, personal blog... -->


# 시험 관련 팁

## 터미널 환경 (2차)

리눅스 마스터 1급의 2차 시험의 경우, 터미널 환경이 제공됩니다. (KAIT 홈페이지 보면 실기 + 필기로 진행된다고 써져있는데 상관없이 시험내내 터미널 쓸 수 있습니다.)

다만 터미널에 모든 명령어가 설치된건 아니니, 감독관 PC 사용 허가가 떨어지면 명령어가 설치되어있는지, 매뉴얼이 있는지 먼저 확인해보시길 바랍니다.

응시자 리뷰보니 시험장 중에 인터넷 연결 된 곳도 있고 안된 곳도 있는 것 같은데, 되어있으면 설치도 가능합니다.

(named나 sendmail 같은 실기 시험용 추천)

## man 명령어 (2차)
 
리눅스 마스터는 명령어를 완전히 암기하고 응시하는 시험이 아닙니다.

명령어나 바이너리만 키워드로 암기해두고 시험에서는 매뉴얼을 참고해서 옵션이나 경로를 찾아 작성해야 합니다.

``` bash
# 쉘에서 find 명령어의 메뉴얼을 보여주는 커맨드
$ man find
```

`man` 명령어의 매뉴얼을 참고하시면 시험에 많은 도움이 됩니다.

### 메뉴얼에서 특정 섹션만 보기

`man` 커맨드의 메뉴얼 페이지를 보면 메뉴얼의 섹터 구성 정보가 있습니다.

이 중 중요한 섹터는 1, 5, 8입니다.
```bash
bure@burepool:  man man , [DESCRIPTION]<br/>
1   : 실행 가능한 프로그램 또는 쉘 명령어 <br/>   
5   : 파일 형식 그리고 규칙 (File formats and conventions, eg /etc/passwd)<br/>
8   : 시스템 관리자 명령어 (System administration commands, usually only for root)<br/>

그 외의 것들...

2   : System calls (functions provided by the kernel)<br/>
3   : Library calls (functions within program libraries)<br/>
4   : Special files (usually found in /dev)<br/>
6   : Games<br/>
7   : Miscellaneous (including macro packages and conventions), e.g. man(7), groff(7)<br/>
9   : Kernel routines [Non standard]<br/>

```

메뉴얼이 기니 필요한 섹터만 확인하시는게 좋습니다.

``` bash

# 매뉴얼의 특정 세션 보기 예시

$ man 1 find # command
$ man 5 find # config, find는 파일에 관한 매뉴얼이 없음

```


### 메뉴얼 페이지 키워드로 검색하기

이름과 설명에 키워드가 포함된 메뉴얼만 검색할 수 있습니다.

```bash

bure@burepool:~/test/docker_cmd_and_entrypoint$ man -k find 

cluster (1)          - find clusters in a graph and augment the graph with this information.
ffs (3)              - find first bit set in a word
ffsl (3)             - find first bit set in a word
ffsll (3)            - find first bit set in a word
File::IconTheme (3pm) - Find icon directories

...

```

### 매뉴얼 페이지 안에서 검색하기

 메뉴얼 페이지는 글이 많아 필요한 내용을 바로 찾기가 쉽지 않습니다. 검색하는 법을 기억해 두는 것이 좋습니다 

 man page에서 `슬래쉬 '/' + 키워드`로 찾을 수 있습니다.


## 명령어의 --help 옵션 활용하기

일부 명령어는 요약 설명인 --help 옵션을 지원합니다.

짧기 때문에 단순하게 옵션이 뭐하는 용도인지 확인할 때 유용합니다.

```bash

[root@localhost share]# cat --help
사용법: cat [<옵션>]... [<파일>]...
Concatenate FILE(s), or standard input, to standard output.

  -A, --show-all           equivalent to -vET
  -b, --number-nonblank    number nonempty output lines, overrides -n
  -e                       equivalent to -vE
  -E, --show-ends          display $ at end of each line
  -n, --number             number all output lines
  -s, --squeeze-blank      suppress repeated empty output lines
  -t                       -vT와 같음
  -T, --show-tabs          탭 문자를 ^I로 표시합니다
...
```


## find 명령어로 파일 찾기

실기문제(11번 ~ 16번)는 서비스 설정 파일 이름, 경로를 적고 내용을 채우는 유형이 나옵니다.

설정 파일 이름을 안다면 find 명령어로 경로를 찾을 수 있습니다.

 ``` bash
# find [시작 경로] [-name 이름] [-type {f | d}] 
[root@localhost ~]# find / -name *named.conf*

...

/etc/named.conf

...

```

`type` 옵션으로 디렉토리나 파일만 필터링할 수 있습니다.

## 실기 문제 위주로 공부하기

2차 시험은 필기 10 문제와 실기 6 문제가 출제되는데, 점수 비율은 실기가 더 높습니다. (필기 : 40, 실기 : 60)

같은 문제를 맞춰도 실기를 맞추는게 배점이 높습니다.

## 매뉴얼 웹에서 보기

웹에서 보면 번역기를 돌리기 쉽습니다.

`https://linux.die.net/man/5/gshadow`

# 문제 범위

제가 시험을 준비하며 공부했던 명령어의 80% 정도 기록한 것 같습니다.

(이론 제외)

보시고 대충 시험에 이런 내용들이 나오는구나 생각하시면 될 것 같습니다.

## 필기

사용자 계정 관리 명령어

- useradd
- passwd
- usermod
- chage

사용자 관리 파일

- /etc/login.defs
- /etc/skel
- etc/default/useradd

사용자 권한 관리 명령어

- chomod
- chown
- whoami
- id
- lslogin

파일 링크 관리 명령어

- ln

파일 시스템 관련 명령어

- mount, umount
- fsck
- mke2fs
- dumpe2fs
- chattr,
- lsattr
- /etc/fstab

디스크 쿼터

- quotacheck
- quota
- edquota
- blkid
- dumpe2fs
- /etc/fstab

볼륨 관련 명령어

- PV, LV

프로세스 관리 명령어

- kill
- ps
- top
- nice
- renice
- pgrep
- /proc/{PID}/exe


어플리케이션 관리 명령어

- rpm, yum
- which, whereis

빌드 명령어

- gcc

커널 관련 명령어

- lselease
- modeinfo
- uname -r

프린터

- lpr
- lp
- lpq
  
로그 관련

- logrotate
- dmesg
- lastlog
- last
- lastb
- logger

모듈 작업 명령어

- depmod
- rmmod

압축

- tar

네트워크 관련 명령어

- ssh
- nmap
- netstat

백업 관련 명령어

- dump
- dd
- cpio
- rsync

## 실기

- named 도메인 주소의 실제 IP 주소를 알려주는 도메인 네임 서버
- sendmail 메일 전달 어플리케이션
- dhcpd 동적 IP 할당 서버
- httpd 웹서버
- NIS
- xinetd
- iptables 네트워크 엑세스 테이블
- samba 파일 관리 시스템
- NFS 서버의 파일시스템을 로컬에 마운트 할 수 있도록 제공 (NFS 사용)
- firewalld
- telent
- TCP Wrapper
- htpasswd
- FTP
- KVM
- xinetd
- squid

---

취득하고 주변에 자랑해봤는데 70%는 모르는거 같습니다 ㅠ 자격증으로서의 입지는 잘 모르겠어요

임베디드랑 보안하는 사람들만 아는 시험인 것 같습니다.


<!-- # 레퍼런스

1. https://ihp001.tistory.com/65 -->