---
layout: single
title: 깃 서브모듈 푸쉬 경고 설정하기
date: 2024-02-28 20:42
category: git
author: Bure5kzam
tags: [submodule]
summary: super project 푸쉬할 때 경고 추가하기
---

서브모듈이 있는 깃 프로젝트를 원격 저장소에 반영할 때는 서브모듈의 수정내용을 먼저 반영해야 한다.

그러지 않으면 원격 저장소가 존재하지 않는 서브모듈 커밋을 가리키게 되기 때문이다.

`git push` 명령을 사용할 때 옵션을 추가하면 순서가 올바른지 체크해준다.

```bash
git push --recurse-submodules=check
The following submodule paths contain changes that can
not be found on any remote:
  DbConnector

Please try

    git push --recurse-submodules=on-demand

or cd to the path and use

    git push

to push them to a remote.
```

다음 상시 활성화할 수 있다.

```bash
git config push.recurseSubmodules check
```