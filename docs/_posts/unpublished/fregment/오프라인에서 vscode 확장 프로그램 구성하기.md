
확장 프로그램 파일을 설치해서 vscode 확장 프로그램 목록에 추가하는 방법을 설명합니다.
> ubuntu 22.04


인터넷 연결없이 확장 프로그램을 구성하고 싶다면 미리 파일로 받아뒀다가 설치할 수 있다.

1. 마켓 플레이스에서 vsix 파일 설치
2. 도커 컨테이너 내에서 확장 프로그램 확인
3. vsix로 확장 프로그램 추가


<img src="{{ site.url }}{{ site.baseurl }}/docs/assets/image/vscode-extension-list.png" alt="">

```bash
root@ef487e4b288b:/workspaces/ros-devcontainer/.devcontainer/extensions# code --list-extensions
Extensions installed on Dev Container: ros-vscode-container:

root@ef487e4b288b:/workspaces/ros-devcontainer/.devcontainer/extensions# code --install-extension ./ms-vscode.cmake-tools-1.16.19.vsix 
Installing extensions on Dev Container: ros-vscode-container...
Extension 'ms-vscode.cmake-tools-1.16.19.vsix' was successfully installed.

root@ef487e4b288b:/workspaces/ros-devcontainer/.devcontainer/extensions# code --list-extensions
Extensions installed on Dev Container: ros-vscode-container:
ms-vscode.cmake-tools
twxs.cmake
```
