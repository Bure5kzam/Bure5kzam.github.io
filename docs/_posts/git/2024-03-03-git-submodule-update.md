---
layout: single
title: 깃 로컬 서브모들 업데이트하기
date: 2024-03-03 19:04
category: git
author: Bure5kzam
tags: [submodule]
summary: 깃에서 서보므듈을 갱신하는 방법들
---


서브모듈 내용을 원격 저장소에 있는 내용으로 가져오려면 여러 방법이 있다.

## 기본 깃처럼 가져오기

이는 서브모듈로 경로 이동 후 작업하는 방법이다.

```bash

# 1.1 서브모듈 경로로 이동
cd /path/to/submodule

# 1.2 원격저장소 내용을 로컬 저장소로 가져옴
git fetch

# 1.3 병합
git merge origin/master

# 또는 pull 
git pull master

```

## 서브모듈 명령어로 가져오기

superproject(서브모듈의 부모 프로젝트) 루트 경로에서 일괄 적용하는 방법이다.

```bash
# 프로젝트 내 서브모듈들 업데이트하기
git submodule update --remote 

# 특정 경로에 존재하는 서브모듈만 업데이트하기
git submodule update --remote {submodule_path}
```

`git submodule update` 는 로컬에 저장된 제일 최신 서브모듈 커밋으로 적용하는 명령어다.

`--remote` 옵션을 적용해야 원격 저장소의 최신 서브모듈 커밋을 적용한다.

