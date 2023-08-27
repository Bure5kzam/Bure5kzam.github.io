---
layout: single
title: 우분투 samba 서버
date: 2022-07-27 22:18
category: LinuxMaster
summary: 
---


# Overview

Samba는 파일/프린터 공유를 지원하는 오픈 소스 소프트웨어입니다.

<!-- - SMB(Server Message Block) 프로토콜로 통신
- 확장된 SMB인 CIFS(Common Internet File System)도 있습니다. -->

<!-- Sections은 패스워드가 필요없는 Guest services로 설계되었습니다. 설정 파일에 명시된 Unix guest account는 서비스의 접근권한을 가지고 있습니다.
guest services가 아닌데 접근하면 패스워드를 요구합니다. username은 클라이언트가 제공합니다. -->

## Share(Shared resource)

`smaba` 서버 외부에서 접근할 수 있는 공유 자원을 의미합니다.

`Share`는 다음과 같이 구성됩니다.

- 엑세스 할 수 있는 디렉토리
- 접근할 수 있는 디렉토리에 대한 권한 설명

> $ man smb.conf, [SECTION DESCRIPTIONS] </br></br>
>   A share consists of a directory to which access is being given plus a description of the access rights which are granted to the user of the service. Some housekeeping options are also specifiable.


## Section

섹션은 스폐셜 섹션과 일반 섹션이 있습니다. 공통적으로 `samba` 서버에서 제공하는 `Shared`를 묘사합니다.

### Section (Normal)

- 외부에서 이름으로 접근할 수 있도록 `Shared`의 이름을 설정(describe)합니다.
- `Shared`에 적용될 파라미터를 묘사합니다.

> $ man smb.conf, [SECTION DESCRIPTIONS] </br></br>
> Each section in the configuration file (except for the [global] section) describes a shared resource (known as a “share”). The section name is the name of the shared resource and the parameters within the section define the shares attributes.

일반 섹션은 클라이언트에게 두 가지 서비스 중 하나를 제공합니다.

- `file share service`, 클라이언트에 의해 추가적인 파일 시스템으로 사용 
- `printable service`, 클라이언트에 의해 서버의 프린터 서비스에 접근하는데 사용

> $ man smb.conf, [SECTION DESCRIPTIONS] </br></br>
>Sections are either file share services (used by the client as an extension of their native file systems) or printable services (used by the client to access print services on the host running the server).


섹션을 활용하면 패스워드 없이도 접근할 수 있는 `guest services`로 사용될 수 있습니다.

지정된 `unix guest account`로 해당 케이스 (서비스)의 접근권한을 정의합니다.

> $ man smb.conf, [SECTION DESCRIPTIONS] </br></br>
 > ections may be designated guest services, in which case no password is required to access them. A specified UNIX guest account is used to define access privileges in this case.

 ## Special section

특수 섹션은 일반 섹션과 같은 문법을 사용하지만 `하나의 share`만 묘사하지 않습니다.

- [global] section : 모든 섹션에 공통적으로 파라미터를 적용. 글로벌 섹션에 선언된 파라미터는 다른 섹션의 디폴트 파라미터로 적용 됩니다,

- [homes] section : 클라이언트의 홈 디렉토리에 연결되는 섹션입니다.

- [printers] section : 클라이언트의 프린트에 연결되는 섹션입니다.

### homes

**homes 섹션의 연결과정**

클라이언트가 `samba` 서버에 섹션을 요청하면, 기본적으로 `samba.conf`에 요청한 섹션에 대한 `description`이 있는지 확인합니다.


 요청한 섹션의 `description`이 있으면 매칭된 섹션의 서비스를 제공하지만, 만약 해당 섹션이 없으면 대신 서버에 있는 패스워드 파일에서 요청한 섹션 이름과 같은 계정이 있는지 검색합니다. 네임과 올바른 패스워드가 주어지면 다음이 수행됩니다.
 
 - `homes` 섹션을 클로닝한 새 `share`가 생성되게 됩니다.
 - `share`의 이름이 homes에서 유저 이름으로 수정됩니다.
 -  원본 `homes` 섹션에 `path` 파라미터 묘사가 없으면 클라이언트 홈 디렉토리를 경로로 설정합니다.

