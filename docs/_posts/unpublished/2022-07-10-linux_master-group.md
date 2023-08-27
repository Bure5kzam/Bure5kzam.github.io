---
layout: single
title: 우분투 그룹
date: 2022-07-10 12:25
category: LinuxMaster
---
# Group 개요

리눅스에서는 한 유저가 여러 그룹에 속할 수 있습니다.

## group의 구분

리눅스에서 유저는 `primary group`과 `second group`을 가질 수 있습니다.

### primary group

파일 생성 시 파일의 `own group`으로 지정되는 그룹들입니다.

 처음 유저를 생성하면 `primary group`이 UID로 지정됩니다. (옵션 없을 경우)


### secondary group

유저가 속한 그룹 중 `primary group`이 아닌 그룹들입니다.

 파일에 접근할 때 `own group`이 유저의 `primary group` 이나 `secondary group` 이면 권한을 휙득합니다.


`primary group`이 `secondary group`보다 유달리 특별한 권한을 갖는건 아니며, 파일의 `own group`으로 지정할 그룹이 필요하기 때문입니다. 

> [Oracle, Unix group](https://docs.oracle.com/cd/E19253-01/817-1985/userconcept-35906/index.html)

# 관련 파일

시스템에서 그룹에 대한 정보는 `/etc/group`과 `/etc/gshadow` 파일에서 관리합니다.

## /etc/group

시스템 내의 그룹과, 속한 유저들을 관리합니다.

```console
# 각 필드는 :로 구분됩니다.
Group Name:Password:GID:UserId1, UserId2...
```

## /etc/gshadow

그룹 계정을 위한 암호화된 정보를 관리하는 계정입니다.

일반 유저는 접근할 수 없으며, 

`man 5 gshadow` 매뉴얼에서 필드별 의미를 확인할 수 있습니다.

```console
Group name : Encrypted password : ADMINISTRATOR : MEMBERS
```

**Group name**

`/etc/group`에 존재하는 그룹 명이어야합니다.

**Encrypted password**

그룹 구성원이 아닌 계정이 그룹 권한을 얻을 때 사용합니다.

암호화된 패스워드입니다. `!`나 `*`의 경우, 현재 사용할 수 없음을 의미합니다.

**administrator**

그룹의 패스워드를 수정할 수 있는 계정입니다.

**Members**

패스워드 없이 로그인할 수 있는 계정입니다.


# 명령어



## $gpasswd

그룹의 관리자와 멤버를 설정하는 명령어입니다.(`/etc/gshadow`를 수정)<br/>
passwd 접미사를 갖는 명령어는 shadow파일과 많은 관련이 있습니다.<br/>
(`/etc/gshadow`에는 그룹 이름과 gid, 그룹 관리자와 멤버 정보가 있습니다.)

```console
gpasswd [option] group

[root@localhost ~]# head -3 /etc/group
root:x:0:
bin:x:1:
daemon:x:2:
[root@localhost ~]# gpasswd root -a linuxMaster
사용자 linuxMaster을(를) root 그룹에 등록 중
[root@localhost ~]# head -3 /etc/group
root:x:0:linuxMaster
bin:x:1:
daemon:x:2:
```

`gpasswd`는 많은 옵션을 갖고 있습니다.

**관리자 및 멤버 관리 명령어**

gpasswd 명령어로 그룹의 관리자와 멤버를 설정할 수 있습니다.
이 옵션들은 단독으로만 사용됩니다.

| 주요 옵션                  | 내용                             |
| -------------------------- | -------------------------------- |
| -A, --administrator _user_ | 관리자 유저 리스트를 설정합니다. |
| -M, --member _user_        | 멤버 유저 리스트를 설정합니다.   |


**비밀 번호 관리 명령어**

| 주요 옵션             | 내용                                                                                                                       |
| --------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| -r, --remove-password | group 비밀번호를 제거합니다. 그룹 비밀번호가 empty기 되며, 그룹 맴버만이 `newgrp` 명령어로 그룹에 가입할 수 있습니다.      |
| -R, --restirct        | 그룹으로의 접근을 제한합니다. 그룹 비밀번호가 `!`가 됩니다. 비밀번호를 아는 유저만이 `newgrp`로 그룹에 합류할 수 있습니다. |
| _group_               | 그룹의 비밀번호를 변경합니다. administrator일때만 가능합니다.                                                              |


**유저 관리 명령어**

/etc/gshadow/를 수정해 그룹에 속한 유저들을 관리할 수 있습니다.

| 주요 옵션           | 내용                             |
| ------------------- | -------------------------------- |
| -a, --add _user_    | 유저를 멤버 리스트에 추가합니다. |
| -d, --delete _user_ | 멤버 유저 리스트를 설정합니다.   |

## $groupmod

그룹 정의를 수정하는 명령어입니다.<br/>
GID, 이름, 비밀번호 수정을 할 수 있습니다.

```console
groupmod [options] GROUP
```

| 주요 옵션                  | 내용                                                                       |
| -------------------------- | -------------------------------------------------------------------------- |
| -n, --new-name _NEW-GROUP_ | 그룹 이름을 변경합니다.                                                    |
| -g, --gid _GID_            | GID를 변경합니다.       <br/> `-o` 옵션으로 중복 GID를 적용할 수 있습니다. |
| -p, --password _PASSWORD_  | PASSWORD를 변경합니다.                                                     |

```console
groupmod [options] GROUP
```

## $groupdel

시스템 계정 파일을 수정하고 GROUP과 관련된 엔트리들을 모두 삭제합니다.

```console
groupdel [options] GROUP
```

## $groups

유저가 속한 그룹을 출력합니다.

## $grpck

`/etc/group`와 `/etc/gshadow` 파일의 무결성을 검사합니다.

## $newgrp

접속중인 상태에서 gid를 변경합니다.<br/> - 옵션을 추가하면 재부팅하지 않아도 적용되게 할 수 있습니다.

```console
newgrp [-] [group]
```


# Reference

[/etc/shadow에 대한 이해](https://www.cyberciti.biz/faq/understanding-etcshadow-file/?utm_source=Related_Tutorials&utm_medium=faq&utm_campaign=Apr_22_2022_EOP_TEXT)
[/etc/shadow에 대한 이해2](https://www.techtarget.com/searchsecurity/definition/shadow-password-file)
[linux manual](https://man7.org/linux/man-pages/man5/passwd.5.html)
[/geeksforgeeks](https://www.geeksforgeeks.org/introduction-to-linux-operating-system/?ref=lbp)
[sudoer](https://wiki.gentoo.org/wiki/Sudo/ko)