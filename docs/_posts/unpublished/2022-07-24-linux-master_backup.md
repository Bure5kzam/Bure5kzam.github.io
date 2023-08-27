---
layout: single
title: 우분투 백업
date: 2022-07-24 23:06
category: LinuxMaster
---

# $cpio

아카이브에 파일을 저장하거나 추출하는 명령어입니다. tar 외에도 여러 포맷들을 적용할 수 있습니다.

cpio는 세 가지 모드로 동작합니다.

## copy-out 

파일 시스템에서 아카이브를 생성하는 명령어입니다.
`copy-out` 모드의 `cpio` 명령어는 인자로 파일 목록을 표준 입력으로 입력받고, 출력결과를 stdout 또는 `-F` 옵션으로 지정된 디바이스로 보냅니다.

일반적으로 `ls`나 `find` 명령어로 입력값을 지정합니다.

| 옵션         | 내용                   |
| ------------ | ---------------------- |
| -o, --create | 파일로 아카이브를 생성 |
| -v, vurbose  | 파일 리스트 출력       |

**example**

```console
# 1. 상대경로로 파일 아카이빙
$ find . -print -depth | cpio -ov > tree.cpio

# 2. 현재 폴더의 파일 및 폴더 아카이빙
ls | cpio -ov > directory.cpio
```

## copy-in

아카이브 --(복사)--> 파일 시스템. 또는 아카이브의 파일 목록 출력. 
패턴 입력시, 패턴에 해당되는 아카이브만 복사. 인자를 입력하지 않으면 모든 아카이브가 추출됩니다.

| 옵션                   | 내용                                   |
| ---------------------- | -------------------------------------- |
| -i, --extract          | 아카이브로부터 추출                    |
| -v, --verbose          | 추출되는 파일들 이름 출력              |
| -d, --make-directories | 디렉토리 트리 생성                     |
| -u, --unconditional    | 조건없이 모든 파일들을 새 것으로 교체. |

- 기본 디렉토리를 만들지 않음
- 옵션없으면 덮어쓰기를 하지않음.

**example**

```console
$ cpio -iv < dir.cpio       # cpio extract
                            # 이름이 같고 수정시간이 원본보다 이후인 파일이 있으면 복사안함

$ cpio -iuv < dir.cpio       # 덮어쓰기
```

## copy-pass
파일을 하나의 디렉토리 트리에서 다른곳으로 복사하거나 copy-in, copy-out 컴비네이션을 아카이브 없이 사용할 수 있습니다.                          |

입력받은 복사본에서 파일 목록을 읽고 복사합니다.

## Example

```console
$ find . -depth -print0 | cpio --null -pvd new-dir

```

# $rsync

Rsync는 다른 호스트나 원격 Rsysnc 데몬에서 파일을 가져오고 전송할 수 있는 프로그램입니다.

- 원격 호스트에서 로컬로 복사하거나, 같은 호스트 안에서 복사 지원
  - 하지만 원격 호스트간에 복사는 지원하지 않음
- 원격 rsync daemon 간에 복사 가능
- 파일 변경 내용만 전송해 네트워크 부하를 줄여주는 delta-transfer 적용.
- 주로 백업과 미러링에 사용



## Source and Destination

목적지나 출발지의 유형을 특정 키워드 포함여부로 확인합니다.

- `:`를 포함하면 remote-shell transport (ssh, rsh)
- `::`를 포함하면 rsync daemon
- `rsync://` + `URL`도 rsync daemon

복사할 로컬을 client, 원격 지역을 server라고 합니다.
키워드가 없으면 동일 client간의 이동으로 판단합니다.

daemon과 server를 구분하세요. daemon은 항상 서버이지만 server는 daemon이나 remote-shell spawned process가 될 수 있습니다.

## 예시
rsync를 사용하려면 기본적으로 source와 destination을 명시해야합니다.

`rsync -t *.c foo:src/` <br/>
*.c와 매치되는 파일들을 foo라는 머신의 src에 디렉토리로 전송합니다. 같은 이름의 파일이 있다면 rsync remote-update protocol 덕분에 파일의 데이터 변경점만 전송할 수 있습니다.


