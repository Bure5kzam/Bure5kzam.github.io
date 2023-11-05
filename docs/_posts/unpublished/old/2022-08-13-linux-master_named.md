---
layout: single
title: 우분투 DNS
date: 2022-08-13 21:29
category: LinuxMaster
---

# Overview

`named`는 `Domain Name Server` 서비스를 위한 `Deamon`으로, IP 주소와 자연어 주소간 변환을 제공합니다.

예를 들어 주소창에 www.google.com을 검색하면, 구글에 접속하기 전에 DNS에게 www.google.com의 실제 IP 주소를 요청합니다.

회사 IP 주소도 정리가 안되는데, 기회가 되면 하나 세팅해두고 싶네요..

# Concept

`named`

## Type of domain name

도메인 네임은 표현 범위에 따라 FQDN(Fully Qualified Domain Name) 과 PQDN (Partially Qualified Domain Name)으로 나뉩니다.

도메인만 명시 한 주소를 PQDN 이라고 하고, 도메인 앞에 호스트까지 명시한 주소를 FQDN이라고 합니다.

```console
# example 
github.io                 # PQDN
bure5kzam.github.io       # FQDN, 호스트는 도메인에 포함됩니다.
```

## Transration of domain name

누군가 네임 서버에 도메인 네임의 IP 주소를 알려달라고 요청하면, 네임 서버는 IP 주소를 반환하기 위해 아래 행위를 수행합니다.

- 캐싱된 데이터가 있는지 찾습니다. 요청한 도메인이 이전에 찾았던 주소라면, 빠르게 응답하기 위해 가까운 캐시 저장소에 IP 주소를 저장해뒀을 것입니다.
- slave 네임서버에 재귀적으로 질의 합니다.

~~네임 서버는 실제 주소를 가지고 있는 `Authoritative name server`와 `Recursive name server`로 나뉘는데, 사용자가 최상위 DNS 서버에 쿼리를 요청하면 `Recursive name server` 에게 재귀적으로 요청해 `Authroitative` 서버에 도달하는 방식입니다. 한번 반환된 정보는 Recursive 서버에 캐싱되므로 반복될 필요가 없습니다.~~

# $ named

> 아래 내용은 CentOS 7 기준입니다.

 <!-- CentOS 기준 기본 DNS 주소를 `/etc/resolv.conf` 파일에 보관하고 있습니다. 그리고 -->

 named는 ISC(Internet System Consortium)에서 제공하는 Bind 9 패키지에 포함된 DNS입니다. 
달리 설정하지 않으면 기본 설정 파일은 `/etc/named.conf`입니다.

## man page

시험을 응시할 때 `man named.conf`에서 문법을 확인할 수 있지만 설명은 나와있지 않습니다.

```console
# part of named.conf man pages

CONTROLS
           controls {
                inet ( ipv4_address | ipv6_address |
                    * ) [ port ( integer | * ) ] allow
                    { address_match_element; ... } [
                    keys { string; ... } ] [ read-only
                    boolean ];
                unix quoted_string perm integer
                    owner integer group integer [
                    keys { string; ... } ] [ read-only
                    boolean ];
           };
```

참고하실 점은

- Underline : 설정할 때 직접 입력해야 하는 부분 (위 예시에는 표현되지 않음)
- brackets `\{\}` : 필수적으로 입력해야 하는 부분
- Squar brackets `\[\]` : 필수적이지 않은 선택적인 부분
- `...` : 여러개 입력할 수 있음.
- string : 문자열
- quoted_string : `"`로 감싸야 하는 문자열
- boolean : true or false ?? (쓰는데 갑자기 정확하게 기억이 안나네요...)

## named.conf 문법

