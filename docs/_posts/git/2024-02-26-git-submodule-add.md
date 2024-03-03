---
layout: single
title: 깃 서브모듈 추가하기
date: 2024-02-26 19:55
category: git
author: Bure5kzam
tags: [submodule]
summary: 서브모듈 추가 하는 방법
---

<!-- 처음 서브모듈을 추가할 때 `.gitmodule` 파일에 있던 기존 내용을 복제하여 추가했는데, 의도대로 되지 않았다.

수정 후 `git submodule init` 명령어를 실행하면 서브모듈들이 갱신되는 과정이 보이는데, 추가한 내용은 보이지 않았다.

`git submodule init`은 stage index에 기록된 서브모듈들을 `.gitmodules` 파일 설정을 템플릿으로 사용하여 `.git/config`에 기록된 `submodule.$name.url` 값으로 초기화한다.
 -->

```bash
# 서브모듈 정보 추가하기.
git submodule add -b {브랜치 명} --name {서브모듈 명} {원격 저장소 주소} {superproject 내 서브모듈 배치 경로}

# .gitmodules 명세 기반으로 .git/modules에 서브모듈 정보 생성하기
# 로컬 환경설정 파일이 준비된다.
git submodule init

# 서브모듈의 리모트 저장소에서 데이터를 가져오고 서브모듈을 포함한 프로젝트의 현재 스냅샷에서 Checkout 해야 할 커밋 정보를 가져와서 서브모듈 프로젝트에 대한 Checkout을 한다.
git update --remote
```

또는 `init`과 `update`를 한번에 할 수 있다.

```bash
# 서브모듈 정보 추가하기.
git submodule add -b {브랜치 명} --name {서브모듈 명} {원격 저장소 주소} {superproject 내 서브모듈 배치 경로}

# 위 명령어를 한번에 
git update --remote --init

```

서브모듈이 정상적으로 추가되면 세 부분에서 서브모듈이 보여야 한다.

1. `.gitmoudle` 파일에 추가되었는지
2. `.git/config` 파일에 `submodule`로 추가되었는지
3. `git config --get-regexp submodule` 에서 추가되었는지