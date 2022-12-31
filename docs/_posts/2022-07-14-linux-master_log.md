---
layout: single
title: 리눅스 로그 (rsyslog)
date: 2022-07-14 21:52
category: LinuxMaster
---

# Overview

리눅스에서 유저 접속, 프로그램 실행 같은 시도를 하면 로그로 남습니다.
로그 파일들은 대부분 `/var/log` 디렉토리에 존재하며, 로그 설정 파일을 수정해 (`/etc/syslog.conf`) 파일 경로, 이름 등을 수정할 수 있습니다.

## Bianry Files

바이너리 형태로 저장되는 로그 파일들은 명령어로 조회해야 합니다.
로그인이나 명령어 사용 기록 같이 계속해서 쌓이는 로그들이 바이너리 형태로 존재합니다.

| 로그 파일          | 내용                                                                                                                          |
| ------------------ | ----------------------------------------------------------------------------------------------------------------------------- |
| `/var/run/utmp`    | 시스템 현재 상태, 시스템 부팅 시간, 터미널에서 사용자의 로그인, 로그아웃 기록, 시스템 이벤트에 대한 계정                      |
| `/var/log/btmp`    | 로그인 실패기록                                                                                                               |
| `/var/log/wtmp`    | 로그인, 로그아웃 성공 기록                                                                               , boot/shutdown 기록 |
| `/var/log/lastlog` | 유저별 마지막 접속 기록                                                                                                       |

# Command

## $lastlog
유저별 마지막 로그인 기록을 확인하는 명령어입니다.
`/var/log/lastlog` 파일을 참고합니다.

```console
[root@localhost ~]# lastlog
사용자이름       포트     어디서           최근정보
root             tty1                      일  9월  4 16:07:41 +0900 2022
bin                                        **한번도 로그인한 적이 없습니다**
daemon                                     **한번도 로그인한 적이 없습니다**
adm                                        **한번도 로그인한 적이 없습니다**
lp                                         **한번도 로그인한 적이 없습니다**
tss                                        **한번도 로그인한 적이 없습니다**
a1               pts/0                     일  8월 28 19:45:37 +0900 2022
a2               pts/0                     월  8월 29 10:31:57 +0900 2022
```

## $last

시스템에 로그인한 유저 로그와 시스템 부팅 기록을 표시합니다.

- 로그인 성공 기록과 로그아웃 기록
- 시스템 부팅과 종료 기록

`/var/log/wtmp` 파일을 참조합니다.

```console
[root@localhost ~]# last
root     tty1                          Sun Sep  4 16:07   still logged in   
reboot   system boot  3.10.0-1160.62.1 Sun Sep  4 16:07 - 19:52  (03:45)    
root     tty1                          Sun Aug 28 19:43 - crash (6+20:24)   
reboot   system boot  3.10.0-1160.62.1 Sun Aug 28 19:39 - 19:52 (7+00:13)   
root     tty1                          Tue Aug 23 10:47 - 10:47  (00:00)    
reboot   system boot  3.10.0-1160.62.1 Sat Aug 20 10:21 - 19:52 (15+09:30)  
root     tty1                          Thu Aug 18 13:36 - crash (1+20:45)   
reboot   system boot  3.10.0-1160.62.1 Thu Aug 18 13:34 - 19:52 (17+06:18)  
root     tty1                          Sun Jul 31 13:43 - crash (17+23:51)  
```

## $lastb

로그인 실패 기록을 표시합니다.
`/var/log/btmp` 파일을 참조합니다.

## $dmesg

커널버퍼에 담긴 메세지들을 출력합니다.

```console
dmesg [options]
```

# rsyslogd

`rsyslog`는 로그 데몬 유틸리티입니다. 프로그램이 rsyslogd에게 메세지를 보내면 로그 파일을 생성 합니다.
로그 파일들을 주기적으로 삭제하고 관리하는 `logrotate` 명령어와 함께 사용합니다.

