---
title: npm Sequelize migration
date: 2021-08-20 20:00:00 +/-TTTT
categories: [TIL, nodejs]
toc : true
comments : true
math : true
mermaid: true
---
# npm Sequelize migration
$ npx sequelize model:generate

#schedule 모델 생성
```bash
$ npx sequelize model:generate --name schedules --attributes schedule_id:integer,user_id:string,date:date,title:string,context:string,started_at:date,finished_at:date,deadline_at:date,created_at:date,updated_at:date,point:integer,is_finished:boolean,notification:date,noti_extend:integer,challenge_id:integer

$ npx sequeilze model:generate --name daily --attributes date:string,week:string,month:string,year:string,user_id:string,avgpoint:string,cntschedule:integer
#environment X

$ npx sequelize model:generate --name weekly --attributes week:string,month:string,year:string,user_id:string,avgpoint:integer
#environment X

$ npx sequelize model:generate --name monthly --attributes month:string,year:string,user_id:string,avgpoint:integer,avgpoint:integer
#environment X

$ npx sequelize model:generate --name yearly --attributes year:string,user_id:string,avgpoint:integer,avgpoint:integer
#environment X


```



