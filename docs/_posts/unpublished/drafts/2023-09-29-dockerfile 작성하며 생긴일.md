---
layout: post
title:
date: 2023-09-29 18:14
category:
author:
tags: []
summary:
---

Dockerfile을 작성하며 직면한 문제들

## apt 설치 중 사용자 입력을 요구하는 문제 (Solved)

Dockerfile에서 데비안 패키지 설치 중에 추가입력을 요구하는 상황이 발생했다.
빌드 중에 콘솔에 입력을 할 수 없어 대기 상태에서 진행되지 않았다.


```bash
Setting up keyboard-configuration (1.108ubuntu15) ...
debconf: unable to initialize frontend: Dialog
debconf: (TERM is not set, so the dialog frontend is not usable.)
debconf: falling back to frontend: Readline
Configuring keyboard-configuration
----------------------------------

The layout of keyboards varies per country, with some countries having multiple
common layouts. Please select the country of origin for the keyboard of this
computer.

  1. Afghani                                     48. Irish
  2. Albanian                                    49. Italian
...
  1.  English (UK)                               75. Slovak
  2.  English (US)                               76. Slovenian
...
  1.  Icelandic                                  92. Vietnamese
  2.  Indian                                     93. Wolof
  3.  Iraqi
Country of origin for the keyboard:

# apt로 ros 관련 패키지 설치 중 키보드 입력 언어를 선택하라는 요청이 있었다.
# 빈 도커파일이라 추가 설치가 있었던 모양이다.
```

입력을 미리 지정해두거나 우회할 방법이 필요했다.

### 방법 1. 파이프라인으로 입력할 인자 전달 (yes 커맨드)

yes는 인자로 받은 값을 파이프라인으로 전달하는 쉘 명령어다.

```bash
yes hello | bash -c '
> read a
> echo "input argument : $a"
> '
input argument : hello
```

`bash -c`를 사용해서 임시로 read로 입력받아 echo로 출력하는 스크립트를 작성했다.

위 스크립트의 실행 순서는 다음과 같다.

1. hello를 전달해 stdout으로 전달.
2. read로 hello롤 입력 버퍼에서 가져와 echo로 출력

```bash

# 베이스 이미지를 선택합니다.
FROM ubuntu:latest

# 필요한 패키지 설치 중에 사용자 입력을 기다립니다.
RUN printenv
RUN apt-get update
RUN yes y | apt-get install cowsay

# Docker 컨테이너에서 실행될 애플리케이션 또는 명령을 여기에 추가합니다.

```

스크립트에서 Y 입력을 요구할 때 y를 전달할 수 있다.

### 방법 2. 패키지 매니저에 패키지 설정 값 사전 설정하기

데미안 패키지 매니저에 설정값을 미리 지정하여 패키지 매니저 설치 시 적용되도록 할 수 있다.

(필요시 debconf-utils를 설치)

호스트 머신에 적용되어있는 패키지 매니저 설정 값을 파일로 복사해서 컨테이너 안에서 적용할 수 있다.

다음은 컨테이너 내부에서 `keyboard-configuration` 패키지 설치시 질의를 방지하기 위해 컨테이너의 패키지 매니저에 호스트 머신의 설정값을 적용하는 과정이다.

```bash
# 패키지 매니저 설정 값 파일로 추출 (호스트 머신 쉘에서)
$ debconf-get-selections | grep keyboard-configuration > dpkg-selections.conf

# 컨테이너에서 복사 (도커파일 안에서)
COPY ./dpkg-selections.conf /etc/

# 컨테이너 내부에서 설치하려는 패키지의 설정값을 데비안 패키지 매니저에 적용 (필요시 debconf-utils 설치)
RUN debconf-set-selections < dpkg-selections.conf

# 대상 패키지 설치 (도커파일 안에서)
RUN apt-get install -y keyboard-configuration

```

### 방법 3. 패키지 매니저에 질의하지 않도록 설정 값 설정하기

