익스텐션은 vs code가 editor 이상의 역할을 할 수 있게 구성하는데 필수적이다.

- 디버거
- 컴파일러
- 인텔리젠스
- 린트
- 포매터
- 원격 접속
- 이력 관리
- DB 뷰어
- 기타
	- 가독성 유틸리티
	- 커맨드 명령어 단축키

## extension 설치하기

vscode-server에 접속하면 원격 접속 상태에서도 확장프로그램을 사용할 수 있다.

인터넷이 있으면 마켓 플레이스 기능을 이용해서 설치할 수 있다. ( ctrl + shift + x )

마켓 플레이스에서 에러 메세지 'XHR Request fail'가 뜬다면 vs code가 익스텐션 목록을 불러오는데 실패한 것이다. 이 경우 인터넷 연결이 비활성화 되었거나 방화벽에 막혔을 수 도 있다.

오프라인에서 설치하고 싶은 경우, 'vsix' 파일을 받아다가 있으면 설치할 수 있다.

> 관련 vscode 링크 추가

[[오프라인에서 vscode 확장 프로그램 구성하기.md]]

> ssh 접속 상태에서 사용하기
> 테마, 스니펫처럼 UI 관련 지원을 제공하는 확장 프로그램은 로컬에 설치되는 반면, 그 외 대부분은 SSH로 접속한 호스트머신에 설치된다.
> [vscode document](https://code.visualstudio.com/docs/remote/ssh#_managing-extensions)
1

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