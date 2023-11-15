---
layout: posts
title: vscode setting.json
date: 2023-11-14 22:12
category: vscode
author: Bure5kzam
tags: []
summary: 
---

## vscode의 settings.json

vscode는 편집기 설정이나 UI 설정을 Json 파일로 관리한다.

- UI 설정은 **시각적인** 설정을 의미하며 **글자색**, **배경색**, **선색**, **폰트 크기**가 해당한다
- 편집기 설정은 **편집기 기능, 성능** 관련 설정을 의미하며 **키 바인딩**, **자동 괄호 닫기**, **GPU 가속 설정**

vscode 설정은 화면 중앙에 빨간 줄을 그어 버리는 수정이 있을 정도로 자유도가 높은 편이다.

vscode 설정은 크게 나누면 `기본 설정`, `유저 설정`, `워크스페이스 설정` 이 있다.

셋 중에는 `워크스페이스 설정`이 가장 우선순위가 높다.

설정 파일은같은 속성 key 값이 여러개면 우선 순위가 높은 순으로 적용된다.

1.  기본 설정
	1. 모든 인스턴스에 적용되는 기본 설정. Microsoft에서 권장하는 기본 구성으로 이루어져있다.
	2. IDE에서 열어 볼 수 있으며 read-only가 적용돼있다.
	3. 경로 : `defaultSetting.json`  
2. 유저 설정
	1. OS에 로그인한 유저에 적용되는 설정. 유저 홈디렉토리에 있다.
	2. 경로 : `~/.config/Code/User/settings.json`
3. 워크스페이스 설정
	1. 현재 워크스페이스에 적용되는 설정. 워크스페이스는 vscode에서 열린 현재 폴더들을 의미하며, `워크스페이스 설정`은 워크스페이스 저장 폴더에 저장된다.
	2. 경로 : `${workspaceFolder}/.vscode/settings.json`
	2. `workspaceFolder`는 현재 열린 프로젝트 루트 경로로 치환되는 vscode 자체 변수다.

vscode를 사용하다보면 더 많은 설정 파일을 수정해야할 수도 있다. ssh 접속을 하거나 멀티 루트 워크스페이스 기능을 사용하면 여기서 설명하지 않은 설정 파일을 수정해야 할 수도 있다. 

[vscode setting.json more]({% post_url 2023-10-17-vscode-setting-scope %}) 참고

> - defaultSetting은 프로그램에 하드코딩되어있다는 [Stack overflow](https://stackoverflow.com/questions/53369371/what-is-the-location-of-the-default-settings-file-of-vscode) 글이 있었다.
>
> - workspace와 관련된 내용들
> [vscode doc, default setting](https://code.visualstudio.com/docs/getstarted/settings#_default-settings)
> [vscode doc, User & workspace setting](https://code.visualstudio.com/docs/getstarted/settings)
> [vscode doc, Workspace setting](https://code.visualstudio.com/docs/getstarted/settings#_workspace-settings)
>
>
> - vscode 자체 변수가 궁금하다면 [vscode 도큐먼트, Variable referecne](https://code.visualstudio.com/docs/editor/variables-reference#_predefined-variables)

## 설정하는 방법


앞서 말했듯 설정은 Json 파일로 관리하기 때문에, 수정하려는 속성의 key값을 도큐먼트에서 찾아서 수정해서 설정할 수 있다.

IDE에서 편집창을 제공하므로 혹시 임의 수정이 부담스럽다면 UI로 하면 된다. (내 버전에서는 File >> Settings나 사이드바 하단의 톱니 버튼을 눌러 수정할 수 있다.)

`command palette`를 이용하면 키워드 검색을 제공해서 더 간단하다. 기본 단축키는 `ctrl + shift + p`다.

Json 수정과 UI 수정 모두 지원하니 편한 대로 고르자

명령형 팔레트에 `user setting`을 검색하면 **Preference Open UserSetting**와 **Preference Open UserSetting(JSON)**이 있다.

개인적으로는 인터넷 없이 설정하는 경우가 많아서 Json 설정을 쉘 스크립트로 만들어두는 방법을 선호한다.


## 다른 설정 파일들

앞서 vscode 대표적인 설정 파일인 `settings.json` 파일을 언급했었다.

`settings.json` 은 vscode 편집기 관련 설정이 저장된 파일이다.

vscode에서 디버깅, 실행, 자동완성, 컴파일같은 개발 기능을 사용하려면 **익스텐션을 설치**하고 **익스텐션 설정 파일**을 적절하게 **수정**해줘야한다.


<!-- vscode에서 폴더를 열 수 있는데, 이를 워크스페이스라고 한다.

워크스페이스 단위로 적용되는 설정을 분리해서 관리할 수 있는데 `.vscode` 폴더에 저장된다.

`settings.json`와 기타 설정파일들이 여기 저장된다. -->

```bash
settings.json
# vscode 설정 파일. vscode에서 value로 설정된다 싶은 내용 중 따로 설명되지 않은 내용은 다음과 같다.

launch.json
# vscode에 내장된 디버그 기능과 관련된 설정 파일.
# 디버깅할 언어 종류와 파일, 중단 패턴 시나리오 등을 설정할 수 있다.

task.json
# vscode와 상호작용 하는 스크립트 / 프로세스를 구성하는 파일.
# 스크립트나 프로세스를 테스크로 저장해두면 필요시 단축키로 실행할 수 있음.

c_cpp_properties.json
# 언어 관련 확장 프로그램 중 C++ 파일에 적용되는 확장 프로그램을 설정하는 파일. 자동 완성 기능인 Intelisence, 실시간 컴파일 및 의존성 확인 기능 등을 지원함.
# 기본적으로 파일의 확장자를 기준으로 적절한 확장 프로그램이 설정된다.
# 언어 별로 연관 확장프로그램과 설정 파일이 있음. [문서](https://code.visualstudio.com/docs/languages/overview) 참고

jsconfig.json
# 마찬가지로 자바스크립트용 확장 프로그램 설정 파일

...
```

컴파일이나 자동완성 기능은 사용 언어에 따라 다르기 때문에 확장 프로그램을 설치해서 설정해야 한다.

세부적인 내용은 향후 시간이 되면 다루겠다.

<!-- [dotfiles]({{site.baseurl}}/vscode/vscode-dotfiles) -->

> [vscode 도큐먼트, Programming Launguage](https://code.visualstudio.com/docs/languages/overview)
> 단축키 지정 파일인 keybindings.json도 있는데 아직 문서를 확인하지 못함.