만약 파라미터로 경로를 미리 지정해두려면, Section 이름을 의미하는 `%S` 포맷과 함께 지정하면 유용합니다.

``` console
# example of home's section parameter

[homes]
path = /data/pchome/%S
is useful if you have different home directories for your PCs than for UNIX access.
```

> $ man smb.conf, [SPECIAL SECTIONS] </br></br>
> If a section called [homes] is included in the configuration file, services connecting clients to their home directories can be created on the fly by the server. When the connection request is made, the existing sections are scanned. If a match is found, it is used. If no match is found, the requested section name is treated as a username and looked up in the local password file. If the name exists and the correct password has been given, a share is created by cloning the [homes] section.
>
> Some modifications are then made to the newly created share:
>
> ·   The share name is changed from homes to the located username.

> ·   If no path was given, the path is set to the user's home directory.

> If you decide to use a path = line in your [homes] section, it may be useful to use the %S macro. For example:
>
> ·   path = /data/pchome/%S
>
> is useful if you have different home directories for your PCs than for UNIX access.
> (...)



<!-- homes 디렉토리는 모든 유저가 패스워드 없이도 볼 수 있는 디렉토리입니다. 문제가 될 수 있다면 read only 파라미터를 no로 합니다. 아니면 browsable 파라미터를 no로 설정하는 방법도 있습니다. `global` 섹션에서 설정하면 새로 생성되는 홈 섹션들은 yes가 적용되어 기본 home 디렉토리는 보여주지 않고 신규 디렉토리만 보여줄 수 있습니다. -->


### printers

`homes` 섹션과 유사하지만 프린터를 위한 섹션입니다. 설정 파일에 `printers` 섹션이 있으면 로컬 호스트의 `printcap` 파일에서 지정된 프린터에 접근할 수 있습니다.

``` console
[printers]
path = /usr/spool/public
guest ok = yes
printable = yes
```

앞서 설명한 **homes 섹션 연결 과정**에서 `homes` 섹션과 연결되지 않으면, 요청한 섹션 이름을 프린터 이름으로 치고 유효한 이름인지 확인하기 위해 printcap 파일을 스캔합니다. 매치되면 `printers`섹션을 클로닝한 새 share가 생성됩니다.

생성된 share는 printer name이 주어지지 않았으면 located printer 이름으로 바뀝니다. 만약  share 설정이 게스트가 접근할 수 없을 때 username 이 주어지지 않는다면 username은 located printer name으로 변경됩니다. 

프린터 섹션은 무조건 `printable`속성이 `yes`여야 합니다. 그렇지 않으면 로드되지 않습니다.

---


### Parameter

Parameters는 섹션에 지정된 속성을 정의합니다.

> $ man smb.conf, [PARAMETERS] </br></br>
> Parameters define the specific attributes of sections.

`$ man smb.conf`의 `[EXPLANATION OF EACH PARAMETER]` 에서 각 파라미터에 대한 설명을 볼 수 있는데, 파라미터 이름 뒤에는 (G)나 (S) 표기가 있습니다.


- `G`는 globals 섹션에서만 사용하는 고유 파라미터를 의미합니다.
- `S`는 `service specific section` 안에서 지정되는 파라미터입니다.
  - 또한 `S` 파라미터는 모두 `global section`에서도 사용할 수 있습니다.
  - 이는 모든 서비스에 대한 기본 속성을 정의합니다.

[homes]와 [prints]는 `special section` 이지만, 위 설명에서는 `normal section`로 고려해야 합니다.

> $ man smb.conf, [PARAMETERS] </br></br>
> Some parameters are specific to the [global] section (e.g., security). Some parameters are usable in all sections (e.g., create mask). All others are permissible only in normal sections. For the purposes of the following descriptions the [homes] and [printers] sections will be considered normal. The letter G in parentheses indicates that a parameter is specific to the [global] section. The letter S indicates that a parameter can be specified in a service specific section. All S parameters can also be specified in the [global] section - in which case they will define the default behavior for all services.


