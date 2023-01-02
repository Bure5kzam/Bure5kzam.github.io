---
layout: single
title: This is a new article
published : false
---

# Command

## ypserv

Network Information Service 를 제공하는 Server입니다.

NIS는 데이터베이스와 프로세스들로 구성된 간단한 네트워크 조회 서비스를 제공합니다. 이 데이터베이스들은 `/var/yp` 디록토리 안의 gdbm 파일들입니다.

> $ man ypserv, [DESCRIPTION] </br></br>
> The Network Information Service (NIS) provides a simple network lookup service consisting of databases and processes. The databases are gdbm files in a directory tree rooted at /var/yp.

`ypserv` 데몬은 시스템이 시작하면 동작합니다. `ypserv`가 실행되려면 NIS server 머신에 완전한 NIS 데이터베이스가 있어야 합니다. NIS 서비스를 사용하려는 다른 머신은 클라이언트 로서 `ypbind`를 실행하거나, Linux에서 `NYS`를 지원하는 `libc` 를 사용해야 합니다. `ypbind`는 NIS 클라이언트 프로세스가 있는 모든 머신에서 실행되어야 합니다. `ypserv`는 네트워크가 연결된 곳에서 실행되어야 합니다. `ypserv`가 실행되면 `/etc/ypserv.conf`를 파싱합니다. (이하생략)

> $ man ypserv, [DESCRIPTION] </br></br>
> The ypserv daemon is typically activated at system startup.  ypserv runs only on NIS server machines with a complete NIS database. On other machines using the NIS services, you have to run ypbind as client or under Linux you could use the libc with NYS support.  ypbind must run on every machine which has NIS client processes; ypserv may or may not be running on the same node, but must be running somewhere on the network. On startup ypserv parses the file /etc/ypserv.conf.  It is also possible to pass OPTIONS to ypserv using the environment variable YPSERV_ARGS and this variable can be set in /etc/sysconfig/network.
> 
## ypbind

ypbind는 NIS에서 서버-클라이언트간 binding을 담당하는 프로세스입니다.

NIS domains의 서버를 찾고 NIS 바인딩 정보를 유지합니다.

클라이언트는 RPC를 통해 ypbind에서 정보를 얻거나 바인딩된 파일을 읽을 수 있습니다. 바인딩 파일은 `/var/yp/binding`에 보관되고 통상적으로 `domainname.version` 포맷으로 명명됩니다.

> $ man ypbind, DESCRIPTION </br>
> ypbind finds the server for NIS domains and maintains the NIS binding information. The client (normally the NIS routines in the standard C library) could get the information over RPC from ypbind or read the binding files. The binding files resides in the directory /var/yp/binding and are conventionally named [domainname].[version]. The supported versions are 1 and 2. There could be several such files since it is possible for an NIS client to be bound to more than one domain.

바인딩에 성공한 후, ypbind는 현재 NIS 서버에게 20초마다 YPPROC_DOMAIN 요청을 전송합니다. 만약 응답이 없으면 해당 서버는 domain을 유지하지 못합니다. ypbind는 새로운 NIS 서버를 찾습니다. 

15분마다 ypbind는 현재 서버가 최상의 속도인지 체크합니다. 만약 더 빨리 응답하는 서버가 있다면 그 서버로 스위칭됩니다. 필요하다면 새 안전하지 않은 새 서버를 찾기위해 `ypbind`에게 브로드캐스트를 요청할 수도 있으며, 안전한 서버 목록을 제공할 수도 있습니다.  이 경우 리스트에 있는 모든 서버에 핑을 보내 가장 먼저 답당한 서버를 사용합니다.


## ypcat

<!-- NIS db에서 모든 키의 value를 출력합니다. -->
:
`map name`을 입력받아 NIS db에 있는 모든 키의 값을 출력합니다.

## ypwhich

클라이언트와 연결된 NIS 서버의 이름 또는 map master의 이름을 반환합니다.

`argument`로 `hostname`이 주어지면 해당 머신이 사용하는 NIS master를 찾기 위해 질의됩니다. 주어지지 않으면 로컬 호스트가 연결된 NIS server를 반환합니다.

`-m` 옵션으로 `mapname`이 주어지면 맵에 대한 마스터 NIS server를 찾습니다.
`mapname`이 생략되면 가능한 맵을 모두 생성합니다.

## ypinit

NIS 데이터베이스를 설치하고 빌드하는 프로그램입니다.

ypinit은 현재 기본 도메인을 위해 `domain sub directory`인 `/var/yp`를 빌드합니다.

`domain sub directory` 빌드 후에는 시스템 전체 관리 맵 집합을 빌드하고 서브 디렉토리에 배치합니다. 

처음 생성되는 맵은 `ypinit -m`로 빌드되어 생깁니다. 이는 `ypservers` 맵입니다. root 권한으로 실행해야 마스터 서버가 될 수 있습니다. NIS domain 당 하나의 마스터 서버밖에 가질 수 없습니다.

모든 데이터베이스들은 `scratch`로부터 빌드됩니다. 이들은 런타임에 프로그램이 사용할 수 있는 정보나 `/etc` 안의 아스키 데이터베이스 파일을 이용합니다.

> $ man ypinit, [DESCRIPTION] </br></br>
> All databases are built from scratch, either from information available to the program at runtime, or from the ASCII data base files in /etc. These files are listed below under FILES.

slave server 상의 NIS 데이터베이스는 실행 중인 서버에 기존에 존재하는 데이터베이스를 복사본으로 설정됩니다. `master_name` 아규먼트는 NIS 서버의 호스트 이름을 제공해야 합니다.

> An NIS database on a slave server is set up by copying an existing database from a running server. The master_name argument should be the hostname of an NIS server (either the master server for all the maps, or a server on which the data base is up-to-date and stable).
