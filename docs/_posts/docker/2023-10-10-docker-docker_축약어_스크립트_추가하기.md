---
layout: single
title: 도커 관련 명령어 축약어를 스크립트에 추가하기
date: 2023-10-10 07:02
category: docker
layout: single
author: Bure5kzam
tags: []
---

{% include callout-writing.html %}

우분투 쉘에서 도커를 축약어로 사용하기 위한 작업입니다.

``` bash
dockerbash() {
        if [ -z "$1" ]; then
                echo "Usage: dockerbash <container_name>"
        else
                docker exec -it "$1" /bin/bash
        fi
}
```


