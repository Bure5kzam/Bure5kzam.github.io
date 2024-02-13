---
title: 우분투 그룹 멤버 관리하기
category : ubuntu
author: Bure5kzam
tags: [linux, group]
summary: gpasswd 명령어로 그룹의 멤버를 관리하는 방법을 정리합니다. 
---

## gpasswd

`gpasswd`는 그룹의 멤버를 관리하는 명령어입니다.

- 그룹 멤버 중에서 관리자와 멤버를 설정하는 명령어입니다.(`/etc/gshadow`를 수정)
- 그룹에 새 유저를 멤버로 추가하거나 기존의 멤버를 삭제합니다.

아래처럼 그룹에 멤버를 추가하거나 삭제할 때 많이 사용하게됩니다.

```console
[root@localhost ~]# groups a3
a3 : g3 g1

[root@localhost ~]# gpasswd -a a3 g2
사용자 a3을(를) g2 그룹에 등록 중

[root@localhost ~]# groups a3
a3 : g3 g1 g2

```


**관리자 및 멤버 관리 옵션**

gpasswd 명령어로 그룹의 관리자와 멤버를 설정할 수 있습니다.

| 주요 옵션                  | 내용                             |
| -------------------------- | -------------------------------- |
| -A, --administrator _user_ | 관리자 유저 리스트를 설정합니다. |
| -M, --member _user_        | 멤버 유저 리스트를 설정합니다.   |
| -a, --add _user_           | 유저를 멤버 리스트에 추가합니다. |
| -d, --delete _user_        | 멤버 유저 리스트를 설정합니다.   |

멤버 설정 예시

```console
[root@localhost ~]# gpasswd -M a1,a2,a3,test g3

[root@localhost ~]# cat /etc/group | grep g3
g3:x:1006:a1,a2,a3,test
```

멤버가 아니라도 관리자로 등록할 수 있습니다.

```console
[root@localhost ~]# gpasswd -A squid g3

[root@localhost ~]# cat /etc/group | grep g3
g3:x:1006:a1,a2,a3,test

[root@localhost ~]# cat /etc/gshadow | grep g3
g3:!:squid:a1,a2,a3,test
```

**비밀 번호 관리 옵션**

| 주요 옵션             | 내용                                                                                                                       |
| --------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| -r, --remove-password | group 비밀번호를 제거합니다. 그룹 비밀번호가 empty기 되며, 그룹 맴버만이 `newgrp` 명령어로 그룹에 가입할 수 있습니다.      |
| -R, --restirct        | 그룹으로의 접근을 제한합니다. 그룹 비밀번호가 `!`가 됩니다. 비밀번호를 아는 유저만이 `newgrp`로 그룹에 합류할 수 있습니다. |
| _group_               | 그룹의 비밀번호를 변경합니다. 접속계정이 administrator일때만 가능합니다.                                                   |




