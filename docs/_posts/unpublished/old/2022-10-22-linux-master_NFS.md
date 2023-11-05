---
layout: single
title: 우분투 마스터 NFS
published : false
---

# NFS

`/etc/exports` 파일에는 클라이언트가 접근할 수 있는 파일 테이블이 있습니다.
 테이블의 각 필드은 아래의 정보를 가집니다.
 
 - 서버 파일 시스템의 export point
 - 파일 시스템에 접근할 수 있는 엑세스 컨트롤 리스트 (클라이언트 + 권한)
 - 파일 접근 관련 옵션

이 정보는 `$exportfs` 커맨드에 활용됩니다.

> $ man exports, [DESCRIPTION] <br/><br/>
> Each  file  system in this table has a list of options and an access control list.  The table is used by exportfs(8) to give information to mountd(8).

entrypoint 뒤에서 클라이언트 리스트는 공백으로 구분됩니다. 클라이언트 뒤에는 공백없이 관련 옵션이 괄호와 함께 붙으며, 콤마로 구분됩니다.

```console
/               master(rw) trusty(rw,no_root_squash)
```

> $ man exports, [DESCRIPTION] <br/><br/>
> The file format is similar to the SunOS exports file. Each line contains an export point  and  whitespace-separated list of clients allowed to mount the file system at that point. Each listed client may be immediately followed by a parenthesized, comma-separated list  of  export  options for that client. No whitespace is permitted between a client and its option list.


각 라인은 경로 뒤로 기본 옵션을 위한 명세를 갖습니다. 이는 옵션 리스트 뒤의 dash (-) 폼을 갖습니다. 옵션 리스트는 라인 뒤로 뒤따르는 exports에만 적용됩니다.

파일을 적용하려면 `exportfs -ra` 또는 NFS 서버를 재시작 해야 합니다.

## Machine Name Format

NFS에서 클라리언트를 표현하는 방법은 5가지가 있습니다.

### single host

resolver에 의해 축약된 호스트 이름이나 fqdn, IPv4, IPv6 를 사용합니다.

### IP network

디렉토리를 같은 IP에 속하는 호스트들에게 제공할 수 있습니다. IP 주소를 제공하고 `address/netmask` 형태의 `dotted-decimal`나 `address/number` 형태의 `contiguous mask length`로 넷마스크를 지정합니다. 

### wildcard

머신 이름은 와일드 카드(*, ?)나 `[]` 안의 문자 클래스 리스트를 포함할 것입니다. 이것들은 exports 파일을 더 컴팩트 하도록 사용할 수 있습니다. 예를들어 `*.cs.foo.edu`는 도메인 내 모든 `cs.foo.edu`에 포함됩니다. 이 캐릭터들은 오메인 이름의 dots도 매치되기 때문에, 주어진 패턴은 cs.foo.edu의 서브도매인에 포함되는 모든 호스트에 매치됩니다.

> $ man exports, [Machine Name Formats] <br/><br/>
> wildcards <br/>
> Machine names may contain the wildcard characters * and ?, or may contain character class lists  within [square brackets].  This can be used to make the exports file more compact; for instance, *.cs.foo.edu matches all hosts in the domain cs.foo.edu.  As these  characters  also  match  the dots in a domain name, the given pattern will also match all hosts within any subdomain of cs.foo.edu.

### netgroup

NIS netgroups은 `@group` 로 주어집니다. 넷 그룹 멤버의 호스트 부분만 멤버쉽 체크의 대상이 됩니다. 호스트 부분이 비거나 `-`면 무시됩니다.

> $ man exports, [Machine Name Formats] <br/><br/>
> netgroups <br/>
> NIS netgroups may be given as @group.  Only the host part of  each  netgroup  members  is consider  in checking for membership.  Empty host parts or those containing a single dash (-) are ignored.

### anonymous

어나니머스는 `*`로 지정되며 와일드카드와 혼동하면 안됩니다. 모든 클라이언트와 매치됩니다.

