---
layout: post
title: smach 유저 데이터 사용하기
date: 2023-12-18 22:34
category: ros
author: Bure5kzam
---

> https://wiki.ros.org/smach/Tutorials/User%20Data

t
state은 input data과 output data를 가질 수 있음.

input data로 받은 값을 로직에 활용할 수 있으며, output data을 지정하면 다른 state로 전환될 때 데이터를 제공할 수 있음.

이들을 state의 `user data`라고 부름.


## 유저 데이터 예시

```python
class Foo(smach.State):
    # outcomes : Foo 상태에서 가능한 결과들 리스트
    # input_keys : dictionary 객체인 userdata의 자식 중 input data인 데이터들의 키 이름 리스트
    # output_keys : output data인 데이터들의 키 이름 리스트
    def __init__(self, outcomes=['outcome1', 'outcome2'],
                    input_keys=['foo_input'],
                    output_keys=['foo_output']):
        pass

    def execute(self, userdata : dict):
        # Do something with userdata
        if userdata.foo_input == 1:
            return 'outcome1'
        else:
            userdata.foo_output = 3
            return 'outcome2'
```

state에서 `user data`를 사용하려면 생성자에서 dict 객체에서 사용할 이름을 지정해줘야 한다.

`userdata`는 execute 메소드의 인자로 제공되는데, **dict 타입이기 때문에** 생성자에서 정의한 이름으로 input data나 output data를 찾아야 한다.


생성자의 `input_keys` 아규먼트에 포함된 문자열들은 입력 값들의 dict key 이름들이다. 주로 state가 다음 상태를 결정하기 위해 필요한 값들을 받아올 때 사용한다.

`output_keys` 아규먼트의 문자열들은 다음 state로 전환될 때 넘겨줘야 할 값들의 dict 키 이름이다.

주의 사항이 있는데, __input_keys에 지정된 userdata의 value값으로 주어진 클래스는 메서드를 호출할 수 없다.__ 불변성을 위해 래핑된 객체를 전달하기 때문이다. 정 필요하다면 키 이름을 output_keys에도 넣으면 가능은 하지만 혼동과 잠재적인 버그가 있을 수 있다고한다. [문서](https://wiki.ros.org/smach/Tutorials/User%20Data)

2. state 간에 유저 데이터 연결하기

상태 머신에 state를 추가할 때 `state`간에 userdata의 키도 연결해줘야 다른 state에 전달할 수 있다. 만약 두 상태간에 사용하는 userdata의 필드 이름이 같으면 전환에 문제없지만 만약 키 이름이 다를 경우, `name remapping`으로 필드 키를 변경할 수 있다.

<!-- 예를들어 Foo state가 foo_output을 생성하고 BAR state가 bar_input을 생성하면 이 두개의 userdata 포트를  -->


```python
sm_top = smach.StateMachine(outcomes=['outcome4','outcome5'],
                        input_keys=['sm_input'],
                        output_keys=['sm_output'])
with sm_top:
    smach.StateMachine.add('FOO', Foo(),
                        transitions={'outcome1':'BAR',
                                        'outcome2':'outcome4'},
                        remapping={'foo_input':'sm_input',
                                    'foo_output':'sm_data'})
    smach.StateMachine.add('BAR', Bar(),
                        transitions={'outcome2':'FOO'},
                        remapping={'bar_input':'sm_data',
                                    'bar_output1':'sm_output'})
```

아까 state의 정의를 나타낸 예시에서, execute 메소드는 실제로 state 인스턴스들을 관리하는 state machine에서 호출된다. 인자로 있던 userdata는 실제로 `state machine`이 가지고 있는 데이터고 state 클래스는 입력값/출력값을 나타내는 필드의 __이름__만 알고 있다.

remapping 필드는 state의 in/outputkey를 상태머신의 userdata 필드에 실제로 연결하는 역할을 한다.

'x' : 'y'라고 되어있으면 `x`는 state의 key 이름이며, `y`는 userdata에서의 값이다.

위 상태머신의 Foo state 추가 부분을 예로들면, foo로 전환될 때 기존 userdata에 들어있던 `sm_input` 필드 값은 `foo_input` 필드로 바뀌고 `sm_data` 필드는 `foo_output`으로 바뀐 뒤에 인자로 제공된다.

```python

...
def execute(self, userdata : dict):
        # Do something with userdata
        if userdata.foo_input == 1:
            return 'outcome1'
        else:
            userdata.foo_output = 3
            return 'outcome2'

...

```

그래서 Foo의 execute 안에서는 foo_output/input 필드에 접근한다.

