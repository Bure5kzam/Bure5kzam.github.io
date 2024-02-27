---
layout: post
title: vscode task 변수 치환
date: 2024-02-18 23:44
category: vscode
author: Bure5kzam
tags: [task]
summary: 
published : false
---


task.json 파일 내에서 vscode 자체 변수 치환을 지원한다.

모든 속성에서 제공되진 않고, `command`, `args`, `options` 에서 만 가능하다.


```json
{
  "label": "TypeScript compile",
  "type": "shell",
  "command": "tsc ${file}",
  "problemMatcher": ["$tsc"]
}
```

변수 외에 프로젝트 설정을 참조할 수 있다. 

```json
{
  "label": "autopep8 current file",
  "type": "process",
  "command": "${config:python.formatting.autopep8Path}",
  "args": ["--in-place", "${file}"]
}
```