`DEBIN_FRONTEND`는 패키지 매니저가 참조하는 환경변수로, 설치 할 때 사용자 질의를 요청할지를 지정한다.

```bash
# 베이스 이미지를 선택합니다.
FROM ubuntu:latest as base


RUN printenv
RUN apt-get update


FROM base as case1
# 필요한 패키지 설치 중에 사용자 입력을 기다립니다.
RUN DEBIN_FRONTEND=dialog apt-get install -y keyboard-configuration

FROM base as case2
# 필요한 패키지 설치 중에 사용자 입력을 기다립니다.
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y keyboard-configuration

# Docker 컨테이너에서 실행될 애플리케이션 또는 명령을 여기에 추가합니다.
```

위 스크립트는 case1로 실행하면 패키지 매니저 입력 요청 기본값으로 동작하고 case2는 사용자 입력 요청을 비활성화한채 동작한다.

기본값으로 실행하면 패키지 설치시에 사용자 입력을 요청하는 것을 확인할 수 있다.

```bash
$ :~/test$ docker build --tag test -f Dockerfile.input_request --progress=plain  --target=case1 .
#0 building with "default" instance using docker driver

...

#6 [base 3/3] RUN apt-get update
#6 CACHED

#7 [case1 1/1] RUN DEBIN_FRONTEND=dialog apt-get install -y keyboard-configuration
#7 0.252 Reading package lists...
#7 0.963 Building dependency tree...
#7 1.112 Reading state information...
#7 1.260 The following additional packages will be installed:
#7 1.261   liblocale-gettext-perl
#7 1.282 The following NEW packages will be installed:
#7 1.282   keyboard-configuration liblocale-gettext-perl
#7 2.057 0 upgraded, 2 newly installed, 0 to remove and 49 not upgraded.
#7 2.057 Need to get 207 kB of archives.

...

#7 3.611 ----------------------------------
#7 3.611
#7 3.611 The layout of keyboards varies per country, with some countries having multiple
#7 3.611 common layouts. Please select the country of origin for the keyboard of this
#7 3.611 computer.
#7 3.611
#7 3.611   1. Afghani
#7 3.611   2. Albanian
#7 3.611   3. Amharic
#7 3.611   4. Arabic
#7 3.611   5. Arabic (Morocco)
#7 3.611   6. Arabic (Syria)
#7 3.611   7. Armenian
#7 3.611   8. Azerbaijani
...
#7 3.612   97. Uzbek
#7 3.612   98. Vietnamese
#7 3.612   99. Wolof
#7 3.612 Country of origin for the keyboard:
#7 3.612 Use of uninitialized value $_[1] in join or string at /usr/share/perl5/Debconf/DbDriver/Stack.pm line 111.
#7 3.641 Your console font configuration will be updated the next time your system
#7 3.641 boots. If you want to update it now, run 'setupcon' from a virtual console.
#7 3.646 Use of uninitialized value $val in substitution (s///) at /usr/share/perl5/Debconf/Format/822.pm line 83, <GEN6> line 5.
#7 3.646 Use of uninitialized value $val in concatenation (.) or string at /usr/share/perl5/Debconf/Format/822.pm line 84, <GEN6> line 5.
#7 DONE 3.7s

#8 exporting to image
#8 exporting layers 0.0s done
#8 writing image sha256:0551c8495708f9437f7df763cc52949f5aa7506acf420cddbb1842667914413c done
#8 naming to docker.io/library/test done
#8 DONE 0.0s

```

`DEBIAN_FRONTEND=noninteractive`를 적용하면 사용자 입력 요청을 하지 않는다.

