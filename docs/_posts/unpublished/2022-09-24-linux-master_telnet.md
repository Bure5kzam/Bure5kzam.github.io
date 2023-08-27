---
layout: single
title: 우분투 마스터 TCP Wrapper (미완)
---


# Overview

`TCP Wrapper`는 호스트 기반 `Network Access Control List` 시스템입니다. `Rule`을 구성해 서버에 접근하는 호스트들의 엑세스를 거절하거나 수락할 수 있습니다.

`TCP Wrapper`는 오래되어 사용이 지양되고 있지만, 호스트와 호스트의 권한 리스트를 지정하는 `Access Control List` 개념은 아직까지 남아 `telnet`,`named` 같은 서비스들에서도 사용되고 있습니다.

[페도라, TCP Wrapper 사용 지양에 관한 내용](https://fedoraproject.org/wiki/Changes/Deprecate_TCP_wrappers#Deprecate_TCP_wrappers)


## Access Control Rule

`Access Control List` 구성에 사용되는 `access Control Language` 는 (이하 `ACL`) 데몬 프로세스에 접근할 수 있는 호스트를 다음과 같이 지정합니다.

```console
daemon_list : client_list [ : shell_command ]
```

`daemone_list` 값 타입

- 프로세스 이름
- 와일드 카드

`client_list` 값 타입

- 호스트 이름
- 호스트 주소
- 패턴
- 와일드카드

<!-- `ACL`은 client (host name/address, user name)와 server (process name, host name/address) 패턴을 기반으로 합니다. daemon은 네트워크 데몬 프로세스를 의미하고 client는 서비스를 요청하는 호스트 주소와 이름입니다. 네트워크 데몬 프로세스 이름은 inetd 설정 파일에 명시되어 있습니다. -->


### Pattern

`man host_access`의 `ACCESS CONTROL RULES` 섹션에는 Access Control 을 작성하는 구문 양식이 적혀있습니다.

각 엑세스 컨트롤 파일은 보이는 순서대로 처리되며, 매치되면 프로세스가 종료됩니다.

- `\` 는 개행을 무시하고 길게 쓸수있도록 합니다.
- 빈 줄이나 #로 줄은 무시됩니다.
- 그 외 줄은 `daemon_list : client_list [ : shell_command ]` 양식을 따라야 합니다. (\[ shell_command\]는 선택적 옵션입니다.)
- daemon_list 는 데몬 프로세스의 이름이나 와일드카드입니다.
- client_list 는 host names, host address, patterns or wildcard이 될 수 있습니다.
- 리스트 원소들은 공백이나 콤바로 분리돼야 합니다.

- `.`으로 시작하는 문자열 패턴입니다. 호스트 네임의 마지막 컴포넌트와 비교합니다. 예를들어 `.tue.nl`은 `wzv.win.tue.nl`의 뒷부분과 매치됩니다.
- `.`로 끝나는 문자열 패턴은 호스트 주소의 첫 번째 필드와 비교합니다. 예를들어 `131.155`는 `131.155.x.x`로 접근하는 호스트들과 매칭됩니다.
- `@`로 시작하는 문자열은 NIS netgroup으로 판단합니다. 명시된 그룹의 호스트 맴버이면 매칭합니다. Netgroup 매치는 데몬 이름이나 클라이언트 유저 이름을 지원하지 않습니다.
- `n.n.n.n./m.m.m.m.` 포맷은 네트워크 주소와 네트워크 마스크 페어로 인식됩니다.
- `/`로 시작하는 문자열은 파일 이름으로 인식됩니다. 파일 안에 등록된 이름들로 매칭합니다. 파일 안은 공백으로 구분되는 주소 패턴이나 호스트 네임을 기입합니다.

### WildCard

| 와일드카드 | 내용                                   |
| ---------- | -------------------------------------- |
| ALL        | 모든 주소와 매칭됩니다.                |
| LOCAL      | 이름에 .이 들어가지 않은 호스트        |
| UNKOWN     | 이름이나 주소가 unknown인 호스트       |
| KNOWN      | 이름 또는 이름과 주소가 known인 호스트 |


# $ telnet

`$ telnet` 명령어는 다른 호스트와 통신하기 위한 명령어로 TELNET 프로토콜을 사용합니다.

`host` 아규먼트가 없으면 `커맨드 모드`로 실행됩니다. `host` 아규먼트가 있으면 `커맨드 모드`에서 `open` 커맨드가 바로 실행됩니다.

`host` 를 여러개 입력하면 연결 되거나 모두 실패할 때까지 시도 합니다.

# $ telnetd

`$ telnetd`는 DARPA 텔넷 대화형 커뮤니케이션 프로토콜을 지원하는 서버입니다. `$ telnetd`는 일반적으로 internet 서버(see inetd(8))에 의해 `/etc/services`(see services(5))에 명시된 telnet 포트에 연결을 요청하기 위해 호출됩니다.

> $ man telnetd, [DESCRIPTION]
> The telnetd program is a server which supports the DARPA telnet interactive communication protocol.  Telnetd is normally invoked by the internet server (see inetd(8)) for requests to connect to the telnet port as indicated by the /etc/services file (see services(5)).  The -debug option may be used to start up telnetd manually, instead of through inetd(8).  If started up this way, port may be specified to run telnetd on an alternate TCP port number.

# xinetd

`xinetd`는 인터넷을 제공하는 프로그램입니다. 시스템이 초기화되면 시작되고 연결 요청이 있을 때 까지 휴면 상태로 설정 파일에 있는 모든 포트들을 수신 대기하고 있습니다. 요청이 오면 `xinetd`가 적절한 서버를 시작합니다. 동작상의 특성 때문에 `super-server`로도 불립니다.

`xinetd`의 설정 파일에 등록된 서비스들은 두가지 그룹으로 나눌 수 있습니다.

첫 번째 그룹은 `multi-threaded`로 부르며 연결에 대해 새 서버 프로세스를 forking 하도록 요청합니다. 그럼 새 서버가 연결을 처리합니다. `xinetd`가 각서버에 대해 새 요청을 `listen`하고 있으므로 새 서버를 `spawn`할 수 있습니다.

두 번째 그룹은 서비스 데몬이 새 연결에 대해 처리 가능한 서비스들로 이뤄집니다. `single-threaded`로 부르며 이후 이뤄지는 연결 요청에 대해 서비스가 죽을 때 까지 `xinetd`가 관여하지 않습니다.


<!-- 지금까지 `super-server`의 존재 이유는 대부분 휴면 상태인 프로세스 많이 포킹하는 것을 방지해 시스템 리소스를 보존하기 위함이었습니다.  -->

`xinetd` 재시작시 주의 사항으로, `INTERNAL` 타입의 서버에게 `SIGTERM` 시그널이 전달됩니다. `INTERNAL`이 아닌 서비스에게는 `SIGKILL`이 갑니다. 

Ver2

`$ xinetd(8)`는 확장된 인터넷 서비스 데몬입니다.

`xinetd`는 인터넷 서비스를 제공하는 시작 프로그램입니다. 서버를 시스템 초기화 시간에 시작하는 대신, 연결 요청이 도착할 때까지 휴먼 상태로 존재하게 합니다. `xinetd`는 유일한 시작 프로세스이며 설정 파일에 등록된 `서비스` 포트들에 대해 리슨하고 있습니다. 요청이 오면 `xinetd`는 적절한 서버를 시작합니다. 그 동작방식 때문에, xinetd는 수퍼 서버가 선호됩니다.

> $ man xinetd, [DESCRIPTION] </br> </br>
> xinetd performs the same function as inetd: it starts programs that provide Internet services. Instead of having such servers started at system initialization time, and be dormant  until  a connection  request  arrives,  xinetd is the only daemon process started and it listens on all service ports for the services listed in its configuration file.  When  a  request  comes  in, xinetd  starts  the  appropriate  server.   Because of the way it operates, xinetd (as well as       inetd) is also referred to as a super-server.


# File

`/etc/hosts.allow` 와 `/etc/hosts.deny` 파일은 `access control language` 문법으로 작성되어 있습니다. `hosts_access` 커맨드로 관리하니 설명이 부족하면 메뉴얼을 참고바랍니다. `access control language`에 관한 설명은 매뉴얼의 `access control files` 섹션에 있습니다.

## xinetd.conf

xinetd.conf는 

