---
title: 태초의 프로세스
category : ubuntu
author: Bure5kzam
tags: [linux, process]
summary: 우분투가 처음 실행 되었을 때 프로세스가 어떻게 생성되는지
---

리눅스 내 프로세스는 하나를 제외하고 모두 부모 프로세스를 가지며, 예외인 하나를 태초의 프로세스 (init process, init)라고 합니다.

이 태초의 프로세스의 PID는 1이며, 유저 공간의 서비스들을 관리하고 가져오는 초기화 시스템으로 동작합니다.

init이 수행하는 역할은 다음과 같습니다.

- 시스템 초기화를 일부 담당
- 시스템 콘솔을 염
- 루트 파일 시스템을 마운트

`우분투 20.04` 

---
systemd는 리눅스 동작 시스템의 시스템, 서비스 매니저입니다. 부팅 시 systemd가 첫 번째 프로세스로 실행되어 PID가 1이 되면, systemd는 `init system`으로 동작하며 `userspace services` 들을 가져와 유지합니다.

> SysV와의 호환을 위해 만약 systemd가 init으로 호출되었는데 PID가 1이 아니면 telinit을 실행시키고 모든 커맨드 라인 아규먼트들을 수정하지 않고 전달합니다.
> 
> <br/> 이는 init과 telinit이 일반 로그인 세션에서 호출되었을 때 거의 유사하다는 의미입니다. 

systemd가 시스템 인스턴스로 실행되면, systemd는 설정파일 `system.conf`와 `system.conf.d` 디렉토리 안에 있는 파일들을 해석합니다.

## Concept

### Unit

systemd는 `unit`이라고 불리는 12가지 타입의 엔티티들 사이에서 의존 시스템을 제공합니다.

`unit`은 시스템 부팅 및 유지와 관련된 오브젝트들을 캡슐화합니다.

 대부분의 유닛들은 `unit configuration files`에 의해 설정됩니다. (설정 파일의 문법과 옵션 기본 값은 `systemd.unit(5)` 참고)
 
하지만 일부 유닛은 동적으로 시스템 상태나 런타임에 프로그래밍 방식으로, 또는 자동으로 다른 설정에서 생성됩니다.


**Unit의 상태**

- active (또는 started, bound, plugged in 로도 표현)
- inactive (stopped, unbound, unplugged 로도 표현)
- activating
- deactivating
- failed : inactive와 유사합니다. 
  - 프로세스가 종료되었을 때 에러 코드를 반환하거나
  - 메모리 충돌(크래쉬)가 일어나거나
  - 동작 시간을 초과하면 fail 상태로 진입합니다.
  - 이 상태의 경우 실패 원인이 로그에 남습니다.
- 이외에도 상기 5개 상태에 관련된 여러 추가 상태가 있을 수 있습니다.

**unit의 종류**

1. Service units : 데몬과 데몬에 속하는 프로세스들을 시작, 제어 하는 유닛.
   1.  `systemd.service(5)`
2. Socket units : 로컬 IPC나 시스템 내 네트워크 소켓을 캡슐화하는 유닛, 
   1. 소켓 기반 활성화에 효과적임. 
3. Target units : `boot-up` 동안 잘 알려진 동기화 지점을 제공하거나 `group units`에 유용합니다.
   1.  `systemd.target(5)`
4. Device units : systemd 안의 커널 디바이스를 노출시키고 `device-based` 활성화에 유용합니다.
   1.  `systemd.device(5)`
5. Mount units : 파일 시스템의 마운트 포인트를 제어합니다.
   1.  `systemd.mount(5)`
6. Automount units : 병렬 부팅 뿐만 아니라 `on-demand mounting of file system`을 위한 자동 마운트 기능도 제공합니다.
   1.  `systemd.automount`
7. Snapshot units : 일시적으로 systemd units의 설정 상태를 일시적으로 저장하는데 사용합니다. 저장된 snapshot unit을 활성화시켜 복구할 수 있습니다.
   1.  `systemd.snapshot(5)`
8. Timer unit : timer에 기반해 다른 유닛들의 활성화를 유발하는데 유용합니다.
   1. `systemd.timer(5)`
9. Swap unit : mount unit 과 유사하며 메모리 스왑 파티션이나 OS 파일을 암호화합니다.
    1. `systemd.swap(5)`
10. Path unit : 파일 시스템 객체가 바뀌거나 수정될 때 다른 서비스를 활성화 시키는데 사용합니다.
    1.  `systemd.path(5)`
11.  Slice unit : service 나 scope unit 같이 시스템 프로세스를 관리하는 유닛들을 리로스 관리 목적으로 계층 트리에 그룹화 하는데 사용합니다.
     1.   `systemd.slice(5)`
12.  Scope unit : service units 과 유사하지만 ...

유닛 이름은 설정 파일에 따라 지어집니다. 일부 유닛들은 특별한 의미론을 갖습니다. (자세한 목록은 `systemd.special(7)` 참고)

**systemd 의존관계**