```console
options {
        listen-on port 53 { 127.0.0.1; };
        listen-on-v6 port 53 { ::1; };
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        recursing-file  "/var/named/data/named.recursing";
        secroots-file   "/var/named/data/named.secroots";
        allow-query     { localhost; };

        /*
         - If you are building an AUTHORITATIVE DNS server, do NOT enable recursion.
         - If you are building a RECURSIVE (caching) DNS server, you need to enable
           recursion.
         - If your recursive DNS server has a public IP address, you MUST enable access
           control to limit queries to your legitimate users. Failing to do so will
           cause your server to become part of large scale DNS amplification
           attacks. Implementing BCP38 within your network would greatly
           reduce such attack surface
        */
        recursion yes;

        dnssec-enable yes;
        dnssec-validation yes;

        /* Path to ISC DLV key */
        bindkeys-file "/etc/named.root.key";

        managed-keys-directory "/var/named/dynamic";

        pid-file "/run/named/named.pid";
        session-keyfile "/run/named/session.key";
};

logging {
        channel default_debug {
                file "data/named.run";
                severity dynamic;
        };
};

zone "." IN {
        type hint;
        file "named.ca";
};
```

설정 파일은 `Statements`(구문) 과 `Clauses` (절)로 구성됩니다.

- Statements : 들여쓰기가 되어있지 않은 대분류 항목
- Cluses : 들여쓰기된 Statements의 세부 항목

`Statements` 구문에 포함되는 절들을 `{}`로 묶어 주는 포맷입니다.

### Statements

여기서는 `Statements` 중 `acl`, `options`, `zone`을 설명합니다.

```console
 acl ZONE-NAME {
  # 제어할 IP 주소들에 이름을 붙여 사용.
  # 서버에 접근하는 클라이언트나 다른 네임서버가 될 수 있음.
    IP-ADDRESS/SUBNET...;
 };
 

options {
  # named의 서버 옵션
  option parameters...
}

zone ZONE-NAME ZONE-CLASS {
  # 서버에서 제공하는 파일들. 도메인 주소에 연결.
     <zone-options>;
     [<zone-options>; ...]
};

include  "FILE-NAME"
```


### acl 

acl (Access Control List) 구문은 여러 IP 주소들을 이름으로 지정할 때 사용합니다.

호스트 명을 지어두면 호스트를 인자로 받는 `allow-query`나 `allow-transfer` 옵션에 추가할 때 편합니다.

끝에 `;`를 붙이면 여러개 붙일 수 있습니다.


```console
# example
 acl black-hats {
    10.0.2.0/24;
    192.168.0.0/24;
 };

acl red-hats {
    10.0.1.0/24;
 };
```

acl구문 안에서 아래 예약어들도 사용할 수 있습니다.

- any : 모든 ip 주소
- localhost : 로컬 시스템에서 사용중인 모든 IP 주소
- localnets : 로컬 시스템이 연결된 모든 네트워크의 모든 IP 주소
- none : 어떤 IP 주소와도 매치하지 않음

### options

네임 서버의 동작 제어와 관련된 옵션을 설정하는 구문입니다.

| 옵션            | 내용                                                                                                                                                                                                               |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| statistic-file  | 통계 파일이 생성될 디렉토리와 이름을 지정합니다.<br/> 기본값은 `/var/named/named.stats`입니다.                                                                                                                     |
| dump-file       | 덤프파일의 경로와 파일명을 지정합니다.                                                                                                                                                                             |
| directory       | zone 파일이 존재하는 경로를 지정합니다. <br/> 필수 옵션입니다.                                                                                                                                                     |
| allow-query     | named에 포함될 IP 주소나 호스트 이름들 <br/> 지정하지 않으면 모든 서버가 접근할 수 있습니다.                                                                                                                       |
| blackhole       | named 접근을 막을 IP 주소나 호스트 이름들   <br/> 지정하지 않으면 모든 서버가 접근할 수 있습니다.                                                                                                                  |
| allow-transfer  | zone 파일 내용을 복사할 대상을 지정합니다. <br/> 지정하지 않으면 모든 서버에 복사될 수 있습니다.                                                                                                                   |
| forwarders      | 질의를 위임할 수 있는 서버 주소를 지정합니다. <br/>  forword 옵션과 함께 사용됩니다.                                                                                                                               |
| forward         | 질의를 받았을 때 직접 응답할지 다른 서버에 질의를 넘길지 지정합니다. <br/> `first` : 타 서버에 질의를 해보고 없으면 본인이 답함. <br/> `only` : 타 서버가 적절한 답을 응답하지 않으면 본 서버도 응답하지 않음.<br> |
| listen-on       | 특정 인터페이스를 통한 질의만 받고 싶을 때 지정합니다.                                                                                                                                                             |
| notify          |                                                                                                                                                                                                                    |
| allow-recursion |                                                                                                                                                                                                                    |


