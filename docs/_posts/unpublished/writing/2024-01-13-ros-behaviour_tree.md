---
layout: single
title: 
date: 2024-01-13 22:41
category: 
author: 
tags: []
summary: 
---

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

import py_trees
import random

> https://py-trees.readthedocs.io/en/release-2.2.x/behaviours.html

class Foo(py_trees.behaviour.Behaviour):
    def __init__(self, name):
        """
        최소한의 일회성 초기화. 오프라인 렌더링을 위해 이 동작을 `dot graph`에 삽입할 수 있는 트리에 관련된 초기화만 포함하는게 좋다.

        다른 일회성 초기화 요구사항은 setup() 에서 충족시켜야한다.

        https://py-trees.readthedocs.io/en/release-2.2.x/behaviours.html
        나중에 dot graph에서 어떻게 behaviour의 트리를 render하는지 보게 될 것이다. 지금은 이 최소한의 기준을 유지함으로써 Jenkins같은 외부서버에서 트리의 dot graphs를 생성할 수 있다는 것만 이해하는 것으로 충분하다.

        - 하드웨어 연결을 하면 안된다.
        - 다른 소프트웨어로 미들웨어 연결같은걸 하면 안된다.
        - 리소스를 많이 잡아먹는 무서운 작업ㅇ르 하면 안된다.
        """
        super(Foo, self).__init__(name)

    def setup(self):
        """
        When is this called?
            이 함수의 호출 타이밍은
            1. 단독 behaviour를 초기화하기 위해 프로그램에 의해 수동으로 호출
            2. (일반적) py_trees.behaviour.Behaviour.setup_with_dscendants 또는 meth:`~py_trees.trees.BehaviourTree.setup에 의해, 둘은 이 behaviour와 자식들을 순차 조회한다.
                순서대로 calling :meth:`~py_trees.behaviour.Behaviour.setup 를 호출한다.

            만약 behaviour를 성공적으로 수행하는 데 필요한 중요한 초기화 기능이 있는 경우,  :meth:`~py_trees.behaviour.Behaviour.initialise` method 메소드에 guard를 넣어 setup 되지 않은 채 진입하지 않도록 막아야한다.


        What to do here?
            1회 초기화가 지연되면 트리에서 이 behaviour의 오프라인 렌더링을 방해하거나 동작 구성을 검증하는 데 방해가 된다.
            좋은 예
            - 하드웨어 또는 드라이버 초기화
            - 미들웨어 초기화(예: ROS 펍/서브/서비스)
            - 자식을 추가하거나 제거한 후 유효한 정책 구성을 병렬로 확인하는 중

        https://py-trees.readthedocs.io/en/release-2.2.x/behaviours.html
        setup 메소드는 실행에 필요한 1회성 리소스 초기화들을 다룬다.
        기본 생성자가 핸들링 하지 않는 모든 초기화들 (하드웨어 연결, 미들웨어, 다른 무거운 리소스들)
        """

        self.logger.debug("  %s [Foo::setup()]" % self.name)

    def initialise(self):
        """
        When is this called?
          behaviour가 처음 tick될 때, 이후 behaviour의 status가 RUNNING이 아닐 때

        What to do here?
          작업에 behaviour를 넣기 전에 필요한 초기화 작업들

        https://py-trees.readthedocs.io/en/release-2.2.x/behaviours.html
        테스크를 즉시 실행할 수 있도록 준비하는 부분.
        - 변수 초기화, 리셋, 클리어
        - 타이머 시작하기
        - 미들웨어 연결 설정
        - 시스템의 다른 곳에서 실행되는 컨트롤러를 시작하도록 goal을 보내기
        """
        self.logger.debug("  %s [Foo::initialise()]" % self.name)

    def update(self):
        """
        When is this called?
          behaviour가 tick 될 때

        What to do here?
          - 트리거, 확인, 모니터링 등등... block 되지 않는 모든 것
          - feed 메세지 전달
          - py_trees.common.Status.[RUNNING, SUCCESS, FAILURE] 반환
        """
        self.logger.debug("  %s [Foo::update()]" % self.name)
        ready_to_make_a_decision = random.choice([True, False])
        decision = random.choice([True, False])
        if not ready_to_make_a_decision:
            return py_trees.common.Status.RUNNING
        elif decision:
            self.feedback_message = "We are not bar!"
            return py_trees.common.Status.SUCCESS
        else:
            self.feedback_message = "Uh oh"
            return py_trees.common.Status.FAILURE

    def terminate(self, new_status):
        """
        When is this called?
            Whenever your behaviour switches to a non-running state.
                - SUCCESS || FAILURE : your behaviour's work cycle has finished
                - INVALID : a higher priority branch has interrupted, or shutting down

            behaviour가 non-running state로 스위치되 될 때
                - SUCCESS 나 FAILURE : behaviour의 작업 사이클이 끝났을 때
                - INVALID : 높은 우선순의 브랜치가 중단되거나 꺼졌을 때

        """
        self.logger.debug("  %s [Foo::terminate().terminate()][%s->%s]" % (self.name, self.status, new_status))
        ```