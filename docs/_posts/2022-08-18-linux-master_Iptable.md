---
layout: single
title: 
date: 2022-08-18 15:32
category: LinuxMaster
---
# Overview

# Firewall
방화벽은 호스트의 네트워크 트래픽을 모니터링하고 제어할 수 있는 보안 시스템입니다.
패킷 필터 조건을 설정해 패킷을 전달받았을 때 패킷을 수신하지 않고 버리도록 설정 할 수 있습니다.

## $firewall-cmd

`firewalld`을 설정하는 클라이언트 명령어 입니다.

### 반환값

firewall-cmd는 옵션을 연속으로 명시할 수 있습니다. 최소 한개 이상 성공한다면 반환값은 `0`이 됩니다. `ALREADY_ENABLED(11), NOT_ENABLED(12), ZONE_ALEARDY_SET(16)은 성공으로 칩니다. 항목을 파싱하는 중에 이슈가 발생하면 warning으로 취급되며 다른게 하나 이상 성공하지 않으면 결과값을 바꾸지 않습니다. 성공한 항목이 없으면 종료 코드는 error code에 따라 정해집니다. 정확히 하나의 에러 코드만 있으면 사용됩니다. 둘 이상의 오류가 있으면 UNKNOWN_ERROR(254)가 사용됩니다.

### 옵션 : 데몬 상태 확인

| 옵션                   | 내용                                                                                                                                                                                                                                                            |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| --state                | firewalld의 상태를 출력합니다. 0은 활성화, 시작햇을 때 실패하면 `RUNNING_BUT_FAILED`, 아니면 `NOT_RUNNING` 이 나타납니다.                                                                                                                                       |
| --reload               |                                                                                                                                                                                                                                                                 |
| --complete-reload      | firewalld와 netfilter 커널 모듈을 리로드합니다. state 정보를 잃기 때문에 활성화된 연결들은 종료됩니다. 이 옵션은 firewalld 에 문제가 있을 때 사용하는 것이 좋습니다. 예를들어 state 정보가 잘못되어 firewall rule에 맞지만 연결할 수 없는 상태일 때 사용합니다. |
| --runetime-to-permanet | 활성화된 런타임 설정을 영구 설정으로 저장합니다.                                                                                                                                                                                                                |
| --check-config         | 영구 설정의 유효성을 체크합니다.                                                                                                                                                                                                                                |

### 옵션 : 로그 관련

| 옵션                     | 내용                                                                                                                                                                                                                                                                  |
| ------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| --get-log-denied         | 거부당한 세팅 로그를 출력합니다.                                                                                                                                                                                                                                      |
| --set-log-denied=_value_ | INPUT, FORWARD, OUTPUT 체인에 있는 reject, drop 룰이 수행되기 직전, 마지막 reject가 일어나기 직전에 수행될 로깅 룰을 추가합니다. 가능한 값은 all, unicast, broadcast, multicast, off입니다. <br/> 이 옵션은 permananet와 runtime 설정을 변경하고 리로드를 유발합니다. |

### 옵션 : 소스 바인딩 관련 옵션

source를 zone에 바인딩 한다는 것은, 이 존 세팅이 소스로부터 오는 트래픽을 제한한다는 뜻입니다.

# $ iptables

`iptables`는 패킷 필터와 NAT를 관리하는 툴입니다. 패킷 필터링 룰 테이블을 관리합니다.

```console

$ iptables [-t table] {-A | -C} chain rule-specification
$ iptables [-t table] {-D | -I | -R} chain rule_num rule-specification

rule-specification = [matches...] [target]
match = -m matchname [per-match-options]
target = -j targetname [per-target-options]

