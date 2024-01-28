---
layout: single
title: 오프라인에서 vscode 확장 프로그램 구성하기
date: 2023-10-23 19:17
---

## Overview 
먼저 콘솔용 vscode 제어 명령어인 `code`가 있어야 한다.

[docs link, vscode](https://code.visualstudio.com/docs/editor/extension-marketplace#_configuring-extensions)


```bash
bure@burepool:~$ code -v
1.85.1
...
x64
```

---

마켓에서 extension 패키지 파일인 `vsix` 파일을 마켓에서 다운받으면 `code` 명령어로 설치할 수 있다.

[docs link, vscode, vsix](https://code.visualstudio.com/docs/editor/extension-marketplace#_common-questions)

```bash
# install extension in cli
bure@burepool:~$ code --install-extension {<extension-id> | <extension-vsix-path>
```

---

`vsix`파일은 확장 프로그램 웹 페이지에서 `version history` 부분에서 받을 수 있다. (23년 12년 기준)

[download link, vscode extension market](https://marketplace.visualstudio.com/vscode)

<!-- 마켓에서 확장프로그램을 받아 로컬에서 설치하는 방법 설명 글이 있었음. 

>Some users prefer to download an extension once from the Marketplace and then install it multiple times from a local share. This is useful when there are connectivity concerns or if your development team wants to use a fixed set of extensions.
>
>To download an extension, navigate to the details page for the specific extension within the [Marketplace](https://marketplace.visualstudio.com/vscode). On that page, there is a **Download Extension** link in the **Resources** section, which is located on the right-hand side of the page.
>
>Once downloaded, you can then install the extension via the **Install from VSIX** command in the Extensions view command dropdown. -->


<!-- 요약하면
1. 마켓에서 확장 프로그램에서 확장 프로그램 파일을 [다운](https://marketplace.visualstudio.com/vscode)
2. `Install from VSIX` 커맨드로 해당 파일을 지정해  설치.

~~vsix 포맷에서 로컬 확장프로그램을 시스템에 설치하도록 구성할 수 있는 것 같은데, vsix 파일 포맷에 관한 파일을 못찾고 있음.~~ => 그냥 extention 파일을 설치하면 vsix 포맷이 들어있었음. -->

---

<!-- ## dev container에 추가하기

1. 로컬 머신에서 확장프로그램 파일을 패키징해 vsix 파일 생성 (vsce 사용)
2. 컨테이너에 확장 프로그램 파일(vsix) 복사
3. 확장 프로그램에 구성 (code --install-extension {\<extension-id\> | \<extension-vsix-path\>)

위의 방법으로 가능한지 시험해봐야겠음.

### 확장프로그램 다운로드하기

마켓 확장프로그램 페이지 내 `Version History` 탭에서 다운로드하면 vsix 파일을 받을 수 있었다. -->

## code 명령어로 cli에서 설치하기 (devcontainer)

```bash

# 설치된 익스텐션 목록 보기
root@ef487e4b288b:/workspaces/ros-devcontainer/.devcontainer/extensions# code --list-extensions
Extensions installed on Dev Container: ros-vscode-container:

# vsix 파일로 설치하기
root@ef487e4b288b:/workspaces/ros-devcontainer/.devcontainer/extensions# code --install-extension ./ms-vscode.cmake-tools-1.16.19.vsix 
Installing extensions on Dev Container: ros-vscode-container...
Extension 'ms-vscode.cmake-tools-1.16.19.vsix' was successfully installed.

# 설치 결과 확인하기
root@ef487e4b288b:/workspaces/ros-devcontainer/.devcontainer/extensions# code --list-extensions
Extensions installed on Dev Container: ros-vscode-container:
ms-vscode.cmake-tools
twxs.cmake
```

## 해결되지 않은 문제점

### devcontainer 실행시 postCreateCommand에서 code 커맨드 실행이 안됨

ssh에서 실행해서 그런것 같아서 bash로 기본 터미널 설정하는 방법 찾는중

https://stackoverflow.com/questions/55987337/visual-studio-code-remote-containers-change-shell

https://github.com/microsoft/vscode-remote-release/issues/1042

### 설치 시 일부 익스텐션은 완료가 정상적으로 이루어지지 않음

하기 내용이 관련이 있는 것 아닐까 추측중

> 테마, 스니펫처럼 UI 관련 지원을 제공하는 확장 프로그램은 로컬에 설치되는 반면, 그 외 대부분은 SSH로 접속한 호스트머신에 설치된다.
> 
> [vscode document](https://code.visualstudio.com/docs/remote/ssh#_managing-extensions)