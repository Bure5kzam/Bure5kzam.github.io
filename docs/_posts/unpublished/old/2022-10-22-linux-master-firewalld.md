---
layout: single
title: 우분투 마스터 firewalld (작성중)
---

# Concept

firewalld는 신뢰할 수 있는 연결 인터페이스를 정의하기 위해 network/firewall zones 에 대해 동적으로 관리되는 방화벽을 제공합니다.

- IPv4, IPv6 방화벽 설정과 인터넷 브릿지를 지원합니다.
- 런타임 설정과 영구 설정 옵션을 구분해서 제공합니다.
- 서비스나 어플리케이션을 방화벽 룰에 직접 추가할 수 있는 인터페이스를 제공합니다.

> man firewalld [DESCRIPTION]
> firewalld provides a dynamically managed firewall with support for network/firewall zones to define the trust level of network connections or interfaces. It has support for IPv4, IPv6 firewall settings and for ethernet bridges and has a separation of runtime and permanent configuration options. It also supports an interface for services or applications to add firewall rules directly.

## firewalld를 설정하는 방법

`firewalld` 로 방화벽을 설정하려면 2 가지 방식이 있습니다.

1. 쉘에서 `firewall-cmd`로 설정
2. 어플리케이션에서 D-Bus 인터페이스로 `firewalld`에 설정 요청

> $ man firewalld [CONCEPT]
> firewalld has a D-Bus interface for firewall configuration of services and applications. It also has a command line client for the user. Services or applications already using D-Bus can request changes to the firewall with the D-Bus interface directly. For more information on the firewalld D-Bus interface, please have a look at firewalld.dbus(5).


## firewalld 설정의 종류

`firewalld` 설정은 `permanent configuration`설정과 `runtime` 설정이 있습니다 .

`runtime` 으로 적용한 설정은 데몬이 종료되면 사라지기 때문에, 매번 적용해야 하는 설정은 `permanent` 으로 적용합니다. 이 경우 XML 파일로 설정값이 저장됩니다.(기본 경로는 `/usr/lib/firewalld`)

<!-- `firewalld`는 사전 정의된 서비스와 ICMP 타입, 런타임과 영구 설정 옵션을 구분할 수 있는 `Zone supporting`을 제공합니다. 영구 설정은 XML 파일 `/usr/lib/firewalld` (--default-config) 이나 `/etc/firwalld`(--system-config) 에서 로딩됩니다. -->

<!-- `NetworkManager`가 사용되지 않고 이미 네트워크가 이미 가동된 후 `firewalld`가 시작될 때, 연결과 수동으로 생성된 인터페이스는 ifcfg 파일에 명시된 zone 파일에 연동되지 않습니다. 이 경우 인터페이스는 default zone에 의해 자동으로 조작됩니다. firewalld는 네트워크 디바이스의 이름 변경 알림을 받지 않습니다. 이는 `NM_CONTROLLED=no` 가 설정됐을 때 NetworkManager에 의해 조작되지 않는 인터페이스들에게도 적용됩니다.

`$ firewall-cmd [--permanet] --zone=zone --add-interface=interface` 명령어로 이 인터페이스들을 존에 추가할 수 있습니다. 만약 `/etc/sysconfig/network-scripts/ifcfg-interface` 파일이 있다면 firewalld는 ZONE-zone 세팅을 이 파일로 변경하려 할 것입니다. -->

## firewalld 구성 요소


### Zones

`Zone`은 인터페이스나 소스 주소 바인딩, 연결에 대한 신뢰 레벨을 정의합니다. (이는 방화벽 서버에 접근하는 호스트, 인터페이스, 연결을 존으로 지정해 관리할 수 있음을 의미합니다.)

> https://firewalld.org/documentation/zone/ <br/><br/>
> A firewall zone defines the trust level for a connection, interface or source address binding. This is a one to many relation, which means that a connection, interface or source can only be part of one zone, but a zone can be used for many network connections, interfaces and sources.

 firewalld에는 사전 정의된 존들이 있습니다. Zone 설정 옵션들과 일반적인 정보는 `firewalld.zone(5)`를 참고합니다.


### Services

서비스 설정 파일은 방화벽에게 서비스로 진입하기 위한 조건 정보를 제공합니다.
서비스의 포트 목록, 프로토콜, destination 등이 묘사됩니다.

```console
# $man firwalld.service, [DESCRIPTION]

<?xml version="1.0" encoding="utf-8"?>
<service>
    <short>My Service</short>
    <description>description</description>
    <port port="137" protocol="tcp"/>
    <protocol value="igmp"/>
    <module name="nf_conntrack_netbios_ns"/>
    <destination ipv4="224.0.0.251" ipv6="ff02::fb"/>
</service>
```

또한 서비스 활성화시 자동으로 로드되는 `firewall helper module`들도 서비스가 될 수 있습니다.

서비스 설정 옵션, 서비스에 관한 일반적인 정보는 `firewalld.service(5)`를 참고합니다. 사전 정의된 서비스를 이용하면 서비스 엑세스를 활성화/비활성화 하기 쉬워집니다.

> https://firewalld.org/documentation/zone/ <br/><br/>
> A service can be a list of local ports, protocols and destinations and additionally also a list of firewall helper modules automatically loaded if a service is enabled. Service configuration options and generic information about services are described in firewalld.service(5). The use of predefined services makes it easier for the user to enable and disable access to a service.

## firewall-cmd로 설정하기

인터페이스를 zone에 추가하는 명령어입니다.

```console
firewall-cmd [--permanent] --zone=zone --add-interface=interface-
```

<!-- firewall-cmd

연속 옵션에 대해, 여러번 지정될 수 있는 옵션들입니다. 

종료 코드 중 일부는 성공으로 취급됩니다.

- 0 : 최소 한번 이상 성공하면
- 11 : ALREADY_ENABLED (이미 가능한)
- 12 : NOT_ENABLED (사용 불가능한)
- 16 : ZONE_ALREADY_SET (이미 적용된 존)

항목들을 파싱하다 이슈가 발견되면, 이는 경고로 처리되며 하나 이상 성공하면 결과를 바꾸지 않습니다. 하지만 아무것도 성공한 항목이 없으면 종료 코드는 에러 코드에 따라 결정됩니다.

- 에러 코드가 하나면 적용됩니다.
- 에러 코드가 여러개면 UNKNOWN_ERROR(254) 적용 -->


<!-- 옵션들

General Options
Status Options
Log Denied Options
Automatic Helpers Options
Permanent Options
Zone Options
Options to Adapt and Query Zones
Options to Handle Bindings of Interfaces
Options to Handle Bindings of Sources
IPSet Options
Service Options
Helper Options
Direct Options
Lockdown Options
Lockdown Whitelist Options
Panic Options -->