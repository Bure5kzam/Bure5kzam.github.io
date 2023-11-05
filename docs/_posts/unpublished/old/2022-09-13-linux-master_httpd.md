---
layout: single
title: 우분투 httpd
---

# Overview

httpd는 HTTP 프로콜을 이용하는 서버 프로그램입니다. 리눅스에서 웹서버를 구축할 수 있는 프로그램으로 apache나 nginx가 있습니다. `apachectl`로 데몬 하나만 호출하면 알아서 필요한 프로세스를 호출합니다.

# 실행모드

httpd는 두가지 모드로 실행할 수 있습니다.

1. pass-through : 커맨드에서 필요한 환경 변수를 옵션으로 지정하는 모드
2. SysV : apachectl + start, stop, restart 등의 간단한 동작으로 동작하는 SysV 모드.

일반적으로 쉘에서 `apachectl` 명령어로 httpd를 사용합니다.

혹시 아파치 설치 경로가 기본값이 아니면 apachectl의 스크립트를 수정해줘야 동작할 수 있습니다.

아파치 종료시 문제가 없으면 0을, 에러가 발생하면 1 이상의 정수를 반환합니다.

# $httpd

## Options

쉘에서 httpd를 직접 호출할 때 사용하는 옵션입니다.

| 옵션            | 내용                                                                                                                                                                                          |
| --------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| -d _serverroot_ | ServerRoot 경로를 지정합니다. 설정 파일보다 우선순위가 높습니다.  <br/> 기본값은 `/etc/httpd` 입니다.                                                                                         |
| -f _config_     | config 파일의 경로를 지정합니다. _config_ 가 `/`로 시작하지 않으면 (절대경로가 아니면) config를 찾을 때 `/ServerRoot path/config path`로 변환됩니다. <br/> 기본값은 `conf/http.dconf` 입니다. |

**httpd 동작 명령어**

`-k` 옵션 + 명령어로 httpd를 동작시킬 수 있습니다.

| 명령어        | 내용                  |
| ------------- | --------------------- |
| start         | 시작                  |
| restart       | 새시작                |
| graceful      | 연결 종료 없이 재시작 |
| stop          | 중단                  |
| graceful-stop |                       |

## Configuration

작성한 설정파일 적용하려면 `-f` 옵션으로 경로를 지정합니다. `/`로 시작하지 않으면 ServerRoot 기준 상대경로로 지정됩니다.

