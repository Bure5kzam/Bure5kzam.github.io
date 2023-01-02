---
layout: single
title: linux 어플리케이션 설치하기
date: 2021-12-01 12:04
category: [TIL, linux]
author: bure5kzam
tags: [linux]
summary: 
---

# binary 파일 설치

## 파일 받기
wget을 이용
> GNU Wget is a free utility for non-interactive download of files from
       the Web.  It supports HTTP, HTTPS, and FTP protocols, as well as
       retrieval through HTTP proxies.

## 폴더 생성
라이브러리나 커널 괄련 파일은 `/usr/local/lib/directory` 
cd bin

##  실행할 수 있게 심볼릭 걸기
sudo ln -s $PWD/node /usr/bin/node

# 소스코드 받기
소스코드는 다운받은 후 직접 빌드해서 사용해야합니다.
python, gcc 등을 이용하지만 설치 방법이 매번 다를 수 있으며 폴더에서 관련 설명을 읽어야합니다.

# dpkg 설치
dpkg는 데비안 패키지 관리 시스템입니다. apt와 유사하지만 필요한 모듈을 모두 같이 설치해주는 apt와 달리 의존성 관계를 핸들링하지 않습니다.

| 커맨드                | 내용                    |
| --------------------- | ----------------------- |
| dpkg -i [destination] | 해당 경로에 install     |
| dpkg -P [target]      | 해당 패키지 Purge       |
| dpkg -l               | 설치된 리스트 목록 보기 |