systemd는 긍정적이거나 부정적인 요구사항 의존관계(예, Requires= Conflickts=) 뿐만 아니라 의존관계 순서(After=, Before=)를 포함한 다양한 종류의 의존관계를 알고 있습니다. 
(* 주의 : 순서 의존관계와 요구 의존관계는 서로 영향이 없습니다 (orthogonal))

 만약 두 unit 간에 요구사항 의존관계만 존재하고 (e.g. foo.service가 bar.service를 필요로 함), 순서 의존관계가 없는 상태에서 동시에 시작을 요청받는다면, 둘은 병렬로 시작될 것입니다. 일반적으로 유닛 간에는 요구 의존관계와 순서 의존관계를 함께 사용합니다.

 또한 대부분의 의존관계는 systemd에 의해 암묵적으로 생성되고 유지되는 것을 알아야 합니다. 대부분의 경우 추가적인 의존관계를 수동으로 선언할 필요가 없지만 필요하다면 가능합니다.


응용 프로그램과 유닛 (의존관계를 통해)은 유닛의 상태 변화를 요청할 것입니다. systemd에서, 이 요청들은 `job`으로 캡슐화되며 `job queue`에서 관리됩니다. job들은 성공하거나 실패합니다.

잡의 실행 순서는 스케쥴링 되고 있는 유닛들의 순서 의존관계(ordering dependencies)를 기준으로 정렬됩니다.

시스템이 부팅되면 systemd는 의존관계를 가져옴으로써 on-boot 서비스와 다른 on-boot units를 활성화 하려 하는 잡을 가진 default.target을 활성화합니다.

 일반적으로 유닛 이름은 그저 UI에 관한 완전한 기능을 가진 부트를 위한 graphical.target이나 임베디드, 서버 환경 등을 위해 한정적인 콘솔로 부팅되는 multi-user.target을 위한 별칭입니다. 그러나 이름을 다른 타겟 유닛의 별칭으로 설정하는 것은 관리자의 재량입니다.

systemd가 생성한 프로세스들은 `private systemd hierarchy`에서 프로세스가 속한 유닛의 이름을 딴 개별 리눅스 컨트롤 그룹에 배치됩니다. (cpgroups) systemd는 이를 효과적으로 프로세스들을 추적하기 위해 사용합니다. 컨트롤 그룹 정보는 커널에서 유지되며, 이는 파일 시스템 계층 이나 ps(1)을 거쳐 접근할 수 있습니다. (/sys/fs/cgroup/systemd 아래 경로 ) ps xawf -eo pid,user,cgroup,args는 모든 프로세스와 이들이 속한 systemd 유닛을 나열하는데 특히 유용합니다.


...

**Systemd의 트랜잭션**

systemd는 작은 트랜잭션 시스템을 갖고 있습니다.
만약 유닛에게 시작하거나 종료하라고 요청하면 유닛은 다음을 수행합니다.

1. 유닛의 모든 의존관계를 임시 트랜잭션에 저장할 것입니다.
2. 그러고 나면 트랜잭션이 일관되었는지 검사할 것입니다. (예를들어 모든 유닛들의 순서가 싸이클이 없는지, )
3. 만약 문제가 있다면 systemd는 이를 고치려 시도합니다.
   1. 싸이클을 없애기 위해 트랜잭션에서 필수적이지 않은 잡들을 삭제할 것입니다.
   2. 또한 트랜잭션 안에서 필수적이지 않은 job들을 억누르고 실행중인 서비스를 멈추게 할 것입니다.
4. 마지막으로 트랜잭션 job이 이미 대기중인 잡을 방해하고 있는지 확인합니다.
     1. 선택적으로 트랜잭션이 중단 될 것입니다.

만약 모든 작업이 완료되고 트랜잭션이 일관성을 갖고 영향력이 최소화되면 이미 outstanding 한 job들과 머지되고 실행 큐에 추가될 것입니다.
 
요약하면 이는 요청된 동작을 실행하기 전에 systemd가 문제가 없도록 검증하고 고쳐도 동작할 수없으면 동작할 수 없으면 실패하도록 한다는 뜻입니다.


systemd는 부트 프로세스의 일부로서 실행돼야 하는 다양한 'task`의 native implementations를 가지고 있습니다. 예를들어 hostname을 설정하거나 loopback network device를 설정하빈다. 게다가 다양한 API file systemsem (/sys나 /proc) 들을 설정하고 마운트 합니다.

systemd의 비하인드 컨셉과 아이디어 정보는 `Original Design Document[3]`을 참고해주세요.

모든 인터페이스는 아니지만 systemd가 제공하는 일부 인터페이스는 Interface Stability Promise[3] 에 의해 보완됩니다.

유닛들은 부트와 시스템 매니저 리로드 타임에 동적으로 생성됩니다. 예를들어 커널 cli에 전달되는 파라미터나 다른 설정 파일을 기반으로 합니다. `systemd.generator`

POSIX와 SysV란?

()