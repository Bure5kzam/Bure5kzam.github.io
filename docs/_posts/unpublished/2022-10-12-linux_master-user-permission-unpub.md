---
layout: single
title: 우분투 파일의 권한 및 속성
date: 2022-09-23 21:55
category: LinuxMaster
summary: 
---

# Overview

## 권한

<!-- mode는 2가지 포맷이 있습니다.

- numeric mode
- symbolic mode mask -->

## 특수 권한

<!-- 일반 권한은 사용자, 그룹, 기타유저의 엑세스 컨트롤을 설정할 수 있다고 설명드렸습니다.  이외에도 `special permission`은 추가 권한을 부여할 수 있습니다. -->
`special permission` 으로 제 3자에 대한 권한을 부여할 수 있습니다.

### SetUID

`SetUID` 비트가 설정된 파일은 누구나 소유주의 권한으로 실행할 수 있게됩니다.

예를들어 `passwd` 파일 root 소유의 파일이지만 `SetUID` 비트가 설정되어 누구나 실행할 수 있습니다.

```console
[root@local ~]$ ls -l /usr/bin/passwd 
-rwsr-xr-x. 1 root root 33544 Dec 13  2019 /usr/bin/passwd
```

유저 실행 권한을 보면 `x`가 있어야 할 자리에 `s`가 있으며, 이는 `SetUID`입니다.
<!-- 파일 소유자도 실행 권한이 없으면 대문자 `S`를 사용해야 합니다. -->

### SetGID

SetGID는 두 가지 기능이 있습니다

- 파일에 설정되면, 파일 소유 그룹인 것 처럼 실행할 수 있습니다. (SetUID)
- 디렉토리에 설정되면, 내부 생성되는 파일들의 소유 그룹이 디렉토리의 소유그룹으로 설정됩니다. 이는 그룹 멤버들의 협업용 디렉토리에 사용할 수 있습니다. 새로 생성되는 파일에 그룹 멤버들이 엑세스할 수 있게 됩니다. 


### Other(Special permission)

기타 권한에는 특수 권한을 지정하는 비트를 지정할 수 있는데, 비트의 종류가 대상 파일의 타입에 따라 기능이 달라집니다.

- 디렉토리일 경우, `restricted deletion bit`로 설정됩니다. 권한 없는 유저가 디렉토리 내부 파일을 제거하거나 이름을 수정하는 것을 방지합니다.
- 파일일 경우, `stickybit`로 설정됩니다. `sticky bit`는 파일의 프로그램의 텍스트 이미지를 swap device에 저장해 실행 시 빠르게 로드할 수 있도록 합니다.

> $ man 1 chmod, [RESTRICTED DELETION FLAG OR STICKY BIT] 
> The restricted deletion flag or sticky bit is a single bit, whose  interpretation  depends on the file  type. For directories, it prevents unprivileged users from removing or renaming a file in the directory unless they own the file or the directory; this is called the restricted deletion  flag  for  the  directory,  and is commonly found on world-writable directories like /tmp.  For regular files on some older systems, the bit saves the program's text image on  the swap device so it will load more quickly when run; this is called the sticky bit.


```console
[root@local ]$ ls -ld /tmp/
drwxrwxrwt. 15 root root 4096 Sep 22 15:28 /tmp/
```

기타 실행 권한이 `x` 대신 `t`로 나타납니다.



# Command

## $ umask

유저의 `file-creation mask`를 설정합니다.

```console
umask [-p] [-S] [mode]
```

## $ chmod

 `chmod`는 파일의 `file mode bit`를 바꾸는 커맨드입니다.

```console
chmod [OPTION]... MODE[,MODE]... FILE...            # mode에 따라 파일의 mode bits를 변경
chmod [OPTION]... OCTAL-MODE FILE...                
chmod [OPTION]... --reference=RFILE FILE...
```

`chmod` 문법은 권한을 기호로 지정하는 `symbolic mode` 문법과 8진법을 사용하는 `numeric method` 문법이 있습니다.

### Symbolic mode

심볼릭 모드는 권한을 지정할 때 대상, 제어, 권한을 지정합니다.

``` console
chmod [ugoa][[+-=][perms...]...]입니다.

[root@local ~]$ chmod ug+rw test.txt            # 유저, 그룹에게 읽기 쓰기 권한을 추가
```

**ugoa**

제한하려는 유저 종류를 지정합니다. 0개 이상의 조합으로 사용합니다.

