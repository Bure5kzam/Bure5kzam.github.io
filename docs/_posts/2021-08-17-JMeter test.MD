---
title: JMeter 도입기
date: 2021-08-17 20:00:00 +/-TTTT
categories: [TIL, nodejs]
tags: [QA]     # TAG names should always be lowercase
toc : true
comments : true
math : true
mermaid: true
---
# 개요

 이전에 초 단위로 데이터를 받아오는 프로젝트를 구현하면서 성능 이슈를 마주한 적이 있습니다. 3초마다 HTTP로 필요한 데이터를 body에 담아 보낸지 5분쯤 지나자 서버가 수신 데이터를 화면에 출력하는 빈도가 서서히 느려지더니 껏다켜도 데이터를 계속 수신밖에 못하는 사태에 이르렀습니다. 단일소켓 싱글 쓰레드로 돌린 nodejs 서버라 퍼포먼스를 기대하진 않았지만 초단위 통신은 감당할 줄 알았는데...

 문제는 이런 아이러니한 상황이 열심히 통신 로직을 설계하고 프론트와 백엔드 로직 구현이 끝난 후에 나타났다는 것입니다. 당시에는 개인 프로젝트라 저혼자 socket.io를 도입할 수 있었지만, 팀 프로젝트로 협업하다 나타났다면 진짜,,, 사기가 바닥나버렸을 지도 모릅니다. 만에하나 서비스 운영중에 발견하게 됐다면,,,

 그런 의미에서 개발 프로세스에 테스트 과정을 도입해보고 싶었는데, 기회가 되어 정리해봅니다.



## 참고

https://jmeter.apache.org/usermanual/get-started.html

공식문서가 굉장히 잘 작성되어 있었습니다.

https://jybaek.tistory.com/889

원어가 보기 어렵다면 백재연님의 잘 정리해두신 글이 있습니다.

https://stackoverflow.com/questions/37169153/jmeter-does-not-send-json-data-in-post

post로 json 데이터를 보내기 위한 과정입니다.

# 도입

- 계정 로그인

![image](/assets/img/post/2021-08-17-Jmeter%20도입기/1_login.jpg)
- 일정 생성

![image](/assets/img/post/2021-08-17-Jmeter%20도입기/2_make_schedule.jpg)
- 일정 조회

![images](/assets/img/post/2021-08-17-Jmeter%20도입기/3_show_month_schedule.jpg)

10명이서 10초동안 세 가지 기능을 20번 수행하도록 부하테스트를 해보았습니다.



 Summary Report를 확인해보니 일정 생성을 담당하는 make schedule 쓰레드의 latency가 제일 높게 나옴을 알 수 있었습니다. (155)

![images](/assets/img/post/2021-08-17-Jmeter%20도입기/4_summery_report.jpg)



# 개선

 저희 프로젝트의 DB 테이블 중에는 schedule 로부터 통계 값을 산출해 저장하기 위한 반정규화 테이블 daily, weekly, monthly, yearly가 있었습니다. schedule 하나를 생성/수정 할때마다 네 테이블이 덩달아 변경됩니다.

 가령 schedule 테이블을 생성하고 migrate 함수를 실행시키면 해당 schedule을 기반으로 관련된 daily => weekly => monthly => yearly를 모두 생성해주게 됩니다.

일정 생성시 생기는 부하의 원인을 고민해보니, 생성 로직이 쓸데없이 동기 처리되고 있다는 것을 깨달았습니다. 각각의 테이블들은 반정규화된 상호 연관성 없는 테이블이기 때문에 병렬 처리해도 아무런 문제가 없었습니다. 더군다나 멀티 쓰레드 처리를 안한 nodejs 서버였기에 영향이 더 컸을 것입니다.

진단 결과를 바탕으로한 향후 성능 개선 방향 다음과 같습니다.

- 일정 생성,수정,삭제 로직을 비동기로 수행하도록 변경
- 데이터 베이스 캐싱