```bash
$ :~/test$ docker build --tag test -f Dockerfile.input_request --progress=plain  --target=case2 .

...

#6 [base 3/3] RUN apt-get update
#6 CACHED

#7 [case2 1/1] RUN DEBIAN_FRONTEND=noninteractive apt-get install -y keyboard-configuration
#7 0.266 Reading package lists...
#7 1.031 Building dependency tree...
#7 1.203 Reading state information...
#7 1.354 The following additional packages will be installed:
#7 1.354   liblocale-gettext-perl
#7 1.379 The following NEW packages will be installed:
#7 1.380   keyboard-configuration liblocale-gettext-perl
#7 2.345 0 upgraded, 2 newly installed, 0 to remove and 49 not upgraded.
#7 2.345 Need to get 207 kB of archives.
#7 2.345 After this operation, 902 kB of additional disk space will be used.
#7 2.345 Get:1 http://archive.ubuntu.com/ubuntu focal/main amd64 liblocale-gettext-perl amd64 1.07-4 [17.1 kB]
#7 3.114 Get:2 http://archive.ubuntu.com/ubuntu focal/main amd64 keyboard-configuration all 1.194ubuntu3 [190 kB]
#7 4.176 debconf: delaying package configuration, since apt-utils is not installed
#7 4.206 Fetched 207 kB in 3s (77.5 kB/s)
#7 4.218 Selecting previously unselected package liblocale-gettext-perl.
(Reading database ... 4127 files and directories currently installed.)
#7 4.221 Preparing to unpack .../liblocale-gettext-perl_1.07-4_amd64.deb ...
#7 4.224 Unpacking liblocale-gettext-perl (1.07-4) ...
#7 4.270 Selecting previously unselected package keyboard-configuration.
#7 4.271 Preparing to unpack .../keyboard-configuration_1.194ubuntu3_all.deb ...
#7 4.282 Unpacking keyboard-configuration (1.194ubuntu3) ...
#7 4.311 Setting up liblocale-gettext-perl (1.07-4) ...
#7 4.320 Setting up keyboard-configuration (1.194ubuntu3) ...
#7 4.511 Your console font configuration will be updated the next time your system
#7 4.511 boots. If you want to update it now, run 'setupcon' from a virtual console.
#7 DONE 4.6s

#8 exporting to image
#8 exporting layers 0.0s done
#8 writing image sha256:cb5b506d4adbd928fc36c3df90858d843545f118fac3aafefc163124d641e31c done
#8 naming to docker.io/library/test done
#8 DONE 0.0s

```

## Docker 빌드 시 진행상황 확인이 어려운 문제 (Solved)

이미지 빌드 중 발생한 에러들을 확인하고 싶은데, 창을 완전히 표시해주지 않아 확인에 어려움을 겪었다. (buildx 적용된 도커 엔진 사용)

빌드 환경 변수 중에 로그를 전문으로 확인하도록 제어하는 변수가 있었다.

