---
layout: single
title: 우분투 마스터 m4 요약
published : false
---

m4 프로세서는 Sendmail의 설정파일 (.mc)를 컴파일해 (.cf) 로 변환하는데 사용합니다. 

m4는 줄 개념이 없는 stream-based 입니다. .mc 파일에 있는 `dnl` 키워드는 행을 삭제한다는 의미입니다. dnl 키워드에서 다음 개행 문자 까지 지우며 stream-based 인 m4의 결과물에 불필요한 공백을 줄여줍니다.

다른 중요한 지시어는 `define(A,B)` 입니다. 매크로 A에 값 B를 지정한다는 의미입니다.

```console
define(`SMART_HOST', `smart.foo.com') # SMART_HOSE 매크로에 smart.foo.com을 지정
```

m4는 인용할 때 인용문을 \`와 \'로 감쌉니다. 이 기호들은 인용문 내에서 사용할 수 없습니다.


# DOMAINS

`DOMAIN` 매크로로 참조되는 domain-dependent 정의를 한 파일에 모으고 싶을 수 있습니다.
Berkeley domain 파일에는 다음과 같은 포맷으로 호스트를 지정할 수 있습니다.

| 프로토콜 | 형식                                                                           |
| -------- | ------------------------------------------------------------------------------ |
| UUCP     | barbox!user (barbaox 머신에 인접한 유저들)[https://en.wikipedia.org/wiki/UUCP] |
| BITNET   |                                                                                |
| DECNET   |                                                                                |

# ACCEPTING MAIL FOR MULTIPLE NAMES

만약 호스트가 여러 이름으로 알려져있다면, class w augment가 필요합니다. 이것은 당신의 호스트가 알려진 이름 리스트이며, 이 리스트의 호스트 이름을 사용하는 주소로 보내지면 local mail로 취급받습니다. 

local mail 로 만들려면 두 가지 방법이 있습니다.

- /etc/mail/local-host-names 에 이름 리스트를 작성한 후 .mc file에서 ``FEATURE(`use_cw_file')'' 를 사용합니다.
- mc 파일에서 ``LOCAL_DOMAIN(`alias.host.name')''으로 추가합니다.

# Classes

classess는 일반적으로 파일이나 프로그램으로 채워집니다.
아니면 map에서 `F{ClassName}mapkey@mapclass:mapspec` 구문으로 채워집니다.

mapkey는 비어도 됩니다.

`RELAY_DOMAIN_FILE(``@LDAP\')`로 LDAP 에서 가져올 수도 있습니다. attribute sendmailMTAClassValue, objectClass인 sendmailMTAClass,  찾기는 서버가 처음 시작됐을 때만 수행됩니다. 

# Format of files and maps

클래스를 정의하는 파일들이나 `F{classname}`은 각각의 class 의 개별 요소를 포함하는 줄들로 구성됩니다. 예를 들어 `/etc/mail/local-host-names` 에는 아래 같은 컨텐츠가 있습니다.

```
my.domain
another.domain
```

맵은 쉘에서 `makemap hash MAP < MAP` 로 생성할 수 있습니다.

일반적으로 맵에서 생성된 텍스트 파일은 이런 폼을 가집니다.

```console
key     value
```

`key`와 `value`는 각각 `LHS`와 `RHS`로도 불립니다. LHS와 RHS의 구분자 (delimiter)는 비지 않은 white space 몇개 입니다.

# Directory layout

`/usr/shared/sendmail` 안의 서브 디렉토리는 아래와 같은 내용이 있습니다.

| 서브 디렉토리 | 내용                                                                                                                                                                       |
| ------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| m4            | 일반적인 지원 루틴. 중요하며 조심해야 합니다.                                                                                                                              |
| cf            | 설정 파일. 후미에 `.mc`가 붙으며 `m4` 명령어를 통해 실행돼야 합니다. <br/> 결과물은 `.cf`가 붙습니다.                                                                      |
| ostype        | 시스템 동작 타입에 대한 정의가 있습니다. `.mc` 파일에서 `OSTYPE` 매크로로 참조해야 합니다.                                                                                 |
| domain        | `.mc`파일에서 `DOMAIN` 매크로로 사용되는 정의들입니다. 사이트에 따라 다릅니다. <br/> ex) `CS.Berkeley.EDU.m4`는 `CS.Berkley.EDU` 서브 도메인에 있는 호스트들을 의미합니다. |
| mailer        | 메일러들입니다. `MAILER` 매크로로 참조됩니다.                                                                                                                              |
| sh            | 서브디렉토리 `cf`에 있는 `.mc` 파일들이 `.cf`로 빌드할 때 사용되는 쉘 파일들입니다.                                                                                        |
| feature       | 특별한 직교 기능들을 포함할 때 사용합니다. `FEATURE` 매크로로 참조됩니다.                                                                                                  |
| siteconfig    | 사이트 설정입니다. 예를들면 로컬에서 UUCP로 연결된 테이블 같은 것들입니다.                                                                                                 |

# FEATURES

`sendmail.mc`의 `FEATURE` 매크로는 특별한 기능들을 요청하는데 사용합니다. 이중에는 `/etc/mail` 안의 파일들과 관련된 기능들도 있습니다.
| feature     | 내용                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| use_cw_file | ` /etc/mail/local-host-names`을 읽고 sendmail 서버가 운영되는 호스트의 별칭을 얻습니다. <br/> static으로 지정하려면 파일에 `Cw<name> <name2>...`를 추가합니다.                                                                                                                                                                                                                                                                                                                                                                                                                  |
| use_ct_file | `/etc/mail/trusted-users`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| mailertable | 특정 도메인에 대한 라우팅을 오버라이딩하는데 사용되는 `mailer table`을 추가합니다. (local host를 지칭하는 class {w} 제외)                                     <br/> db에 있는 키들은 fqdn이나 pqdn이어야 하며 `mailer:domain` 폼을 따라야합니다. `mailer`는 내부적인 mailer name, `domain`은 메세지를 보낼 곳입니다. 이 maps들은 메세지 헤더에 반영되지 않습니다. <br/>  `local:user`는 특별한 폼으로 local mailer를 사용하는 유저를 나타냅니다. <br/> `local:`은 local mailer를 사용하는 이메일 주소의 유저입니다.                                                             |
| domaintable | 도메인 이름 매핑 기능을 제공하는 `domain table`을 가져옵니다. 오직 본인의 도메인을 사용할 때만 사용해야 합니다.                                                                                                                                                                                                                                                                                                                                                                                                    <br/> 기본적으로 `hash /etc/mail/domaintable` 이 사용됩니다. |

# /etc/mail/sendmail.cf

| 명령어  | 역할                                                                       |
| ------- | -------------------------------------------------------------------------- |
| Fw      | 호스트의 다른 이름들을 가진 파일 지정 <br/> `Fw/etc/mail/local-host-names` |
| Dj      | 메일 서버의 발신 도메인을 지정 <br/> `Dj$w.Foo.COM`                        |
| CP (??) | class P에서 호스트/ 도메인 표준은 토큰으로 끝납니다. <br/> `CP.`           |

