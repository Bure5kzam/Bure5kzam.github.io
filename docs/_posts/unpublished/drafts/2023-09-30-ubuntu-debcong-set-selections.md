---
layout: post
title:
date: 2023-09-30 15:23
category:
author:
tags: []
summary:
---

`debconf-set-selections`는 패키지 설치 시 설정될 값들을 사전에 지정하기 위해 사용하는 쉘 커맨드다.

아래 내용은 [ubuntu manual](https://manpages.ubuntu.com/manpages/trusty/en/man1/debconf-set-selections.1.html)의 관련 문서를 번역한 글이다.

## 시늡시스

debconf-set-selections {file | ssh new-set-selections}


인자 값은 파일 이름이나 stdin으로 전달할 수 있다.

## 주의

설치 될 예정이거나 설치된 패키지를 대상으로만 debconf 값을 사전 지정하는 것을 권장한다. 그렇지 않으면 설치되지 않은 패키지 값이 지정되거나 공유된 값이 지정되어 복잡한 문제가 발생할 수 있다.

## 데이터 형식

데이터는 4개의 값을 가지는 한 줄 단위로 이루어지며 각 값은 공백으로 구분된다. (#로 시작하면 주석으로 처리된다.)

첫 번째 값은 패키지 이름, 두 번째는 질문의 이름(* 질문을 적용할 패키지의 변수 이름), 세 번째는 질문의 타입, 네 번째는 질문의 답 또는 없을 수 있다.

세 번째 값은 seen이 될 수 있다. 이 경우 사전 정의된 줄은 debconf 데이터베이스에서 확인 되었는지 여부만 제어한다. 유의할 점은 미리 값을 설정하면 해당 질문을 보인 것으로 치부하기 때문에, 질문을 본 것으로 표시하지 않으면서 기본값을 덮어씌우려면 2 줄을 사용해야 한다. (* 아래 예시처럼)

줄은 "\" 문자열을 사용해 여러 줄로 표현할 수 있다.

## 예시

```bash
# debconf 우선순위를 critical이 되도록 강제한다.
debconf debconf/priority select critical

# 기본 frontend 값을 readline으로 엎어쓴다. 하지만 유저가 선택할 수 있게 한다.
debconf debconf/frontend select readline
debconf debconf/frontend seen false
```

---

## 추가

### 패키지 변수 이름 찾는 방법

debconf-utils를 설치하면 동봉된 `debconf-show`로 알아낼 수 있다.

[링크](https://unix.stackexchange.com/questions/457388/how-to-find-out-the-variable-names-for-debconf-set-selections)

```bash
# keyboard-configuration의 패키지 변수를 확인하는 방법
ros@burePool:~/test$ debconf-show keyboard-configuration
debconf: DbDriver "passwords" warning: could not open /var/cache/debconf/passwords.dat: Permission denied
  keyboard-configuration/layoutcode: kr
  debian-installer/console-setup-udeb/title:
  keyboard-configuration/switch: No temporary switch
  keyboard-configuration/unsupported_config_options: true
  keyboard-configuration/model: Generic 105-key PC
  keyboard-configuration/optionscode:
  keyboard-configuration/unsupported_options: true
  console-setup/detect:
  keyboard-configuration/ctrl_alt_bksp: false
  keyboard-configuration/other:
  keyboard-configuration/altgr: The default for the keyboard layout
  keyboard-configuration/store_defaults_in_debconf_db: true
  keyboard-configuration/unsupported_config_layout: true
  keyboard-configuration/xkb-keymap:
  keyboard-configuration/variantcode: kr104
* keyboard-configuration/layout: Korean
* console-setup/ask_detect: false
* keyboard-configuration/variant: Korean - Korean (101/104-key compatible)
  console-setup/detected:
  keyboard-configuration/unsupported_layout: true
  keyboard-configuration/modelcode: pc105
  keyboard-configuration/toggle: No toggling
  keyboard-configuration/compose: No compose key

```
