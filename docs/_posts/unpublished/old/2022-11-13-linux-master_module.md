---
layout: single
title: 우분투 마스터 모듈
published : false
---

> 글의 신뢰도가 낮습니다. 추측을 기반으로 작성한 내용이므로 정보로서의 가치가 없습니다.

# Concept

## 커널 모듈

`/lib/moduels/[kernel-version]/` 디렉토리에는 해당 커널 버전에서 필요한 모듈 파일들이 빌드되지 않은 상태로 존재합니다.

커널에 적용하려면 `$ make`로 모듈들을 빌드해야 하며, 빌드된 모듈은 `/usr/src/kernels/[kernel-version]`로 옮겨 사용합니다.

`modules.dep`은 모듈 간의 의존성을 기록한 파일입니다.


# Command

## $ make

새 커널을 적용하려면 make 명령어로 빌드 해서 사용해야 합니다.

