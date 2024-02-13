---
layout: single
title: 도커 파일 지정할 때 유의해야 할 점
date: 2023-10-22 10:32
category: 
author: 
tags: []
summary: 
---

Dockerfile에서 이미지에 파일을 복사하라면 호스트머신에서의 대상 경로와 이미지 상의 경로를 지정해야 한다.

파일 경로를 작성할 때 주의할 점을 정리했다.

## 기본 개념

### Context

복사할 호스트머신 상의 파일 경로는 상대 경로로 지정되어야 한다.

상대 경로가 컴파일 될 때 루트로 지정되는 경로를 `context`라고 한다.

도커 레퍼런스에서 설명하는 컨텍스의 의미는 다음과 같다.

> The build context is the set of files that your build can access. The positional argument that you pass to the build command specifies the context that you wnat to use for the build
>빌드 컨텍스트는 필드 시에 엑세스 할 수 있는 파일들이다. 빌드 커맨드에 위치 인수를 지정해서 컨텍스트를 지정할 수 있다.

빌드 컨텍스트는 다양하게 지정될 수 있다.

- 호스트 머신의 파일 시스템 상의 경로
- Git 저장소
- tar 파일

## 파일 경로

