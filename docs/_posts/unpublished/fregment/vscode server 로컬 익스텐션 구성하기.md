

CLI 에서 확장 프로그램을 관리하는 방법에 대한 섹션이 있었음.

https://code.visualstudio.com/docs/editor/extension-marketplace#_configuring-extensions


`code --install-extension {<extension-id> | <extension-vsix-path>`

설치하려면 두 가지 방법이 있다.
- 마켓 플레이스 확장 프로그램 id를 지정해 설치
- 로컬의 vsix 파일 경로를 지정해 설치


---
마켓에서 확장프로그램을 받아 로컬에서 재사용하는 방법에 대한 내용이 있었음.

https://code.visualstudio.com/docs/editor/extension-marketplace#_common-questions

>Some users prefer to download an extension once from the Marketplace and then install it multiple times from a local share. This is useful when there are connectivity concerns or if your development team wants to use a fixed set of extensions.
>
>To download an extension, navigate to the details page for the specific extension within the [Marketplace](https://marketplace.visualstudio.com/vscode). On that page, there is a **Download Extension** link in the **Resources** section, which is located on the right-hand side of the page.
>
>Once downloaded, you can then install the extension via the **Install from VSIX** command in the Extensions view command dropdown.


요약하면
1. 마켓에서 확장 프로그램에서 확장 프로그램 파일을 [다운](https://marketplace.visualstudio.com/vscode)
2. `Install from VSIX` 커맨드로 해당 파일을 지정해  설치.


vsix 포맷에서 로컬 확장프로그램을 시스템에 설치하도록 구성할 수 있는 것 같은데, vsix 파일 포맷에 관한 파일을 못찾고 있음.


---

## 개발 컨테이너에 확장프로그램을 구성하는 방법

1. 로컬 머신에서 확장프로그램 파일을 패키징해 vsix 파일 생성 (vsce 사용)
2. 컨테이너에 확장 프로그램 파일(vsix) 복사
3. 확장 프로그램에 구성 (code --install-extension {<extension-id> | <extension-vsix-path>)
4. 
위의 방법으로 가능한지 시험해봐야겠음.

1. 확장프로그램 다운로드

마켓 확장프로그램 페이지 내 `Version History` 탭에서 다운로드하면 vsix 파일을 받을 수 있었다.


## postCreateCommand에서 code 커맨드 실행이 안됨 
sh에서 실행해서 그런것 같아서 bash로 기본 터미널 설정하는 방법 찾는중
https://stackoverflow.com/questions/55987337/visual-studio-code-remote-containers-change-shell

https://github.com/microsoft/vscode-remote-release/issues/1042


code --install-extension /~/extensions/*
로 vsix 파일들을 설치하면 가능.