```console
options {
    blackhole { black-hats; };
    allow-query { red-hats; };
    allow-recursion { red-hats; };
 }
```

옵션별 사용 문법은 `man named.conf`로 확인할 수 있습니다. 

### include

include 문을 사용하면 다른 파일에 작성한 내용을 `named.conf`에 포함시킬 수 있습니다.  민감한 데이터를 별도의 파일에서 다룰 수 있습니다. 파일은 절대경로로 명시합니다.

### zone

도메인 네임을 ip주소로 변환해달라고 요청하면 dns는 zone file을 참고해 반환할 주소를 찾습니다.

`named.conf`에서 도메인 주소에 관한 정보를 가지고 있는 부분을 `Zone statement`, 실제 정보를 가진 파일을 `Zone File`이라고 합니다. 반대로 IP 주소에서 도메인 주소로 변환할 때 필요한 정보를 가진 파일인 `reverse zone`도 있습니다.

```console
# exapmle 1

zone string [ class ] {
  type ( delegation-only | forward | hint | master | redirect | slave
                    | static-stub | stub );
  file quoted_string;
}

```

<!-- zone name이 없으면 zone-file의 $origin 값이 할당됩니다.

아래 예처럼 zone 구문에서 이름을 example.com하면 , exapmle.com 존 파일 안의 호스트 네임 끝에 붙게 됩니다.

```console
zone "example.com" IN {
  type master;
  file "example.com.zone";
  allow-update { none; };
};
``` -->

**Zone 옵션들**

| Zone 옵션 | 내용                                                                                                                         |
| --------- | ---------------------------------------------------------------------------------------------------------------------------- |
| type      | DNS 서버의 조율를 나타냅니다. `master`, `slave`, `hint` 가 될 수 있습니다.                                                   |
| file      | 제공할 Zone file의 이름을 지정합니다. 일반적으로 Zone file은 `도메인명.zone`, Reverse Zone file은 `도메인명.rev`를 가집니다. |

<!-- - allow-query
  - 쿼리를 요청할 수 있는 호스트 목록.
- allow-transfer
  - zone의 정보를 전송 요청할 수 있는 slave server들을 지정합니다.
  - 기본값은 all 입니다.
- allow-update 
  - zone 정보를 동적으로 업데이트할 수 있는 호스트들을 지정합니다.
  - 기본값은 all deny입니다.
- file
  - named 워킹 디렉토리에 있는 현재 zone의 설정 파일의 경로입니다.
- master : 이 서버에 zone information 을 요청할 마스터 서버를 지정합니다. (slave 일때만 가능)
- notify : zone 설정이 업데이트되면 slave server를 새로 업데이트 할지 여부.
  - yes no, explicit이 있음.
- type
  - delegation-only : 도메인이 없는 .com, .net, .org 요청은 NXDOMAIN으로 처리됩니다. 이 옵션은 Top Level Domain Server거나 회귀 캐쉬 서버에서 root zone files 에만 적용됩니다.
  - forward : 이 zone에 대한 정보를 다른 네임서버로 전달합니다.
  - hint : 영역이 달리 알려져 있지 않을 때 쿼리를 해결하는 루트 네임서버를 가리키는 데 사용되는 특수 유형의 영역입니다. 힌트 영역 을 사용하면 기본값 이외의 구성이 필요하지 않습니다 .
  - master, slave : 해당 존의 master, slave로 설정합니다. 상대방의 ip를 입력합니다. -->

