---
title: 우분투 보안 식별자
category : ubuntu
author: Bure5kzam
tags: [user]
summary: 리눅스 사용자 계정 
---

참조 글 :

CREDENTIALS(7)          Linux Programmer's Manual         CREDENTIALS(7)

https://man7.org/linux/man-pages/man7/credentials.7.html

## PID

리눅스 프로세스들은 `fork`로 생성될 때  고유한 양의 정수를 식별자를 부여받습니다.

프로세스가 무슨 pid를 가지고 있는지는 `getpid`로 알 수 있습니다.

PID의 사용 목적은 시스템 콜 범위에서 영향 받는 프로세스를 찾기 위해서다.

프로세스의 PID는 execve에 의해 보존된다.

## PPID

프로세스의 부모 프로세스 ID (Parent Process Identifier)로, fork로 이 해당 프로세스를 생성한 프로세스의 아이디이다.


## Process group ID 와 Session ID

각 프로세스는 세션 ID와 프로세스 그룹 아이디를 가지고 있습니다.

fork로 생성된 자식 프로세스는 부모의 `session ID`와 `process group id`를 상속받습니다.

세션과 프로세스 그룹은 쉘에서 잡 컨트롤을 위해 고안되었습니다.

 프로세스 그룹 (가끔 job 이라고도 불림)은 같은 프로세스 그룹 아이디를 공유하는 프로세스들의 집합이다. 단일 명령어나 파이프라인이 실행될 때, 쉘은 사용된 프로세스들을 위해 새 프로세스 그룹을 생성한다. (예를 들어 ls | wc 를 하면 같은 프로세스 그룹으로 실행한다.) 프로세스들의 그룹 멤버쉽은 `setpgid` 명령어로 지정할 수 있다. 프로세스 그룹 아이디와 PID가 같은 프로세스는 `프로세스 그룹 리더` 라고 불릅니다.

세션은 같은 session ID를 갖는 프로세스들의 콜렉션입니다.

프로세스 그룹의 모든 멤버들은 같은 세션 아이디를 갖습니다. (이는 프로세스 그룹의 모든 멤버들은 같은 세션에 속함을 뜻함) 그러므로 세션과 프로세스 그룹들은 프로세스들의 2 단계 계층 제약을 형성합니다. 새 세션은 프로세스가 setsid를 호출할 때 생성됩니다. setsid는 호출한 프로세스의 PID와 같은 SID를 가지는 새 세션을 생성합니다. 세션을 생성한 프로세스는 `session leader`라고 불립니다.

세션에 있는 모든 프로세스들은 `controlling terminal`을 공유합니다. `controlling terminal` 은 `session leader` 프로세스가 터미널을 처음 열 때 설정됩니다 (open을 호출할 때 O_NOCITY flag가 지정되지 않는 한). 터미널은 세션 당 최대 하나의 제어 터미널이 될 수 있습니다.

세션에 있는 job들 중 최대 하나는 foreground job이 될 수 있으며, 세션 내 다른 job들은 backgorund jobs가 됩니다. 오직 foreground job 만이 터미널에서 읽어올 수 있습니다. 백 그라운드에 있는 프로세스가 터미널에서 읽어오려 하면, 프로세스의 프로세스의 그룹이 job을 연기하는 SIGTTIN 시그널을 보냅니다. 만약 터미널에 `TOSTOP` 플레그가 세팅되어잇다면 오직 FOREGROUND JOB 만이 터미널에 쓸 수 있고 BACKGROUND JOB이 쓰면 `JOB`을 연기시키는 `SIGTTOU` 시그널이 생성되도록 유발합니다. 시그널을 생성하는 터미널 키(예를 들면 INTERRUPT KEY)가 눌리면 FOREGROUND JOB에 있는 터미널 키에게 시그널이 전송됩니다.


## User and group identifiers

리눅스 프로세스는 여러 UID, GID와 관계를 갖습니다.

### `Real user id`(uid), `Real group id`(gid)

 프로세스를 소유한 사람을 결정합니다.

 > getuid, getgid : 호출하면 uid와 gid를 알 수 있습니다.

### `Effective user id`(euid)와 `effective group id`(egid)

 프로세스가 `message quere` 나 `shared memory`, `semaphores` 같은 `shared resources` 에 접근할 수 있는지 결정합니다. 대부분의 Unix 시스템은 프로세스가 파일에 접근할 수 있는지 확인할 때도 이를 사용하지만, 리눅스는 파일을 확인할 때 사용하는 `File system ID` 이 따로 있습니다.

### `Saved set-user-ID`(와 `saved set group id`

 이 ID들은 `effective ID`에 대응되는 복사본을 저장하기 위해 사용됩니다. `eid`들은 `set-userID`, `set-group-id` 프로그램에서 프로그램을 실행할 때 설정됩니다. 

 A set-user-ID 프로그램은 effective user ID를 `real user ID`와 `saved-set-user-ID` 간에 스위칭 해서 권한을 가정하거나 제거합니다. 스위칭은 seteuid와 setreuid, setresuid를 호출해 이뤄집니다. set-group-ID 프로그램은 setegid, setregid를 사용해 유사한 동작을 수행합니다. 

 ### `Filesystem user ID`와 `Filesystem group ID` (리눅스 한정)

이 아이디들은 아래 설명된 보조 그룹 아이디들과 함께 파일에 접근하는 권한을 결정합니다. (자세한 내용은 path_resolution 참고). 프로세스의 effective user(group) id가 바뀌면 커널 또한 자동으로 File system user ID를 같은 값으로 바꿉니다. 결과적으로 파일시스템 아이디는 일반적으로 effective ID와 동일한 값을 가지며, 파일시스템 권한 검사의 의미는 리눅스나 다른 유닉스나 동일합니다. 파일 시스템 아이디는 setfsuid에 의해 euid와 달라질 수 잇습니다.

### `Supplementary group IDs`

파일이나 공유 리소스에 접근할 때 체크할 때 사용되는 그룹 아이디입니다. 리눅스 커널 2.6.4 버전 이전에는 프로세스가 최대 32개의 `supplementary group`을 가질 수 있었으나, 2.6.4 커널부터 최대 65536개 가질 수 있습니다.

---

`fork`로 생성된 자식 프로세스는 부모의 UID와 GID의 복사본을 상속받습니다. `execve` 하는 동안, 프로세스의 real user 와 group ID, 그리고 supplementary group ID 들은 유지됩니다. `effective`와 `saved set ID`는 execv에 묘사된대로 수정됩니다.

이외에도 프로레스 ID는 여러 목적으로 사용됩니다.

- 신호 전송 권한을 결정할 때
- 프로세스의 스케쥴 파라미터 설정 권한을 결정할 때
- 리소스 제한을 확인할 때
- 알림 인스턴스 수에 대한 제한을 확인할 때



## execv

execv를 실행하면 현재 실행중인 프로세스가 새로 초기화된 스택, 힙, 데이터 세그먼트가 있는 새 프로그램으로 대체됩니다.

만약 execv로 호출된 프로그램에 set user ID가 지정되어있으면, 호출되는 프로세스의 euid는 프로세스 실행 파일의 소유자 로 변경됩니다. 비슷하게 만약 set-goup-id bit가 설정되어 있으면 egid도 변경됩니다.


프로세스의 euid는 saved set-user-id에 복사됩니다. (egid도 )이 복사는 set uid와 setgid 모드 비트에 의해 euid 변경이 발생한 뒤에 이루어집니다.

프로세스의 read uid와 read gid, supplementary group id는 execve를 실행해도 변경되지 않습니다.

만약 실행 파일이 