[Docker reference](https://docs.docker.com/engine/reference/commandline/buildx_build/#progress) 에서 Set the target platforms for the build 탭

```bash
$ :~/test$ docker build -t test --no-cache -f  ./Dockerfile.input_request --progress=plain .
#0 building with "default" instance using docker driver

#1 [internal] load build definition from Dockerfile.input_request
#1 transferring dockerfile: 459B done
#1 DONE 0.0s

#2 [internal] load .dockerignore
#2 transferring context: 2B done
#2 DONE 0.0s

#3 [internal] load metadata for docker.io/library/ubuntu:latest
#3 DONE 0.0s

#4 [1/5] FROM docker.io/library/ubuntu:latest
#4 CACHED

#5 [2/5] RUN printenv
#5 0.191 HOME=/root
#5 0.191 PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
#5 0.191 PWD=/
#5 DONE 0.2s

#6 [3/5] RUN apt-get update
#6 0.949 Get:1 http://archive.ubuntu.com/ubuntu focal InRelease [265 kB]
#6 1.175 Get:2 http://security.ubuntu.com/ubuntu focal-security InRelease [114 kB]
#6 2.157 Get:3 http://archive.ubuntu.com/ubuntu focal-updates InRelease [114 kB]
#6 2.502 Get:4 http://security.ubuntu.com/ubuntu focal-security/restricted amd64 Packages [2828 kB]
#6 2.504 Get:5 http://archive.ubuntu.com/ubuntu focal-backports InRelease [108 kB]
#6 2.735 Get:6 http://archive.ubuntu.com/ubuntu focal/restricted amd64 Packages [33.4 kB]

...

```



## 빌드 시 스크립트 실행 명령이 캐쉬 적용이 안되는 문제

회사 작업중에 Dockerfile에서 스크립트를 실행하면 캐싱이 안되는 문제가 있었다.

하지만 집에서 해보니 RUN 예약어로 실행하던 스크립트를 실행하던 둘다 캐싱이 되었다.

```bash

# 테스트 도커 파일
ros@burePool:~/test$ cat Dockerfile.time_waste
FROM ubuntu:20.04 AS base

COPY ./execute_apt_update.sh /

FROM base as case1
RUN apt update

FROM base as case2
RUN . /execute_apt_update.sh

# 테스트 스크립트 내용
ros@burePool:~/test$ cat execute_apt_update.sh
apt update

# case 1 실행 결과 (캐싱 과정 생략)
ros@burePool:~/test$ docker build --tag test -f Dockerfile.time_waste --progress=plain  --target=case1 .
#0 building with "default" instance using docker driver

#1 [internal] load .dockerignore
#1 transferring context: 2B done
#1 DONE 0.0s

#2 [internal] load build definition from Dockerfile.time_waste
#2 transferring dockerfile: 192B done
#2 DONE 0.0s

#3 [internal] load metadata for docker.io/library/ubuntu:20.04
#3 DONE 0.0s

#4 [base 1/2] FROM docker.io/library/ubuntu:20.04
#4 DONE 0.0s

#5 [internal] load build context
#5 transferring context: 42B done
#5 DONE 0.0s

#6 [base 2/2] COPY ./execute_apt_update.sh /
#6 CACHED

#7 [case1 1/1] RUN apt update
#7 CACHED

#8 exporting to image
#8 exporting layers done
#8 writing image sha256:08f06232a2289b56dd2e4d3712f3f0815933f76b32c21724077de25ada035b29 done
#8 naming to docker.io/library/test done
#8 DONE 0.0s


# case 2 실행 결과 (캐싱 과정 생략)
ros@burePool:~/test$ docker build --tag test -f Dockerfile.time_waste --progress=plain  --target=case2 .
#0 building with "default" instance using docker driver

#1 [internal] load .dockerignore
#1 transferring context: 2B done
#1 DONE 0.0s

#2 [internal] load build definition from Dockerfile.time_waste
#2 transferring dockerfile: 192B done
#2 DONE 0.0s

#3 [internal] load metadata for docker.io/library/ubuntu:20.04
#3 DONE 0.0s

#4 [base 1/2] FROM docker.io/library/ubuntu:20.04
#4 DONE 0.0s

#5 [internal] load build context
#5 transferring context: 42B done
#5 DONE 0.0s

#6 [base 2/2] COPY ./execute_apt_update.sh /
#6 CACHED

#7 [case2 1/1] RUN . /execute_apt_update.sh
#7 CACHED

#8 exporting to image
#8 exporting layers done
#8 writing image sha256:407860dfb9a8b1693002ec9a8b9ead2ea01f55835049ca480acd95357b8f24bf done
#8 naming to docker.io/library/test done
#8 DONE 0.0s


```

결과적으로 스크립트든 RUN 모두 레이어를 생성하고 캐싱을 한다는 것은 동일하다고 한다.

[Dockerfile RUN layers vs script](https://stackoverflow.com/questions/70883712/dockerfile-run-layers-vs-script)

캐싱 여부는 의존관계의 수정에 의해 결정되며, 개별 레이어의 크기와 캐싱 빈도에 따라 두 방법 중 적절하게 결정 해야 할 것 같다.

## Colcon build

### CMake Error: The current CMakeCache.txt directory...is different than the directory


원인은 지금 빌드하려는 패키지의 package.xml 정보와 원래 빌드된 패키지의 환경 정보가 달라서였다.

내 경우 도커 컨테이너에서 빌드한 파일이 마운트로 공유되고 있었는데, 호스트 머신에서 빌드 시도해서 발생했다.

```bash
ros@burePool:~/ros/workspaces/ros_ws$ colcon build
Starting >>> action_tutorials_interfaces
Starting >>> cpp_pubsub
--- stderr: cpp_pubsub
CMake Error: The current CMakeCache.txt directory /home/ros/ros/workspaces/ros_ws/build/cpp_pubsub/CMakeCache.txt is different than the directory /home/test/ros/ros_ws/build/cpp_pubsub where CMakeCache.txt was created. This may result in binaries being created in the wrong place. If you are not sure, reedit the CMakeCache.txt
CMake Error: The source directory "/home/test/ros/ros_ws/src/cpp_pubsub" does not exist.
Specify --help for usage, or press the help button on the CMake GUI.
make: *** [Makefile:354: cmake_check_build_system] Error 1
---
Failed   <<< cpp_pubsub [0.11s, exited with code 2]
Aborted  <<< action_tutorials_interfaces [0.13s]

Summary: 0 packages finished [0.56s]
  1 package failed: cpp_pubsub
  1 package aborted: action_tutorials_interfaces
  2 packages had stderr output: action_tutorials_interfaces cpp_pubsub
  1 package not processed

```

build 디렉토리에서 기존 빌드 결과물을 확인하면 기존 빌드 정보를 확인할 수 있다.

```bash
ros@burePool:~/ros/workspaces/ros_ws$ cat build/cpp_pubsub/CMakeCache.txt | grep home
# For build in directory: /home/test/ros/ros_ws/build/cpp_pubsub
AMENT_TEST_RESULTS_DIR:PATH=/home/test/ros/ros_ws/build/cpp_pubsub/test_results
CMAKE_INSTALL_PREFIX:PATH=/home/test/ros/ros_ws/install/cpp_pubsub
cpp_pubsub_BINARY_DIR:STATIC=/home/test/ros/ros_ws/build/cpp_pubsub
cpp_pubsub_SOURCE_DIR:STATIC=/home/test/ros/ros_ws/src/cpp_pubsub
CMAKE_CACHEFILE_DIR:INTERNAL=/home/test/ros/ros_ws/build/cpp_pubsub
CMAKE_HOME_DIRECTORY:INTERNAL=/home/test/ros/ros_ws/src/cpp_pubsub

```

#### 방법 1. 워크스페이싀 build 디렉토리 내 파일 삭제 후 다시 빌드

새롭게 build 내 파일들을 생성하므로 해당 내용을 수정하지 않아도 되지만, 빌드 머신 정보가 달라지면 다시 발생할 테니 근본적인 해결법은 아니다.

```bash
ros@burePool:~/ros/workspaces/ros_ws$ rm -rf build/*
```


### usr/bin/ld: /opt/ros/galactic/lib/librcl_logging_spdlog.so: undefined reference to `spdlog::details::log_msg::log_msg(spdlog::source_loc, fmt::v6::basic_string_view\<char\>, spdlog::level::level_enum, fmt::v6::basic_string_view\<char\>)'

원인은 호스트에 ros 실행에 필요한 모듈이 설치되지 않았는데 빌드를 시도해서였다. 컨테이너에서 /opt/ros/galactic 디렉토리를 마운트한 상태에서 ros를 설치해 호스트 머신과 공유하고 있었는데, ros에서 참조하는 /usr 디렉토리는 호스트 머신에서 없어서 찾을 수 없었다. 컨테이너 내에서 빌드하면 문제없이 성공했다.

컨테이너 디렉토리를 잘못 구성해서 발생한 문제라고 판단되어서 머신간에 ros를 독립적으로 구성했다.

