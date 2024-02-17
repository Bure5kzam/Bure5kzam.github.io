---
title: 우분투 사용자가 속한 그룹 조회하기
category : ubuntu
author: Bure5kzam
tags: [linux, user]
summary: 사용자가 속한 그룹들을 조회합니다
---

사용자가 속한 그룹이 무엇인지 조회합니다.

예시에서는 유저 `a3`이 그룹 `g1`, `g2`, `g3`에 속해 있습니다.

## /etc/group에서 확인하기

/etc/group에는 그룹별 멤버 정보가 있으며, 일반 유저도 접근할 수 있습니다.

파일을 쉘에 출력하는 `cat`과 키워드가 속한 행만 보여주는 `grep` 명령어 조합으로 조회할 수 있습니다.

```console
[a3@localhost root]$ cat /etc/group | grep a3
g1:!:root:root,a3
g2:!::a1,a3
g3:!::a3
```

/etc/gpasswd로도 확인할 수 있지만, sudo 권한이 없는 일반 유저는 조회가 불가능합니다.

아래 보듯이 파일 권한이 없기 때문입니다.

```console
[a3@localhost root]$ ls -al /etc/passwd
-rw-r--r--. 1 root root 1686  3월 18 18:37 /etc/passwd

[a3@localhost root]$ ls -al /etc/gshadow
----------. 1 root root 663  3월 18 18:46 /etc/gshadow
```

## groups 명령어로 확인하기

`groups`는 현재 유저가 속한 그룹 이름을 조회하는 간단한 명령어입니다.

```console
[a3@localhost root]$ groups
g3 g1 g2
```

## id 명령어로 확인하기

`id`는 특정 유저의 UID, GID 정보를 조회하는 명령어입니다.

ID대신 이름으로 나타낼 수도 있습니다.

```console
[a3@localhost root]$ id -Gn
g3 g1 g2
[a3@localhost root]$ id -Gr
1006 1003 1004
```