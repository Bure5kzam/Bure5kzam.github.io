---
layout: single
title: 리눅스 커널 컴파일
published: false
---

# Concept

## 커널 모듈

`/lib/moduels/[kernel-version]/` 디렉토리에는 해당 커널 버전에서 필요한 모듈 파일들이 빌드되지 않은 상태로 존재합니다.

커널에 적용하려면 `$ make`로 모듈들을 빌드해야 하며, 빌드된 모듈은 `/usr/src/kernels/[kernel-version]`로 옮겨 사용합니다.

`modules.dep`은 모듈 간의 의존성을 기록한 파일입니다.


# 커널 버전 확인

# Command

## $ make

새 커널을 적용하려면 make 명령어로 빌드 해서 사용해야 합니다.


```console

# 사전 환경설정

make mrproper           # 1. 기존 커널 컴파일 설정 초기화
                        # 2. 설정의 boejct 파일
                        # 백업 파일 모두 제거

make config             # 전통적인 설정
make menuconfig         # 메뉴 방식 화면으로 설정. 텍스트 메뉴 기반 커서 클릭
make xconfig            # X 윈도 환경에서 구현한 방식


# 커널 컴파일

make clean              # 이전에 컴파일해서 생성된 목적파일, 커널, 임시파일 등을 삭제
make bzImage            # 압축된 커널 이미지를 생성
make modules            # 커널 환경 설정에서 설정한 모듈들을 컴파일

# 결과물 처리

make modules_install    # 컴파일된 모듈들을 /lib/modules에 설치
make install            # /boot 디렉토리로 필요한 파일을 복사

```

## $ modprobe

리눅스 커널에 모듈을 추가하거나 제거합니다.

`$ modprobe`는 커널에서 지능적으로 모듈을 추가하거나 제거합니다. (주의, 편의상의 이유로 모듈 이름에서 _와 -를 구분하지 않으며, 자동으로 언더스코어 변환이 수행됨)

`$ modprobe`는 지속적으로 `/lib/modules[uname -r]` 안에 있는 모든 모듈들과 파일들을 조회합니다.(`/etc/modprobe.d` 디렉토리의 일부 설정 파일은 제외). 또한 modprobe는 커널 커맨드 라인에서 `<module>.<option>` 포맷으로 지정된 모듈 옵션들도 사용합니다. `modprobe.blacklist=<module>` 포맷으로 블랙리스트도 지정할 수 있습니다.

> $ man modprobe, [DESCRIPTION] </br></br>
> modprobe intelligently adds or removes a module from the Linux kernel: note that for convenience, there is no difference between _ and - in module names (automatic underscore conversion is performed).  modprobe looks in the module directory /lib/modules/`uname -r` for all the modules and other files, except for the optional configuration files in the /etc/modprobe.d directory (see modprobe.d(5)).  modprobe will also use module options specified on the kernel command line in the form of <module>.<option> and blacklists in the form of modprobe.blacklist=<module>.

`$ modprobe`는 `$ depmod` 유틸리티로 생성되는 `modules.dep.bin` 파일이 최신으로 유지되길 기대합니다. 이 파일은 다른 모듈들이 무슨 모듈을 필요로 하는지 나열하며, `$ modprobe`는 이 파일을 사용해 의존성을 자동으로 추가, 제거합니다.

| 옵션 | 내용                                                                                                                                                                      |
| ---- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| -r   | 모듈을 삽입하는 대신 제거합니다. 모듈이 의존하는 모듈들이 사용되지 않는다면 함께 제거하려고 시도합니다. 삽입과는 다르게 하나 이상의 모듈이 커맨드에서 지정될 수 있습니다. |

## $ modinfo

`$ modinfo`는 리눅스 커널에서 정보를 추출합니다. 모듈 이름이 파일 이름이 아니면, `/lib/modules/version` 디렉토리를 검색합니다. 이는 `$ modprobe` 에서도 적용됩니다.

`$ modinfo`는 기본적으로 각 속성을 `fieldname : value` 형태로 나열합니다. 

(...)

> modinfo extracts information from the Linux Kernel modules given on the command line. If the module name is not a filename, then the /lib/modules/version directory is searched, as is also done by modprobe(8) when loading kernel modules.
>
> modinfo by default lists each attribute of the module in form fieldname : value, for easy reading. The filename is listed the same way (although it's not really an attribute).

## $ depmod

`modules.dep`과 map file을 생성하는 명령어입니다.

리눅스 커널 모듈은 다른 모듈들이 코드에서 EXPORT_SYMBOL 변수로 사용할 수 있는 `symbols` 이라 부르는 서비스들을 제공할 수 있습니다. 만약 두 번째 모듈이 심볼을 사용하면 두 번째 모듈은 완전히 첫 번째 모듈에 의존합니다. 이런 의존성들은 꽤 복잡할 수 있습니다.

`$ depmod`는 `/lib/modules/version`에 있는 모듈들을 읽어 모듈 의존성 목록을 생성하고 어느 심볼간의 관계를 결정합니다. 기본적으로 modules.dep에 쓰이고 바이너리 해쉬 버전인 modules.dep.bin도 같은 디렉토리에 저장됩니다. 파일 이름이 주어지면 해당 모듈만 검사합니다. (모든 모듈이 등록되지 않는 한 거의 사용하기 어렵습니다) 또한 `$ depmod`는 `modules.symbols` 파일에 등록된 모듈들로부터 제공받은 심볼 목록과 바이너리 버전도 생성합니다.

(...)

> $ man depmod, [DESCRIPTION]
> Linux kernel modules can provide services (called "symbols") for other modules to use (using one of the EXPORT_SYMBOL variants in the code). If a second module uses this symbol, that second module clearly depends on the first module. These dependencies can get quite complex.
>
> depmod creates a list of module dependencies by reading each module under /lib/modules/version and determining what symbols it exports and what symbols it needs. By default, this list is written to modules.dep, and a binary hashed version named modules.dep.bin, in the same directory. If filenames are given on the command line, only those modules are examined (which is rarely useful unless all modules are listed).  depmod also creates a list of symbols provided by modules in the file named modules.symbols and its binary hashed version, modules.symbols.bin. Finally, depmod will output a file named modules.devname if modules supply special device names (devname) that should be populated in /dev on boot (by a utility such as systemd-tmpfiles).

