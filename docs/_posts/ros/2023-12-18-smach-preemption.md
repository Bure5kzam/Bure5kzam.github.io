---
layout: single
title: smach preemption
date: 2023-12-18 15:13
category: ROS
author: Bure5kzam
tags: [ROS, smach]
summary: 
---

> 번역글 : smach/Tutorials/StatePreemption Implementation
> https://wiki.ros.org/smach/Tutorials/State%20Preemption%20Implementation

preemption - 선점

https://wiki.ros.org/mysmach/Tutorials/State%20Preemption%20Implementation

상태가 없거나 기본 컨테이너 타입만으론 부족할 수 있다. 판단하기 전에 제공대는 상태와 컨테이너 타입을 체크해봐야한다. 새 Smack state를 정의 하기 전에 세 가지 주요 기능을 고려해야 한다.

**Interface declaration**

SMACH 컨테이너는 포함된 state와 상호작용할 때 state outcome과 userdata key를 활용한다. outcome은 컨테이너의 다음 타겟 state와 연결하는데 사용한다. userdata key는 시스템의 dataflow를 따라가는데 필요하다. 정확한 선언은 SMACH tree의 생성자에서 미리 상태 구조의 문제점을 발견하게 해준다.

**Execution implementation**

컨테이너가 실행되어 state에 진입할 때 state의 execute() 메소드가 호출된다. 이 메소드는 블락하고 등록된 outcome을 반환한다. 이 메소드가 종료되면 해당 state는 휴면 또는 비활성화 상태가 되어야 한다.

만약 특정 상태의 실행 시간이 길 경우, concurrence 처리를 하거나 병렬 컨테이너를 사용하는 것이 좋다.

**Preemption implementation**

예제처럼 간단한 경우, 선점 간단히 `preempt_requested()` 메소드로 완료될 수 있다.만약 안되면 새 state 클래스에서 `request_preempt()` 메소드가 부하를 받을 수 있다. Preempts은 보통 다른 쓰레드에서 발생할 것이며 이 메소드는 하위 스레드나 프로세스에 선점을 알리는데 걸리는 시간보다 더 오래 차단되어서는 안된다.


```python
import roslib; roslib.load_manifest('smach')
import rospy

from smach import State

class FibState(State):
    def __init__(self, n):
        """Constructor."""
        State.__init__(self,
                outcomes = ['done','preempted'],
                input_keys = [],
                output_keys = ['fib_result'])
        self.n = n

    def execute(self,ud):
        """Calculate the nth fibonacci number."""
        f = 0
        f1 = 0
        f2 = 1
        for i in range(self.n):
            # Check for preempt
            if self.preempt_requested():
                self.service_preempt()
                return 'preempted'
            # Calculate the next number
            f = f1 + f2
            f1 = f2
            f2 = f

        # Store a tuple containing the result in userdata
        ud.fib_result = (self.n, f)

        return 'done'

    def request_preempt(self):
        """Overload the preempt request method just to spew an error."""
        State.request_preempt(self)
        rospy.logwarn("Preempted!")
```


https://wiki.ros.org/smach/Tutorials/State%20Machine%20Preemption%20with%20MonitorState

계산이 끝나거나 일부 조건이 충족된 상태머신에서 종료되는 상태를 갖기 위해 사용한다.

예시 코드는 동시성 컨테이너로 선점을 구현하는 state와 monitor를 조인한다. monitor state 는 `/sm_reset` 토픽이 도착했는지 체크한다.

초기 상태 머신은 SETUP 상태이며 FOO로 전환된 뒤 FOO와 BAR를 반복한다. FOO는 `Concurrence Container`이며 FOO_RESET 과 Monitor State, 긴 계산을 수행하는 FOO_CALC 상태를 갖는다. 만약 머신이 FOO 상태고 /sm_reset 메세지가 오면 FOO_RESET이 FOO를 반환할 것이며, FOO_CALC는 선점되고 머신이 SETUP 상태로 전환환다.

FOO_CALC가 완료되면, FOO_RESET이 선점되고 머신은 BAR로 전환한다.