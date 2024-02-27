---
layout: post
title: vscode task 예제 따라하기
date: 2024-02-18 23:39
category: vscode
author: Bure5kzam
tags: [task]
summary: 커스텀 쉘 스크립트 실행하는 task 만들어보기
---

## 공식 예제 1

```json
{
  // See https://go.microsoft.com/fwlink/?LinkId=733558
  // for the documentation about the tasks.json format
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Run tests",
	  // task의 type
      // 쉘에 입력하는 task면 shell, 프로세스를 실행하는 task면 process를 지정
      "type": "shell",

		// 실행할 명령어 (type이 shell인 경우)
      "command": "./scripts/test.sh",
      
	  // task가 윈도우에서 실행될 경우 대체되는 속성들을 정의하는 블록
      "windows": {
        "command": ".\\scripts\\test.cmd"
      },
      
      // task가 속한 그룹.
      "group": "test",

		// UI에서 task 실행 결과를 보여주는 방법을 정의하는 부분
      "presentation": {
      
	      // 항상 결과를 터미널에 드러냄
        "reveal": "always",
        // 결과를 보여줄 때 새 터미널을 생성
        "panel": "new"
      }
    }
  ]
}
```

## 공식 예제 2

Task를 생성해서 실행해보자.

커맨드 팔레트에서 configure default build task를 실행하면 빌드와 연동되는 task 초안을 생성해준다.

//
경로는 `{$home}/.config/Code/User/profiles/{profile_user}/tasks.json`

```json
{

	// See https://go.microsoft.com/fwlink/?LinkId=733558
	
	// for the documentation about the tasks.json format
	
	"version": "2.0.0",

	// 테스크 정의 목록
	"tasks": [
	
		{

			// UI에서 보여지는 task 이름
			"label": "echo",
	
			// task 종류
			"type": "shell",
	
			// task에서 실행할 명령 (type이 shell인 경우)
			"command": "echo Hello",
			
			"problemMatcher": [],
	
			// task가 속한 그룹
			"group": {
			
				"kind": "build",
	
				// group에서 기본 
				"isDefault": true
				
				}
	
			
			// 환경변수를 재정의할 수 도 있음, 
				// env : 환경 변수
				// cwd : 현재 작업 디렉토리
				// shell : 기본 쉘
		}
	
	]

}
```


명령형 커맨드에서 `task : run build task`를 실행하면 출력결과가 나온다.

```bash
 *  작업 실행 중: echo Hello 

Hello
 *  터미널이 작업에서 다시 사용됩니다. 닫으려면 아무 키나 누르세요. 

