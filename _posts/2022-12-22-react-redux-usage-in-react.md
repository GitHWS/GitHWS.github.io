---
layout: single
classes: wide
title: "[22.12.22] React에서 Redux 사용해보기"
categories: TIL
tag: [React, Redux]
toc: true
author_profile: false
sidebar:
  nav: "docs"
---

## React에서 Redux 사용해보기

[이전 포스트](https://githws.github.io/til/react-redux-usage/)에서 JavaScript에서 Redux를 사용해봤다.
이처럼 Redux는 React 뿐만 아니라 JavaScript에서도 활용할 수 있다.
자세히 말하자면 Redux는 **특정 프레임워크/라이브러리를 위한 서드 파티 라이브러리**가 아니다.

> 해당 포스트는 '카운터 기능’을 React 프로젝트 환경에서 예제로 하고 있습니다.

### React 프로젝트에서 Redux를 위한 설치

React 프로젝트에서 사용할 때에는 `redux` 뿐만 아니라 React를 위한 패키지인 `react-redux`를 하나 더 설치를 해야한다.
왜냐하면 Redux는 **React에 관해 알지 못하기 때문에 React에서 사용함을 알려주기 위함**이다.

```bash
npm install redux react-redux
```

`react-redux`를 설치함으로써 **React 애플리케이션이 Redux의 저장소에 컴포넌트를 구독(Subscribe)할 수 있고 Reducer에 쉽게 접근할 수 있다.**

### React 프로젝트에서 Redux를 위한 폴더/파일 생성하기

React 프로젝트에서 Redux의 하나의 저장소를 위한 `store` 폴더를 `src` 폴더 안에 생성한다.
그리고 해당 폴더 안에 Redux의 로직을 위한 파일 `index.js`도 생성해준다.

<p style="text-align:center">
	<img src="https://user-images.githubusercontent.com/96808980/209160431-ee25867c-de73-4d60-9c84-7acb98483499.png" alt=""/>
</p>

### 저장소 생성과 Reducer 함수 생성하기

Redux 로직을 위한 `store/index.js`파일에 저장소의 생성과 Reducer 함수를 생성한다.

우선 redux를 가져와서 저장소를 생성한다.
굳이 redux를 가져올 필요없이 **구조 분해**(Destructuring)를 하여 사용할 메서드만 가져와도 상관없다.

```js
// store/index.js

// Redux 가져오기
import { createStore } from "redux";

// Redux 저장소 생성
const store = createStore();
```

그리고 저장소의 데이터를 업데이트할 때 어떤 Reducer 함수를 사용할 것인지 알려주기 위해 생성한 Reducer 함수를 `createStore()` 메서드의 인자로 준다.

Reducer 함수의 첫번째 매개변수인 state에 잊지않고 기본 값을 설정해준다. 이 기본 값은 state의 초기 값이 된다.

```js
// store/index.js

// Redux 가져오기
import { createStore } from "redux";

// Reducer 함수 생성
const counterReducer = (state = { counter: 0 }, action) => {
  if (action.type === "INCREMENT") {
    return { counter: state.counter + 1 };
  }

  if (action.type === "DECREMENT") {
    return { counter: state.counter - 1 };
  }

  return state;
};

// Redux 저장소 생성 - Reducer 함수 인자로 전달
const store = createStore(counterReducer);
```

### React 앱과 Redux 저장소를 제공하기

Redux의 저장소가 만들어졌으니 이제 React 앱과 Redux를 연결해보자.
우리가 생성했던 `store/index.js` 파일의 로직은 하나의 저장소를 생성을 위한 것이다.
**생성한 저장소를 React 앱에서 접근하려면 export를 해야한다.**

```js
// store/index.js
export default store;
```

이제 저장소를 `store/index.js` 파일의 외부에서도 사용이 가능하다.
외부에서 사용하기 위해선 React 앱에 저장소를 제공을 해야하는데 최상단 루트에 위치한 `index.js`로 가보자.

```js
// index.js

import React from "react";
import ReactDOM from "react-dom/client";

import "./index.css";
import App from "./App";

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(<App />);
```

여기서 제공을 하려면 추가적으로 설치했던 `react-redux` 패키지에서 `Provider`컴포넌트를 가져와서 사용해야한다.

```js
// index.js

import React from "react";
import ReactDOM from "react-dom/client";
// Provider 컴포넌트를 react-redux 패키지로부터 가져온다.
import { Provider } from "react-redux";

import "./index.css";
import App from "./App";

const root = ReactDOM.createRoot(document.getElementById("root"));
// Provider 컴포넌트로 App 컴포넌트를 감싼다.
root.render(
  <Provider>
    <App />
  </Provider>
);
```

`Provider` 컴포넌트는 React의 `Context`를 사용할 때와 동일하게 `Provider`로 `App` 컴포넌트를 감싸서 **저장소를 사용할 영역을 설정**한다.
위의 코드처럼 `App` 컴포넌트를 감싸면 `App` 컴포넌트의 **모든 하위 컴포넌트에서 Redux 저장소에 접근할 수 있게 된다.**

하지만 여기까지만 하면 React는 Redux의 저장소가 있다는 것을 인지하지 못한다.
그래서 저장소를 가져와서 `Provider` 컴포넌트의 `store` props에 해당 저장소를 설정한다.
**이것은 `Provider` 컴포넌트가 감싸고 있는 영역에서 저장소 `store`를 사용하겠다는 의미이다.**

```js
// index.js

import React from "react";
import ReactDOM from "react-dom/client";
import { Provider } from "react-redux";

import "./index.css";
import App from "./App";
// Redux 저장소를 가져와서
import store from "./store/index";

const root = ReactDOM.createRoot(document.getElementById("root"));
// Provider 컴포넌트의 store props의 값으로 설정한다.
root.render(
  <Provider store={store}>
    <App />
  </Provider>
);
```

### Redux 데이터 사용하기

이제 `Counter` 컴포넌트에서 Redux 저장소의 데이터를 사용해보도록 하자.

```js
// ./components/Counter.js

import classes from "./Counter.module.css";

const Counter = () => {
  const toggleCounterHandler = () => {};

  return (
    <main className={classes.counter}>
      <h1>Redux Counter</h1>
      <div className={classes.value}>COUNTER VALUE</div>
      <button onClick={toggleCounterHandler}>Toggle Counter</button>
    </main>
  );
};

export default Counter;
```

#### 저장소 데이터를 받기 위한 커스텀 훅 useSelector 사용하기

외부의 컴포넌트에서 저장소의 데이터를 사용하려면 `react-redux` 패키지에서 커스텀 훅인 `useSelector`을 가져와야한다.
`useSelector`은 아래처럼 저장소가 관리하는 상태의 '부분'을 우리가 자동으로 선택할 수 있다.
`useSelector`를 호출할 때는 인자로 '함수'를 줘야하는데 이 함수는 **저장소에서 추출하려는 데이터 부분을 결정하여 잘라내는 역할을 한다.**
이 함수는 매개변수로 `state`를 가지는데 이것은 **Redux가 관리하는 모든 데이터를 넣어서 추출할 데이터만 결정**한다.

```js
// ./components/Counter.js

// 커스텀 훅 useSelector을 가져와서
import { useSelector } from "react-redux";
import classes from "./Counter.module.css";

const Counter = () => {
  // useSelector 호출하여 함수를 인자로 하여 저장소의 데이터에서 state.counter를 잘라냈다.
  // 잘라낸 데이터는 상수(const)에 할당하여 사용한다.
  const counter = useSelector((state) => state.counter);

  const toggleCounterHandler = () => {};

  return (
    <main className={classes.counter}>
      <h1>Redux Counter</h1>
      // 저장소에서 추출한 데이터 counter를 JSX 구문으로 사용한다.
      <div className={classes.value}>{counter}</div>
      <button onClick={toggleCounterHandler}>Toggle Counter</button>
    </main>
  );
};

export default Counter;
```

하지만 우리가 잊고 있었던 사실이 있다. **우리는 컴포넌트에 구독을 하지 않았다는 사실이다.**
구독을 하기 위한 `subscribe()` 메서드도 사용하지 않았다.
그런데 어떻게 Redux 저장소의 데이터를 사용할 수 있을까?

> 그 이유는 커스텀 훅 `useSelector`를 사용하게 되면 `react-redux`는 해당 컴포넌트를 Redux 저장소에 자동으로 구독시킨다.

그래서 Redux 저장소에서 데이터가 변경될 때마다 자동으로 업데이트를 하고 컴포넌트 함수가 재실행되어 최신 데이터의 값을 받게 된다.

#### 저장소 데이터를 업데이트하기 위한 커스텀 훅 useDispatch 사용하기

현재까지 저장소에 데이터를 사용하여 화면에는 초기 counter의 값인 0이 출력되어 있을 것이다.
이제 카운터 기능을 이용해서 1씩 증가/감소시켜보자.

우선 버튼을 눌렸을 때 클릭 이벤트가 감지되어 증가/감소 기능을 실행하기 위한 버튼을 만들어보자.

```js
// ./components/Counter.js

import { useSelector } from "react-redux";
import classes from "./Counter.module.css";

const Counter = () => {
  const counter = useSelector((state) => state.counter);

  const toggleCounterHandler = () => {};

  return (
    <main className={classes.counter}>
      <h1>Redux Counter</h1>
      <div className={classes.value}>{counter}</div>
      // 증가/감소 button을 추가했다.
      <div>
        <button>INCREMENT</button>
        <button>DECREMENT</button>
      </div>
      <button onClick={toggleCounterHandler}>Toggle Counter</button>
    </main>
  );
};

export default Counter;
```

그리고 버튼을 클릭했을 때 증가/감소하는 로직의 함수를 생성한다.
생성한 함수는 각 버튼에 `onClick` prop으로 설정한다.

```js
// ./components/Counter.js

import { useSelector } from "react-redux";
import classes from "./Counter.module.css";

const Counter = () => {
  const counter = useSelector((state) => state.counter);

  // 버튼 클릭 이벤트 시 호출되는 증가/감소 로직 함수
  const incrementHandler = () => {};
  const decrementHandler = () => {};

  const toggleCounterHandler = () => {};

  return (
    <main className={classes.counter}>
      <h1>Redux Counter</h1>
      <div className={classes.value}>{counter}</div>
      // onClick props에 증가/감소 로직 함수를 설정했다.
      <div>
        <button onClick={incrementHandler}>INCREMENT</button>
        <button onClick={decrementHandler}>DECREMENT</button>
      </div>
      <button onClick={toggleCounterHandler}>Toggle Counter</button>
    </main>
  );
};

export default Counter;
```

여기서 Redux 저장소에 있는 데이터를 업데이트하려면 `action` 객체가 필요하다고 했는데 이 객체는 `Dispatch`가 이루어졌을 때 생성이 된다.
그렇다면 **Dispatch**를 먼저 해야하는데 어떻게 할 수 있을까?

방법은 매우 간단하다.

> `react-redux` 패키지에서 또 다른 커스텀 훅 `useDispatch`를 가져온다.

`useDispatch` 훅은 `type` 프로퍼티가 있는 `action` 객체를 Reducer 함수에 전달하여 조건에 맞는 작업을 수행할 수 있게 돕는다.

`useDispatch`를 호출할 때는 아무런 인자를 전달하지 않고 상수에 할당하여 **실행할 수 있는 함수**를 반환한다.

```js
// ./components/Counter.js

// 커스텀 훅 useDispatch을 react-redux로부터 가져온다.
import { useSelector, useDispatch } from "react-redux";
import classes from "./Counter.module.css";

const Counter = () => {
  // 사용할 수 있는 함수 dispatch
  const dispatch = useDispatch();
  const counter = useSelector((state) => state.counter);

  const incrementHandler = () => {};
  const decrementHandler = () => {};

  const toggleCounterHandler = () => {};

  return (
    <main className={classes.counter}>
      <h1>Redux Counter</h1>
      <div className={classes.value}>{counter}</div>
      <div>
        <button onClick={incrementHandler}>INCREMENT</button>
        <button onClick={decrementHandler}>DECREMENT</button>
      </div>
      <button onClick={toggleCounterHandler}>Toggle Counter</button>
    </main>
  );
};

export default Counter;
```

그리고 dispatch 함수를 사용하여 각 함수에서 `type` 프로퍼티를 가진 `action` 객체를 `Dispatch`한다.

```js
// 각 action 객체가 Reducer 함수에 전달된다.
const incrementHandler = () => {
  dispatch({ type: "INCREMENT" });
};
const decrementHandler = () => {
  dispatch({ type: "DECREMENT" });
};
```

이제 버튼을 클릭하면 각 버튼의 `onClick` prop에 설정한 함수가 실행이 되면서 Reducer 함수에 `action` 객체가 전달된다.
이 객체의 `type` 프로퍼티의 고유한 값으로 조건문을 거쳐 작업을 수행하여 `state`의 최신값을 얻게 된다.

```js
// Reducer 함수의 조건문을 통해 반환하는 상태 값이 달라진다.
const counterReducer = (state = { counter: 0 }, action) => {
  if (action.type === "INCREMENT") {
    return { counter: state.counter + 1 };
  }

  if (action.type === "DECREMENT") {
    return { counter: state.counter - 1 };
  }

  return state;
};
```

### Reducer 함수를 통해 반환되는 State에서 명심해야할 점

#### 1. Reducer가 반환하는 State는 완전히 새로운 객체로 '교체'된다.

Reducer 함수는 `action`객체를 받아 `type` 프로퍼티의 고유한 값으로 수행할 업데이트 작업이 결정된다.
작업이 완료된 후 최신으로 업데이트된 State를 반환하는데 여기서 주의해야할 점이 있다.

> Reducer 함수를 통해 반환되는 객체(최신의 State)는 업데이트되는 데이터만 업데이트하지 않고 **"객체 자체를 교체"한다.**

즉, 기존의 존재하던 기존의 State에서 변경하는 부분만 변경하는 것이 아닌 **기존의 객체를 변경된 데이터를 추가/업데이트하여 참조하는 주소가 다른 완전히 새로운 객체로 교체**하여 반환한다.

#### 2. 절대 State를 직접 변경해서는 안된다.

절대 기존(원본)의 State를 아래와 같은 예시처럼 직접 변경해서는 안된다.

```js
// Reducer 함수 중...
if (action.type === "INCREMENT") {
  // 이렇게 직접 수정 절대 금지!
  state.counter++;

  return { counter: state.counter };
}
```

State는 '객체'이다. 즉 '참조 타입'으로 뜻하지 않게 기존의 State를 재정의하거나 변경하기 쉽기 때문에 예를 들어 같은 값을 비교할 때 참조하는 주소가 달라지면서 문제가 발생할 수 있다.

그래서 반드시 Reducer 함수가 반환하는 State는 항상 새로운 State 객체로 대체하여 반환하는 것이 올바른 State 사용법이다.

### 추가) 만약 페이로드를 추가하고 싶다면?

만약 추가적인 페이로드를 추가하고 싶다면 전달하는 Reducer에 전달할 `action`객체에 프로퍼티로 추가하고 Reducer 함수에서 반환값에서 `action` 객체에 접근하여 사용하면 된다.

```js
// Reducer 함수
const counterReducer = (state = { counter: 0 }, action) => {
  // 만약 5씩 카운터가 증가하는 기능이라면 아래처럼 페이로드를 추가한다.
  if (action.type === "INCREMENTBY5") {
    return { counter: state.counter + action.amount };
  }

  // ...
};

// dispatch하는 action 객체
const incrementHandler = () => {
  dispatch({ type: "INCREMENT", amount: 5 });
};
```

## TIL 핵심 정리

- 커스텀 훅 `useSelector` : 'react-redux' 패키지의 커스텀 훅으로 **저장소의 전체적인 데이터를 가져와서 추출하고자 하는 데이터만 잘라서 사용할 수 있는 커스텀 훅**이다.
  `useSeletor`는 인자로 **함수**가 들어가는데 이때 매개변수인 `state`는 **Redux 저장소의 모든 데이터를 가져오며** 그 중 원하는 데이터만 추출한다.

```js
const counter = useSeletor((state) => state.counter);
```

- 커스텀 훅 `useDispatch` : 'react-redux' 패키지의 커스텀 훅으로 **저장소의 데이터를 업데이트하기 위해 dispatch 함수를 사용하여 action 객체를 Reducer 함수에 전달하는 커스텀 훅**이다.
  dispath 함수를 사용할 때 전달한 `action` 객체에는 고유한 값을 가진 `type` 프로퍼티가 있어야한다.

```js
// 아무런 인자없이 사용하는 대신 사용할 수 있는 dispatch 함수를 상수에 할당한다.
const dispatch = useDispatch();

const func = () => {
  dispatch({ type: "INCREMENT" });
};
```
