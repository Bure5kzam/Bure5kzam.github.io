---
date: 2021-10-20 20:00:00 +/-TTTT
title : React-redux1
categories: [TIL,react]
tags: [React]     # TAG names should always be lowercase
toc : true
comments : true
math : true
mermaid: true
---
# [redux ](https://react-redux.js.org/introduction/getting-started)

작성일자 21.10.20
```js
node redux 모듈 설치
npm install @reduxjs/toolkit react-redux
```

# redux API 종류
## Provider
앱에서 Provider를 통해 Redux 저장소에 접근할 수 있음.
>Once the store is created, we can make it available to our React components by putting a React Redux <Provider> around our application in src/index.js. Import the Redux store we just created, put a <Provider> around your <App>, and pass the store as a prop:
> 스토어가 생성되고 나면, Provider 태그로 index.js의 어플리케이션을 감싸서 리엑트 저작소에 접근할 수 있게 만들 수 있음.

```js
// App.js
import React from 'react'
import ReactDOM from 'react-dom'

import { Provider } from 'react-redux'
import store from './store'

import App from './App'

const rootElement = document.getElementById('root')
ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  rootElement
)
```

## hook
```js
import React from 'react'
import { useSelector, useDispatch } from 'react-redux'
import {
  decrement,
  increment,
  incrementByAmount,
  incrementAsync,
  selectCount,
} from './counterSlice'
import styles from './Counter.module.css'

export function Counter() {
  const count = useSelector(selectCount)
  const dispatch = useDispatch()

  return (
    <div>
      <div className={styles.row}>
        <button
          className={styles.button}
          aria-label="Increment value"
          onClick={() => dispatch(increment())}
        >
          +
        </button>
        <span className={styles.value}>{count}</span>
        <button
          className={styles.button}
          aria-label="Decrement value"
          onClick={() => dispatch(decrement())}
        >
          -
        </button>
      </div>
      {/* omit additional rendering output here */}
    </div>
  )
}
```

dispatch 함수를 자식 컴포넌트에게 props로 넘겨주는 구조.
selector는 좀 더 봐야 알겠다.

# Redux 툴킷 사용
## Purpose
>The Redux Toolkit package is intended to be the standard way to write Redux logic. It was originally created to help address three common concerns about Redux:
"Configuring a Redux store is too complicated"
"I have to add a lot of packages to get Redux to do anything useful"
"Redux requires too much boilerplate code"
>
> 리덕스 패키지는 Redux 로직을 작성하는 표준 방법을 의도합니다. 기존의 세 가지 문제를 해결하기 위해 만들어졋습니다.
> Redux 저장소 구성이 너무 복잡하다.
> 패키지가 유용하지만 너무 많이 추가해야한다.
> 너무 많은 상용구 코드가 필요하다.
## [Terminology](https://redux.js.org/tutorials/essentials/part-1-overview-concepts)
### Actions
액션은 type field를 가진 일반 js 객체다.
type은 domain/eventName 형태를 가진다.
type 외에도 추가 정보가 있을 경우 payload로 나타낼 수 있다.

```js
const addTodoAction = {
  type: 'todos/todoAdded',
  payload: 'Buy milk'
}
```

### Action Creator
```js
const addTodo = text => {
  return {
    type: 'todos/todoAdded',
    payload: text
  }
}
```
Actions creator는 액션 객체를 반환하는 함수이다. 액션을 일일이 생성하기보다는 파라미터를 설정할 수 있는 creator를 더 많이 생성하게 된다.

### Reducer
state action 객체를 업데이트하는 방법을 명시하는 함수.
state를 인자로 받아 새로운 state를 반환한다.

명명법은 [domain]Reducer이다.
동작시킬 action type이 명시된 객체와 함께 state를 인자로 받는다.
```js
const initialState = { value: 0 }

function counterReducer(state = initialState, action) {
  // Check to see if the reducer cares about this action
  if (action.type === 'counter/increment') {
    // If so, make a copy of `state`
    return {
      ...state,
      // and update the copy with the new value
      value: state.value + 1
    }
  }
  // otherwise return the existing state unchanged
  return state
}
```

### store
redux 어플리케이션의 상태는 store 객체에 저장되어있다.

```js
import { configureStore } from '@reduxjs/toolkit'

const store = configureStore({ reducer: counterReducer })

console.log(store.getState())
// {value: 0}
```

### Dispatch
Dispatch는 state를 업데이트하는 유일한 방법이다. store는 reducer function을 실행실키고 새로운 state를 저장할 것이다. 

```js
store.dispatch({type: 'counter/increment'})

console.log(store.getState)
```
형식적으로 action creator를 생성해 호출함.
```js
const increment = () => {
  return {
    type: 'counter/increment'
  }
}

store.dispatch(increment())

console.log(store.getState())
// {value: 2}
```

### Selector
>Selectors are functions that know how to extract specific pieces of information from a store state value. As an application grows bigger, this can help avoid repeating logic as different parts of the app need to read the same data:


selectors는 store state에서 특정 부분의 정보를 추출하는 함수입니다.
```js
const selectCounterValue = state => state.value

const currentValue = selectCounterValue(store.getState())
console.log(currentValue)
// 2
```
store에서 사용할 데이터를 호출하는 함수를 selector에 정의하고 store.getState()로 state어서 사용할 수 있는 것 같다.

## [Dataflow](https://redux.js.org/tutorials/essentials/part-1-overview-concepts)

## Install


```js
npm install @reduxjs/toolkit

create-react-app에서 인자로 template을 줘 생성할 수 있다.
npx create-react-app app --template redux
```

## What's Included
툴킷에 포함된 API들은 다음과 같다.
> configurestore()
> wraps createStore to provide simplified configuration options and good defaults. It can automatically combine your slice reducers, adds whatever Redux middleware you supply, includes redux-thunk by default, and enables use of the Redux DevTools Extension.

createStore단순화된 구성 옵션과 좋은 기본값을 제공하기 위해 래핑 합니다. 자동으로 슬라이스 리듀서를 결합하고, 제공하는 모든 Redux 미들웨어를 추가 redux-thunk하고, 기본적으로 포함 하고, Redux DevTools Extension을 사용할 수 있습니다.

> createReducer(): that lets you supply a lookup table of action types to case reducer functions, rather than writing switch statements. In addition, it automatically uses the immer library to let you write simpler **immutable updates with normal mutative code, like state.todos[3].completed = true.**

> 기본의 Switch문으로 인자를 비교해서 실해던 방법대신 action types의 look up table로 적용할 수 있다. 또한 자동으로 immer liberary를 사용한다.
> 