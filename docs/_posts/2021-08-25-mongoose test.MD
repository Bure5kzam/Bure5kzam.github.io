---
title: mongoose
date: 2021-08-25 20:00:00 +/-TTTT
categories: [TIL, mongodb]
tags: [mongodb, fhth]     # TAG names should always be lowercase
toc : true
comments : true
math : true
mermaid: true
---

ORM의 migrate를 사용하기 위해 프로젝트에 mongoose를 도입했습니다.

```bash
# 의존성 설치
$ npm i mongoose-cli moa-console mongoose

# mongoose 폴더 생성
$ npx mongoosejs-cli init 

# mongodb seed 제거
npx mongoosejs-cli db:seed:undo:all
npx mongoosejs-cli db:seed:all

# 모델 생성
## robots
$ npx mongoosejs-cli model:generate --name robots --attributes modules_id:Array,serial:String,name:String
## users
$ npx mongoosejs-cli model:generate --name users --attributes robots_id:Array,email:String,pw:String
## moduleTypes
$ npx mongoosejs-cli model:generate --name moduleTypes --attributes name:String
## registedModules
$ npx mongoosejs-cli model:generate --name registedModules --attributes serial:String,type_id:**ObjectId,data:Mixed**,name:String

```
| 링크                                                       | 내용                       |
| ---------------------------------------------------------- | -------------------------- |
| [링크](https://www.npmjs.com/package/mongoosejs-cli)       | Mongoose Official Document |
| [링크](https://mongoosejs.com/docs/schematypes.html#mixed) | Mongoose Data Types        |