아래 예시는 example.com을 호스팅하는 primary nameserver를 위한 zone statement 문입니다.
(192.168.0.1)

```console
# Master DNS
zone "example.com" IN {
  type master;
  file "example.com.zone";
  allow-update { none; };
};
```

example.com 도메인을 요청하면 해당 zone 파일을 참조하며:ㅃ: update를 허용하지 않습니다.

아래의 example.com의 slave server(secondary server) zone은 위 예와 조금 다릅니다. type은 slave로 설정되었으며 allow-update에 master server의 주소가 있습니다.

``` console
#Slave DNS
zone "example.com" {
  type slave;
  file "example.com.zone";
  masters { 192.168.0.1; };
};
```

이 zone 구문은 slave server가 example.com에 대한 정보를 master server에 쿼리하도록 설정합니다. 마스터 서버에서 받은 정보는 /aver/named/example.com.zone 파일에 저장됩니다.

## Zone file

`Zone file`은 도메인 주소를 IP 주소로 변환하는데 필요한 정보를 갖고 있습니다. 기본적으로 `/var/named`에 보관하며, `httpd.conf`의 Zone 구문 안에서 `file` 옵션을 지정하면 다른 경로에 있는 파일도 사용할 수 있습니다. 반대로 IP 주소를 도메인 주소로 바꾸는 `Reverse zone file`도 있습니다.


Zone file 구문은 도메인들을 정의하는 `Resource Records`와 부가 옵션을 적용하는
`Directive`로 구성됩니다. `Resource Records`는 필수입니다.


### Directive

존파일에 특수 설정을 적용하도록 지시합니다. Zone에 필수적인 정보를 지정하는 Resource Records와 다르게 없어도 동작할 수 있습니다.

디렉티브는 달러 기호 문자($)와 함께 디렉티브 이름으로 시작합니다. 일반적으로 영역 파일의 맨 위에 나타납니다.


`$INCLUDE`: 다른 존 파일을 포함시킵니다.
이렇게 하면 추가 구역 설정을 주 구역 파일과 별도로 저장할 수 있습니다.

`$ORIGIN` : 도메인 네임을 정의합니다. 이후 레코드 중 완전히 명시 되지 않은 (`.`로 끝나지 않는) 레코드들에 추가됩니다. 

`$TTL` : 다른 서버에서 도메인 정보를 조회해서 가져가면 서버의 캐쉬에 정보가 저장되는데, 이 캐쉬 정보의 유효기간을 지정합니다. 숫자 뒤에 주 `W`, 일 `D`, 시 `H`, 분 `M`이 가능합니다.

```console
$ORIGIN example.com.
```

### Resource Record

존의 파라미터를 정의하고 개별 호스트에 식별자를 정의합니다.
각 줄은 하나씩 한 쌍으로 구성되며 앞에 ;를 붙이면 주석으로 인식됩니다.


**A** : Address record

```console
<host>     IN     A     <IP-address>
```

호스트 이름에 할당할 주소를 지정합니다. 호스트 이름이 생략된 경우 top of the namespace의 기본 IP를 가리킵니다.

```console
             IN     A       10.0.1.3    # example.com의 IP 주소는 10.0.1.3
server1      IN     A       10.0.1.5    # server1.domain의 IP 주소는 10.0.1.5
```

**CNAME** : 하나의 이름을 다른 이름과 매치합니다. alias record라고도 합니다.

```console
; <alias-name>     IN     CNAME       <real-name>
server1      IN     A       10.0.1.5
www          IN     CNAME   server1
```

위 예는 이름 server1에 10.0.1.5를 할당하고 www라는 별칭을 갖도록 합니다.

**MX** : 해당 네임스페이스로 보낸 메일이 어디로 가야하는지 알려줍니다.

```console
;     IN     MX     <preference-value>  <email-server-name>
      IN MX 10 mail.example.com. 
      IN MX 20 mail2.example.com.
```