`rsync -avz foo:src/bar /data/tmp` <br/>
호스트 foo의 src/bar와 안의 파일들을 재귀적으로 로컬 머신의 /data/tmp/bar로 이동합니다.


`-a`파일은 전송 시 심볼릭 링크, 장치, 속성, 권한, 소유권 등을 보존하는 "아카이브" 모드로 전송됩니다.  

`rsync -avz foo:src/bar/ /data/tmp` <br/>
이번엔 source의 끝에 /가 있습니다. 마지막 '/'는 디렉토리 안의 컨텐츠만 지정합니다. 

따라서 아래 두개의 결과물은 같습니다.

```console
rsync -av /src/foo /dest
rsync -av /src/foo/ /dest/foo
```

host와 모듈 참조는 뒤에 '/'를 붙여 디렉토리 내 파일을 가져올 필요가 없습니다. 기본적으로 컨텐츠만이 이동됩니다.

```console
rsync -av host: /dest
rsync -av host::module /dest
```
host 내의 컨텐츠가 /desf로 이동됩니다.

## 고급 예시
한 호스트에서 여러개의 파일을 복사할 수도 있습니다.첫 번째와 달리 host를 생략해도 됩니다.


`rsync -av host:file1 :file2 host:file{3,4} /dest/`

원격 접속시 유저를 정하려면 아래처럼 사용합니다.
`rsync -av /file1 user@192.0.0.1:/backup`

ssh 포트가 다를 경우 아래처럼 사용합니다.
`$ rsync -avzh -e 'ssh -p 8888' /data 192.168.1.100:/backup`

## rsync daemon

데몬끼리 연결해 원격쉘 없이도 파일을 전송할 수 있습니다. 873 tcp 포트를 사용합니다.
- 호스트네임과 경로 사이에 `::`를 사용합니다.
- 경로의 첫번째 단어는 모듈명입니다.
- 정상적으로 연결되면 날짜를 출력합니다.
- 원격 데몬에 없는경로를 명시할 경우, 접근가능한 경로 목록을 보여줍니다.
- destination이 로컬이 아닐 경우, 원격 데몬이 제공할 수 있는 파일 목록을 보여줍니다.

```console
rsync -av host::src /dest
```

원격 데몬에 있는 모듈은 권한을 요구하기 때문에 비밀번호를 입력해야합니다.
이를 피하려면 환경변수 `RSYNC_PASSWORD`를 설정하거나 `--password-file` 옵션을 사용합니다.

웹 프록시를 이용하려면 `RSYNC_PROXY`를 설정합니다. 이는 프록시에서 873 포트를 지원해야합니다.

## 주요 옵션

```
    -v, --verbose               상세 설명
    -a, --archive               아카이브 모드에 해당하는 옵션 -rlptgoD (no -H,-A,-X)
        --no-OPTION             turn off an implied OPTION (e.g. --no-D)
        -r, --recursive             recurse into directories 
                                    (디렉토리 내부도 이동 적용)
        -l, --links                 copy symlinks as symlinks (심볼릭 링크 복사)
        -p, --perms                 preserve permissions (권한 유지)
        -t, --times                 preserve modification times (수정 시간 유지)
        -g, --group                 preserve group (그룹 유지)
        -o, --owner                 preserve owner (super-user only) (슈퍼 유저 유지)
        -D                          same as --devices --specials 
            --devices               preserve device files (super-user only) (디바이스 유지)
            --specials              preserve special files (special file 유지)
    -z, --compress              compress file data during the transfer
                                (전송하는 동안 압축)

```

| 옵션                | 의미                                    |
| ------------------- | --------------------------------------- |
| -a                  | 아카이브 모드에 해당하는 옵션(-rlptgoD) |
|                     |                                         |
| -v                  | verbos                                  |
| -z                  | 전송하는 동안 파일을 압축               |
| -e, --rsh=_COMMAND_ | 사용할 원격 쉘을 지정                   |
| -t, --time          | 파일 수정 시간을 보존                   |

# dd

파티션이나 디스크 단위로 백업할 때 사용하는 명령어입니다.

```console
$ dd if=/dev/sda1 of=/dev/sdb1 bs=1k
```