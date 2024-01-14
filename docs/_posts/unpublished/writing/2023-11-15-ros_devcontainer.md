---
layout: post
title: 
date: 2023-11-15 22:36
category: 
author: 
tags: []
summary: 
---
ros humble 도큐먼트에서 제시하는 devcontainer 구성이다.

## 파일 구조

```bash
ws_[project]
├── cache
|   ├── [ROS2_DISTRO]
|   |   ├── build
|   |   ├── install
|   |   └── log
|   └── ...
|
├── src
    ├── .devcontainer
    │   ├── devcontainer.json
    │   └── Dockerfile
    ├── package1
    └── package2
```

`colcon build` 시 생성되는 `build`, `install`, `log`를 배포 버전 별로 구성한 것이 특이하다.

```json
{
    // UI에 표시되는 devcontainer 이름
    "name": "ROS 2 Development Container",
    "privileged": true,
    // 컨테이너 내에서 사용할 유저 계정.
    // 컨테이너 유저의 uid / gid를 로컬 유저와 일치하도록 업데이트해 마운트된 바운드 공간에서의 권한문제를 해결할 수 있다고함
    "remoteUser": "USERNAME",
    "build": {
        "dockerfile": "Dockerfile",
        "args": {
            // Dockerfile 빌드 시 사용할 인자
            "USERNAME": "USERNAME"
        }
    },

    // 컨테이너 내 쉘의 시작 경로
    "workspaceFolder": "/home/ws",
    // vscode에서 워크스페이스로 오픈한 폴더 내 경로를 컨테이너 내 파일시스템에 마운트함
    // localWorkspaceFolder는 워크스페이스 루트 경로를 나타내는 vscode 변수
    "workspaceMount": "source=${localWorkspaceFolder},target=/home/ws/src,type=bind",
    "customizations": {
        "vscode": {
            "extensions":[
                // 설치할 익스텐션 마켓 ID
                // 인터넷이 연결되어있으면 vscode 서버 실행 후 자동으로 설치함
                "ms-vscode.cpptools",
                "ms-vscode.cpptools-themes",
                "twxs.cmake",
                "donjayamanne.python-extension-pack",
                "eamodio.gitlens",
                "ms-iot.vscode-ros"
            ]
        }
    },
    "containerEnv": {
        //컨테이너의 환경변수 재정의
        "DISPLAY": "unix:0",
        "ROS_AUTOMATIC_DISCOVERY_RANGE": "LOCALHOST",
        "ROS_DOMAIN_ID": "42"
    },
    "runArgs": [
        // 도커 CLI 아규먼트 
        "--net=host",
        "-e", "DISPLAY=${env:DISPLAY}"
    ],
    "mounts": [
       "source=/tmp/.X11-unix,target=/tmp/.X11-unix,type=bind,consistency=cached",
        "source=/dev/dri,target=/dev/dri,type=bind,consistency=cached",
        "source=${localWorkspaceFolder}/../cache/ROS_DISTRO/build,target=/home/ws/build,type=bind",
        "source=${localWorkspaceFolder}/../cache/ROS_DISTRO/install,target=/home/ws/install,type=bind",
        "source=${localWorkspaceFolder}/../cache/ROS_DISTRO/log,target=/home/ws/log,type=bind"
    ],
    "postCreateCommand": "sudo rosdep update && sudo rosdep install --from-paths src --ignore-src -y && sudo chown -R USERNAME /home/ws/"
    // 컨테이너 실행 후 실행할 쉘 명령어
}
```