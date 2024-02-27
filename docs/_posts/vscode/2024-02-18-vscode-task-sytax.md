---
layout: single
title: vscode task 문법
date: 2024-02-18 23:40
category: vscode
author: Bure5kzam
tags: [task]
summary: 테스크에서 지정가능한 프로퍼티 예시
---

# 상세 문법

테스크에서 지정가능한 속성들에 대한 설명

## command

실행할 명령어를 지정하는 속성이다.

**one line command**

```json
{
  "label": "dir",
  "type": "shell",
  "command": "dir 'folder with spaces'"
}
```

**command with args**

```json
{
  "label": "dir",
  "type": "shell",
  "command": "dir",
  "args": ["folder with spaces"]
}
```

**command with args and escape**

```json
{
  "label": "dir",
  "type": "shell",
  "command": "dir",
  "args": [
    {
      "value": "folder with spaces",
      "quoting": "escape"
    }
  ]
}
```

## depend

다른 테스크과의 의존관계를 정의할 수 있다.

**테스크 병렬 실행**

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Client Build",
      "command": "gulp",
      "args": ["build"],
      "options": {
        "cwd": "${workspaceFolder}/client"
      }
    },
    {
      "label": "Server Build",
      "command": "gulp",
      "args": ["build"],
      "options": {
        "cwd": "${workspaceFolder}/server"
      }
    },
    {
      "label": "Build",
      "dependsOn": ["Client Build", "Server Build"]
    }
  ]
}
```


**테스크 순차 실행**

```json
{
  "label": "One",
  "type": "shell",
  "command": "echo Hello ",
  "dependsOrder": "sequence",
  "dependsOn": ["Two", "Three"]
}
```

단 순차 실행하려면 task가 완료되었음을 알려주는 problem matcher가 필요하다.
