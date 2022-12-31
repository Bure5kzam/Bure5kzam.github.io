---
layout: single
title: 리눅스 도메인 서버 구축하기
---

# Overview

회사 IP 주소가 너무 헷갈려서 DNS서버 하나 두려고 만들었습니다. 

리눅스에서 named를 구성하고 nslookup으로 동작을 테스트합니다.

# named.conf 구성하기

```console
# 호스트 정의하기
acl cloud {

}

acl pcs_server {

}

options {
    directory "/var/named";
    statics-file "/var/named/data/named_statics";
    memstatics-file "/var/named/data/named_memstatics";

    forward {only};
}

zone "." IN {
    file name.ca;
    type hint;
}

zone "bure5kzam.com" {
    file bure5kzam.zone;
    type slave;
    master 127.0.0.1;
}
```

# Zonefile 구성하기

```console
$TTL 1D

@ SOA IN bure5kzam.com. (
                    0; serial
                    1D; refresh
                    1H; retry
                    1W; expire
                    3H; minimum
)

    IN  A   127.0.0.1
```

# 테스트 : nslookup

```console
# nslookup이 없으면 bind-utils 설치

yum -y install bind-utils
```