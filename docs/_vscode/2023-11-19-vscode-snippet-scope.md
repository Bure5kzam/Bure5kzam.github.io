---
title:  vscode snippet 설정 파일 범위
date: 2023-11-19 16:20
tags: [vscode, snippet]

---

> ubuntu 22.04
> vscode 1.84

> vscode reference : https://code.visualstudio.com/docs/editor/userdefinedsnippets#_snippet-syntax

스니펫이 제공되는 범위에 대한 글이다.

## 스니펫 범위

스니펫은 적용되는 기준에 해당하는 파일에서만 사용할 수 있다. **언어**, **프로젝트**가 해당한다.

## Language snippet scope

language snippet을 정의하는 파일은 두 종류가 있다. 저장되는 파일에 따라 다르다.

1. a language snippet file
2. a global snippet file

### language snippet file

우분투 22.04 기준으로 `~/.config/Code/User/snippets/` 는 우분투 유저별 vscode 스니펫 설정 파일들을 보관하는 경로이며, javascript.json, cpp.json 같은 언어별 스니펫 파일이 있다. 이들을 `Single-language user-defined snippet`라고 한다.

스니펫들을 수정하려면 커맨드 팔레트에서 `Snippets: Configure User Snippets.` 와 수정할 언어를 선택하면 된다.

### a global snippet file

언어 + json 확장사 파일에 저장되는 `Single-language user-defined snippet` 과 다르게 `Multi-language snippet` 과 `global user-defined snippet`들은 `.code-snippets` 접미사를 갖는 global-snippt 파일에 저장된다. 경로는 같다

수정하려면 마찬가지로 커맨드 팔레트에서 `Snippets: Configure User Snippets.`를 선택 후 `global snippets file`을 선택하면 된다.

이 파일에 정의된 스니펫들은 스니펫을 사용할 언어를 지정하는 `scope` 속성을 갖는다. 없으면 모든 언어에서 사용할 수 있다.

## Project snippet scope

프로젝트 스니펫 파일은 해당 프로젝트에서 작업하는 모든 유저들이 사용할 수 있게 해야할 때 사용한다.

프로젝트 스니펫 파일 경로는 프로젝트 루트 경로 `.vscode` 안의 `.code-snippets` 로 끝나는 파일이다.

프로젝트 스니펫을 만드려면 `Snippets: Configure User Snippets.` 커맨드에서 `New Snippets file for`을 선택한다.

global snippet 처럼 scope로 언어를 지정할 수 있다.

