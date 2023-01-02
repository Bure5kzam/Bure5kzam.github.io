---
layout: single
title: 리눅스 마스터 sendmail
date: 2022-08-01 19:20
category: LinuxMaster
published : true
---

# Overview

리눅스 메일 서버를 다루는 글입니다.

# Concept

## mail component

메일 서비스는 세 가지 컴포넌트로 구성됩니다. 메일을 작성하는 `MUA`, 작성한 메일을 메일 서버로 전송하는 `MTA`,  메일서버에서 주기적으로 메일을 가져오는 `MDA`가 있습니다. 

| 약어 | 풀네임               | 설명                                                | 예시                   |
| ---- | -------------------- | --------------------------------------------------- | ---------------------- |
| MUA  | Mail User Agent      | 사용자가 메일을 송/수신 할 때 사용하는 클라이언트   | Thunderbird, Evolution |
| MTA  | Mail Transfer Agent  | 메일을 서버로 보내는 서버 클라이언트                | Postfix                |
| MDA  | Mail Delievery Agent | 서버에 수신된 메일을 사용자에게 전달해주는 에이전트 | Procmail, SpamAssassin |

컴포넌트마다 다른 프로토콜을 사용합니다.

| 약어  | 풀네임                        | 설명                                                                          | 예시                                      |
| ----- | ----------------------------- | ----------------------------------------------------------------------------- | ----------------------------------------- |
| SMTP  | Simple Mail Transfer Protocol | 서버와 클라이언트 사이에서 MTA가 사용하는 프로토콜. TCP / 25                  | 송신자 -> 메일서버, 메일서버 -> 메일 서버 |
| POP3  | POST OFFICE Protocol          | 메일 서버에서 메일을 가져오는 프로토콜 TCP / 110 **(가져오면 메일이 삭제됨)** | 메일서버 -> 수신자                        |
| IMAP4 | Internet Mail Access Protocol | 서버에서 메일을 가져오는 프로토콜 TCP / 143                                   | 메일서버 -> 수신자                        |

## Relaying

호스트 외부에서 전달받은 메세지를 다른지역으로 옮기는 것
> $ sendmail-cf/README, [ ANTI-SPAM CONFIGURATION CONTROL] <br/>
> Relaying (transmission of messages from a site outside your host (class {w}) to another site except yours) is denied by default. 

## Class

~~sendmail-cf/README의 FORMAT OF FILES AND MAPS 참고~~
> $ sendmail-cf/README [TWEAKING CONFIGURATION OPTIONS] <br/> <br/>
>/etc/mail/local-host-names
> Name of file used to get the local additions to class {w} (local host names).<br/> <br/>
> /etc/mail/trusted-users <br/> Name of                                        file used to get the local additions to class {t} (trusted users). <br/> <br/>
> /etc/mail/relay-domains <br/> Name of file used to get the local additions to class {R} (hosts allowed to relay).<br/> <br/>


sendmail에서는 서버에 접속하는 호스트들을 목록으로 관리하고 목록의 종류를 `Class`라고 합니다.

클래스를 정의할때는 `C`나 `F` 접두사를 사용합니다. 아래와 같이 사용합니다.

클래스 w에 host_a와 host_b를 추가하는 예시입니다.

```console
# `Cw` 뒤에 공백이 없는것은 오타가 아닙니다.
Cwhost_a host_blin
```

`F`는 클래스 명단을 파일에서 가져올 때 사용합니다.

```console
# w 클래스를 가져오는 예시
Fw/etc/mail/local-host-names
```


