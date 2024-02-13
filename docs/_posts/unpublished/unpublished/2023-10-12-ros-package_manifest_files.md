---
layout: single
title: ros2 manifest file
date: 2023-10-12 22:48
category: ROS
author: Bure5kzam
tags: [ros, manifest]
summary:
published : false
---

{% include callout-writing-done.html %}

ros의 manifest.xml은 ROS 패키지에 대한 최소한의 명세이며, 컴파일, 문서화, 배포를 위해 넓고 다양한 툴들을 지원합니다. 게다가 패키지의 메타데이터에 대한 최소 명세를 제공하는 것 외에도 매니페스트 파일의 중요한 역할은 언어와 운영체제에 대해 중립적인 태도를 갖게 해주는 것입니다. manifest.xml 파일의 존재는 상징적입니다. manifest.xml 파일을 포함하는 모든 ROS 패키지 경로는 패키지로 간주됩니다.

아무것도 없는 최소한의 매니페스트 파일은 마치 패키지의 작성자와 라이센스를 나타내는 README 파일 같습니다. 패키지는 ROS 코드를 배포하는 수단이므로 라이센스가 중요합니다. 보통 <depend>와 <export> 태그도 포함되는데, 이는 패키지의 설치 관리와 사용을 돕습니다.

<depend> 태그는 패키지 사용을 위해 필요한 다른 ROS 패키지를 지목합니다. 이 태그는 가리키는 패키지의 내용에 따라 다양한 의미를 가질 수 있습니다. 예를들어 rospy 코드를 가리키면 depend는 다른 패키지들이 PYTHONPATH 환경변수가 가리키는 경로에 추가되어야 한다고 선언합니다.roslaunch 파일을 가리키면 이 패키지는 다른 패키지에서 온 roslaunch파일을 포함한다고 나타냅니다.

<export> 태그는 언어 지정 빌드를 묘사하고 패키지를 의존하는 다른 패키지들의 런타임 플래그를 묘사합니다.