문법 예시는 `/etc/httpd/conf/httpd.conf` 를 참조하면 되고, 지시어 설명은 [apache.org, directives docs](https://httpd.apache.org/docs/2.4/mod/directives.html)를 참고합니다.

이 글에서는 자주 사용되는 일부 지시어만 다룹니다.

설정 값

```console
ServerRoot "/"                      # 서버의 루트 디렉토리 경로, 끝에 `/` 없이 기입.

ServerType standalone               # 값은 standalone 또는 inetd

PidFile     "/var/run/apache.pid"   # 데몬의 PID를 기록할 파일 경로
                                    # 여러 데몬에서 한 설정파일을 공유시 여기는
                                    # 변경해야함.

Listen      80                      # 데몬 포트 설정

ServerToken                         # 서버 응답시 헤더에 OS-Type 정보를 포함할지 설정
ServerAdmin                         # 서버 오류시 연락처 주소
Timeout
MaxClients


KeepAlive On                         # long-lived HTTP 세션 여부를 설정

MaxKeepAliveRequest 100              # 하나의 long-lived HTTP 세션에서 요청가능한 횟수

KeepAliveTimeout 5                   # long-lived HTTP 세션 유지 시간.

HostnameLookups Off
DocumentRoot “/usr/local/apache/htdocs”     # 웹 문서(html, php 파일)의 기본 디렉터리 
LoadModule foo_module libexec/mod_foo.so    # 모듈 목록에 구조체 추가.
```

### ServerType

응답 헤더에 포함할 정보의 범위를 지정합니다. 데몬 호스트의 OS, 모듈 정보, 버전을 포함할 수 있습니다.

| 값                      | 예시                                            |
| ----------------------- | ----------------------------------------------- |
| Full (or not specified) | Server: Apache/2.4.2 (Unix) PHP/4.2.2 MyMod/1.2 |
| Prod`[`uctOnly`]`       | Server: Apache                                  |
| Major                   | Server: Apache/2                                |
| Minor                   | Server: Apache/2.4                              |
| Min`[`imal`]`           | Server: Apache/2.4.2                            |
| OS                      | Server: Apache/2.4.2 (Unix)                     |


### ServerAdmin

서버에서 오류가 발생하면 클라이언트로 보내는 오류 메세지에 ServerAdmin 연락처 주소를 포함해 전송하게 됩니다. 값이 URL이 아니라고 판단되면 mailto:라고 가정하며, 실제로 이메일을 제공하는 것이 좋습니다.

```console
ServerAdmin www-admin@foo.example.com
```

### LoadModule
모듈의 위치를 지정합니다. ServerRoot의 하위디렉토리인 modules에서 읽습니다. 모듈들은 파일의 module 자료형 외부 변수명으로 사용할 수 있습니다.

### `<`Directory _directory-path_`>`


특정 디렉토리와 내부에 한정되는 Directives를 묶을 때 사용합니다.

디렉토리 경로에서 와일드카드 적용시 주의사항으로, `/`는 적용되지 않으며 depth가 다르면 맞춰주어야 합니다.

아래는 와일드카트 depth가 달라 target을 매치하지 못하는 예시입니다.

```console
/home/user/public_html                  # target

<Directory "/*/public_html">            # mismatch
<Directory "/home/*/public_html">       # matched
```

[directory, apache.org](https://httpd.apache.org/docs/2.4/mod/core.html#directory)

### Options

`Options`는 특정 디렉토리에서 사용할 수 있는 서버 기능을 제어합니다.


 부모 디렉토리의 `Options` 값을 적용받을 수도 있는데, Options 값 접두사로 `+`나 `-` 를 붙이면 부모 `Options` 값에서 추가하거나 제외합니다.

| 값            | 의미                                                                              |
| ------------- | --------------------------------------------------------------------------------- |
| Indexes       | `DirectoryIndex`의 파일이 없으면 mod_autoindex 지정 디렉토리 목록을 생성.         |
| FolowSymLinks | 서버가 디렉토리 안에 있는 symbolic links를 따라갑니다. <br/> 기본으로 적용됩니다. |
| includes      | mod_include에서 제공받은 includes 들이 허용됩니다.                                |

### DirectoryIndexs

디렉토리 접속 요청시 보여줄 인덱스 파일 경로입니다. 클라이언트가 url 뒤에 '/'를 붙여서 요청하면 인덱스를 반환합니다.

파일을 여러개 지정할 수 있고 가장 처음 존재하는 파일을 반환합니다.
파일이 없고 Indexes가 설정되어있으면 직접 만든 목록을 보여줍니다.


### AllowOverride

.htaccess 파일이 있으면 적용합니다.

값은 `All` , `None`, `directive-type` 을 가질 수 있습니다.

### Require all

### Options Index FollowSymLinks

# Command

## $ htpasswd

HTTP 유저 인증 파일을 생성, 수정하는 명령어입니다.

htpasswd는 HTTP 유저의 기본 인증 절차에 사용되는 유저네임과 패스워드를 저장하는데 사용되는 flat-file을 생성하고 업데이트할 때 사용합니다.

만약 파일에 접근할 수 없으면 에러를 반환합니다.

Apache HTTP 서버에서 사용할 수 있는 리소스들은 `$ htpasswd` 명령어로 생성된 파일에 등록된 유저만 접근할 수 있도록 제한될 수 있스빈다. 이 프로그램은 오직 `flat-file` 안의 유저 이름과 비밀번호만 관리합니다. 다른 유형의 데이터 저장소에서 사용하기 위해 암호 정보를 암호화하고 표시할 수 있습니다. DBM 데이터베이스를 사용하려면 dbmmanage 또는 htdbm을 참조하십시오.


 Apache 서버 내의 리소스를 파일에 등록된 유저들에게만 제공할 수 있습니다.

```console
$ htpasswd -c /etc/password ihduser                 # ihduser 유저를 생성하고 /etc/password 파일에 저장합니다.
```

# Reference
[httpd directives, apache.org](https://httpd.apache.org/docs/2.4/mod/directives.html)

<!-- [Example Acache httpd.conf, linuxhint](https://linuxhint.com/apache-httpd-configuration/) -->