---
layout: single
title: 우분투 보조기억장치
date: 2022-07-10 14:51
category:  ubuntu
---

# Overview

디스크는 전력이 없어도 프로그램을 보존할 수 있는 보조 기억 장치입니다. `RAM` 보다 데이터 접근 속도가 느리지만 반영구적으로 저장할 수 있습니다.


<!-- (! 폰노이만 구조) -->


## Disk

물리적으로 데이터를 저장할 수 있는 공간입니다. 레이저 각인이나 전기 신호 등으로 데이터를 읽어들일 수 있으며, 앞서 말한대로 전원이 없어도 보존되는 것이 특징입니다.



## Partition

디스크는 보통 파티션 단위로 나눠 사용합니다. 이유는 다음과 같습니다.

- 메모리 주소의 표현 범위를 넘기지 않기위해. 32bit 아키텍처와 64bit 주소 아키텍처에서 사용할 수 있는 메모리 범위가 다른 이유이기도 합니다.
- 파티션별로 다른 파일 시스템을 사용하기 위해, 멀티 OS를 사용할 수 있음.
- 검색 범위를 줄여 조회속도를 향상시키기 위해

### Partition table

디스크에 파티션 테이블을 추가하면 각 파티션들을 별도의 디스크인것 처럼 사용할 수 있습니다.

파티션 테이블에는 각 파티션에 대한 중요한 정보들이 저장됩니다.

- 파티션의 시작, 끝지점
- 파티션의 활성 여부
- 파티션 유형

시작지점과 끝 지점은 디스크 내 파티션의 위치와 용량을 정의합니다.

파티션의 활성 플래그는 부트 로더에서 사용됩니다. 플래그 온 된 파티션의 운영체제가 부팅됩니다.

파티션 유형은 16진수 숫자로 지정되며, 파티션의 예상 사용을 식별하는데 사용합니다. (부팅할 OS 등)

#### MBR

고전적으로 사용되는 파티션 테이블 입니다. 

- 4개의 Partition을 가질 수 있음
- 2TB 까지 인식할 수 있음.

