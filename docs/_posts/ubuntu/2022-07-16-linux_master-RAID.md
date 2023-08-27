---
layout: single
title: 우분투 RAID
date: 2022-07-16 12:32
category : ubuntu
---

# RAID

RAID는 여러 디스크 드라이브를 논리 장치로 결합하는 `스토리지 가상화` 기술입니다.

데이터를 나눠 저장하거나, 에러 검증 코드를 사용하는 등의 솔루션으로 저장된 데이터의 안정성과 신뢰도를 보장합니다.

RAID의 풀네임은 복수 배열 독립 디스크(Redundant Array of Independent Disks) 혹은 복수 배열 확장 디스크 (Redundant Array of Inexpensive Disks) 입니다.

# 관련 기술

RAID에서 사용하는 기법에 대해 설명합니다.

RAID가 적용된 디스크는 데이터를 여러 디스크에 분산 저장, 중복 저장해서 데이터를 안전하게 보존하지만 필요한 디스크가 많아집니다.

## 스트라이핑

데이터를 여러 디스크에 분산 저장하는 기술입니다.

[![스트라이핑 예시](/assets/image/RAID-Data_striping_example.svg.png){: .full}](https://en.wikipedia.org/wiki/Data_striping)

데이터 A, B를 디스크 D1, D2, D3에 분산 저장하는 예시입니다.

장점

- 여러 디스크에 동시에 I/O 가능. 시간단 처리량 증가

단점

- 여러 디스크에 I/O 하면서 오류 확률 증가
- 데이터 시퀀스 중 하나라도 손상되면 무결성에 문제가 생김.

## 미러링

데이터를 다른 하드 디스크에 실시간으로 복제하는 기술입니다.

[![미러링 예시](/assets/image/RAID-Data_striping_example.svg.png){: .full}](https://en.wikipedia.org/wiki/Disk_mirroring)

디스크 A의 데이터를 C에, B의 데이터를 D에 미러링하는 예시입니다.

미러링된 다른 디스크에서 데이터를 가져오는 것을 `rebuilding`, `less commonly`, `resilvering` 등으로 부릅니다.

장점

- 데이터 오류 발생시 미러링된 디스크로 복구할 수 있음.
- 같은 데이터가 여러 디스크에 저장되므로 읽기 작업시 가장 빠른 디스크로 선택해서 읽을 수 있음.

# RAID 종류

[Wikipedia RAID 글 참고](https://en.wikipedia.org/wiki/Standard_RAID_levels#RAID_0)


<!-- 

| 종류   | 내용                                                                                                                   |
| ------ | ---------------------------------------------------------------------------------------------------------------------- |
| RAID 0 | 스트라이프 기법을 적용해 데이터 저장시 여러 장치에 분산 저장합니다.                                                    |
| RAID 1 | 미러링 기법을 적용해 하나의 데이터를 다른 저장 장치에 중복 저장합니다.                                                 |
| RAID 2 | ECC. 에러가 발생하면 에러비트로 검출할 수 있습니다. 에러 비트를 검사해 해당 디스크에 문제가 있는지 확인할 수 있습니다. |
| RAID 3 | 에러 비트만 따로 저장하는 디스크가 추가됩니다. 최소 3개 디스크가 필요합니다.                                           |
| RAID 4 | RAID 3에서 저장하던 에러비트를 블록 단위로 저장합니다.                                                                 |
| RAID 5 | 에러 비트를 한 디스크에 몰아 저장하는 RAID 3, RAID 4와 달리 나머지 디스크에 분산 저장합니다                            |
| RAID 6 | 에러 비트를 다른 두개의 디스크에 분산 저장하는 방식입니다. 고장 대비 능력이 매우 높습니다.                             |

-->

# Reference

<!-- https://access.redhat.com/documentation/ko-kr/red_hat_enterprise_linux/6/html/installation_guide/create_software_raid-x86 -->

[Wikipedia, RAID](https://ko.wikipedia.org/wiki/%EB%94%94%EC%8A%A4%ED%81%AC_%EC%84%B9%ED%84%B0)
[Wikipedia, Standard RAID levels](https://en.wikipedia.org/wiki/Standard_RAID_levels#RAID_0)
[Wikipedia, Data striping](https://en.wikipedia.org/wiki/Data_striping)