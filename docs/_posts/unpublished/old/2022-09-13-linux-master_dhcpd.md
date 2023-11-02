---
layout: single
title: 우분투 dhcpd
---

# Overview

`dhcpd`는 할당 가능한 IP 주소를 반환하는 데몬 서버입니다. 할당 받은 IP 주소가 없을 때 dhcpd 서버에 요청 적절한 서브넷 주소와 ip 주소를 반환합니다.

## 네트워크 주소와 클래스

...

## 서브넷

> [CDIR, Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 관점에서 IPv4 주소를 해석하는 글입니다.

IPv4 주소는 4개의 2진수 8자리로 표현합니다,

```console

# 2진수 표현
00000000.00000000.00000000.00000000(2) ~ 11111111.11111111.11111111.11111111(2)

# 10진수 표현
255.255.255.255(10) ~ 255.255.255.255(10)
```

2진수 32개 숫자로 호스트를 나타내려면 2^32 개의 호스트를 구분할 수 있습니다. 하지만 IP 주소로 호스트뿐만 아니라 서브네트워크도 나타내기 때문에 실제로 표현할 수 있는 호스트 수는 더 많습니다.

서브네트워크를 가리키는 IP 주소는 두 부분으로 구성됩니다.

- `network prefix`
  - 네트워크의 서브넷 주소.
- `host identifier`
  - 서브넷 네의 호스트

IP 주소에서 `network prefix`와 `host identifier`를 구분하기 위해 `subnet mask`를 사용합니다.


만약 주소가 192.168.2.17 이고 subnet mask가 255.255.255.224 일 때 둘을 추출하는 방법은 다음과 같습니다.

<!-- ```console

11000000.10101000.00000010.00000000 # IP주소
11111111.11111111.11111111.00000000 # C클래스 넷마스크
11111111.11111111.11111111.11100000 # 서브넷 마스크

00000000.00000000.00000000.11100000 # 서브넷 마스크를 C클래스와 XOR 하면 서브넷 개수는 2의 3승
``` -->

```console

# Ipv4 address
11000000.10101000.00000010.00010001(2)    
  
# Subnetmask
11111111.11111111.11111111.11100000(2)      # 2^5 개의 호스트를 가지는 subnetwork의 subnet mask

# 추출된 network prefix
11000000.10101000.00000010.00000000(2)      # Ipv4 address와 subnet mask를 & 연산

# 추출된 host identifier
00000000.00000000.00000000.00010001(2)      # Ipv4 address와 subnet mask를 &~ 연산
```

192.168.2.17 서브네트워크에는 32개의 호스트를 할당할 수 있습니다.

 

<!-- ## 서부넷의 구성

서브넷은 `서브넷 주소`와 `서브넷 마스크`로 구성됩니다. -->

<!-- IP 주소에는 네트워크 주소와, 네트워크에 속하는 호스트의 주소 정보가 담겨있습니다.  -->
<!-- 호스트가 `서브넷에 포함되는지` 확인하려면 `서브넷 안에서의 주소`를 알아내는데 사용됩니다. `서브넷 주소`는 서브넷 안의 주소들이 갖는 공통 주소입니다. -->


# dhcpd

`dhcpd`는 호스트에게 ip를 할당하기 위하는 데몬입니다 .

호스트가 `dhcpd` 에 IP를 요청하면, `dhcpd`는 남는 호스트 주소를 찾아 반환합니다.
호스트가 TCP/IP 주소를 반환받은 IP 주수로 설정하면 외부에서 그 주소로 호스트에 엑세스할 수 있게됩니다.


`man dhcp`에 기록된 기능은 다음과 같습니다.

- TCP/IP 네트워크를 사용하는 호스트들에게 IP 주소를 제공
- 호스트에 연결된 네트워크에 대한 정보도 검색할 수 있습니다.

> $man dhcpd, [DESCRIPTION]
> DHCP allows hosts  on  a  TCP/IP network  to  request  and be assigned IP addresses, and also to discover information about the network to which they are  attached.

<!-- - Dynamic Host Configuration Protocol (DHCP), 
- Internet Bootstrap Protocol (BOOTP), 유사한 -->

`dhcp` 서버와 관련된 파일은 아래와 같습니다. 파일 내 구문은

```console
/etc/dhcp/dhcp.conf         # 서브넷에서 할당되지 않은 주소들을 보관합니다.
/etc/dhcp/dhcp.lease        # 호스트들에게 할당했던 주소의 수명을 관리합니다.
```

`dhpcd`가 사용하는 프로토콜은 `dhcp`, `BOOTP`이 있습니다.


시험에서 참고할 수 있는 메뉴얼은 다음과 같습니다.

```console
man dhcpd
man dhcpd.conf
man dhcpd.lease
```

# dhcpd.lease

dhcpd에서 할당해주는 주소는 임대기한(lease)이 있습니다. lease는 설정된 시간이 지나면 만기되지만 요청할 때마다 새롭게 갱신됩니다. 리스가 만료되면 리스가 할당된 클라이언트는 더이상 리스된 ip 주소를 사용할 수 없습니다.

혹시 시스템이나 서버가 재시작되어도 리스가 유지되도록, dhcpd는 `dhcpd.leases` 파일에 관련 정보를 보관합니다. 폴더가 커지면 파일을 새로 생성하고 기존 파일은 이름을 `dhcpd.lease~` 로 변경합니다.

<!-- dhcpd는 모든 서브넷들의 서브넷 넘버와 netmasks를 알아야합니다. 게다가 동적으로 주소를 할당하기 위해 서브넷에는 하나 이상의 주소 범위를 할당해야합니다. -->

dhcp에서 리스가 할당되는 시간은 0에서 무한대까지 할 수 있습니다. 호스트의 종류에 따라 적절한 리스 기간을 설정하면됩니다.

# dhcpd.conf

`dhcpd` 데몬의 설정 파일입니다. 수정되면 다시 시작해야 적용됩니다.

<!-- - 대소문자 구분 없음
- `#`로 주석 표시 -->

아래는 해당 파일의 예시입니다

```console
option domain-name "isc.org";                           # global parameter

subnet 204.254.239.0 netmask 255.255.255.224 {
  option routers 204.254.239.1;                         # subnet-specific parameters
  range 204.254.239.10 204.254.239.30;
}

subnet 204.254.239.32 netmask 255.255.255.224 {
  subnet-specific parameters...
  range 204.254.239.42 204.254.239.62;
}

group {
  group-specific parameters...
  host zappo.test.isc.org {
    host-specific parameters...
  }
  host beppo.test.isc.org {
    host-specific parameters...
  }
}
```

`dhcpd.conf` 파일은 `statements`(구문)의 집합으로 구성됩니다.
`statements`은 `parameter` 구문와 `declaration`구문으로 구분됩니다.

`parameter` 구문은 서버 전체의 인자를 지정할 때 쓰이기도 하고 `declaration` 의 인자를 지정하기 위해 내부에서 사용되기도 합니다.

`declaration` 구문은 주로 서브넷이나 네트워크를 묘사하는데 사용됩니다.

## Parameter statement

파라미터는 접두사 `option`가 있는 파라미터와 없는 파라미터가 있습니다.

 <!-- `global parameter` 는 서버 전역에 영향을 미치는 파라미터를 지정하고, `parameter`는 `declaration`에 관련된 파라미터를 지정합니다. -->
`parameter` 중 `option` 접두사를 가지는 파라미터는 실제 DHCP 옵션에 대응되는 파라미터들입니다.

그렇지 않은 파라미터들은 dhcp 서버의 행동을 제어하거나(lease 관련 파라미터로 주소의 수명을 결정), DHCP에서 필수적인 파라미터들을 지정하는 (클라이언트에게 필요한 server-name, filename 정의)역할을 합니다.

> $ man dhcpd.conf, [EXAMPLE] <br/>
> Parameters starting with the option keyword correspond to actual DHCP options, while parameters that do not start with the option keyword either control the behavior of the DHCP server (e.g., how long a lease dhcpd will give out), or specify client parameters that are not optional in the DHCP protocol (for example, server-name and filename).


```console
option domain-name "isc.org";
```

모든 파라미터는 선언에서 사용되기 전에 명시되어야 합니다.


## Declaration(선언) statement

`declaration`의 역할은 아래와 같습니다.

1. 네트워크의 위상을 묘사 (shared-network, subnet)
2. 네트워크 상의 클라이언트들을 묘사 
3. 할당 가능한 주소들을 제공
4. `parameter` 그룹을 `declaration` 그룹에 적용

> $ man dhcpd.conf, [Description] <br/>
> Declarations are used to describe the topology of the network, to describe clients on the network, to provide addresses that can be assigned to clients, or to apply a group of parameters to a group of declarations. In any group of parameters and declarations, all parameters must be specified before any declarations which depend on those parameters may be specified.

네트워크 위상 관련 선언에는 `shared-network`와 `subnet`가 있습니다.

 서브넷에 해당하는 클라이언트가 주소를 할당받아야 할 경우, `subnet_`선언 안에 `range` 선언이 있어야합니다. 
 
 정적 주소가 필요한 클라이언트의 경우 `host` 선언이 있어야 합니다. 서브넷으로 구분되지 않는 호스트들에게 파라미터를 적용하려면 `group` 선언을 사용합니다.

> $ man dhcpd.conf, [Description] <br/>
>Declarations about network topology include the shared-network and the subnet declarations. If clients on a subnet are to be assigned addresses dynamically, a range declaration must appear within the subnet declaration. For clients with statically assigned addresses, or for installations where only known clients will be served, each such client must have a host declaration. If parameters are to be applied to a group of declarations which are not related strictly on a per-subnet basis, the group declaration can be used.

제공될 모든 서브넷과 dhcp 서버와 연결된 서브넷들에 대해, `dhpcd`에게 주소가 서브넷에 포함되는지 인지하는 방법을 알려줄 하나의 `subnet` 선언이 필요합니다. 서브넷에 할당된 주소가 없어도 선언은 필요합니다.

> $ man dhcpd.conf, [Description] <br/>
> For every subnet which will be served, and for every subnet to which the dhcp server is connected, there must be one subnet declaration, which tells dhcpd how to recognize that an address is on that subnet. A subnet declaration is required for each subnet even if no addresses will be dynamically allocated on that subnet.


호스트 개수가 서브넷 마스크의 호스트 표현 범위를 넘어서는 경우, 같은 비트 서브넷이 여러개 필요할 수 있습니다. 그럴 때는 `shared-network` 선언으로 `subnet` 선언을 감싸 공유네트워크를 구성해야합니다.

> $ man dhcpd.conf, [Description] <br/>
> Some installations have physical networks on which more than one IP subnet operates. For example, if there is a site-wide requirement that 8-bit subnet masks be used, but a department with a single physical ethernet network expands to the point where it has more than 254 nodes, it may be necessary to run two 8-bit subnets on the same ethernet until such time as a new physical network can be added. In this case, the subnet declarations for these two networks must be enclosed in a shared-network declaration.

<!-- `shared-network`가 없는 것처럼 보이더라도, 서버가 서브넷들을 포함하는 빈 공유 선언을 생성합니다. 이는 주소로 맺어지지 않는 stateless DHCP 클라이언트  -->

클라이언트가 부팅되면 아래 순서대로 선언을 참조해 IP 주소를 결정합니다.

클라이언트의 `host` 선언 => 클라이언트가 매칭되는 `class` 선언 => `pool` => `subnet` => `shared-network`


```console
# example from https://support.microfocus.com/kb/doc.php?id=7004091

shared-network "MySharedNetwork" {
    subnet 10.0.3.0 netmask 255.255.255.0 {
        default-lease-time 259200;
        max-lease-time 259200;
        pool {
        range 10.0.3.1 10.0.3.251;
        }
    }
    subnet 10.0.4.0 netmask 255.255.255.0 {
        default-lease-time 259200;
        max-lease-time 259200;
        pool {
        range 10.0.4.1 10.0.4.251;
        }
    }
}
```
<!-- 

~~dhcpd는 주소가 없는 클라이언트에게 주소를 할당하기 위해 존재하는 서버입니다.~~
dhcpd.conf에는 서브넷의 개수 만큼의 subnet 선언이 필요합니다.



<!-- #### host

host 선언은 병시된 클라이언트의 설정 정보를 제공할 범위를 제공합니다. 또한 클라이언트에게 fixed-address를 할당할 방법도 제공합니다.

클라이언트가 여러 서브넷에서 고정주소를 가져야 할 수도 있습니다.

- 여러 주소를 fixed-address 선언으로 지정할 수 있습니다.
- 클라이언트가 여러 host 구문에 매칭될 수 있습니다. -->

## example

아래는 dhcpd.conf 맨 페이지에서 제공하는 `dhcpd.conf`의 예시입니다. (figure1)

```console
# global parameters...

subnet 204.254.239.0 netmask 255.255.255.224 {
  # subnet-specific parameters...
  range 204.254.239.10 204.254.239.30;
}

subnet 204.254.239.32 netmask 255.255.255.224 {
  # subnet-specific parameters...
  range 204.254.239.42 204.254.239.62;
}

subnet 204.254.239.64 netmask 255.255.255.224 {
  subnet-specific parameters...
  range 204.254.239.74 204.254.239.94;
}

group {
  # group-specific parameters...
  host zappo.test.isc.org {
    host-specific parameters...
  }
  host beppo.test.isc.org {
    host-specific parameters...
  }
  host harpo.test.isc.org {
    host-specific parameters...
  }
}
```

### Statement 

**parameter statement(global parameter)**

파일 시작 부분에는 전역에 적용되는 파라미터가 있습니다. 조직의 도메인 이름, 네임 서버의 주소 같은 것들이 가능합니다.

```console
# example of global parameter

option domain-name "isc.org";
```

> $ man dhcpd.conf, [Example]
> Notice that at the beginning of the file, there's a place for global parameters. These might be things like the organization's domain name, the addresses of the name servers (if they are common to the entire organization), and so on. So, for example:


**group statement**

`subnet` 선언으로 서브넷 별 파라미터를 지정할 수 있지만, 서로 다른 서브넷에 포함된 클라이언트들에 파라미터를 적용할 때는 `group` 선언을 사용합니다.

Figure1 예시에서는 3개의 호스트 (zappo, beppo, harpo)의 파라미터를 지정합니다.

```console
group {
  # group-specific parameters...
  host zappo.test.isc.org {
    host-specific parameters...
  }

  host beppo.test.isc.org {
    host-specific parameters...
  }
  host harpo.test.isc.org {
    host-specific parameters...
  }
}
```

도메인 주소가 모두 `test.isc.org`에 속하므로 `group-specific parameter`를 사용해 호스트에 적용되는 도메인 네임의 파라미터를 오버라이드 하는것이 적합합니다.

`group-specific parameter`의 예시로 다음을 추가할 수 있습니다.

```console
option domain-name "test.isc.org";
max-lease-time 120;
default-lease-time 120;
```

**host statement**

`host-specific parameter`로는 `hostname`이나 `filename`(업로드할 파일), `next-server`(파일을 업로드할 주소)가 될 수 있습니다.

```console
...
    host zappo.test.isc.org {
        host-specific parameters...
    }

    host beppo.test.isc.org {
        host-specific parameters...
    }
    host harpo.test.isc.org {
        host-specific parameters...
    }
...
```

### Parameter

**subnet-specific parameter**

서브넷이 가리키는 인터페이스 주소를 의미합니다. `declaration` 내부에서 사용합니다.

```console
subnet 204.254.239.0 netmask 255.255.255.224 {
    # subnet-specific parameters

    option routers 204.254.239.1;                         
    range 204.254.239.10 204.254.239.30;
}
```

`host-specific` 파라미터는  -->


<!-- #### shared-network

장치 하나가 두개 이상의 subnet이 필요한 경우도 있습니다.  예를들어 꼭 8비트 서브넷 마스크를 사용해야 하지만 연결된 호스트 개수가 표현 가능한 최대수(254) 를 넘길 수 있습니다. 이럴 경우 주소를 할당하려면 같은 이더넷에서 동작하는 8비트 서브넷이 두개 필요합니다. 두 네트워크를 위한 _subnet_ 선언은 _shared-network_ 선언으로 감싸져 있어야 합니다.

#### group

어떤 사이트들은 하나 이상의 클라이언트를 갖는 departments를 가질 수 있습니다. 하지만 동일한 파라미터를 클라이언트에게 제공하는 것이 바람직할 수 있습니다.  host 선언으로 고정 주소를 갖는 클라이언트에게, 이 선언들은 department에게 일반적인 파라미터를 따라 group 선언으로 감싸질 수 있습니다. 동적 주소를 할당받는 클라이언트들은, class 선언들과 conditional 선언들이 클라이언트가 보내는 정보들을 기반으로 할당하는 파라미터들을 그룹화 하기 위해 사용될 수 있습니다. -->


<!-- dhcpd가 클라이언트의 host 선언을 찾을 때는 fixed-address 선언으로 고정된 주소를 먼저 찾습니다.
-->

### Declaration

**pool declaration**

`pool 선언`은 주소 풀을 지정할 때 사용합니다. 같은 네트워크 세그먼트나 서브넷이라도 다른 주소 pool을 제공할 수 있습니다.

예를들어 DHCP 서버에 등록된 클라이언트들에게는 더 넓은 범위의 주소를, 알려지지 않은 클라이언트들에게는 작은 범위의 주소를 제공할 수 있습니다.

> $man dhcpd.conf [Address Pools]
>For example, you may want to provide a large set of addresses that can be assigned to DHCP clients that are registered to your DHCP server, while providing a smaller set of addresses, possibly with short lease times, that are available for unknown clients.

만약 방화벽이 있다면, 풀에 있는 주소들은 인터넷 접근을 허용하면서 다른 풀에 있는 주소들은 DHCP clients로 등록하도록 준비할 수 있습니다.

> $man dhcpd.conf [Address Pools]
> If you have a firewall, you may be able to arrange for addresses from one pool to be allowed access to the Internet, while addresses in another pool are not, thus encouraging users to register their DHCP clients. 

```console
subnet 10.0.0.0 netmask 255.255.255.0 {
    option routers 10.0.0.254;

    # Unknown clients get this pool.
    pool {                                                    
        option domain-name-servers bogus.example.com;

        max-lease-time 300;

        range 10.0.0.200 10.0.0.253;

        # 이 풀의 주소는 unknown-clients list에 있어야 할당받을 수 있습니다.                            
        allow unknown-clients;      
    }

  # Known clients get this pool.
    pool {
        option domain-name-servers ns1.example.com, ns2.example.com;

        max-lease-time 28800;

        range 10.0.0.5 10.0.0.199;

        deny unknown-clients;   # 이 풀의 주소는 unknown-clients list의 호스트는 할당받을 수 없습니다.
    }
}
```

클라이언트의 등록 여부에 따라 완전히 다른 서브넷을 설정할 수도 있습니다. 주소 풀은 공유 네트워크 레벨에 존재하므로 pool 선언 안의 주소범위는 다른 서브넷이 될 수 있습니다.

풀은 허용되는 리스트를 가질 수 있습니다. 허용 리스트 안의 엔트리는 allow와 deny 키워드로 수행됩니다.

## Dynamic Address Allocation

주소 할당은 클라이언트가 INIT 상태고 DHCPDISCOVER 메세지를 보냈을 때만 수행됩니다.

만약 클라이언트가 lease가 다되어 갱신을 위해 DHCPREQUEST를 보내면, 서버는 세 가지 선택지가 있습니다.

- DHCPREQUEST를 무시
- DHCPNACK를 전송, 주소를 사용하지 못할것임을 알림
- DHCPACK를 전송, 주소 연장

클라이언트가 요청하는 주소를 서버가 찾고 그 주소가 할당 가능하면 DHCPACK를 보냅니다.
주소가 더이상 불가능하거나 클라이언트가 허용되지 않으면 DHCPNAK를 보냅니다.

`host` 선언이 클라이언트에 매칭될 때 클라이언트가 연결된 네트워크 세그먼트에 대해 유효한 IP 주소를 나열하는 `fixed-address` 선언을 포함하고 있으면 동적 할당을 수행하지 않습니다.



#### shared-network 구문

이 구문은 서브넷이 같은 물리 네트워크를 공유한다는 의미입니다. host 구문이나 subnet 구문이 있으면 파라미터가 오버라이드 됩니다. 만약 공유 네트워크에 잇는 서브넷이 동적으로 할당될 수 있는 주소를 가지고 있으면 그 주소들은 common pool에 수집됩니다.

```console
shared-network [ name ] {
    [ parameters ]
    [ declarations ]
}
```

name은 공유네트워크의 이름으로 설정되는 것이 좋습니다.

#### subnet 구문

```console 
subnet [subnet-number] netmask [netmask] {
    [ parameters ]
    [ declarations ]
}

```

서브넷에 해당하는 주소들에게

- subnet-specific 파라미터를 적용하기 위해
- 할당할 수 있는 주소 범위를 설정하기 위해 (range)
  
subnet-number는 도메인과 숫자 주소 가능합니다. 

#### host 구문

- fixed-address를 할당위해

클라이언트가 여러 서브넷에서 고정주소를 가져야 할 수도 있습니다.

- 여러 주소를 fixed-address 선언으로 지정할 수 있습니다.
- 클라이언트가 여러 host 구문에 매칭될 수 있습니다.

# Reference

[DHCP, Redhat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/9/html/managing_networking_infrastructure_services/providing-dhcp-services_networking-infrastructure-services)

<!-- [linuxlab, 배철수](http://www.linuxlab.co.kr/docs/00-07-7.htm) -->