다음은 시험에서 주로 나오는 파라미터입니다

| 종류         | 파라미터                         | 의미                                                                                                   |
| ------------ | -------------------------------- | ------------------------------------------------------------------------------------------------------ |
| G            | workgroup                        | 클라이언트에게 보이는 서버의 공유 그룹 명입니다.                                                       |
| G            | server string = Samba            | 서버에 대한 설명입니다. <br/> 프린터 매니저와 IPC 커낵션에서 옆에 보일 커맨트를 설정합니다.            |
| G            | netbios name = MYNAME            | 삼바 서버가 알려진 NetBios 이름을 설정합니다. <br/> 설정하지 않으면 서버의 도메인 네임으로 설정됩니다. |
| S            | comment = Web                    | 공유 리소스에 설명을 추가합니다. <br/> 클라이언트가 요청했을 때 옆에 보입니다.                         |
| S            | hosts allow = 150.203. EXCEPT    |
| 150.203.6.66 | 접근 가능한 호스트를 설정합니다. |
| S            | path = /home/fred                | 공유 리소스의 경로입니다.                                                                              |
| S            | valid users = greg, @pcusers     | 접근 가능한 사용자입니다.                                                                              |
| S            | writable = yes                   | 파일 생성과 삭제권한을 부여합니다.                                                                     |


# File

## smb.conf

> Reference : man 5 smb.conf
> [EXPLANATION OF EACH PARAMETER]

`smbd` 서버 접근을 설정하는 파일입니다.


```console
# 설정파일 구문은 Section과 내부의 Parameter로 이루어집니다.
# Section은 smaba 서버에서 제공하는 공유 리소스의 이름이 됩니다.
# Parameter는 공유 리소스에 적용되는 속성들을 의미합니다.

# 주석은 #나 ;로 시작합니다.
# 파라미터 이름이 중복되면 처음 등록된 파라미터만 적용됩니다.
# 등호(=) 바로 앞, 뒤의 공백은 버려지며 parameter 값의 선행 및 후행 공백은 삭제됩니다. 매개 변수 값 내의 내부 공백은 문자 그대로 유지됩니다.
# 파라미터는 대소문자를 구분하지 않습니다.
# '\' 뒤에서 개행하면 줄이 유지됩니다.

[section-name]
parameter-name = parameter-value
```



<!-- ### Section

섹션은 삼바 서버가 클라이언트에게 제공하는 리소스를 묘사합니다.

섹션은 크게  공유 리소스를 의미하는 일반 섹션과 스폐셜 섹션으로 구분됩니다만, 구분과 관계없이 공유 리소스(`shared resource` 나 `share`)를 묘사합니다. (`global section` 제외) -->

<!-- ([global], [homes], [printers]) -->

<!-- 섹션은 클라이언트가 native file system 처럼 이용할 수 있는 `file share service`와 클라이언트가 호스트에 연결된 프린터 서비스를 이용할 수 있는 `printable service` 입니다. -->

 <!-- guest를 위한 섹션의 경우, 비밀번호가 없어도 대상에 접근할 수 있어야 합니다. 이 경우 명시된 UNIX _guest account_ 의 권한을 사용합니다. -->


<!-- `share`는 두 가지로 구성됩니다.

- 엑세스할 수 있는 디렉토리
- 사용자에게 부여되는 엑세스 권한에 대한 설명 -->

<!-- > $ man smb.conf, [SECTION DESCRIPTIONS] <br/> <br/>
> A share consists of a directory to which access is being given plus a description of the access rights which are granted to the user of the service. Some housekeeping options are also specifiable. -->



### 예시

아래 예시 섹션은 `/home/bar path`에 접근해 쓰기를 할 수 있습니다.

```console
[foo]
path = /home/bar
read only = no
```

다음은 프린트 share 섹션입니다.

