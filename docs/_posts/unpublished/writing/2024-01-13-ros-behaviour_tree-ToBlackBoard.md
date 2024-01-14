---
layout: post
title: 
date: 2024-01-14 16:51
category: 
author: 
tags: []
summary: 
---

pytree_ros에서 토픽으로 받은 메세지를 Blackboard에 추가하려면 ToBlackboard를 상속하는 객체를 구현해야 한다.

## Overview

1. 초기화 (init)
    1. 부모 객체 초기화 단계에서 토픽을 받아올 subscriber를 정의
    2. blackboard에서 사용할 데이터의 key를 등록
    3. 2.에서 등록한 데이터를 초기화
2. tick (update)
    1. blackboard에 등록한 데이터를 초기화


## Example code

```python
import py_trees
import py_trees_ros
import std_msgs.msg as std_msgs


class NumberToBlackboard(py_trees_ros.subscribers.ToBlackboard):
    """
    생성자에서 정의한 토픽 이름으로 오는 메세지를 BlackBoard에 등록하는 객체
    """
    def __init__(self,
                 name: str,
                 topic_name : str
                 ):
        
        # * 부모 객체 정의할 때 토픽을 받아올 subscriber를 정의해주면 됨.
        super().__init__(
            name = name,
            topic_name= topic_name,
            topic_type= std_msgs.Int32,
            qos_profile=10,
            blackboard_variables={"number" : None},
            clearing_policy=py_trees.common.ClearingPolicy.NEVER
        )

        self.blackboard.register_key(
            key="number",
            access=py_trees.common.Access.WRITE
        )

        self.blackboard.number : std_msgs.Int32 = std_msgs.Int32()
        self.blackboard.number.data = 0
        # self.blackboard.number.power = std_msgs.Bool()

    def update(self) -> py_trees.common.Status:
        self.logger.debug("%s.update()" % self.__class__.__name__)
        status = super(NumberToBlackboard, self).update()
        if status != py_trees.common.Status.RUNNING:
            # we got something
            if self.blackboard.number.data > 5:
                pass
            else:
                self.node.get_logger().error("{}: battery level is low!".format(self.name))
            

            self.feedback_message = "Battery level is low" if self.blackboard.number.data < 5 else "Battery level is ok"
        return status

```

## Reference

> document, pytree_ros, battery : https://py-trees-ros.readthedocs.io/en/devel/_modules/py_trees_ros/battery.html