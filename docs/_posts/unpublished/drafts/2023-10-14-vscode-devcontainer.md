---
layout: single
title:
date: 2023-10-14 17:48
category:
author:
tags: []
summary:
---

Devcontainer는 vscode에서 컨테이너를 다룰 수 있게 해주는 확장프로그램이다.

한번 설정하면 vscode에서 나가지 않고 아래와 같은 것들이 가능하다.

- 컨테이너 빌드 및 ssh 접속
- 내부에서 vscode 사용 및 확장프로그램 구성
- 기존에 빌드된 이미지, Dockerfile, Docker-compose를 재사용 및 확장


devcontainer.json은 vscode 확장프로그램 `DevContainer`로 개발용 컨테이너를 사용할 때 적용되는 설정 파일이다.

devcontainer를 사용하면 다음과 같은 역할 수행이 가능하다.

## 레퍼런스

vscode [devcontaienr](https://code.visualstudio.com/docs/devcontainers/containers)

[container.dev](https://containers.dev/)

## devcontainer의 경로

devcontainer.json은 아래 경로 중 하나여야 적용된다.

- 프로젝트 루트의 .devcontainer/devcontainer.sjon
- 프로젝트 루트의 .devcontainer.json

## devcontainer의 이미지

devcontainer.json에서 이미지를 시작점으로 사용할 수 있다.
이미지가 없다면 컨테이너 레지스트리에서 필요한 이미지를 pull 해서 사용할 수 있다.

```json
{
  "image": "mcr.microsoft.com/devcontainers/typescript-node",

  "customizations": {
    "vscode": {
      "extensions": ["streetsidesoftware.code-spell-checker"]
    }
  },
  "forwardPorts": [3000]
}
```

위는 미리 빌드된 typescript, nodejs VS code 개발용 컨테이너 이미지를 사용하는 예시다.

extension 속성으로 확장프로그램 `dbaeumer.vscode-eslint`을 포함시켰다.

해당 베이스 이미지를 기반으로 생성되는 컨테이너들은 추가로 `streetsidesoftware.comde-spell-checker`를 vscode 확장프로그램으로 갖게 된다.

## dev container medtata

devcontainer.json 파일에서 개발용 컨테이너 설정에 필요한 기본적인 속성들을 metadata라 하는 것 같다.

컨테이너에 적용될 마운트, 권한, 이름 등을 지정할 수 있다. (도커파일이나 컴포즈의 경우 덮어씌울 수 있는 것 같다.)

[링크](https://containers.dev/implementors/json_reference/)


도커 컴포즈와 비교했을 때 조금 특이한 속성들도 보이는데

- init : 리눅스 init 프로세스를 활성화 시킬수있음
- customizations : 지원용 도구들에 대한 설정값. vscode 확장 프로그램도 여기서 추가할 수 있음.
-

### Dev Contaeinr Features

https://containers.dev/implementors/features/

Dev container의 Features 는 독립적이고 공유가능한 설치 코드 및 개발 컨테이너 구성 단위다. (Feature라는 이름은 하나를 참조하면 공동 작업자가 사용할 수 있게 더 개발 컨테이너에 더 많은 도구, 런타임, 기능을 빠르고 쉽게 추가할 수 있게하기 위해 가져왔다고 한다.)


Feature metadata는 feature의 루트 폴더에 있는 devcontainer-feature.json 에서 가져온다.

> Features는 이미지 위에 설치될 수 있지만, Feature의 구현은 가능한 베이스 이미지의 집합으로 제한합니다. 예를 들어 apt 패키지 매니저를 사용하는 데비안 기반 처럼 Feature는 특정 리눅스 버전을 특정하여 적절하게 작성될 수 있습니다.

### 폴더 구조

Feature는 devcontainer-feature.json과 install.sh을 포함하는 폴더안에 있는 엔티티다.


## dev container 사용 시나리오

- tool chain과 독립시키거나 설정 시간을 단축시키기 위해 stand-alone 컨테이너를 활성화
- 이미지, 도커 파일, 도커 컴포즈로 정의되어 어플리케이션이 배포된 컨테이너로 작업할 수 있음.
- 앱을 빌드하고 배포하기 위해 dev container 내부에서 도커 / 쿠버네틱스를 실행.


devcontainer.json에서 지원되는 워크플로우가 요구사항을 충족하지 못한다면, 이미 실행중인 컨테이너에 부착할 수 있음.

## Install additional software

dev container에 추가적인 소프트웨어를 추가하고 싶을 수 있다. 한번 VS Code가 컨테이너에 연결되면 VS Code 터미널을 열고 커맨드를 컨테이너안의 OS에서 실행할 수 있다. 그래서 새 커맨드라인 유틸리티를 설치하고 db나 어플리케이션 서비스를 실행할 수 있다.

devcontainer.json의 features 속성을 사용하면 미리 정의된 Features에서 툴이나 언어들을 설치할 수 있다.

예를 들어 다음 처럼 Azure CLI 최신 버전을 설치할 수 있다.

```bash
"features": {
    "ghcr.io/devcontainers/features/azure-cli:1": {
        "version": "latest"
    }
  }
```



### devcontainer-feature.json 속성

devcontainer-feature.json 파일은 주어진 Feature의 메타데이터를 정의한다.

모든 속성은 id, version, name으 제외하고 선택적이다.

devContainerFeature.schema.json은 devcontainer-feature.json 파일의 스키마를 정의한다.

이외의 속성들은 [링크](https://containers.dev/implementors/features/#folder-structure)를 참고.


## vscode server

[링크](https://code.visualstudio.com/docs/remote/vscode-server)

vscode 서버는 원격 개발 머신에서 실행할 수 있는 서비스로, 마치 가상머신이나 데스크탑 PC처럼 사용할 수 있게 해준다.

로컬 vs code 클라이언트를 통해 원격 머신에 보안접속할 수 있게 해주며, SSH가 필요하지 않다.

remote extension, 상호작용되는 cli, 보안 기능을 추가로 제공한다.

### 아키텍처

https://code.visualstudio.com/assets/docs/remote/vscode-server/server-arch-latest.png

## vscode 확장프로그램 설정

닷 파일은 파일 이름이 (.)로 시작하는 파일들이며, 다양한 어플리케이션들의 설정 정보를 담고있다. 개발 컨테이너는 넓은 범위의 어플리케이션 타입을 포함할 수 있기 때문에, 닷 파일을 저장해뒀다가 필요할 때 컨테이너에 복사해서 사용할 수 있다.



<!--
각 속성은 devcontainer.json에서 대응되는 속성들의 behavior를 미러링한다. 이는 커맨드가 프로젝트 워크스페이스 폴더의 컨텍스트에서 실행되는 behavior를 포함한다.

각 라이프 사이클 훅들과 Feature에 의해 기여되는 커맨드들은 시퀀스 (다음 커맨드가 실행되지 않게 막는)에 따라 실행된다. Feature에 의해 제공되는 커맨드들은 항상 유저에 의해 제공된 라이프사이클 커맨 -->

<!-- ## dev container image의 Pre building

매번 dev container에서 프로젝트를 열 때마다 이미지를 빌드하는 것 보다는 이미지를 pre-build할 것을 권장한다. pre-built image를 사용하면 더 빨리 컨테이너를 시작하고, 간단히 설정하고 서플라이 체인 보안을 향상시키고 잠재적인 에러를 피하기 위해 툴들의 버전을 지정하기 편해진다.

DevOps로 빌드 스케쥴링을 하면 사전 빌드를 자동화하고 지속적으로 통합할 수 있다.

무엇보다 좋은 점은 pre-built 이미지는 Dev container의 메타데이터를 포함할 수 있어 이미지를 참조했을 때 설정이 자동으로 가져와지는 것이다.

이미지를 pre-build하려면 DevContainer CLi 나 지원한다고 명시된 Github action을 사용할 것을 권장한다. 이들은 Dev container 확장프로그램들과 Feature들이 최신으로 사용할 수 있도록 지속해서 동기화되고 있기 때문이다. 한번 빌드하면 컨테이너 레지스트리에 push 해서 지속적으로 참고할 수 있다. -->




