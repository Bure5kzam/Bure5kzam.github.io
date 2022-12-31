---
date: 2021-10-15 20:00:00 +/-TTTT
title : React-setting
categories: [TIL, react]
tags: [React]     # TAG names should always be lowercase
toc : true
comments : true
math : true
mermaid: true
---
# [리액트 프로젝트 만들기](https://create-react-app.dev/docs/getting-started)

## webpack
> [NAVER D2](https://d2.naver.com/helloworld/0239818)
Javacript 자체에는 단위작업을 지원하는 모듈 시스템이 없습니다.
이런 한계를 극복하기 위해 모둘화 명세를 지원하는 여러 도구가 존재하는데 그 중 하나가 webpack입니다.

webpack을 이용하면 javascript에서 exports 객체를 사용할 수 있습니다.

```js
npm init
npm install -g webpack
npm react react-dom

//ES6 => ES5
npm install --save-dev babel-core babel-loader babel-preset-es2015 babel-preset-react

//JSX 사용 
npm install --save-dev react-hot-loader webpack webpack-dev-server
```

# webpack 설정

npx create-react-app my-app --use-npm --template typescript

[Create React App](https://create-react-app.dev/docs/getting-started)

[Android Studio](https://developer.android.com/studio?gclsrc=aw.ds&gclid=CjwKCAjw8KmLBhB8EiwAQbqNoDPqnfgyXJhEZZrE4dPFQXcIDFFbc0aFlV9Nygaj7pwyWjr43GuFGRoCcakQAvD_BwE)
## 안드로이드 설치 이슈
```
[SC] 4294967201 오류가 발생하여 StartService이(가) 실패했습니다.
Android Emulator Hypervisor Driver for AMD Processors installation failed. To install Android Emulator Hypervisor Driver for AMD Processors follow the instructions found at: https://github.com/google/android-emulator-hypervisor-driver-for-amd-processors

조치
1. 윈도우 프로그램 추가/제거의 기능 추가에서 Hyper-V 비활성화
2. npx react-native run-android

이슈2
 Failed to install the following Android SDK packages as some licences have not been accepted.
     build-tools;30.0.2 Android SDK Build-Tools 30.0.2

조치2
sdk manager에서 google Licensing 툴설치
```



[React native](https://reactnative.dev/docs/environment-setup)


```sh
안드로이드 실행
npx react-native init AwesomeProject

npx react-native init AwesomeTSProject --template react-native-template-typescript

npx react-native start

npx react-native run-android

#에뮬레이터 목록
adb devices

#개발 서버 연결
$ adb -s <device name> reverse tcp:8081 tcp:8081

# 개발자 메뉴 열기
**adb shell input keyevent 82**

# 디버그 툴
npm install -g react-devtools
```