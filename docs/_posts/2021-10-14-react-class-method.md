---
date: 2021-10-14 20:00:00 +/-TTTT
title : React-Class and Method
categories: [TIL, react]
tags: [React]     # TAG names should always be lowercase
toc : true
comments : true
math : true
mermaid: true
---
# 개요
## Vue, Angular와의 차이
대부분 완성이 되어있음
- 한가지 프레임워크만 사용하는 경우가 많음.
- 사용하지 않을 기능들도 구현되어있기 때문에 무겁다고 느껴질 때가 있음.
  
리액트는 라이브러리로 컨트롤러, AJAX, 라우터 기능을 제공하지 않으며, 이들은 써드파티를 활용해 구현할 수 있음.

Vue와 Angular는 framework임.

## Virtual Dom
리엑트는 15 버전 이후 2개 파일을 필요로함
- react.min.js : 리엑트 컴포넌트에 관련된 파일
- react.dom.js : 리엑트 virtual Dom 관리에 관한 파일


# 문법

## [Class, Method](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/class)

react는 자바스크립트의 클래스를 활용해 컴포넌트를 구성함.

```js
// 클래스 문법1

class Class1 {
    constructor(height, width) {
        this.height = height;
        this.width = width;        
    }
}
// 클래스 문법2 unnamed
var Polygon = class {
    constructor(height, width) {
        this.height = height;
        this.width = width;
    }
}

// 클래스 문법3 named
var Polygon = class Polygon {
    constructor(height, width) {
        this.height = height;
        this.width = width;
    }
    // 반환 데이터 형이 없음!
    calcArea() {
        return this height * this.width
    }
    // 객체로 만들기 전
    static 
}
```

## 주의사항
>The same error is thrown when a class has been defined before using the class expression.
```
let Foo = class {};
class Foo {}; // Uncaught 
SyntaxError: Identifier 'Foo' has already been declared
```
위 내용은 클래스를 다른 두가지 문법 unnamed, named으로 정의한 것이다.
 예상으로는 호이스팅 과정에서 발생하는 에러인 것 같은데, 문법이 달라서 익숙하지 않으면 헷갈릴 수 있겠다.


## Static
```js
var y = 'Outer y';

class A {
  static field = 'Inner y';
  static {
    var y = this.field;
  }
}

// var defined in static block is not hoisted
console.log(y);
// > 'Outer y'
```


