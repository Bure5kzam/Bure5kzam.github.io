
커맨드는 액션을 동작시키는 트리거다.

설정된 단축키 사용도 커맨드에 속한다.

커맨드는 **확장프로그램이 기능을 유저에게 노출**시키고 **액션을 vscode ui에 바인드**하며, **내부 로직을 내부 로직을 시행**하는데 사용된다.

[vscode document, extension](https://code.visualstudio.com/api/extension-guides/command)

## task

Make, Link, Typescript 등의 도구들은 대부분 커맨드로 실행된다. vscode에서 도구를 실행하고 결과를 분석할 수 있으면 도움이 된다.

task는 스크립트를 실행하고 프로세스를 시작하도록 구성할 수 있으므로, 커맨드 입력이나 새롭게 코드를 작성해야하는 것을 예방해준다. 

tasks.json은 `.vscode` 디렉토리 안에 있다.

> task는 워크스페이스 활성화 상태에서만 사용할 수 있음.

