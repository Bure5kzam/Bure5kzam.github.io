---
layout: single
title: 유저의 종류와 권한
date: 2023-03-15 22:54
category: 
author: 
tags: []
summary: 
---


# 유저의 종류

유저는 목적에 따라 3가지 종류가 있습니다.

**root**

linux 시스템의 수퍼유저 계정입니다. 설치시 자동으로 생성되며 시스템에서 가장 높은 권한을 갖습니다.

보안을 위해 관리 작업을 위해서만 사용해야 하며, 필요시 비활성화 시켜둘 수 있습니다.

UID가 0인 계정은 root로 취급됩니다.


**user account**

파일이나 서비스 권한을 지정하기 위한 계정입니다. root 계정으로 로그인해서 생성할 수 있으며, `sudoer` 파일을 수정해 활동 권한을 제어할 수 있습니다.

**service account**

설치 패키지가 설치될 때 설치 패키지에 의해 생성되는 계정입니다. 이러한 계정은 서비스에서 프로세스를 실행하고 기능을 실행하는 데 사용됩니다. 이러한 계정은 일상적인 작업을 위해 사용하거나 사용해서는 안 됩니다.


ubuntu는 `/etc/login.defs`에서 유저 종류마다 사용할 UID 대역을 설정할 수 있습니다.

```console
# /etc/login.defs
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

위 예시는 `user account` 생성시 지정되는 UID가 1000 ~ 60000 사이로 설정되어있습니다.


# 유저의 sudo 권한

일반 유저에게 sudo 권한을 부여하면 root가 아니어도 관리 명령어를 실행할 수 있습니다.

`sudo` 권한을 가진 유저를 `sudoer`라고 하기도 합니다.



일반유저가 `sudo` 권한을 얻어 터미널에서 관리 명령어를 실행하는데는 두 가지 방법이 있습니다.

1. `sudo` 명령어 사용하기

`superuser do`의 약어인 `sudo` 명령어는 뒤에 입력된 명령어를 루트 권한으로 실행합니다.

root 권한이 필요한 명령어를 1회성으로 실행할 때 사용합니다.

sudo를 **실행**하려면 root거나 `sudoer` 권한을 가지고 있어야 합니다.

`/etc/sudoer`을 수정해 sudo 권한을 부여할 계정과 실행할 수 있는 명령어를 지정할 수 있습니다.

2. `su` 명령어 사용하기

`swtich user` 의 약어이며, 접속 계정을 변경하는 명령어입니다.

`sudo`가 일시적으로 명령어를 루트 권한으로 실행하는 것과 다르게, `su` 계정에 직접 로그인하는 효과가 있습니다.

로그아웃하기 전까지 계정을 유지하며, `-` 와 함께 쓰면 계정의 경로나 환경변수까지 사용할 수 있습니다.

[Redhat, sudo와 su의 차이점](https://www.redhat.com/sysadmin/difference-between-sudo-su)

만약 권한 없는 유저가 사용하면 `journal log` 에 기록이 남습니다. (`username : user NOT in sudoers`)