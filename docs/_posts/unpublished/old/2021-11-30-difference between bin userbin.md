---
layout: single
title: bin과 /usr/bin의 차이
date: 2021-11-30 23:35
category: [TIL, linux]
author: bure5kzam
tags: [linux]
summary:
---
[Understanding the bin, sbin, usr/bin , usr/sbin split](http://lists.busybox.net/pipermail/busybox/2010-December/074114.html)


[StackOverflow](https://unix.stackexchange.com/questions/5915/difference-between-bin-and-usr-bin/6140)

/sbin - 부팅에 필요한 바이너리들, low-level system 복구와 유지 (run level 1 or S)

/bin - 어떤 런 레벨에서나 동작할 수 있는 일반/스탠다드 시스템에서 사용되는 바이너리

/usr/bin - Application/distribution binaries meant to be accessed by locally logged in users
지역적으로 로그인된 유저들이 접근할 수 있는 Application/distribution binarys

/usr/sbin - Application/distribution binaries that support or configure stuff in /sbin.
/sbin의 항목을 지원하거나 구성하는 애플리케이션/배포 바이너리.

/usr/share/bin - Application/distribution binaries or scripts meant to be accesed via the web, i.e. Apache web applications
웹을 통해 액세스할 수 있는 애플리케이션/배포 바이너리 및 스크립트(예: Apache 웹 애플리케이션)

*local* - Binaries not part of a distribution; locally compiled or manually installed. There's usually never a /local/bin but always a /usr/local/bin and /usr/local/share/bin.
배포판의 일부가 아닌 바이너리 로컬로 컴파일되거나 수동으로 설치됩니다. 일반적으로 /local/bin은 없지만 /usr/local/bin 나 /usr/local/share/bin이 있습니다.
