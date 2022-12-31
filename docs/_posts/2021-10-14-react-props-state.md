---
date: 2021-10-14 20:00:00 +/-TTTT
title : React-props and state
categories: [TIL, react]
tags: [React]     # TAG names should always be lowercase
toc : true
comments : true
math : true
mermaid: true
---
# 개요
## [props란](https://developer.mozilla.org/ko/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/React_getting_started)

MDN의 문서에서 prop객체의 특징을 정리해보았다.

```
prop는 React 컴포넌트에 전달되는 모든 데이터를 뜻합니다.
React props는 HTML 속성과 비교할 수 있습니다.
HTML 요소는 속성을 가지고 있고 React 컴포넌트는 props를 가지고 있습니다.
props는 컴포넌트 호출 안에 쓰이며 prop="value"와 같이 HTML 속성과 동일한 구문을 씁니다.
React에서, 데이터의 흐름은 단방향(unidirectional)입니다: props는 오직 부모 컴포넌트에서 자식 컴포넌트로 내려갑니다. 그리고 props는 오로지 읽기 전용입니다.
```

## 사용해보기
### JSX Function
```js
//JSX APP component를 function으로 정의하고 subject라는 props를 생성함
function App(props) {
    console.log(props)
  return (
    // return statement
  );
}

//App에서 subject props에 Clarice를 정의해준다.
ReactDOM.render(
    <App subject="Clarice" />,
    document.getElementById('root');
)
```
MDN 문서에서 props가 오로지 읽기 전용이라고 적힌 문구는 이는 js에서 ReactDOM에서 JSX에 데이터를 전달할 수 는 있지만 반대로 JSX에서 ReactDOM으로 전달할 수는 없다는 뜻인 것 같다.

### JSX Class
인프런 강의를 보니 JSX를 Class로 정의하면 부모객체를 상속하기 때문에 this 객체로 props에 접근할 수 있었다.

```js
class Codelab extends React.Components {
    render() {
        return (
            <div>
                <h1>Hello {this.props.name}</h1>
                <div>{this.props.children}</div>
            </div>
        )
    }
}

// props의 defaultProps로 기본값을 default로 정의할 수 있다
App.defaultProps = {
    name : "Unknown"
}

// propsTypes로 객체 타입 유효성을 검증할 수 있으니 상당히 편리해보인다.
App.propsTypes = {
    name : React.PropTypes.string
}
```

# States
> [MDN](https://developer.mozilla.org/en-US/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/React_interactivity_events_state)
> 데이터의 흐름은 단방향(unidirectional)입니다

앞서 props는 immutable한 객체만을 다룰 수 있다고 명시된 바 있다.
States는 이와 반대로, DOM 객체와 상호작용하며 update될 수 있는 데이터들을 다루는 역할이다.
Vue와 유사한 개념이 많은 것을 느낄 수 있었다.

> [MDN](https://developer.mozilla.org/en-US/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/React_interactivity_events_state)
> React는 상태와 같은 구성 요소에 새로운 기능을 제공할 수 있는 다양한 특수 기능을 제공합니다. 이 함수는 호출되는 후크 , 그리고 useState그 이름에서 알 수 있듯이 후크는 정확하게 우리가 우리의 구성 요소 몇 가지 상태를 제공하기 위해 필요한 것입니다.

이벤트가 발생했을 때 알려주는 기능을 `hook`이라고 한다. React에서는 데이터 업데이트가 발생하면 hook을 통해 다른 객체들에게 알려 관련된 모든 데이터를 갱신할 수 있다.

## use States
```js
import React, { useState } from "react";
const [name, setName] = useState('Use hooks!');
```
useState를 실행하면 배열 구조 분해가 일어난다.
- name에 'Use hooks' 값을 할당한다.
- name을 변동하는 함수를 setName에 할당한다.

```js
//JAX render data
<input
  type="text"
  id="new-todo-input"
  className="input input__lg"
  name="text"
  autoComplete="off"
  value={name}
/>
```
new-todo-input의 value 속성을 추가하고 값을 name으로 설정한다.
브라우저는 value에 "Use hooks!"를 넣는다.


