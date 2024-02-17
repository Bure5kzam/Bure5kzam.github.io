---
layout: single
title: 우분투 유저
date: 2022-07-10 12:25
category: ubuntu
---

# Overview

사용자는 물리적 사용자나 특정 응용 프로그램이 사용하기 위해 존재하는 계정을 의미합니다.<br/>

처음 설치한 리눅스에는 sudo 권한을 가진 root 계정이 존재하며, `서비스` 또는 `파일` 권한 관리를 위해 추가로 생성해서 사용하게 됩니다.

## 유저의 구성 및 목적

각 사용자는 필수적으로 **UID**(UserID)와 **Username**를 부여받습니다.

**UID**는 유저를 나타내는 양수 숫자이며 일반적으로 고유합니다.

 **username**은 사람이 알아보기 쉬운 이름입니다.

 파일을 생성하면 파일의 소유자, 소유 그룹, 그 외(user, group, other)에 대해 읽기, 쓰기, 실행(read, write, execute) 권한이 생기며, 엑세스한 유저가 소유자면 `user`권한이 적용 되고 아니면 `other` 권한이 적용됩니다.

## UID

실제 사용자가 누구냐는 관점에서 유저를 `일반 유저`와 `시스템 유저`로 구분할 수 있습니다.

`일반 유저`는 접속하는 사람에게 홈 디렉토리와 권한을 공유하기 위한 계정입니다.

`시스템 유저`는 프로그램이 파일에 접근하거나 명령어를 실행할 때 권한 문제가 발생하지 않도록 필요 권한을 설정해둔 계정입니다.

기능상 차이는 없지만 우분투에서는 구분을 위해 명시적으로 일반 유저 UID는 1000 이상 숫자를, 시스템 유저는 1000 미만을 사용합니다.

`/etc/login.defs` 파일을 수정하면 UID와 SYS_UID 대역을 변경할 수 있습니다.

```bash
# /etc/login.defs 파일
...

#
# Min/max values for automatic uid selection in useradd
#
UID_MIN                  1000
UID_MAX                 60000

# System accounts
SYS_UID_MIN               201
SYS_UID_MAX               999

...

```

# 사용자 정보 파일

우분투에는 유저 정보를 관리하는 2개 파일이 존재합니다.

`/etc/password`는 유저의 정보(UID, Comment, home directory) 등을 관리하는 파일입니다.

`/etc/shadow`에는 `Password Aging` 관련 정보가 있습니다.

> Password Aging : 패스워드의 유효기간과 교체 시기를 관리하는 파일

---


## /etc/passwd

ubuntu에서는 `/etc/passwd`에서 유저 정보 중 보안과 관련없는 기본적인 정보들을 관리합니다.


### 필드 설명

```bash

# each fields's Means at file /etc/passwd

NAME:PASSWORD:UID:GID:GECOS:HOME_DIR:SHELL

```

 필드 별 정보는 다음과 같습니다.

| 필드 | 설명 |
|name | 유저의 로그인 이름입니다. (소문자로 구성되는게 좋습니다.)|
| paassword | 유저의 암호화된 패스워드입니다. |
| UID | 유저를 식별하는 고유한 양수 숫자입니다. UID가 0이면 root를 의미합니다.|
| GID | 유저가 속한 기본 group의 guid입니다. 파일 생성시 기본적으로 생성자의 GID가 소유자 그룹으로 지정됩니다.|
| GECOS | 유저에 대한 코멘트 필드입니다. |
| directory | 유저 홈 디렉토리입니다. 유저 로그인시 처음 이동하는 경로입니다. |
| shell | 로그인시 실행할 파일입니다. (기본 /bin/sh). 만약 실행되지 않는 파일을 지정하면 사용자는 로그인을 할 수 없습니다. |

> 출저 : man /etc/passwd

비밀번호 필드가 있지만 암호화된 패스워드 결과만 가지며, 자세한 내용은 보안을 위해 `/etc/shadow`에서 관리합니다.

## /etc/shadow

`/etc/shadow` 는 유저 정보 중에서도 특히 패스워드 암호화 정보와 비밀번호 수명을 관리합니다.

쉘 명령어가 passwd로 끝나면 대부분 계정의 `password aging` 와 관련이 있습니다.

이 파일의 정보는 주로 명령어로 관리하기 때문에 값을 직접 수정하는 일이 없습니다.

### 필드 설명

파일의 필드별 정보는 다음과 같습니다.

```console
NAME:PASSWD:LAST_CHANGED:MINIMUM:MAXIMUM:WARN, 
```

1. NAME : 유저 이름
2. PASSWD : 암호화된 비밀번호.
   1. !로 시작되는 경우, 비밀번호가 잠겼음을 의미
3. LAST_CHANGED : 마지막 변경 날짜.
   1. 1970.01.01.에서부터 지난 일 수를 숫자로 나타냄
   2. 0은 다음에 로그인할 때 변경해야 함을 의미
   3. Empty는 Password Aging이 불가능함을 의미
4. MINIMUM_PERIOD : 비밀번호 변경 후 유지해야 하는 일 수
5. MAXIMUM_PERIOD : 변경 후 번호를 사용할 수 있는 최대 일 수, 만료까지 남은 일 수
6. WARNNING_PERIOD : 번호 만기 경고를 만기 몇일 전에 알릴 건지 일 수 를 설정
7. INACTIVITY_PERIOD : 만료 후 사용자 계정이 비활성화되기 까지의 일 수
   1. 1970.01.01.로부터 지난 일 수
8. EXPIRED_DATE : 계정이 비활성화 될 날짜
9. UNUSED : 향후 사용을 위해 존재만하는 빈 필드

---


## $pwck

`/etc/passwd`와 `/etc/shadow`파일의 무결성을 검사합니다.

# Reference

[Red hat, sudo 엑세스 관리](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/managing-sudo-access_configuring-basic-system-settings)