> $ man exports, [Machine Name Formats] <br/><br/>
> anonymous
> This is specified by a single * character (not to be confused  with  the  wildcard  entry above) and will match all clients.


만약 접속한 클라이언트가 여러 포맷에 매치되면, 위에 나열된 순서대로 첫 번째 포맷으로 매치됩니다.

 만약 클라이언트가 매치된 포맷이 위 나열 순서에서 같다면 (e.g. 여러 netgroup에 포함될 때) export line 기준 빠른 순서대로 설정됩니다.

---

## Example

``` console
/               master(rw) trusty(rw,no_root_squash)
```

모든 파일 시스템을을 머신(master와 trusty)에게 `export`합니다. no_root_squash 옵션의 효과로 모든 uid squashing은 호스트 신뢰성을 위해 동작하지 않습니다.

``` console
/projects       proj*.local.domain(rw)
/usr            *.local.domain(ro) @trusted(rw)
```

호스트 네임 와일드카드와 넷그룹(`@trusted`)의 예시입니다. 

``` console
/home/joe       pc001(rw,all_squash,anonuid=150,anongid=100)
```

PC/NFS 클라이언트를 다룹니다.

```console
/pub            *(ro,insecure,all_squash)
```

public FTP 디렉토리를 모든 호스트에게 `export` 합니다. nobody 계정으로 모든 요청을 실행합니다. `inscure` 옵션이 NFS를 실행하는 클라이언트 중 예약된 NFS 포트를 사용하지 않는 클라이언트를 허용합니다. 

```console
/srv/www        -sync,rw server @trusted @external(ro)
```

`@trusted`, `@extrernal` netgroup에 속하는 멤버들과 `server` 머신에게 디렉토리를 제공합니다.
`@trusted` 멤버와 `server`에게는 읽기 쓰기 권한(rw)을, `@external` 에게는 읽기 권한(ro)을 줍니다. 셋 모두 `sync` 옵션을 사용할 수 있습니다.


```console
/foo            2001:db8:9:e54::/64(rw) 192.0.2.0/24(rw)
```
디렉토리를 ipv6와 ipv4 주소 서브넷에게 제공합니다.



<!-- 여덟 번째 줄은  -->

<!-- `mount` 커맨드는 파일 시스템을 주어진 마운트 포인트에 추가합니다.
`/etc/fstab`은 `mount`명령어가 어떻게 다양한 파일시스템에서 `name space hierarchy`를 어셈블하는지 묘사합니다. 파일에서 필드별 의미는 다음과 같습니다.

1. 싱글 파일시스템
2. 마운트 포인트
3. 마운트 포인트의 기본 옵션을 설명합니다.

NFS 파일 시스템 마운트의 경우는 다음과 같습니다.
1. 외부 서버 이름과 마운트 될 디렉토리 경로.
2. 로컬의 마운트 포인트 디렉토리 경로
3. 마운트될 파일시스템 타입
4. 파일시스템이 마운트될 방법과 마운트 포인트의 파일에 접근하는 NFS 클라이언트의 행동을 설정할 옵션
5. NFS는 5번, 6번 필드를 쓰지않아 0값을 갖습니다.

```console
server:path   /mountpoint   fstype   option,option,...   0 0
```


서버 호스트네임은 fqdn, IPv4 주소, `[]`로 둘러친 IPv6 주소 등이 가능합니다. 
fstype 필드는 "nfs"를 사용하며 "nfs4"는 추천되지 않습니다. -->

# Command

## $ exportfs

외부에 공유된 NFS 파일 시스템을 유지합니다.

`NFS` 서버는 클라이언트가 접근할 수 있는 로컬 파일 시스템을 테이블로 유지합니다. 테이블에 있는 각각의 파일시스템들은 `exprted file system`으로 언급되거나 줄여서 `export` 라고 합니다.

`exportfs` 커맨드는 NFS 서버의 export 테이블을 유지, 점검합니다. master export 테이블은 `/var/lib/nfs/etab` 파일에서 관리되며, 클라이언트가 `NFS MOUNT` 요청을 보냈을 때 `rpc.mountd` 에 의해 읽힙니다.