[OsDev Wiki, Partition table](https://wiki.osdev.org/Partition_Table)

[Wikipedia, Master Boot Record](https://en.wikipedia.org/wiki/Master_boot_record)

MBR 파티션 테이블을 가진 디스크는 최대  4개의 `Primary Partition`를 가질 수 있습니다.

만약 부족한 경우 `Primary Partition` 일부의 유형을 `Extend`로 지정하고, 내부에 `Logical Partition`을 구성해야합니다.

`Extend` 파티션은 여러개의 `logical partition` 테이블들로 구성되며, 디스크 내 최대 12개의 `logical partition`을 구성할 수 있습니다.

#### GPT

MBR의 대안으로 개발된 파티션 테이블 입니다.

[OsDev Wiki, GPT](https://wiki.osdev.org/GPT)


## Logical Management와 Volume

앞서 설명한 것처럼 디스크를 그대로 사용할 수도 있지만, 논리적으로 재정의해 사용할 수도 있습니다. (스토리지 가상화)

`Logical Management`는 OS와 하드웨어간에 추가적인 인터페이스를 제공합니다.

![LVM](/assets/image/LVM.png)

물리적인 스토리지 (Physical Volume)을 논리적인 스토리지(Logical Volume)으로 그룹핑해 사용합니다.

볼륨은 커널이 식별할 수 있는 데이터 스토리지입니다. 여러 파티션이 모인 볼륨 공간은 재 분배할 수 있습니다.

볼륨 개념은 물리적인 저장공간의 한계를 넘기 위해 존재합니다.
 예를들어 1TB 디스크 3 개를 사용할 경우, 1TB 넘는 데이터는 저장할 수 없습니다. 연속적인 저장공간이 아니기 때문입니다. 3개의 디스크로 볼륨 그룹을 만들면 이를 3TB 디스크 1개처럼 사용할 수 있습니다.

볼륨은 LVM(Logical Volume Manager)에 의해 관리되며 3가지 종류가 있습니다.

- Pysical volume (PV) : 기존 스토리지를 커널이 인식해 볼륨화 한 것입니다. 
- Volume group (VG) : Pysical volume의 그룹입니다. 하나 이상의 Pysical volume으로 구성되어있습니다.
- Logical volume (LG) : 논리적으로 재구성된 저장공간입니다. LG는 하나 이상의 VG에서 파생되며, 커널에서는 LG에 접근할 수 있습니다.
  
![Red Hat LVM 아키텍처](/assets/img/2022-07-16/basic-lvm-volume.png)

## 디스크의 인식 과정

### 1. 마운팅

보조 기억 장치를 장착하면 컴퓨터의 커널이 이를 인식하고 파일 시스템의 어딘가와 연동합니다. 이를 `마운팅`이라고 합니다. 장치가 마운팅되면 장치와 메모리를 공유하는 파일이 컴퓨터에 생성됩니다.

리눅스에는 `mount` 명령어로 디바이스의 엑세스 포인트를 생성합니다.
실제 존재하는 폴더에만 엑세스 포인트를 생성할 수 있습니다.

```console
[root@attic4‑cent ~]#mount /dev/sda3 /dos
mount: mount point /dos does not exist
[root@attic4‑cent ~]#mkdir /dos
[root@attic4‑cent ~]#mount /dev/sda3 /dos
```

### 2. 장치 확인

마운팅된 디스크 이름은 메인보드와 연결된 인터페이스에 따라 달라집니다.

- sd* (SCSI DISK, 스카시 디스크)
- sr* (SCSI CD-ROM, 스카시 롬)
- sg* (SCSI GENERIC)

리눅스 내에서 연결된 스토리지는 `/dev/` 폴더에서 볼 수 있으며, `fdisk -l` 명령어로 확인할 수 있습니다.

```console
[root@localhost ~]# fdisk -l

Disk /dev/sda: 8589 MB, 8589934592 bytes, 16777216 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x000badbb

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     2099199     1048576   83  Linux
/dev/sda2         2099200    16777215     7339008   8e  Linux LVM

Disk /dev/mapper/centos-root: 6652 MB, 6652166144 bytes, 12992512 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/mapper/centos-swap: 859 MB, 859832320 bytes, 1679360 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```


리눅스에서 파티션 마운트 시 이름은 아래와 같습니다.

- 첫 번째 연결된 HDD 이름은 `/dev/sda`입니다.
- 두 번째 연결된 HDD 이름은 `/dev/sdb`입니다.
- sda 디스크의 첫 번째 파티션 이름은 `/dev/sda0`이며 기본 파티션입니다.
- sda 디스크의 두 번째 파티션 이름은 `/dev/sda1`이며 기본 파티션입니다.
- sda 디스크의 세 번째 파티션 이름은 `/dev/sda2`이며 기본 파티션입니다.
- sda 디스크의 네 번째 파티션 이름은 `/dev/sda3`이며 논리 파티션 구성을 위한 확장 파티션입니다.
- sda 디스크의 다섯 번째 파티션 이름은 `/dev/sda4`이며 논리 파티션입니다. 확장 파티션 내에 구성됩니다.
- sda 디스크의 여섯 번째 파티션 이름은 `/dev/sda5`이며 논리 파티션입니다. 확장 파티션 내에 구성됩니다.

<!-- ## File과 File System


### /proc/partitions

파티션에 명명된 주번호와 부번호, 디바이스 명을 관리하는 링크 파일입니다.
디바이스 명은 /dev에서 사용됩니다.

```console
paul@RHELv4u4:~$ cat /proc/partitions 
major minor  #blocks  name

   3     0     524288 hda
   3    64     734003 hdb
   8     0    8388608 sda
   8     1     104391 sda1
   8     2    8281507 sda2
   8    16    1048576 sdb
   8    32    1048576 sdc
   8    48    1048576 sdd
 253     0    7176192 dm-0
 253     1    1048576 dm-1
``` -->


<!-- 
# Command(CentOS)

스토리지 제어에 관련된 명령어를 정리한 내용입니다.

## 파티션
fdisk 저장소의 파티션을 관리할 수 있다.
| 옵션        | 커맨드                                 |
| ----------- | -------------------------------------- |
| -l [device] | 명시된 디바이스의 파티션 테이블을 출력 |

## 볼륨

```console
[root@localhost ~]# pvcreate /dev/sda1
[root@localhost ~]# vgcreate vg00 /dev/sda1
[root@localhost ~]# lvcreate -L 10G -n lvname vgname 

```-->

## 디스크 RAID

RAID는 디스크 여러개를 하나의 디스크 처럼 사용하기 위해 적용된 디스크 표준 수준입니다.
디스트 세트의 데이터의 분산 저장, 데이터 오류(변조) 감지, 데이터 복구를 위한 기능을 제공하며, 디스크 세트에서 기능을 사용하려면 디스크들이 그에 맞는 RIAD 수준이어야 합니다.

### RAID 0

RAID 0가 적용된 디스크는 [`스트라이핑`](https://en.wikipedia.org/wiki/RAID) 기술을 사용할 수 있습니다.

`스트라이핑`은 순차 데이터를 세그먼트 단위로 분할하고 세그먼트 별로 디스크를 순환하며 저장하는 기술입니다. 세그먼트들을 순차적으로 다른 디스크에 저장하는 기술이며, 같은 세그먼트를 여러 디스크에 중복 저장하는 `미러링`과 다릅니다.

장점 1. 물리적으로 별개인 디스크들을 용량이 큰 하나의 디스크처럼 사용할 수 있습니다.
장점 2. 여러 디스크에 동시에 저장하므로 쓰기 속도가 빠릅니다.

단점 1. 세그먼트에서 오류가 발생하면 데이터 시퀀스의 정합성을 잃습니다.
단점 2. 세트에 연결된 드라이브 수에 따라 평균 장애율이 증가합니다.

### RAID 1

RIAD 1 이 적용된 디스크는 하나의 데이터를 여러 디스크에 중복 저장합니다(`미러링`).  

장점 1. 디스크 불량시 다른 디스크에 데이터가 보존되어있음.
장점 2. 멀티 쓰레드에서 [읽기 성능 향상](https://ko.wikipedia.org/wiki/RAID)


단점 1. 데이터 쓰기 속도 저하. 여러 디스크에 저장하기 때문

<!-- ### RAID 3, 4

`스트라이프` + `패리티 비트`

### RAID 5, 6

`스트라이프` + `패리티 비트`(배분)

### RAID 0+1

`스트라이프` 된 디스크를 `미러링` -->


# Reference

https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=skyluvtoya&logNo=100120822628

파티션 네이밍 룰 https://www.debian.org/releases/bullseye/amd64/apcs04.en.html

https://wiki.debian.org/Partition

마운트 https://developer.ibm.com/tutorials/l-lpic1-104-3/

볼륨 https://www.techtarget.com/searchstorage/definition/volume

LVM 아키텍처 개요 https://www.redhat.com/sysadmin/lvm-vs-partitioning