---
layout: single
title: 깃 서브모듈에서 작업하기
date: 2024-02-29 19:33
category: git
author: Bure5kzam
tags: [submodule]
summary: 깃 서브모듈 내 프로젝트에 작업하고 반영하기
---

별다른 설정이 없으면 서브모듈의 HEAD 커밋은 `DETACHED`로 지정된다.

```bash
git branch
* (HEAD detached at 3edd422)
  develop
  master
```

이는 `git submodule update` 기본 옵션이 `checkout` 이기 떄문이며, 커밋에 저장된 `work directory`만 가져온다. (`git checkout`과 의미가 같음)

`Detached HEAD` 인 경우 `git checkout {branch-name}`으로 작업할 브랜치로 HEAD를 옮겨주고 작업하면 된다.

또는 다음 명령어로 일괄 변경해줘도 된다.

```bash
git submodule foreach 'git checkout develop || true'
```