```

`iptables` 명령어는 3가지 유형의 옵션이 있습니다.  `man iptables` 매뉴얼의 `Options` 섹션을 참고하세요.

- Command : 테이블에 수행할 액션을 지정. 체인에 룰을 추가, 삭제 등의 액션이 가능하며 명령어 실행 시 하나만 존재 할 수 있음. ex) -A, -C, -D
- Parameters : 체인에 추가되거나 삭제될 rule을 명시하는 옵션 (rule specifecation) ex) -4, -6, -p, -s, -d, -m, -g, -i, -o.
- Other options : 동작과 관계없는 사용자 편의 옵션 ex) -v, -w, -W

iptables에는 다양한 모듈이 있으며 `-p` 옵션이 있으면 모듈에 필요한 옵션을 명시할 수 있습니다. 이는 `man iptables-extensions`를 참고하세요.

table, target, chain, rule 개념을 알아야 동작을 이해할 수 있습니다.

## rule

`rule`은 패킷을 어떻게 처리할지를 지정하는 것입니다.
**검출할 패킷의 패턴(match)**과, **검출한 패킷에 취할 행위(target)**를 정의합니다.

여러개 모이면 chain이 됩니다.

## chain

`chain`은 룰의 모음입니다. 패킷이 도착하면 체인에 있는 룰을 순서대로 조회합니다.


만약 패킷이 첫 룰에 매치되지않으면, 체인의 다음 룰과 비교합니다. 매치되면 다음 체인룰은 `target`으로 지정된 `user-defined chain`으로 지정됩니다.

체인의 종류는 기본 체인룰인 `built-in chain`과 관리자가 생성한 `user-defined chain`이 있습니다.

## target

`target`은 패킷이 `rule`에 매칭되면 수행할 행위를 지정합니다.


target 값은 아래 중 하나입니다.

- 값은 테이블에 존재하는 `user-defined chain` 이름
- `man iptables-extensions(8)`의 `TARGET EXTENSIONS` 섹션에 명시된 타겟들
- 스페셜 값 `ACCEP`, `DROP`, `RETURN`이 될 수도 있습니다. 

### target : iptables-extensions

### target: special value

- ACCEPT : 패킷을 통과시킴
- DROP : 패킷을 floor로 drop함
- RETURN : chain traversing을 끝내고 이전 chain의 next rule을 재개함.
- SNAT :  nat 테이블에서 `POSTROUTING`, `INPUT` 체인이나 여기서 호출된 

탐색이 `build-in chain`의 마지막에 도달하거나  `built-in chain`의 룰이 target RETURN과 매치되면 타겟이 패킷의 운명을 결정합니다.

### build in chain

built-in chain에는 종류는 다음과 같습니다.

- PREROUTING (DNAT) : 패킷의 도착지 주소를 변경합니다.
- POSTROUTING (SNAT) : 패킷의 출발지 주소를 변경합니다.
- OUTPUT : 호스트에서 생성된 패킷의 도착지 주소를 변경합니다.

패킷의 주소가 rule에 매치되면 rule의 target 값에 지정된 rule을 검사합니다. target 값은 user-defined chain이 될 수 있고, 이외에도 ACCEPT, DROP, RETURN 값을 가질 수 있습니다.

ACCEPT은 패킷을 허용, DROP은 패킷을 버림, RETURN은 이전 호출의 다음 규칙에서 시작을 의미합니다. 
<!-- 
패킷이 기본 제공되는 built in chain의 끝에 도달하거나 built in chain의 return에 도달하면 chain policy 에의해 정해진 target이 패킷의 운명을 결정합니다. -->

## Table

테이블들은 `built-in chain`들을 가지고 있으며 `iptables` 명령어로 테이블의 chain에 rule을 추가할 수 있습니다.

 테이블에 있는 built-in chain들이 동작하는 타이밍은 다음과 같습니다.

| 타겟 이름     | 내용                              |
| ------------- | --------------------------------- |
| `INPUT`       | 패킷이 로컬 소켓을 통해 도착할 때 |
| `OUTPUT`      | 내부에서 생성된 패킷이 나갈 때    |
| `FORWORD`     | 패킷이 box를 거쳐 라우팅 될 때    |
| `PREROUTING`  | 패킷이 라우팅 되어 들어오기 전    |
| `POSTROUTING` | 패킷이 라우팅 되어 나갈 때        |

커맨드는 5개 테이블을 기준으로 동작합니다.


### filter table

`iptables`에서 사용되는 기본 테이블입니다.

- 로컬 소켓으로 도착하는 패킷을 위한 `INPUT` 체인
- box를 통해 라우팅된 패킷을 위한 `FORWORD` 체인
- 내부적으로 생성된 패킷을 위한 `OUTPUT`체인을 타겟팅 할 수 있습니다.

### nat table

새 연결이 필요한 패킷이 있을 때 참조하는 테이블입니다. 3개의 빌트인 체인으로 구성되어 있습니다.

- PREROUTING (방금 들어온 패킷을 대체하기 위해 사용)
- OUTPUT (라우팅 되기 전의 로컬에서 생성된 패킷들을 대체할 때사용)
- POSTROUTING (패킷이 나갈 때 대체하기 위해 사용)
  
### mangle

특수한 패킷들을 대체할 때 사용합니다. 버전에 따라 빌트인 체인 구성이 다릅니다.

**before Kernel 2.4.17**

- PREROUTING (들어오는 패킷을 라우팅 전에 변환하기 위해 사용)
- OUTPUT ( 로컬에서 생성된 패킷을 라우팅 전에 변환하기 위해 사용)

**after Kernel 2.4.18** 추가

- INPUT (box로 들어오는 패킷을 변환하기 위해 사용)
- FORWARD (박스를 거쳐 라우팅되는 패킷을 변환하기 위해 사용)
- POSTROUTING ( 패킷이 전송될 때 변환하는데 사용)

## Options

iptable 명령어의 옵션은 성격에 따라 `command`, `parameter`, `other`로 나눠집니다.

- `command`, 수행할 액션을 지정하는 옵션. 이 유형의 옵션은 대부분 한번에 하나만 사용됩니다.
- `parameter`, rule을 명시하는 옵션. ip4, ip6, protocol, destination 등을 설정
- `other`, verbose, wait 등

### Command

| 옵션                                                 | 내용                                                                                                                                                                                                  |
| ---------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| -A, --append _chain_ _rule-specification_            | 체인의 끝에 룰을 추가합니다. 출발지, 목적지 주소가 여러 주소로 해석되면 주소별로 rule이 추가됩니다.                                                                                                   |
| -C, --check _chain_ _rule-specification_             | 룰에 체인이 매칭되는지 확인합니다. <br/> 매칭되는 엔트리를 찾는 `-D` 옵션과 같은 로직을 사용하지만 기존 설정을 대체하지 않고 결과값을 `success`나 `failure`로 나타냅니다.                             |
| -D, --delete _chain_ _rule-specification_            | 체인에서 룰을 삭제합이나. 룰은 이름 대신 1부터 시작하는 체인에서의 룰 번호로도 지정할 수 있습니다.                                                                                                    |
| -I, --insert _chain_ [rulenum] _rule-specification_  | 체인에 룰을 추가합니다. 룰 번호로 1을 넣으면 제일 처음에 추가합니다.                                                                                                      _rulenum_ 기본값은 1입니다. |
| -R, --replace _chain_ _rulenum_ _rule-specification_ | 체인에 있는 룰을 교체합니다. 출발지나 목적지 이름이 여러 주소와 매칭되면 실패합니다.                                                                                                                  |
| -L, --list \[chain\]                                 | 체인의 룰들을 출력합니다. 기본값은 모든 체인입니다. 다른 커맨드처럼 지정된 테이블에 적용됩니다. (기본 default, filter) <br> `-n`, long reverse DNS 조회를 방지함.                                     |

### Parameter

| 옵션 | 내용 |
| \[!\] -i, --in-interface _name_| 패킷을 수신받을 인터페이스의 이름을 지정합니다. `FORWARD`, `INPUT`, `PREROUTING` 체인에서 사용합니다. 인터페이스 이름 전에 `!`를 붙이면 반대로 지정됩니다. 인터페이스 이름이 `+`로 끝나면 와일드카드처럼 동작합니다. <br> 기본값은 모든 인터페이스입니다.|
| \[!\] -o, --out-interface _name_ | 패킷이 전송될 인터페이스의 이름을 지정합니다. `FORWARD`, `OUTPUT`, `POSTROUTING`에 사용됩니다. `-i`와 같습니다. |
| -j, --jump _target_| rule의 target값을 지정합니다. 패킷이 매치되면 수행할 행동입니다. `target`은 `user-defined chain`이나 packet의 운명을 결정하는 `special built in chain`이 가능합니다. `-g`가 없는 기본값은 아무 효과가 없습니다. 룰의 카운터는 증가합니다.|


# iptables-extensions

iptables에서 `-m` 옵션을 지정하면 추가적인 패킷 매칭 모듈을 사용할 수 있습니다. 이 경우 뒤에 모둘명과 모듈 관련 옵션이 뒤따릅니다. 한 줄에 여러개의 확장 매치 모듈을 사용할 수 있습니다.

확장 모듈 뒤에 `-h`를 지정하면 모듈 관련 도움말을 제공합니다.

확장 모듈들은 지정된 순서대로 평가됩니다.

 ```console
 iptables [-m name [module-options...]]  [-j target-name [target-options...]
 ```

-p 또는 --protocol이 지정되어 있고 알 수 없는 옵션이 발견된 경우에만 iptables는 프로토콜과 동일한 이름의 일치 모듈을 로드하여 옵션을 사용할 수 있도록 시도합니다.

> $ man iptabes-extension, [MATCH EXTENSION] </br> </br>
> iptables  can use extended packet matching modules with the -m or --match options, followed by the matching module name; after these, various extra command line  options  become  available, depending  on  the  specific  module.   You can specify multiple extended match modules in one line, and you can use the -h or --help options after the module has been specified to  receive help  specific to that module.  The extended match modules are evaluated in the order they are       specified in the rule.
>
> If the -p or --protocol was specified and if and only if an  unknown  option  is  encountered, iptables  will  try  load  a  match module of the same name as the protocol, to try making the option available.



# Reference

[github, iptables-cheatsheet, 
mcastelino](https://gist.github.com/mcastelino/c38e71eb0809d1427a6650d843c42ac2)

[133. iptables의 NAT 테이블 (DNAT, SNAT 등) 을 사용한 트래픽 제어하기, alice 님](https://m.blog.naver.com/alice_k106/221305928714)

[iptables, MSDN](https://learn.microsoft.com/ko-kr/azure/rtos/netx-duo/netx-duo-nat/chapter1)

<!-- [SNAT, DNAT, 네떡지기](https://zigispace.net/1121) -->