## rsyslogd 설정하기

`rsyslog`는 기본 설정파일인 `/etc/rsyslog.conf` 을 참고하며, 사용자 설정 파일을 사용하려면 `-f` 옵션으로 경로를 지정합니다.
설정 파일 구성의 예시는 `man 5 rsyslog.conf`에 있습니다.

설정 구문은 크게 `Selector`와 `Actions`으로 구성됩니다. `Selector`에 해당하는 프로세스에서 메시지가 발생했을 때 취할 `Actions`을 설정할 수 있습니다.

### Selector

rsyslog.conf에서 Selector부분은 메시지가 발생했을 때 감지할 **대상**과 취할 **액션**을 명시합니다.
Selector는 Facility와 Priority로 구분되며, 작성 시에 Facility.Priority 형태로 기재합니다.

간단한 정보는 `man rsyslog.conf`의 `Selectors` 섹션에서 볼 수 있고 자세한 정보는 `man 3 syslog` 에서 관련정보를 찾을 수 있습니다.

#### Facility

로그 메세지를 발생시킨 어플리케이션 유형을 의미합니다.

```console
auth : 인증 및 인증 권한에 관한 명령
authpriv : 인증 메세지 중 민감한 정보를 담은 메시지
cron, daemon, kern, lpr, mail,  mark,  news,  security  (same  as  auth),  syslog, user, uucp and local0 ~ local7.
```

#### Priority

경고 레벨을 의미 하는 Priority는 아래처럼 구성됩니다.

 ```
 debug, info, notice, warning,  warn  (same  as warning), err, error (same as err), crit, alert, emerg, panic (same as emerg).
 ```

### Actions

Actions은 메시지 발생 시 어떻게 할지를 적는 필드입니다.
일반적으로 파일의 위치를 명시하면 logfile로 사용할 수 있으며, 이외에도 다른 호스트에 있는 데이터베이스에 로그를 작성하는 등의 액션도 할 수 있습니다.

# $logrotate

logrotate는 로그파일들을 압축, 회전, 메일로 전송하는 명령어입니다. 큰 규모의 로그파일들을 자동으로 관리하기위해 사용합니다.


## 사용법

일반적으로 logrotate는 cron으로 등록하고 매일 실행시키는 방식으로 사용합니다.
하루에 한번 이상 로그들을 수정하지 않지만 다음과 같은 경우 동작합니다.

- 로그 핸들링 기준 Size를 넘긴 경우
- 강제 실행 옵션 `-f` 사용

```console
logrotate [-dv] [-f|--force] [-s|--state _file_] _config_file_ ..
```

| 옵션                     | 내용                                                                                                                                               |
| ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| -v, --verbose            | 회전하는 동안 화면에 표시                                                                                                                          |
| -d, --debug              | 디버그 모드르 설정하고 `-v` 적용. <br/> 이 모드에서는 로그나 `logrotate 상태 파일의 변화가 반영되지 않습니다.                                      |
| -f                       | 강제 적용 옵션입니다. 당일 실행 회수와 상관없이 적용합니다.<br/> -  새 옵션을 추가했고 바로 적용하고 싶을 때 <br/> - 파일을 추가했거나 삭제했을 때 |
| -m _command_             | 메일을 메일로 송신하도록 지정하는 옵션. `subject`와 `recepitent`를 인자로 줘야함. 기본 메일 커맨드는 `/bin/main -s`                                |
| -s, --state _state-file_ | state file을 명시합니다. <br/> - 여러 유저로 logrotate를 실행할 때 유용합니다.                                                                     |


아래는 `man logrotate` 의 `CONFIGURATION FILE` 섹션을 해석한 내용입니다.

## 설정 파일

logrotate는 커맨드에서 설정파일을 여러개 입력할 수 있으며, 디렉토리 경로를 입력하면 폴더 내의 설정 파일들을 사용합니다.
나중에 등록된 설정 파일이 이전 파일을 오버라이딩 하기 때문에 순서가 중요합니다.
보통 설정 파일 하나에 include로 다른 파일들을 추가하는 구성이 일반적입니다.

## 예시

처음 몇 줄들은 global options입니다. (# 주석 제외)
예시는 rotated 후 압축되도록 지시한 내용입니다.

``` console
# sample logrotate configuration file 전문