- 읽기 전용
- 프린터 가능. 다시말해 클라이언트가 스풀 파일을 열고, 쓰고, 닫는 호출을 통해서만 쓰기 액세스가 허용됨을 의미합니다.
- guest ok 매개 변수는 액세스가 기본 게스트 사용자(다른 곳에서 지정)로 허용됨을 의미합니다.

```console
[aprinter]
path = /usr/spool/public
read only = yes
printable = yes
guest ok = yes
```

그 외 예시입니다.

```console
[global]
workgroup = fedora
security = user # samba 서버 접속시 부여하는 인증 절차 레벨
log file = #log 파일 위치


[homes]
comment = SMB Home Directories
browsable
writeable

[public]
valid users = // 공유 디렉토리에 접근할 수 있는 사용자 지정
path =  # 섹션의 디렉토리

comment = # 설명

valid users = alpha, beta # 접근을 허용할 리스트

hosts allow =  # 권한을 줄 이름 리스트
```

--- 

# Command

`$ man samba`에서 소개하는 관련 프로그램입니다.

| 프로그램  | 역할                                                                                 |
| --------- | ------------------------------------------------------------------------------------ |
| testperm  | `smb.conf` 구문 체크 유틸리티.                                                       |
| smbclient | 클라이언트 프로그램, <br/> samba에 연결된 프린터나 공유 자원에 접근할 수 있도록 지원 |
| smbpasswd | 로컬이나 원격에 있는 삼바 서버의 패스워드 세팅 툴                                    |
| pdbedit   | 삼바 서버에 있는 로컬 유저 유지관리 툴                                               |

맨 페이지에 나열되지않은 프로그램

| 프로그램  | 역할                          |
| --------- | ----------------------------- |
| showmount | NFS 서버의 마운트 정보를 확인 |


> $ man samba, [DESCRIPTION]<br/><br/>
> testparm(1) <br/>
> The testparm utility is a simple syntax checker for Samba's smb.conf(5) configuration file. In AD server mode samba-tool testparm should be used though.<br/><br/>
>  smbclient(1) <br/>
> The smbclient program implements a simple ftp-like client. This is useful for accessing SMB shares on other compatible SMB servers, and can also be used to allow a UNIX box to print to a printer attached to any SMB server.<br/><br/>
> smbclient(1)<br/>
> The smbclient program implements a simple ftp-like client. This is useful for accessing SMB shares on other compatible SMB servers, and can also be used to allow a UNIX box to print to a printer attached to any SMB server.<br/><br/>
> smbclient(1) <br/>
> The smbclient program implements a simple ftp-like client. This is useful for accessing SMB shares on other compatible SMB servers, and can also be used to allow a UNIX box to print to a printer attached to any SMB server.


## $smbclient

samba 서버에 질의할 수 있는 클라이언트 프로그램입니다.

<!-- 오퍼레이션은 서버에서 파일 가져오기, 로컬에서 서버에 넣기, 서버에서 디렉토리 정보가져오기  -->


```console
$ smbclient -L ip_address               # 서버의 서비스 목록을 출력
$ smbclient //192.168.5.13/data         # 해당 서비스에 접근
```

`servicename` 는 서버에서 사용할 서비스 이름입니다. 서비스 이름 포맷은 SMB/CIFS 관련  서버의 경우 `//server/service` 포맷을 사용하며 `service`는 제공되는 서비스의 이름입니다. 

<!-- 서버 이름은 꼭 IP 주소일 필요가 없습니다. 요구되는 이름이 NetBIOS 서버 이름일 경우, 서버를 동작하는 머신의 IP 호스트네임 -->


## $testparm

`smb.conf` 설정이 올바른지 확인하는 명령어

## $showmount

원격 호스트의 mount daemon에게 NFS 서버의 상태 정보를 질의합니다. 
<!-- 옵션이 없으면 호스트에 마운팅된 클라이언트 집합 목록을 표시합니다. -->

```console
# NFS 서버에서 제공하는 export 목록을 표시
showmount -e 10.217.181.11
```

