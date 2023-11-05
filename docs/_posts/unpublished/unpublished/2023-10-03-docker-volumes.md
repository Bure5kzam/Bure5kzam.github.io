---
layout: post
title: 도커 볼륨
date: 2023-10-03 18:21
category:
author:
tags: []
summary:
---

## Bind mount

`Bind mount`는 볼륨보다 제한된 기능을 가지고 있다. 바인드 마운트를 사용하면 호스트 머신의 파일, 디렉토리가 컨테이너에 마운트된다.파일이나 디렉토리는 호스트머신의 `절대경로`에 마운트된다. 반대로 볼륨을 사용하면 새 디렉토리가 호스트 머신에 있는 도커의 저장소 디렉토리 내에 생성되고 도커에 의해 관리된다.

파일이나 디렉토리는 도커 호스Bind mount트 상에 미리 존재하지 않아도 된다. 없으면 생성된다.
`Bind mount`는 매우 효과적이지만 지정된 디렉토리 구조를 사용할 수 있는 호스트 머신의 파일시스템에 의존적이다. 만약 새롭게 도커 어플리케이션을 개발하고 있다면 `named volumes`를 대신 고려하는 것이 좋다. CLI 명령으로 bind mounts를 직접 관리할 수 없다.


## Volume

볼륨은 도커 컨테이너 내부에서 사용되고 생성되는 데이터를 유지하기 위해 선호되는 매커니즘이다.

`bind mounts`가 디렉토리 구조와 호스트 머신 OS에 의존적인 반면 볼륨은 완전히 도커에 의해 관리된다.

Volume이 갖는 상대적 이점은 다음과 같다.

- 백업 / 마이그레이션이 수월함
- CLI로 관리할 수 있음.
- 리눅스와 윈도우에서 동시에 동작함
- 컨테이너 간에 더 안전하게 메모리를 공유한다.
- 원격 호스트나 클라우드 제공자에 원격으로 볼륨을 저장할 수 있다.
- 새 `Volume`은 컨테이너에 의해 미리 채워질 수 있다.
- ...
