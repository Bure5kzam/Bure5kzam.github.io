---
title: vscode 설정하기
layout: single
permalink: /vscode/
collection: vscode
entries_layout: grid
author_profile: true
toc : true

author: Bure5kzam
classes: wide
---


> Doc Reference : 23-10-23 [Vscode  Reference](https://code.visualstudio.com/docs)
> OS : ubuntu 20.04 - 22.04

# Overview


> watch out
> 작성 중인 글
{: .notice--warning}

<!-- <div class="entries-grid">
  {% include documents-collection.html collection="ubuntu" sort_by=page.sort_by sort_order=page.sort_order type="grid" %}
</div> -->


팀을 옮기면서 IDE를 Visual studio에서 Visual studio로 바꾸게 되었습니다.

개인적으로 비교되는 장점은 다음과 같습니다.

- ubuntu에서도 사용할 수 있습니다.
- ssh로 다른 디바이스에 접속해서 사용할 수 있음.
- 확장프로그램이 다양하며 snippet, lint, debugger 같은 개념이 잘 구성되어있음.
- 커스터마이징 기능이 다양해서 생산성을 향상시킬 수 있음 **(설정에 익숙하다면)**

팀원들과의 효율적인 개발라이프를 위해 설정 과정을 기록으로 남깁니다.

# vscode의 강력한 기능들

- debugger
  - debug point를 걸어 런타임 프로세스에서 변수나 스택 프레임 주소를 확인할 수 있음.
- compiler
  - 코드를 빌드해 실행 파일을 생성할 수 있음 (c계열 언어들, js 기반 프레임워크들)
- intelisence
  - 변수, 모듈, 라이브러리 명을 칠 때 자동완성을 제공 (+ 파일 위치 파악)
- lint
  - 코드 스타일 규약 적용 (ex 파이썬의 pep8, c++의 google core guideline 등)
- Formatter : 
  - 자동 줄바꿈 및 정렬 제공. 주로 lint와 함께 많이씀
- Remote connection
  - 다른 기기의 파일 시스템 접속, docker container에 접속 지원
- git history : 파일의 수정 내역, 소스코드 비교 지원
- Snippet
  - 축약어로 사용자 정의 소스코드 삽입하는 기능
- Task
  - 터미널에서 사용하는 커맨드 도구 (npm, tsc, python 등)를 편하게 실행하는 기능
- Devcontainer
  - 폴더 별로 사용할 도커 컨테이너 구성을 저장해두는 기능. dockerfile이나 docker-compose 파일을 이용할 수 있음.

# 콘셉트 

## 에디터 설정하기 : setting.json

vscode의 설정 내용은 `setting.json` 파일에 json 형식으로 저장됩니다.

`setting.json`은 여러 종류가 있으며, 가장 우선순위가 높은 파일의 설정이 적용됩니다. 

약 열 개 정도 되며 상세 내용은 관련 포스트 참고바랍니다.

빈번하게 설정하는 것들은

모든 vscode 인스턴스에 공통 적용되는 `defaultsetting.json`, 윈도우/리눅스 유저별로 적용되는 home dir의 `setting.json`, 

그리고 vscode에서 폴더를 열면 열린 폴더에 생성되어 적용되는 `.vscode/.setting.json`이 있습니다.

(적용 우선순위는 `defaultsetting.json` < `~/setting.json` < `pwd/.vscode/.setting.json` 순서로 점점 높아집니다.)

<br/>

`setting.json`에서 수정할 수 있는 내용은 너무 많아서 [vscode 공식 페이지 링크](https://code.visualstudio.com/docs/getstarted/settings#_default-settings)로 대체합니다.

시간 나면 사용하는 수정 사항 위주로 올려보겠습니다.

**관련 포스트**

{% include posts-tags.html cat="vscode" tag="setting" type="grid" %}

## 확장 프로그램 : Extension

확장 프로그램은 단순한 문서 편집 프로그램인 vscode를 개발툴로 만들어 줍니다.

생산성 향상에 중요한 역할을 하기 때문에 언어별 intellisence나 권장 확장 프로그램들은 설치하시는게 좋습니다.

확장프로그램 구성을 용도별로 프로필로 저장해두면 필요할 때 전환해서 사용할 수 있음 (파이썬용 프로필, 마크다운용 프로필 등)

**관련 포스트**

{% assign entries_layout = page.entries_layout | default: 'list' %}
{% include documents-collection-by-section.html collection=page.collection
section='extension' %}

{% include posts-tags.html cat="vscode" tag="extension" type="grid" %}


## Profile


프로필은 vscode 설정을 용도별로 나눠서 필요한 설정만 활성화시킬 수 있는 기능입니다.

~~예전에 atom 쓸 때 js, c++, python 구분없이 짬뽕이다보니 키는데 한세월이던 때를 떠올리면 아주 은혜로운 기능입니다...~~

프로필에서 구성할 수 있는 항목들은

- Settings - 프로필의 `setting.json`
- Extensions - 현재 프로필의 확장 프로그램 목록
- UI state - 뷰 배치 위치, 시각적인 뷰와 액션
- Keybindings - 프로필별 키 바인딩 파일인 `keybinding.json`
- Snippets - 프로필의 언어 스니펫 파일 `{language}.json`
- User Tasks - 프로필의 `tasks.json`

[vscode 도큐먼트 : Profile](https://code.visualstudio.com/docs/editor/profiles) 에는 다음과 같은 설명이 있습니다.

> 프로필을 선택하면 현재 워크스페이스 설정과 연동되며, 해당 폴더를 열 때마다 워크스페이스에 지정된 프로필이 활성화됩니다. 
> 워크 스페이스 활성화 시에 마지막으로 사용한 프로필 정보가 남아 있으면 프로필은 해당 관련 프로필로 전환됩니다.



## 기기간 설정 동기화 : sync

vscode는 github 계정 기반의 온라인 계정 동기화를 제공합니다. (gist)

포맷 후 재설정 할 때 불러오기로 활용하면 유용합니다.

로컬에 한번 저장하면 오프라인에서도 사용할 수 있습니다.

## 코드 조각 삽입하기 : snippet

스니펫은 접두사를 입력해서 코드조각을 삽입하는 기능입니다.

스니펫 설정은 간단합니다.

1. 언어별 스니펫 파일 생성

2. 삽입할 스니펫의 내용과 불러올 접두사, 삽입 후 이동될 커서 위치를 명시한다.

끝!

**관련 포스트**

{% assign entries_layout = page.entries_layout | default: 'list' %}
{% include documents-collection-by-section.html collection=page.collection
section='snippet' %}


## 원격 접속해서 사용 : vscode server

vscode에서 ssh 접속을 하면 접속지의 파일시스템이나 익스텐션을 사용할 수 있는데, 이는 접속지에 설치되는 vscode-server를 사용하는 덕분입니다.

[vscode 도큐먼트 : Visual studio server](https://code.visualstudio.com/docs/remote/vscode-server)


## 개발용 컨테이너 : devcontainer

devcontainer를 사용하면 vscode에서 열린 파일들을 공유하는 도커 컨테이너를 생성할 수 있습니다.

[vscode 도큐먼트 : devcontainer 개발](https://code.visualstudio.com/docs/devcontainers/containers#_opening-a-terminal)

[devcontainer sepcification](https://containers.dev/)

## 기타

이외에도 vscode 설정에 관련된 꿀팁들이 [문서](https://code.visualstudio.com/docs/getstarted/tips-and-tricks)로 정리되어있으니 확인해보면 좋다.


