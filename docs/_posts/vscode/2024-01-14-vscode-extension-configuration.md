---
layout: single
title: vscode 확장 프로그램 구성하기
date: 2024-01-14 19:17
author: Bure5kzam
tags: [vscode, snippet]
summary : 온/오프라인 환경에서 확장 프로그램을 설치하는 방법(미완)
---

**인터넷이 있으면** 마켓 플레이스 기능을 이용해서 설치할 수 있다. ( ctrl + shift + x )

마켓 플레이스에서 에러 메세지 'XHR Request fail'가 뜬다면 vs code가 익스텐션 목록을 불러오는데 실패한 것이다. 이 경우 인터넷 연결이 비활성화 되었거나 방화벽에 막혔을 수 도 있다.

**오프라인**에서 구성하고 싶은 경우, 'vsix' 파일이 있으면 설치할 수 있다.

vsix 파일은 마켓 플레이스에서 미리 받아둬야 한다.

[오프라인에서 vscode 확장 프로그램 구성기]( {% post_url /vscode/2023-10-23-오프라인 환경에서 vscode server 익스텐션 구성하기 %})


<!-- > ssh 접속 상태에서 사용하기
> 테마, 스니펫처럼 UI 관련 지원을 제공하는 확장 프로그램은 로컬에 설치되는 반면, 그 외 대부분은 SSH로 접속한 호스트머신에 설치된다.
> [vscode document](https://code.visualstudio.com/docs/remote/ssh#_managing-extensions) -->


```json
{
  // See https://go.microsoft.com/fwlink/?LinkId=733558
  // for the documentation about the tasks.json format
  "version": "2.0.0",
  "tasks": [
    {
		# 표시되는 라벨 이름
      "label": "Run tests",
	  # task 유형. 사용자 정의 작업의 경우 shell 또는 process
      "type": "shell",
	  # task에서 실행할 커맨드
      "command": "./scripts/test.sh",
      "windows": {
        "command": ".\\scripts\\test.cmd"
	  # task가 속한 그룹.
      "group": "test",
	  # 출력이 사용자 인터페이스에서 어떻게 나올지를 정의
      "presentation": {
        "reveal": "always",
        "panel": "new"
      }
	  # 기존의 cwd ( 현재 작업중인 디렉토리), env (환경 변수), shell (기본 쉘)을 재정의.
	  "options" : {

	  }
	  # 작업이 실행되는 시기, 방법 정의
	  runOptions : {

	  }
    }
  ]
} 

```
[vscode document](https://code.visualstudio.com/docs/editor/tasks)