> $ man exports, [DESCRIPTION] </br> </br>
> The  exportfs  command  maintains the current table of exports for the NFS server.  The master export table is kept in a file named /var/lib/nfs/etab.  This file is read by rpc.mountd  when a client sends an NFS MOUNT request.

일반적으로 `master export table`의 초기화는 `/etc/exports`와 `$ exportfs -a` 명령어로 호출되는 `/etc/exports.d` 아래에 있는 파일 컨텐츠와 함께 이루어집니다. 하지만 시스템 관리자는 `$ exportfs`를 사용해 exports를 추가하거나 삭제할 때 `/etc/exports`나 `/etc/exports.d` 를 수정할 수 있습니다.

> $ man exports, [DESCRIPTION] </br> </br>
> Normally  the  master  export table is initialized with the contents of /etc/exports and files under /etc/exports.d by invoking exportfs -a.  However, a system administrator can  choose  to add  or  delete  exports without modifying /etc/exports or files under /etc/exports.d by using the exportfs command.

<!-- `exportfs`와 파트너 프로그램인 `rpc.mount`는 2 가지 모드에서 동작합니다.

lagacy mode : 리눅스 커널 버전 2.4와 이전 버전용

new mode : 2.6과 이후 버전용, nfsd 가상 파일 시스템이 `/proc/fs/nfsd`나 `/proc/fs/nfs`에 마운트되었을 때 이를 제공합니다.

2.6 버전에서는 파일시스템이 마운트되지 않으면 `legacy mode`로 적용됩니다. -->

(이하 생략)

**Exporting Directories**

첫 번째 시놉시스는 새 엔트리가 export table에 추가됐을 때 어떻게 `exportfs`를 호출하는지 보여줍니다. `exportfs -a`를 사용했을 때, `/etc/exports`와 `/etc/exports.d` 아래의 파일들에 나열된 exports 목록은 `/var/lib/nfs/etab` 에 추가됩니다. 커널의 export table도 필요하다면 업데이트 됩니다.

> $ man exports, [DISCUSSION - Exporting Directories] </br> </br>
> The  first  synopsis shows how to invoke exportfs when adding new entries to the export table. When using exportfs -a, all exports listed in /etc/exports and files under /etc/exports.d  are added to /var/lib/nfs/etab.  The kernel's export table is also updated as needed.

## $ showmount

NFS 서버의 마운트 정보를 보여줍니다. 클라이언트에서 사용합니다.

`$ showmount`는 원격 호스트에 있는 마운트 데몬에게 NFS 서버 상태 정보를 질의합니다. 

`$ showmount`는 옵션 없이 사용하면 호스트에 원격 호스트에 마운팅 중인 클라이언트 목록을 보여줍니다.

`-e`와 함께 사용하면 NFS 서버의 `export` 명단을 출력합니다.

## nfsstat

NFS 통계 정보를 나열합니다.

`nfsstat`은 NFS 클라이언트와 서버 활동에 관한 통계정보를 보여줍니다.


## $ rpcinfo

RPC 정보를 보고합니다.

`$ rpcinfo`는 RPC 서버로의 RPC call을 생성하고, 결과를 보고합니다.

> $ man rpcinfo, [DISCUSSION] </br></br>
> rpcinfo makes an RPC call to an RPC server and reports what it finds.

<!-- 첫 번째 시놉시스에서 `$ rpcinfo`는 호스트의 rpcbind에 등록된 모든 RPC 서비스를 나열합니다. 만약 host가 지정되지 않으면 로컬 호스트가 지정됩니다. 

`-s` 옵션은 간결한 정보를 출력합니다.

두 번째 시놉시스에서, rpcinfo는 


> $ man rpcinfo, [DISCUSSION] </br></br>
> In the first synopsis, rpcinfo lists all the registered RPC services with rpcbind on host.  If host is not specified, the local host is the default.  If -s is used, the information is displayed in a concise format. -->
