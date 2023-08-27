---
layout: single
title: 우분투 SELinux
published: false
---

# Overview
SELinux는 리눅스의 보안 엑세스 매커니즘입니다. 파일에 접근 가능한 정책을 구성해 프로세스의 접근을 제한할 수 있습니다.

# DAC

SELinux에서 Object(객체)는 파일과 디렉토리, 장치같은 시스템 리소스들입니다.
그리고 Object에 접근하는 명령어나 응용 프로그램 같은 프로세스를 Subject(주체)라 합니다.

Discretionary Access Control (임의 접근 컨트롤)는 Object와 Subject가 상호 작용하는 방식을 Object의 소유자가 정의하는 방식입니다.

예를 들어, 유저가 홈디렉토리(Object)의 읽기/쓰기 권한을 수정하면 모든 사람들과 프로세스(Subject)들이 접근할 수 있습니다.



# $setuid
리눅스 권한에는 owner, group, other 말고도 setuid가 있습니다.
유저가 바이너리를 실행시키면 일반적으로 유저의 권한이 적용되지만 setuid 비트가 설정된 바이너리를 실행시키면 바이너리의 소유자 권한으로 실행됩니다.

setuid가 적용된 바이너리 예시로는 계정 비밀번호를 변경하는 명령어 `passwd`가 있습니다.
`passwd`는 `/etc/shadow`에 파일에 암호화된 비밀번호 정보를 저장하는데, 이 파일은 root 권한이 있어야 편집할 수 있습니다.

일반 유저는 `/etc/shadow`를 직접 편집할 수 없지만 `passwd` 명령어로 root 권한을 취득해서 편집할 수 있습니다.

아래 예시에서 passwd의 owner 실행권한에 setuid를 의미하는 s를 확인할 수 있습니다.

```
[root@localhost bin]# pwd
/usr/bin


[root@localhost bin]# ls -al |grep passwd
-rwsr-xr-x.  1 root root     78408  8월  9  2019 gpasswd
-rwxr-xr-x.  1 root root    277808 10월 14  2021 grub2-mkpasswd-pbkdf2
-rwxr-xr-x.  1 root root     19552  3월 24 23:58 htpasswd
-rwsr-xr-x.  1 root root     27856  4월  1  2020 passwd 
-rwxr-xr-x.  1 root root     35912  2월  1  2022 smbpasswd
```

# $setuid의 보안위협

setuid가 설정된 바이너리는 권한 상관없이 실행할 수 있습니다.
실행하는 유저가 누군지 상관하지 않기 때문에 유저에 별로 다른 권한을 부여할 수가 없습니다.

# SElinux

SElinux는 리소스에 접근하는 주체에 따라 다른 접근법을 적용시키기 위해 사용합니다. 이를
 MAC(의무 접근 컨트롤)이라 합니다.

Red Hat 지침서에서는 이를 `subject가 object에 대해 action을 할 수 있는지 지정한다`고 표현합니다.
 

## Mode
SELinux에는 `disable`, `permissive`, `enforcing` 모드가 있습니다.
`permissive` 모드에서는 개체에 레이블을 지정하고 접근금지를 거는 등의 동작이 이루어지는것처럼 보이지만 실제로 작업을 거부하지는 않습니다.

기본적으로는 force모드로, 시스템 전반에 보안 정책을 적용합니다.

```console
$ setenforce 0 # Permissive로 설정
$ setenforce 1 # Enforcing으로 설정
$ getenforce
Enforceing
```

## Context
리눅스 파일 속성에는 일반적으로 소유자, 그룹 등이 있습니다.
SELinux가 적용되면 파일에 SELinux 사용자(User), 역할(Role), 유형(Type), 레벨(Level)이 추가됩니다. 이를 리눅스 Context라하고 SElinux Lable라고도 합니다.

Object인 파일 뿐만 아니라 유저나 프로세스 같은 Subject들도 Context를 가지고 있으며, SELinux는 Context 정보를 참고해 Subject Access Control을 수행합니다.

Object의 Context를 보려면 `ls` 명령어에 `-Z` 옵션을 함께 사용합니다.
프로세스의 Context는 `ps` 와 `-eZ`로 확인합니다.

### user
SELinux user는 리눅스 유저 계정에 대응되는 개념입니다. 리눅스 유저를 SELinux user에 매핑하면 그 SELinux 계정에 적용된 제한 사항을 상속할 수 있습니다.

SELinux 계정은 `semanage login -l`로 확인할 수 있습니다.

```console
[root@localhost ~]# semanage login -l

로그인 이름               SELinux 사용자          MLS/MCS 범위           서비스 

__default__          unconfined_u         s0-s0:c0.c1023       *
root                 unconfined_u         s0-s0:c0.c1023       *
system_u             system_u             s0-s0:c0.c1023       *
```

첫 열인 Login name은 리눅스 유저 목록을 의미합니다.
둘 째 열 SELinux User는 리눅스 유저가 매핑된 SELinux 입니다.
프로세스에 SELinux 유저가 적용되어있으면 프로세스가 수행할 수 있는 작업과 수준을 제한합니다.
셋 째 열 MLS/MCS는 각각 **다중 수준 보안**과 **다중 범주 보안**을 의미합니다.
넷 째 열 Service는 사용자가 로그인할 때 사용하는 올바른 SELinux 컨텍스트 입니다. 



# Reference
[Fedora Document, Security Manual](https://docs.fedoraproject.org/en-US/Fedora/19/html/Security_Guide/ch09.html)

[보안 강화 리눅스, lesstif](https://lesstif.gitbooks.io/web-service-hardening/content/selinux.html)

[스티키 비트, thegeekstuff](https://www.thegeekstuff.com/2013/02/sticky-bit/)

[Linux 특수권한, eunguru](https://eunguru.tistory.com/115)

[SELinux 유저 관리자 안내서](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/selinux_users_and_administrators_guide/sect-security-enhanced_linux-introduction-selinux_modes)