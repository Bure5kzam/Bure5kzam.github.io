---
title: vscode 개발 환경 설정하기
date: 2023-10-23 19:17
category: vscode
author: Bure5kzam
published: false
tags:
---

> Doc Reference : 23-10-23 [Vscode  Reference](https://code.visualstudio.com/docs)
> OS : ubuntu 20.04 - 22.04

회사 팀을 옮기면서 IDE를 Visual studio에서 Visual studio로 바꾸게 되었다.

일주일 정도 사용해보니

- 지원하는 os 폭이 넓음. => ubuntu, window 모두 사용할 수 있음.
- ssh 접속 지원 => 디바이스에 ssh로 접속해서 사용할 수 있음.
- 플러그인 세팅이 다양한 => 기능이 많음, 설정파일을 수정하면 커스터마이징도 할 수 있음.

앞으로 계속 사용할 것 같아 설정 과정을 기록으로 남긴다.

<!-- ## Purpose

- 폴더(워크스페이스) 별 독립적인 설정
- 확장 프로그램 설정 및 공유
- 디버거 설정 (C++, Python)
- 여러 프로젝트를 한번 인스턴스에서 열어서 보기 (워크스페이스)
- 폴더(워크스페이스) 별 개발용 도커 컨테이너 구성 -->

## setting.json

vscode 편집기 설정은 json 파일로 구성하는데, 설정 파일이 많아서 사전 파악이 필요하다.

기본 옵션은 `defaultsetting.json` 가 적용되고, 우선순위가 더 높은 설정 파일에 중복되는 설정이 있으면 새 설정을 재적용한다.

기본적으로 설정 파일에는 `기본 설정`, `유저 설정`, `워크스페이스 설정`가 있다는 것만 알아도 vscode를 IDE로 쓸 수 있겠다.
`
[vscode setting.json ]({% post_url 2023-11-14-vscode-setting-json %})


## 확장 프로그램

vscode에서 개발 관련 기능인 디버거, 자동완성 등을 사용하려면 확장 프로그램을 설치하고 구성해야 한다.

[vscode extension 구성]({$ post_url 2023-10-21-vscode 확장 프로그램 구성 % })

## 프로필

위에서 vscode는 다양한 설정들을 파일로 관리한다고 설명했다.

프로필은 설정을 온라인이나 계정에 저장해둿다가 복원하는 기능이다.

온라인(Github gist)로 공유할 수 있고 한번 저장하면 오프라인에서도 사용할 수 있지만 다른 곳에서 설정해둔 값을 가져올려면 인터넷에 한번은 연결을 해야한다.

[vscode 도큐먼트 : Profile](https://code.visualstudio.com/docs/editor/profiles) 에는 다음과 같은 설명이 있다.

> 프로필을 선택하면 현재 워크스페이스 설정과 연동되며, 해당 폴더를 열 때마다 워크스페이스에 지정된 프로필이 활성화된다. 
> 워크 스페이스 활성화 시에 마지막으로 사용한 프로필 정보가 남아 있으면 프로필은 해당 관련 프로필로 전환된다.

## 개발용 컨테이너

devcontainer를 사용하면 vscode에서 열린 파일들을 공유하는 도커 컨테이너를 생성할 수 있다.

[vscode 도큐먼트 : devcontainer 개발](https://code.visualstudio.com/docs/devcontainers/containers#_opening-a-terminal)

[devcontainer sepcification](https://containers.dev/)

## Vscode server

vscode에서 ssh 접속을 하면 접속지의 파일시스템이나 익스텐션을 사용할 수 있는데, 이는 접속지의 vscode-server를 사용하는 덕분이다.

[vscode 도큐먼트 : Visual studio server](https://code.visualstudio.com/docs/remote/vscode-server)


## 기타

이외에도 vscode 설정에 관련된 꿀팁들이 [문서](https://code.visualstudio.com/docs/getstarted/tips-and-tricks)로 정리되어있으니 확인해보면 좋다.