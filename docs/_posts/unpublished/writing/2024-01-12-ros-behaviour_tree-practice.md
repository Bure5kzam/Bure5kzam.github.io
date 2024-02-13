---
layout: single
title: pytree 테스트 용으로 만들기
date: 2024-01-14 14:11
category: 
author: Bure5kzam
tags: []
summary: 
---

> https://py-trees-ros-tutorials.readthedocs.io/en/devel/


pytree 도입을 검토하기 위해, 초안을 만들어 본다.

<!-- 시간 활용을 위해 회사에서 구현해야할 작업을 구현하는 것을 목표로 한다. -->

<!-- 실제 요구사항은 여러 테스크들을 구현할 수 있는 인터페이스를 정의해야 하지만, pytree의 동작 방식에 대한 감을 잡아야 여부를 확인할 수 있을 것 같다. -->


요구사항

- task 실행 요청
- task 중지 요청
- task 캔슬 요청

필요한 것

- task 요청 수신 노드
    - 테스크 실행 요청을 받으면 테스크 작업 플로우를 실행시키고, 중지 요청을 받으면 진행상황을 보유한 채로 중지할 수 있도록 해야함.
- action 노드
    - 액션을 실행하도록 구현

## pytree, pytree_ros 설치

py_trees 및 py_trees_ros는 ros2에서 의존성을 제공하는 파이썬 패키지들이다.

package.xml에서 ros 의존성을 명시하면 `rosdep` 명령어로 설치할 수 있다.

```bash
bure@burepool:~/ros/workspaces/pytree_ws/src$ rosdep resolve py_trees
#apt
ros-humble-py-trees
bure@burepool:~/ros/workspaces/pytree_ws/src$ rosdep resolve py_trees_ros
#apt
ros-humble-py-trees-ros
```

```xml
# ros2 package.xml
<?xml version="1.0"?>
<?xml-model href="http://download.ros.org/schema/package_format3.xsd" schematypens="http://www.w3.org/2001/XMLSchema"?>
<package format="3">
  <name>pytree_test_package</name>
  <version>0.0.0</version>
  <description>TODO: Package description</description>
  <maintainer email="bure5kzam@gmail.com">bure</maintainer>
  <license>TODO: License declaration</license>

  <test_depend>ament_copyright</test_depend>
  <test_depend>ament_flake8</test_depend>
  <test_depend>ament_pep257</test_depend>
  <test_depend>python3-pytest</test_depend>
  <depend>py_trees</depend>
  <depend>py_trees_ros</depend>
  

  <export>
    <build_type>ament_python</build_type>
  </export>
</package>

```

```bash
# rosdep 명령어로 설치
bure@burepool:~/ros/workspaces/pytree_ws$ rosdep install --from-paths src --ignore-src
executing command [sudo -H apt-get install ros-humble-py-trees]
[sudo] bure 암호: 
패키지 목록을 읽는 중입니다... 완료
의존성 트리를 만드는 중입니다... 완료
상태 정보를 읽는 중입니다... 완료        
다음 패키지가 자동으로 설치되었지만 더 이상 필요하지 않습니다:
  nvidia-firmware-545-545.29.02
'sudo apt autoremove'를 이용하여 제거하십시오.

...

Processing triggers for libc-bin (2.35-0ubuntu3.6) ...
#All required rosdeps installed successfully
bure@burepool:~/ros/workspaces/pytree_ws$ 
```
