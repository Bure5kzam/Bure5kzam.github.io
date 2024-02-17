---
title: 
category : ubuntu
author: Bure5kzam
tags: [linux, user]
summary: 리눅스 사용자 계정 
---

우분투에서 일반적인 사용자 정보는 `/etc/passwd`에서 관리하지만, 비밀번호에 관한 정보는 분리되어 루트만 접근할 수 있는 `/etc/shadow` 에서 다룹니다.

그 이유는 계정 암호화에 단방향 알고리즘을 사용하는데, 성능 좋은 컴퓨터로 전사공격할 경우 해독될 위험이 있기 때문입니다.

암호문을 모든 계정에서 접근할 수 있는 `/etc/passwd`에 저장하면 누구나 해독을 시도할 수 있으니 `sudo`만 확인할 수 있도록 해 이중 보안을 적용한 것입니다.

```console
# passwd는 비밀번호를 뜻하는 2번째 필드에 'x'값이 들어있음.

[root@localhost ~]# cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin

# shadow는 2번째 필드에 암호화된 비밀번호가 저장되어있음.
[root@localhost ~]# sudo cat /etc/shadow
root:$6$8jfW476nA.IBZt9R$mv2SCp7mkvW9bQuceAySINppMi0T/FQ7DykQjOdzuFFSAfKiU6ViUx1eiE.b4i3klWm/pgpM9HvSOhLIfxAlv.::0:99999:7:::
bin:*:18353:0:99999:7:::
daemon:*:18353:0:99999:7:::
```

### /etc/passwd

```console

# each fields's Means at file /etc/passwd

NAME : PASSWORD : UID : GID : GECOS : HOME_DIR : SHELL
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

### /etc/shadow

`/etc/shadow` 는 유저 정보 중에서도 특히 패스워드 암호화 정보와 비밀번호 수명을 관리합니다.

쉘 명령어가 passwd로 끝나면 대부분 계정의 `password aging` 와 관련이 있습니다.

이 파일의 정보는 주로 명령어로 관리하기 때문에 값을 직접 수정하는 일이 없습니다.


```console
NAME : PASSWD : LAST_CHANGED : MINIMUM:MAXIMUM : WARN, 
```

1. NAME : 유저 이름
2. PASSWD : 암호화된 비밀번호.
   1. !나 * 시작되는 경우, 계정이 잠겼음을 의미
   2. !!는 비밀번호가 설정된 적 없음을 의미
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