이 예에서 example.com이 메일을 받을 때 mail.example.com 이메일 서버는 mail2.example.com 서버보다 우선시됩니다.

**NS** : Nameserver record. 존에 대한 권한있는 네임 서버를 알립니다.
name server는 Fully Qulified Domain Namesperver여야 합니다.

이레 예는 도메인에 권한이 있는 두 개의 네임서버가 지정됩니다. 이 네임서버가 슬레이브인지 아니면 마스터인지는 중요하지 않습니다. 둘 다 여전히 권한이 있는 것으로 간주됩니다.

```console
      IN NS dns1.example.com. 
      IN NS dns2.example.com.
```



**SOA** : Start of Authority resource record. 네임서버의 설정값을 선언합니다.

```console
@     IN     SOA    <primary-name-server>     <hostmaster-email> (
                    <serial-number>   # 영역파일이 수정 될 때마다 변경되는 번호입니다.
                    <time-to-refresh> # 슬레이브 서버가 데이터를 갱신하는 시간
                    <time-to-retry>   # 마스터 서버가 응답하지 않으면 기다리는 시간
                    <time-to-expire>  # 마스터가 응답하지 않으면 대기를 중지하는 시간
                    <minimum-TTL> )   # 다른 네임 서버가 영역을 캐시하는 시간
```

@는 $ORIGIN 또는 네임스페이스로 치환됩니다. primary-name-server는 도메인 권한을 가진 기본 네임 서버 호스트이고, hostmaster-email은 네임스페이스에 대해 연락할 사람의 이메일입니다.

## Reverse Zone file

IP 주소를 FQDN로 변환하기 위한 정보를 가진 파일입니다. `Zone file`과 거의 유사하나 아래와 같은 차이점이 있습니다.

- IP 주소 => FQDN 변환을 위해 `PTR`이라는 `resource record`를 사용함.
- 역주소.in-addr.arpa 네임스페이스를 가짐

`Reverse Zone file` 구문 양식입니다.


```console
<last-IP-digit>      IN     PTR    <FQDN-of-system>
```

`last-IP-digit` 필드는 FQDN이 가리키는 실제 IP 주소의 마지막 숫자를 의미합니다.

```console

$ORIGIN 1.0.10.in-addr.arpa.
$TTL 86400
@     IN     SOA    dns1.example.com.     hostmaster.example.com. (
                    2001062501 ; serial
                    21600      ; refresh after 6 hours
                    3600       ; retry after 1 hour
                    604800     ; expire after 1 week
                    86400 )    ; minimum TTL of 1 day

      IN     NS     dns1.example.com.
      IN     NS     dns2.example.com.

20    IN     PTR    alice.example.com.
21    IN     PTR    betty.example.com.
22    IN     PTR    charlie.example.com.
23    IN     PTR    doug.example.com.
24    IN     PTR    ernest.example.com.
25    IN     PTR    fanny.example.com.
```

작성된 존파일은 `named.conf` 파일의 `zone` 구문에서 아래처럼 사용됩니다.

``` console
zone "1.0.10.in-addr.arpa" IN {
  type master;
  file "example.com.rr.zone";
  allow-update { none; };
};
```

- reverse zone file의 이름은 주로 [domain].rr.zone 형식으로 작성
- 도메인 주소란에는 IP 주소의 처음 세 블록과 .in-addr.arpa.를 기입해줘야 Zonefile에서 사용되는 IP 블록과 연결할 수 있습니다.


# Reference

[MIT.edu, Red Hat Enterprise Linux 4: 참조 가이드 : 12.2 /etc/named.conf](https://web.mit.edu/rhel-doc/4/RH-DOCS/rhel-rg-en-4/s1-bind-namedconf.html)
[MIT.edu, Red Hat Enterprise Linux 4: Reference Guide : 12.3 Zonefile](https://web.mit.edu/rhel-doc/4/RH-DOCS/rhel-rg-en-4/s1-bind-zone.html)

[bind9](https://bind9.readthedocs.io/en/v9_18_4/chapter3.html#introduction)