compress 

/var/log/messages {
    rotate 5
    weekly
    postrotate
        /usr/bin/killall -HUP syslogd
    endscript
}

"/var/log/httpd/access.log" /var/log/httpd/error.log {
    rotate 5
    mail www@my.org
    size 100k
    sharedscripts
    postrotate
        /usr/bin/killall -HUP httpd
    endscript
}

/var/log/news/* {
    monthly
    rotate 2
    olddir /var/log/news/old
    missingok
    postrotate
        kill -HUP `cat /var/run/inn.pid`
    endscript
    nocompress
}

~/log/*.log {}
```


```console
/var/log/messages {
    rotate 5
    weekly
    postrotate
        /usr/bin/killall -HUP syslogd
    endscript
}

~/log/*.log {}
```

위는 파일 /var/log/messages에 대한 logrotate 설정입니다.
- 5 주동안 5번씩 주마다 회전한 후 삭제될 것입니다.
- 로그가 회전한 후, (하지만 old version들이 압축되기 전에) /sbin.killall -HUP 와 syslogd가 실행됩니다.
- 

```console
"/var/log/httpd/access.log" /var/log/httpd/error.log {
    rotate 5
    mail www@my.org
    size 100k
    sharedscripts
    postrotate
        /usr/bin/killall -HUP httpd
    endscript
}
```

위는 두 파일에 모두 적용되는 설정입니다. 

- 파일이 100k 가 넘어가면 주기에 상관없이 회전됩니다.
- 오래된 로그 파일은 5번 회전하고 나면 삭제되는 대신 www@my.org로 전송됩니다.
- sharedscripts는 postrotate script가 각 로그에 한번이 아니라, old logs가 완전히 압축된 뒤 한번만 실행한다는 뜻입니다.

```console
/var/log/news/* {
    monthly
    rotate 2
    olddir /var/log/news/old
    missingok
    postrotate
        kill -HUP `cat /var/run/inn.pid`
    endscript
    nocompress
}
```

- `/var/log/news/` 디렉토리 내의 파일들을 의미합니다.
- 달 주기로 2번 회전합니다.
- 에러가 발생하면 로그 파일은 압축되지 않습니다.

**압축에 관한 옵션**

| 옵션                                                     | 내용                                                                                                                                                                                     |
| -------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| compress                                                 | 이전 버전을 압축합니다. 압축에 관한 옵션은 기본값이 모두 `gzip`입니다.                                                                                                                   |
| compresscmd                                              | 압축에 사용할 명령어를 지정합니다.                                                                                                                                                       |
| compressoptions                                          | 압축에 사용할 명령어의 옵션을 지정합니다.                                                                                                                                                |
| uncompresscmd                                            | 압축 해제에 사용할 명령어를 지정합니다.                                                                                                                                                  |
| compressext                                              | 압축 결과물에 사용할 확장자를 지정합니다. 기본값은 설정된 compression command 를 따릅니다.                                                                                               |
| copy                                                     | 원본을 보존하고 로그 복사본을 만듭니다. 로그 파일 스냅샷을 만들거나, truncate 할 로그가 필요할 때 사용합니다. <br/> 이 옵션은 `create` 옵션을 무효화합니다.                              |
| copytruncate                                             | 기존파일에서 데이터를 잘라내 복사본을 만듭니다. 원본은 사이트 0이됩니다. **자르는 사이 로그가 손실될 수 있습니다.** <br/>이 옵션은 create를 무효화합니다.                                |
| create _mode_ _owner_ _group_<br/> create _ower_ _group_ | 회전 직후, postrotate가 실행되기 전에 로그 회전이 끝난 로그 이름으로 파일이 생성됩니다.  기본값은 원본 로그파일과 동일한 옵션값입니다. <br/> `nocreate` 옵션으로 비활성화할 수 있습니다. |
| createolddir _mode_ _owner_ _group_                      | old log를 저장할 디렉토리의 폴더 속성 명시. <br/> `olddir` 폴더에 명시된 내용이 없을 때 생성.                                                                                            |
| olddir _directory_                                       | 이전 버전 로그가 디렉토리에 저장됩니다. 원본과 동일한 물리적 장치에 저장되어야합니다. <br/> `noolddir` 옵션으로 재정의됩니다.                                                            |

**주기에 관한 옵션**

rotate 옵션으로 지정된 회전의 주기를 설정하는 옵션입니다.
로그 파일은 삭제되거나 송신되기 전에 rotate 명령어의 인자 횟수만큼 회전합니다.

| 옵션                   | 내용                                                                                                                                        |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| daily                  | 매일 회전                                                                                                                                   |
| weekly '['_weekday_']' | 명시된 weekday 요일이 되거나 7일이 지나면 회전합니다. <br/> 0~ 6은 일 ~토를 의미 <br/> 7은 요일과 관계없이 각 7일일을 의미. <br> 기본값은 0 |
| monthly                | 매달 회전                                                                                                                                   |
| yearly                 | 매년 회전                                                                                                                                   |

**사이즈 옵션**

| 옵션           | 내용                                                                                                                                |
| -------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| size _size_    | 로그 크기가 size를 초과할 때**만** 회전. <br/> 단위는 `k`, `M`, `G`                                                                 |
| minsize _size_ | 로그 크기가 size를 초과하면서 명시된 주기를 지났을 때 회전. 회전 주기가 다 되지 않아도 회전한다는 점을 제외하면 주기 옵션과 비슷함. |
| maxsize _size_ | 명시된 주기 이전이라도 로그 크기가 size를 초과하면 회전.                                                                            |


**스크립트에 관한 옵션**

 주기가 회전할 때마다 스크립트를 실행시킬 수 있습니다.
 
 스크립트가 실행될 때마다 첫번째 인자로 로그파일의 절대경로가 입력되는데, 절대경로가 같은 로그들이 회전할 경우 스크립트가 여러번 실행될 수 있습니다. 가령 디렉토리를 회전시키면 스크립트가 디렉토리 내 로그파일 개수만큼 실행됩니다.

`sharedscripts` 옵션으로 이를 방지할 수 있습니다.

| 옵션                    | 내용                                                                               |
| ----------------------- | ---------------------------------------------------------------------------------- |
| prerotate 와 endscript  | log 파일이 회전되기 직전에 옵션 사이의 줄을 실행합니다. `/bin/sh`로 실행됩니다.    |
| postrotate 와 endscript | log 파일이 회전한 직후에 옵션 사이 줄을 실행합니다. `/bin/sh`로 실행됩니다.        |
| lastaction 와 endscript | 와일드카드에 매칭된 log 파일이 회전한 직후, postrotate script 가 동작한 직후 동작. |
| sharedscripts           | 동일 절대경로에서 스크립트를 한번만 실행함.                                        |

**기타 옵션**

`missingok` 로그가 존재하지 않아도 에러 발생없이 넘어감.
`notifempty` 빈 로그는 회전하지 않음.


# Reference

 [Difference with auth and authpriv](https://unix.stackexchange.com/questions/59525/difference-between-authpriv-and-auth)
 [logrotate 정리글, 지니님](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=zinyboy&logNo=220651013609)

 <!-- https://unix.stackexchange.com/questions/21251/execute-vs-read-bit-how-do-directory-permissions-in-linux-work -->