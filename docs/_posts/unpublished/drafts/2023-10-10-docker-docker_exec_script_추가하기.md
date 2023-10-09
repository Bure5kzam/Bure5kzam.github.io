---
layout: post
title: 도커 exec 스크립트 추가하기
date: 2023-10-10 07:02
category: docker
author: Bure5kzam
tags: []
summary:
---

``` bash
dockerbash() {
        if [ -z "$1" ]; then
                echo "Usage: dockerbash <container_name>"
        else
                docker exec -it "$1" /bin/bash
        fi
}
```
