---
layout: single
title: 리눅스 마스터 1급 실기
date: 2022-07-10 11:20
categor: LinuxMaster
summary: 
---
취업하고 손놓아 버린 리눅스 마스터 실기...<br/>


필기 통과 기간이 만료되기 전에 서둘러 정리하기로 했습니다.


취업 후 5개월 째 윈도우만 사용해 기억이 가물가물했습니다. 명령어는 기억나지만 세부 옵션은 매뉴얼을 참고해야 했고, 서비스 파일 구성법은 아예 기억나지 않는 상태로 시작했습니다.


# 학습 기간

7.10 ~ 9.3 (3개월)

# 학습 방법

[https://hellocbt.com/](https://hellocbt.com/)에서 기출문제를 참고하며 먼저 문제 동향을 파악했습니다.

커맨드나 서비스 사용법을 찾아볼 때는 주로 아래와 같이 학습했습니다. 짧은 기간에 자격증 취득을 위해서는 적합하지 않지만, 공신력 있는 정보를 보고싶었습니다.

- man pages 활용
- Linux Enterpirse 서비스를 제공하는 Red Hat사의 매뉴얼 참고
- MIT[Red Hat 교육자료](https://web.mit.edu/rhel-doc/4/RH-DOCS/rhel-rg-en-4/) 참고
- MIT [오픈 코스 강의](https://ocw.mit.edu/) 참고
- 모교 교육자료 참고
- geekforgeeks, w3schools, personal blog...


# 시험 관련 팁

## man pages 활용하기

 리눅스 마스터 실기 시험은 명령어의 매뉴얼 페이지를 참조할 수 있습니다.
 
 ``` console

# 명령어 매뉴얼 보기
man find
```

man pages는 같은 명령어라도 섹션 마다 다른 내용을 갖고 있습니다.

> $ man man , [DESCRIPTION]
> 1   Executable programs or shell commands
> 2   System calls (functions provided by the kernel)
> 3   Library calls (functions within program libraries)
> 4   Special files (usually found in /dev)
> 5   File formats and conventions eg /etc/passwd
> 6   Games
> 7   Miscellaneous (including macro packages and conventions), e.g. man(7), groff(7)
> 8   System administration commands (usually only for root)
> 9   Kernel routines [Non standard]

``` console
# 매뉴얼의 특정 세션 보기
man 1 find # command
man 5 find # config
```

 메뉴얼 페이지는 글이 많아 필요한 내용을 바로 찾기가 쉽지 않습니다.  검색하는 법을 기억해 두는 것이 좋습니다 
 man page에서 `슬래쉬 '/' + 키워드`로 찾을 수 있습니다.


쉘에서 --help 옵션을 사용해 요약본을 볼 수도 있습니다.

```console
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

## man pgages 문법 해석하기

...


## find 명령어 활용하기

 시험의 11번~ 16번 문제는 실기 문제로, 서비스의 설정파일을 수정해 올바르게 동작하도록 하는 문제들이 주어집니다.
 
 설정 파일의 문법을 알아야 풀 수 있기 때문에 `find` 명령어로 예시 파일을 확인할 필요가 있습니다.

 ``` console
 # find {명령어} [-name 이름] [-type {f | d}] 
 [root@localhost ~]# find / -name *named.conf*
/etc/named.conf
 ```

type 옵션의 값은 디렉토리(d)와 파일 (f)을 의미합니다.

# 문제 유형

## 필기

## 실기


- [named]({% link _posts/2022-08-13-linux-master_named.md %}), 도메인 주소의 실제 IP 주소를 알려주는 도메인 네임 서버
- [sendmail]({% link _posts/2022-08-01-linux-master_sendmail.md %}), 메일 전달 어플리케이션
- [dhcpd]({% link _posts/2022-09-13-linux-master_dhcpd.md %}), 동적 IP 할당 서버
- [httpd]({% link _posts/2022-09-13-linux-master_httpd.md %}) 요청시 웹페이지를 제공하는 웹서버 (http 사용)
- NIS, 
- xinetd
- [iptables]({% link _posts/2022-08-18-linux-master_Iptable.md %})
- [samba]({% link _posts/2022-07-27-linux-master_samba.md %})
- NFS 서버의 파일시스템을 로컬에 마운트 할 수 있도록 제공 (NFS 사용)
- firewalld
- telent
- TCP Wrapper
- htpasswd
- FTP
- KVM
- xinetd
- squid

# 레퍼런스

1. https://ihp001.tistory.com/65