---
layout: post
title: vscode extension 설치하기
date: 2023-10-17 07:25
category:
author:
tags: []
summary:
---

## 2023-10-17

Devcontainer 내에서 vscode 개발환경을 설정하는 법을 찾는 중

dotfile은 프로젝트 루트의 .으로 시작하는 파일로, vscode 확장프로그램의 설정값을 저장하고 있음.
git 저장소에 dotfiles을 저장했다가 불러와서 환경을 설정할 수 있다고 하는데 , 원격 네트워크가 필요한 것 같아 후순위로 오프라인으로 설정할 수 있는 방법을 찾는 중

쉘 실행 순서에 관한 글
https://shreevatsa.wordpress.com/2008/03/30/zshbash-startup-files-loading-order-bashrc-zshrc-etc/


vscode 내부 환경변수에 관한 글
https://code.visualstudio.com/docs/editor/variables-reference


### dotfile 살펴보기

dotfile 사용 예시를 보고 어떻게 쓰는지 파악한 다음 자체 구성하는 방법을 찾아봐야겠다.

[vscode 문서](https://code.visualstudio.com/docs/devcontainers/containers#_personalizing-with-dotfile-repositories%EC%9D%84%20%EC%B6%94%EA%B0%80%ED%95%A0%20%EC%88%98%20%EC%9E%88%EB%8B%A4%EA%B3%A0%20%ED%95%98%EB%8A%94%EB%8D%B0)

~/.vscode에 익스텐션 관련 파일들이 있었음.

인터넷이 없어도 구성이 되나 해서 온라인일 때 컨테이너 빌드한 로그와 오프라인을 비교해봤다.

```bash

# 온라인 이미지 빌드
[1700 ms] Port forwarding 53788 > 32893 > 32893 stderr: Connection established
[1720 ms] [12:43:31] Installing extensions...
[1722 ms] [12:43:31] Extension host agent started.
[1758 ms] Port forwarding connection from 53802 > 32893 > 32893 in the container.
[1759 ms] Start: Run in container: /root/.vscode-server/bin/6c3e3dba23e8fadc360aed75ce363ba185c49794/node -e
[1765 ms] [12:43:31] Started initializing default profile extensions in extensions installation folder. file:///root/.vscode-server/extensions
[1769 ms] [12:43:31] [127.0.0.1][e5f317b5][ManagementConnection] New connection established.
[1771 ms] [12:43:31] ComputeTargetPlatform: linux-x64
[1781 ms] [12:43:31] Log level changed to info
[1800 ms] [12:43:31] Completed initializing default profile extensions in extensions installation folder. file:///root/.vscode-server/extensions
[1881 ms] Port forwarding 53802 > 32893 > 32893 stderr: Connection established
[1955 ms] [12:43:31] [127.0.0.1][9242d602][ExtensionHostConnection] New connection established.
[1963 ms] [12:43:31] [127.0.0.1][9242d602][ExtensionHostConnection] <282> Launched Extension Host Process.
[3241 ms] Start: Run in container: cat /proc/315/environ
[3323 ms] [12:43:33] Installing extension 'twxs.cmake'...
[3325 ms] [12:43:33] Getting Manifest... twxs.cmake
[3356 ms] [12:43:33] Installing extension 'sweilz.ros-snippets'...
[3357 ms] [12:43:33] Getting Manifest... sweilz.ros-snippets
[3363 ms] [12:43:33] Installing extension 'deitry.colcon-helper'...
[3363 ms] [12:43:33] Getting Manifest... deitry.colcon-helper
[3561 ms] [12:43:33] Installing extension 'ms-vscode.cmake-tools'...
[3561 ms] [12:43:33] Getting Manifest... ms-vscode.cmake-tools
[3689 ms] [12:43:33] Installing extension: twxs.cmake
[3710 ms] [12:43:33] Installing extension: sweilz.ros-snippets
[3752 ms] [12:43:33] Installing extension: deitry.colcon-helper
[4234 ms] [12:43:34] Installing extension: ms-vscode.cmake-tools
[4288 ms] [12:43:34] Extracted extension to file:///root/.vscode-server/extensions/sweilz.ros-snippets-0.1.0: sweilz.ros-snippets
[4291 ms] [12:43:34] Renamed to /root/.vscode-server/extensions/sweilz.ros-snippets-0.1.0
[4293 ms] [12:43:34] Extracting extension completed. sweilz.ros-snippets
[4298 ms] [12:43:34] Extension installed successfully: sweilz.ros-snippets
[4299 ms] [12:43:34] Extension 'sweilz.ros-snippets' v0.1.0 was successfully installed.
[4407 ms] [12:43:34] Extracted extension to file:///root/.vscode-server/extensions/twxs.cmake-0.0.17: twxs.cmake
[4410 ms] [12:43:34] Renamed to /root/.vscode-server/extensions/twxs.cmake-0.0.17
[4411 ms] [12:43:34] Extracting extension completed. twxs.cmake
[4413 ms] [12:43:34] Extension installed successfully: twxs.cmake
[4414 ms] [12:43:34] Extension 'twxs.cmake' v0.0.17 was successfully installed.
[4662 ms] [12:43:34] Getting Manifest... twxs.cmake
[5258 ms] [12:43:35] Extracted extension to file:///root/.vscode-server/extensions/deitry.colcon-helper-1.3.0: deitry.colcon-helper
[5260 ms] [12:43:35] Renamed to /root/.vscode-server/extensions/deitry.colcon-helper-1.3.0
[5261 ms] [12:43:35] Extracting extension completed. deitry.colcon-helper
[5264 ms] [12:43:35] Extension installed successfully: deitry.colcon-helper
[5265 ms] [12:43:35] Extension 'deitry.colcon-helper' v1.3.0 was successfully installed.
[5880 ms] [12:43:35] Extracted extension to file:///root/.vscode-server/extensions/ms-vscode.cmake-tools-1.15.31: ms-vscode.cmake-tools
[5887 ms] [12:43:35] Renamed to /root/.vscode-server/extensions/ms-vscode.cmake-tools-1.15.31
[5890 ms] [12:43:35] Extracting extension completed. ms-vscode.cmake-tools
[5893 ms] [12:43:35] Extension installed successfully: ms-vscode.cmake-tools
[5894 ms] [12:43:35] Extension 'ms-vscode.cmake-tools' v1.15.31 was successfully installed.
[11467 ms] Start: Run in container: mkdir -p '/vscode/vscode-server/extensionsCache' && cd '/root/.vscode-server/extensionsCache' && cp 'deitry.colcon-helper-1.3.0' '/vscode/vscode-server/extensionsCache'
[11473 ms]
[11473 ms]
[11474 ms] Start: Run in container: cd '/vscode/vscode-server/extensionsCache' && ls -t | tail -n +50 | xargs rm -f
[11479 ms]
[11480 ms]

# 온라인 이미지 빌드 후 익스텐션
root@107d2aa375fb:/workspace# ls ~/.vscode-server/extensions
deitry.colcon-helper-1.3.0  extensions.json  ms-vscode.cmake-tools-1.15.31  sweilz.ros-snippets-0.1.0  twxs.cmake-0.0.17
root@107d2aa375fb:/workspace# cat ~/.vscode-server/extensions/extensions.json
[{"identifier":{"id":"sweilz.ros-snippets","uuid":"57e1ca0b-e650-4732-ae75-72be97b23976"},"version":"0.1.0","location":{"$mid":1,"path":"/root/.vscode-server/extensions/sweilz.ros-snippets-0.1.0","scheme":"file"},"relativeLocation":"sweilz.ros-snippets-0.1.0","metadata":{"id":"57e1ca0b-e650-4732-ae75-72be97b23976","publisherId":"82851a77-f3c2-4095-81e3-4e07bb970da6","publisherDisplayName":"Liews Wuttipat","targetPlatform":"undefined","isApplicationScoped":true,"updated":false,"isPreReleaseVersion":false,"installedTimestamp":1697547262594,"preRelease":false}},{"identifier":{"id":"deitry.colcon-helper","uuid":"65caa881-4cb8-4d55-8ce0-a026684ad423"},"version":"1.3.0","location":{"$mid":1,"path":"/root/.vscode-server/extensions/deitry.colcon-helper-1.3.0","scheme":"file"},"relativeLocation":"deitry.colcon-helper-1.3.0","metadata":{"id":"65caa881-4cb8-4d55-8ce0-a026684ad423","publisherId":"a3241d89-ef12-4fd0-88ed-b41a7e7c3014","publisherDisplayName":"deitry","targetPlatform":"undefined","isApplicationScoped":true,"updated":false,"isPreReleaseVersion":false,"installedTimestamp":1697547262655,"preRelease":false}},{"identifier":{"id":"twxs.cmake","uuid":"2be7cf14-2603-402e-9771-fd79df83cdf8"},"version":"0.0.17","location":{"$mid":1,"path":"/root/.vscode-server/extensions/twxs.cmake-0.0.17","scheme":"file"},"relativeLocation":"twxs.cmake-0.0.17","metadata":{"id":"2be7cf14-2603-402e-9771-fd79df83cdf8","publisherId":"b5ab7991-81a8-4557-af54-4cb8e01e85c1","publisherDisplayName":"twxs","targetPlatform":"undefined","isApplicationScoped":true,"updated":false,"isPreReleaseVersion":false,"installedTimestamp":1697547262595,"preRelease":false}},{"identifier":{"id":"ms-vscode.cmake-tools","uuid":"7c889349-8749-43d4-8b5e-08939936d7f4"},"version":"1.15.31","location":{"$mid":1,"fsPath":"/root/.vscode-server/extensions/ms-vscode.cmake-tools-1.15.31","external":"file:///root/.vscode-server/extensions/ms-vscode.cmake-tools-1.15.31","path":"/root/.vscode-server/extensions/ms-vscode.cmake-tools-1.15.31","scheme":"file"},"relativeLocation":"ms-vscode.cmake-tools-1.15.31","metadata":{"id":"7c889349-8749-43d4-8b5e-08939936d7f4","publisherId":"5f5636e7-69ed-4afe-b5d6-8d231fb3d3ee","publisherDisplayName":"Microsoft","targetPlatform":"undefined","isApplicationScoped":true,"updated":false,"isPreReleaseVersion":false,"installedTimestamp":1697547263828,"preRelease":false}}]root@107d2aa375fb:/workspace#
```

```bash
# 오프라인 이미지 빌드
[1812 ms] [12:39:09] Installing extensions...
[1814 ms] [12:39:09] Extension host agent started.
[1824 ms] Port forwarding 45824 > 45773 > 45773 stderr: Connection established
[1857 ms] [12:39:10] Started initializing default profile extensions in extensions installation folder. file:///root/.vscode-server/extensions
[1860 ms] Port forwarding connection from 52780 > 45773 > 45773 in the container.
[1860 ms] Start: Run in container: /root/.vscode-server/bin/6c3e3dba23e8fadc360aed75ce363ba185c49794/node -e
[1865 ms] [12:39:10] ComputeTargetPlatform: linux-x64
[1867 ms] [12:39:10] [127.0.0.1][3d720a94][ManagementConnection] New connection established.
[1869 ms] [12:39:10] Completed initializing default profile extensions in extensions installation folder. file:///root/.vscode-server/extensions
[1875 ms] [12:39:10] ComputeTargetPlatform: linux-x64
[1892 ms] [12:39:10] Log level changed to info
[1991 ms] Port forwarding 52780 > 45773 > 45773 stderr: Connection established
[2067 ms] [12:39:10] [127.0.0.1][9e37e7e8][ExtensionHostConnection] New connection established.
[2071 ms] [12:39:10] [127.0.0.1][9e37e7e8][ExtensionHostConnection] <284> Launched Extension Host Process.
[3237 ms] Start: Run in container: cat /proc/315/environ

# 오프라인 빌드 후 익스텐션
root@be11c876d097:/workspace# ls ~/.vscode-server/extensions
extensions.json
root@be11c876d097:/workspace# cat ~/.vscode-server/extensions/extensions.json
[]root@be11c876d097:/workspace#
```

오프라인일 경우 설치가 진행되지 않는걸 확인할 수 있었다.



#### 가설 1. volume으로 .vscode를 볼륨하는 방법

워크스페이스를 공유하고
해당 폴더로 workspaceFolder를 지정하면 되지 않을까 하는 마음

```json
"workspaceMount": "source=${localWorkspaceFolder},target=/workspace,type=bind,consistency=cached",

// https://containers.dev/implementors/json_reference/
```

위 설정을 적용하니 프로젝트 루트 폴더가 컨테이너의 /workspace에 공유된다.

#### 오프라인으로 확장프로그램 구성하기

VSIX 확장자 파일이 있으면 code 명령어로 cli에서 extension 구성이 가능하다고 한다.

- 관련 [스택 오버플로우](https://stackoverflow.com/questions/37071388/how-can-i-install-visual-studio-code-extensions-offline)
- vscode Doc. Install from VSIX [섹션](https://code.visualstudio.com/docs/editor/extension-marketplace#_common-questions)
- vscode Doc. Packaging extension [섹션](https://code.visualstudio.com/api/working-with-extensions/publishing-extension)


* 10월 17일자 기준 설명

vsce는 vscode extension publish / packaging 툴이다. 사용하려면 npm으로 설치해야한다.

```bash
npm install -g @vscode/vsce
```

vsce에서 packaging은 빌드된 패키지들로 .vsix 확장자 파일을 작성하는 것을 의미한다.

```bash
code --install-extension myextension.vsix
```