**w** : 호스트 외부 도메인
> $ sendmail-cf/README, [ ANTI-SPAM CONFIGURATION CONTROL] <br/>
> (transmission of messages from a site outside your host (class {w})

**m** : 로컬 도메인에 속한 호스트
> $ sendmail-cf/README, [ ANTI-SPAM CONFIGURATION CONTROL] <br/>
> then any host in any of your local domains (that is, class {m})

**R** : 릴리이가 허용된 도메인

> $ sendmail-cf/README, [ ANTI-SPAM CONFIGURATION CONTROL] <br/>
> You can allow certain domains to relay through your server by adding their domain name or IP address to class {R} using RELAY_DOMAIN() and RELAY_DOMAIN_FILE() or via the access database (described below)


# sendmail

sendmail은 유저와 메일 서버, 메일 세버와 메일 서버간 통신을 수행하는 MTA 입니다. SMTP 프로토콜로 `MUA`와 `MTA` 사이에서 메일 전달을 담당합니다. 다시말해, 유저 친화적인  인터페이스 보다 포맷팅된 메세지를 전송하는데 목적이 있습니다. 

> man sendmail [DESCRIPTION]
> Sendmail  is  not  intended  as a user interface routine; other programs provide user-friendly front ends; sendmail is used only to deliver pre-formatted messages.

비슷한 용도로 postfix가 있습니다.

## Files

`sendmail` 서버에 설정은 `sendmail.cf` 파일로 이뤄집니다.


파일 내엔 추가기능을 위해 `FEATURE` 매크로와 함께 지정된 파일들이 있는데, `/usr/share/sendmail-cf/README` 문서의 [FEATURE] 섹션에서 관련 파일 설명을 볼 수 있습니다. 8.10 버전을 기준으로 경로가 달라졌으며 아래는 8.14 기준임을 참고합니다.

> $ sendmail-cf/README [FILE LOCATION] <br/>
> sendmail 8.9 has introduced a new configuration directory for sendmail related files, /etc/mail.  <br/>
> ...
>  Beginning with 8.10, all files will use this directory by default (some options may be set by OSTYPE() files).



| 파일                         | 역할                                                                                                               |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| `/etc/mail/local-host-names` | 로컬 호스트의 이름의 별칭을 얻는데 사용. <br/> 로컬 호스트 이름을 의미하는 class{w}로 지정됨.                      |
| `/etc/mail/trusted-users`    | `trusted user`설정 파일.<br/> 해당 유저의 `envelop`은 `-f`를 경고없이 사용할 수 있음.                              |
| `/etc/mail/mailertable`      | 일부 도메인 주소의 라우팅을 오버라이딩 하는데 사용                                                                 |
| `/etc/mail/domaintable`      | 도메인 네임 매핑에 사용                                                                                            |
| `/etc/mail/access`           | 특정 도메인에게서 받은 메일을 허용, 거부 하는데 사용. (RELAY, REJECT) <br/> (부가적인 sendmail의 행동 제어도 가능) |
| `/etc/mail/aliases`          | 수신한 메일을 등록된 다른 계정에도 전달하기 위해 사용                                                              |
| `/etc/mail/virtusertable`    | 별칭의 도메인 명시 폼.여러 가상 도메인이 한 머신에서 제공되기위해 사용                                             |
| `/etc/mail/mailer.conf`      | 메일러 프로그램 구성                                                                                               |

> $ sendmail-cf/README [FEATURES] <br/>
> 
> /etc/mail/local-host-names<br/>to get alternate names for this host. This might be used if you were on a host that MXed for a dynamic set of other hosts. <br/><br/>
> /etc/mail/trusted-users<br/>to get the names of users that will be ``trusted'', that is, able to set their envelope from address using -f without generating a warning message. <br/><br/>
> /etc/mail/mailertable<br/>which can be used to override routing for particular domains (which are not in class {w}, i.e.  local host names)<br/><br/>
> /etc/mail/domaintable<br/>which can be used to provide domain name mapping.<br/><br/>
> /etc/mail/virtusertable<br/>A domain-specific form of aliasing, allowing multiple virtual domains to be hosted on one machine.<br/><br/>
> /etc/mail/access<br/>gives you the ability to allow or refuse to accept mail from specified domains for administrative reasons.  Moreover, it can control the behavior of sendmail in various situations.<br/><br/>
> /etc/mail/authinfo<br/>Provide a separate map for client side authentication information.<br/><br/>
> 

## /etc/mail/aliases

호스트의 별칭을 지정하는 파일입니다. 수신한 메일을 호스트 내 다른 계정으로 전달할 때 사용합니다.

메일의 헤더까지는 변환하지 못한다는 한계가 있습니다.

> $ vi /etc/aliases <br/>
> Aliases in this file will NOT be expanded in the header from Mail, but WILL be visible over networks or from /bin/mail.

`aliases`에 주소를 추가하려면 `$ newaliases`를 사용합니다. 

시험 중 구문을 참고하려면 아래를 참고합니다.

- `$ man aliases`
  - `aliases` 파일의 문법과 동작에 관한 설명
- `$man newaliases`
  - `aliases.db` 파일을 적용시킬 때 필요한 설명
<!-- - `vi /etc/mail/aliases` -->

**/etc/aliases 구문**

`aliases` 파일은 이름 _name_ 과 치환될 별칭 _arr_ 여러개가 한 줄에 지정됩니다. 

```console
name: addr_1, addr_2, addr_3, . . .
```

_arr_ 에는 아래의 내용이 들어갈 수 있습니다.

| _arr_             | 내용                                                                           |
| ----------------- | ------------------------------------------------------------------------------ |
| _local User name_ | 호스트에 존재하는 또 다른 유저이름. `getpwnam`에서 가능한 계정만 가능합니다.   |
| _local Filename_  | 메세지가 `/path/name` 파일에 추가됩니다. `/`로 시작하는 절대경로가 필요합니다. |
| _Command_         | 메세지를 명령어의 입력값으로 전달합니다(stdin).  파이프 `                      | `로 시작하는 명령어를 지정합니다. <br/> ` | command` |
| Include File      | `:include: /path/name` 경로에 명시된 이름들이 별칭으로 추가됩니다.             |
|                   | E-mail Address                                                                 | RPC 822 포맷의 e0mail 주소입니다.         |          |

별칭 변환은 로컬 이름에만 발생합니다. 메일이 딱 한번만 전달되기 때문에 여러번 발생할 수 없습니다.

> $ man aliases <br/>
> Aliasing occurs only on local names.  Loops can not occur, since no message will  be  sent  to any person more than once.

**sendmail과의 동작과정**

 name의 별칭이 발견되면 `sendmail`은 `owner-name`의 별칭을 체크합니다. 별칭이 싱글 주소로 변환되면, 메일(envelop)을 보낸 송신자의 주소가 덮어씌워집니다. address가 여러개 발견되면 `owner-name`으로 변경됩니다,

별칭 변환 후, 홈 디렉토리에 `.forwad`가 있는 수신자(호스트) 와 로컬은 해당 파일에 등록된 유저들에게 메세지를 보냅니다.


`/etc/aliases`를 재적용하려면 아래의 명령어 중 하나를 사용합니다.

```console
$ newaliases
$ sendmail -bi
$ sendmail -i
```

## /etc/mail/access

도메인이 보내는 메일을 허용하거나 거절하도록 설정할 수 있는 파일입니다.

파일 구문은 호스트인 `Recipient`와 취할 정책인 `Action`을 한 줄에 등록합니다.

<!-- >Turns on the access database feature.  The access db gives you the ability to allow or refuse to accept mail from specified domains for administrative reasons.  Moreover, it can control the behavior of sendmail in various situations. By default, the access database specification is: -->

아래 예시는 `bure5kzam@github.io`에서 전달된 메일은 거절하고, `naver.com` 도메인에서 전달된 메일은 다른 호스트에 전달하도록 설정합니다.

```console
# 예시

#Recipient                  Action

From:bure5kzam@github.io    REJECT
From:naver.com              RELAY
```

**Recipient** 은 메일 서버에 접근하는 호스트입니다. `태그:호스트` 형식으로 지정합니다.

| 태그    | 뜻                            |
| ------- | ----------------------------- |
| From    | 메일을 보낸 사람              |
| To      | 메일을 받을 사람              |
| Connect | 메일 서버에 접속한 클라이언트 |

> $ sendmail-cf/README, [ ANTI-SPAM CONFIGURATION CONTROL] <br/>
> Connect:        connection information (${client_addr}, ${client_name})
> From:           envelope sender
> To:             envelope recipient
> Notice: untagged entries are deprecated.

**Action**은 호스트를 어떻게 처리할지 `대문자`로 지정합니다.

| Action             | 뜻                                                                                                                                  |
| ------------------ | ----------------------------------------------------------------------------------------------------------------------------------- |
| RELAY              | 명시된 도메인으로부터 받은 메일을 다른 STMP 서버로 중계합니다. 이는 암묵적인 OK의 역할을 합니다.                                    |
| OK                 | 메일을 Accept합니다 (reject 당했을 때에도 포함). Accept와 Relay는 다릅니다. Accpet는 Relay보다 범위가 작습니다.                     |
| REJECT             | 송신자나 수신자를 메세지와 함께 Reject(거부) 합니다.                                                                                |
| DISCARD            | 메세지를 완전히 버립니다. 응답 메세지를 보내지 않습니다.                                                                            |
| ERROR:### any text | useful to mark error messages as such.                                                                                              |
| SKIP               | host/domain과 IP addresses/nets만 사용할 수 잇습니다. 현 엔트리는 accept나 reject 없이 검색을 중단하고 default action만 동작합니다. |


[sendmail-accessdb-example](http://www.linuxweblog.com/sendmail-accessdb-example)

수정된 access 테이블은 `makemap` 명령어로 DB 맵을 생성해야 적용할 수 있습니다.

### $ makemap

makemap은 stdin의 입력값을 읽고 _mapname_ 파일에 출력물을 저장합니다.

makemap은 _maptype_ 값에 따라 3가지 컴파일을 지원합니다.  (btree, has)

makemap [options] _maptype_ _mapnam_

> makemap hash /etc/mail/access < /etc/mail/access

makemap 입력값 포맷은 `db key` + 공백 + `value`입니다. `value` 는  파라미터 치환을 나타내는 `%n`을 포함합니다. #로 시작하는 줄은 주석처리됩니다.

## /etc/mail/sendmail.cf

sendmail 서버 환경설정 파일입니다. 

| 구문 | 설명                                                                             |
| ---- | -------------------------------------------------------------------------------- |
| Fw   | 메일을 받을 호스트 목록을 파일로 지정합니다. <br/> `Fw/etc/mail.local-host-name` |
| Cw   | 메일을 받을 호스트를 추가합니다. <br/> `Cwbure5kzam.com`                         |
| Dj   | 메일 발신 도메인을 지정합니다. <br/> `Djbure5kzam.com`                           |

## /etc/mail/local-host-names

접근하는 도메인 목록을 저장하는 파일입니다.

한 줄에 도메인 하나씩 기입하면 됩니다.

```console
bure5kzam.com
naver.com
```


## /etc/mail/virtusertable

<!-- 기기에서 가상 도메인을 갖게 해주는 별칭의 도메인 지정 포맷입니다. -->

<!-- 가상 도메인을 지정하는 파일입니다. 도메인은 같지만 호스트가 같은 계정이 있을 때 계정간에 포워딩을 지정할 수 있습니다. -->

가상 도메인을 지정하는 파일입니다. 한 머신에서 여러 도메인을 사용할 수 있게 해줍니다.

> $ sendmail-cf/README, [FEATURE] <br/>
> A domain-specific form of aliasing, allowing multiple virtual domains to be hosted on one machine.



```console
# 예시 1

info@foo.com  foo-info
info@bar.com  bar-info
joe@bar.com   error:nouser 550 No such user here
jax@bar.com   error:5.7.0:550 Address invalid
@baz.org      jane@example.net
```

- info@foo.com로 보낸 메일은 foo-info에게 전달됩니다.
- info@bar.com로 보낸 메일은 bar-info에게 전달됩니다.
- joe@bar.com, jax@bar.com 으로 보낸 메일은 에러메세지와 함께 거절됩니다.
- @bar.org로 보낸 메일은 jane@example.net으로 보내집니다.

```console
# 예시 2

@foo.org        %1@example.com
```

- `%1`는 오리지널 메일의 유저 이름으로 치환됩니다. 
  - `somone@foo.org`로 메일이 온다면 `someone@exapmle.com`으로 전송됩니다.
- 만약 오리지널이 `user+detail`로 구성되면 `%2`는 `detail`로, `%3`은 `+detail`로 치환됩니다.

파일을 적용하려면 `makemap`로 `virtusertable.db`를 변환해야 합니다.

# Reference

[freebsd, sendmail](https://docs.freebsd.org/doc/5.4-RELEASE/usr/share/doc/handbook/sendmail.html)

[mik.ua, sendmail.cf에서 class의 정의](https://docstore.mik.ua/orelly/networking_2ndEd/tcp/ch10_05.htm)