---
layout: single
title: 리눅스 그룹
date: 2022-07-10 12:25
category: LinuxMaster
---
# Group

리눅스에서 한 유저는 여러 그룹에 속할 수 있습니다.

primary group은 사용자가 처음 속하는 그룹으로, 파일을 생성할 때 그룹 소유자로 primary group이 설정됩니다.
secondary group은 유저가 속했지만 유저의 primary group이 아닌 그룹들입니다.

별 다른 설정 없이 유저를 생성하면 유저 이름과 동일한 그룹이 함께 생성되고 유저의 Primary group으로 지정됩니다. 유저는 Primary group외에도 Secondary group의 권한을 갖게되며, 유저가 파일을 생성하면 Primary group의 소유로 생성이 됩니다.

시스템 내 그룹에 대한 정보는 /etc/group과 /etc/gshadow 저장됩니다.

# Files
## /etc/group

```console
# 필드별 의미, :로 구분
그룹이름:비밀번호:GID:UserId1, UserId2...
```

## /etc/gshadow

그룹별 비밀번호와 관리자와 멤버 정보를 관리합니다.

```console
# man 5 gshadow`로 필드별 의미를 확인할 수 있음.
GNAME : PW : ADMINISTRATOR : MEMBERS
```

# Command

## $groupadd
그룹을 생성하는 명령어입니다.

| 주요 옵션 | 내용 |
| -g, --gid _GID_ | 해당 그룹 GID로 생성 <br/> `-f` 이미 존재하면 다른 gid 선택. <br/> `-o` 중복 선택| 
|`-o`, --none-uniqe | 중복 GID 허용|

## $gpasswd
`/etc/group`과 `/etc/gshadow`를 수정하며 그룹의 관리자와 멤버를 설정하는 명령어입니다.
passwd가 붙는 명령어는 shadow파일과 많은 관련이 있습니다.

`/etc/gshadow`에는 그룹 이름과 gid, 그룹 관리자와 멤버 정보가 있습니다.

```
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
그룹 정의를 수정하는 명령어입니다.
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
접속중인 상태에서 gid를 변경합니다. - 옵션을 추가하면 재부팅하지 않아도 적용되게 할 수 있습니다.

```console
newgrp [-] [group]
```


# Reference
[/etc/shadow에 대한 이해](https://www.cyberciti.biz/faq/understanding-etcshadow-file/?utm_source=Related_Tutorials&utm_medium=faq&utm_campaign=Apr_22_2022_EOP_TEXT)
[/etc/shadow에 대한 이해2](https://www.techtarget.com/searchsecurity/definition/shadow-password-file)
[linux manual](https://man7.org/linux/man-pages/man5/passwd.5.html)
[/geeksforgeeks](https://www.geeksforgeeks.org/introduction-to-linux-operating-system/?ref=lbp)
[sudoer](https://wiki.gentoo.org/wiki/Sudo/ko)