| 문자 | 이름                           |
| ---- | ------------------------------ |
| u    | 파일을 소유한 유저             |
| g    | 파일의 소유 그룹에 속하는 유저 |
| o    | 파일 그룹에 속하지 않는 유저   |
| a    | 모든 유저                      |

`ugoa` 중 아무것도 주어지지 않으면 `a`을 지정한 것 처럼 동작하지만 `umask`의 `file-creation mode`에 설정된 유저는 설정되지 않습니다.

> $ man 1 chmod, [DESCRIPTION]
> If none of these are given, the effect is as if a were given, but bits that are set in the umask are not affected.

**operator**

연산자를 지정해 권한을 추가, 제거, 지정합니다. 하나 이상의 `perms`가 필요하고 `,`로 `operator` + `perms` 조합을 여러개 지정 할 수 있습니다.

`+`는 선택된 파일의 file mode bit에 비트를 더합니다. <br/>
`-`는 비트를 제거합니다. <br/>
`=`는 디렉토리의 언급되지 않은 setuid와 setgid가 영향을 미치는 경우를 제외하고, 비트를 더하거나 제거합니다. <br/>

**perms**

`rwxXst` 중 하나 이상, 또는 `ugo` 중 하나 이상입니다.

**rwxXst**

| 문자 | 의미                                              |
| ---- | ------------------------------------------------- |
| r    | 읽기 권한                                         |
| w    | 쓰기 권한                                         |
| x    | 실행 (디렉토리는 검색) 권한                       |
| X    | 디렉토리거나 실행 권한이 있는 파일일 때 실행/검색 |
| s    | `special permission` (SetUID, SetGID)             |
| t    | 제한된 삭제 플래그 또는 고정 비트                 |

> $ man 1 chmod, [DESCRIPTION]
> The  letters rwxXst select file mode bits for the affected users: read (r), write (w), execute (or search for directories) (x), execute/search only if the file is a directory or already has execute  permission for some user (X), set user or group ID on execution (s), restricted deletion flag or sticky bit (t).  

이 문자열들 대신 `ugo` 중 하나 이상을 사용할 수도 있습니다.

| 문자 | 의미                                             |
| ---- | ------------------------------------------------ |
| u    | 파일을 소유한 유저에게 허가된 권한               |
| g    | 파일을 소유한 그룹에 포함된 유저에게 허가된 권한 |
| o    | 위 두개에 포함되지 않는 유저에게 인가된 권한     |


### Numberic mode

`numberic mode`는 1~4자리의 8진수 숫자로 구성됩니다. 각 숫자는 4, 2, 1 값이 의미를 가지며, 생략된 숫자는 0으로 가정합니다.

| 순서(큰 수부터) | 4                            | 2                            | 1                            |
| --------------- | ---------------------------- | ---------------------------- | ---------------------------- |
| 1               | SetUID                       | SetGID                       | restricted deletion / sticky |
| 2               | (파일 소유자의) 읽기 권한    | 쓰기 권한                    | 실행 권한                    |
| 3               | (파일 소유 그룹의) 읽기 권한 | (파일 소유 그룹의) 쓰기 권한 | (파일 소유 그룹의) 실행 권한 |
| 4               | (기타 유저의) 읽기 권한      | (기타 유저의) 쓰기 권한      | (기타 유저의) 실행 권한      |


```console
[root@local ~]$ chmod ### file | directory
```


### SETUID와 SETGID BITS

일반파일을 제어할 때, 아래의 경우 파일의 SetGID를 제거합니다.

- 유저가 적절한 권한이 없고, 파일의 GID가 유저가 속한 GID에 매치되지 않는 경우

그리고 `restriction`은 MODE나 RFILE의 SetUID와 SetGID 비트를 무시하도록 합니다. 이 행위는 정책과 chmod 내부 기능에 의해 수행됩니다.

<br/>

디렉토리를 제어할 때, 명시적으로 수정하지 않는 한 SetUID,SetGID를 보존합니다. 

``` consol
[root@local ~] chmod u+s text.txt
[root@local ~] chmod g-s text.txt
```

### Symbolic link

`chmod`는 `symbolic link` 파일의 권한을 제어하지 않습니다. 커맨드에서 명시된 파일들은 심볼릭 링크가 가리키는 파일의 권한을 수정하고, `recursive direcory` 과정에서 마주한 심볼릭 파일들은 무시합니다.



# Reference

[red hat : linux permissions: suid, sgid, stikky bit](https://www.redhat.com/sysadmin/suid-sgid-sticky-bit)