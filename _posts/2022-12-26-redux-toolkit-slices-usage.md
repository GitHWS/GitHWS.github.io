---
layout: single
classes: wide
title: "[22.12.26] 여러 Slice를 사용해보기"
categories: TIL
tag: [React, Redux, Redux-toolkit]
toc: true
author_profile: false
sidebar:
  nav: "docs"
---

## 여러 Slice를 사용해보기

> 해당 포스트는 여러 Slice를 사용하는 것에 중점을 두고 있어 React 앱에 대한 기능 설명이 적습니다.

[이전 포스트](https://githws.github.io/til/redux-toolkit-usage/)에서는 `counterSlice`만을 사용하여 Counter 기능을 구현해봤다.

그리고 Redux의 단 하나뿐인 저장소에 `configureStore` 함수를 사용하여 Slice를 등록하여 사용했었다.
`configureStore` 함수는 여러 개의 Reducer들을 하나로 합치는 역할을 하는데 이번 포스트에서 `authSlice`를 추가하여 등록하여 사용해보도록 하자.

### authSlice 추가하기

추가한 `authSlice`는 로그인/로그아웃 상태를 업데이트하는 State를 담고 있다.

```js
// store.js

// Counter Slice
const counteInitState = { counter: 0, showCounter: true };
const counterSlice = createSlice({
  name: "counter",
  initialState: counteInitState,
  reducers: {
    increment(state) {
      state.counter++;
    },
    increase(state, action) {
      state.counter = state.counter + action.payload;
    },
    decrement(state) {
      state.counter--;
    },
    toggleCounter(state) {
      state.showCounter = !state.showCounter;
    },
  },
});

// Auth Slice
const authInitState = { isAuth: false };

const authSlice = createSlice({
  name: "authenticated",
  initialState: authInitState,
  reducers: {
    LOGIN(state) {
      state.isAuth = true;
    },
    LOGOUT(state) {
      state.isAuth = false;
    },
  },
});
```

위의 코드와 같이 `authSlice`를 추가하여 `counterSlice`를 포함하여 총 두 개의 Slice가 있다.

이것을 `configureStore`의 객체 형태의 인자에 `reducer` 프로퍼티에 객체로 추가한다. <br/>
추가를 했다면 이제 다른 컴포넌트에서 dispatch를 통해 `action` 객체를 전달받기 위해 위해 Slice의 `actions` 프로퍼티에 접근하여 export 해준다.

```js
// store.js

const store = configureStore({
  // reducer 프로퍼티의 값을 객체로 하면 다수의 Slice의 Reducer를 추가할 수 있다.
  reducer: {
    counter: counterSlice.reducer,
    auth: authSlice.reducer,
  },
});

export const counterActions = counterSlice.actions;
export const authActions = authSlice.actions;

export default store;
```

<br/>

### action 객체 전달하기

사용하는 것은 다른 컴포넌트에서 `authActions`와 `useDispatch`를 사용하여 action 객체를 전달할 수 있다.

`Auth.js` 컴포넌트 파일에서는 `authActions`와 `useDispatch`를 사용하여 form을 제출하면 `authSlice`의 Reducer인 `LOGIN`을 호출해서 로그인 상태를 업데이트하고 있다.

```js
// Auth.js

import { useDispatch } from "react-redux";
import { authActions } from "../store/auth";

import classes from "./Auth.module.css";

const Auth = () => {
  const dispatch = useDispatch();

  const loginHandler = (event) => {
    event.preventDefault();
    dispatch(authActions.LOGIN());
  };

  return (
    <main className={classes.auth}>
      <section>
        <form onSubmit={loginHandler}>
          <div className={classes.control}>
            <label htmlFor="email">Email</label>
            <input type="email" id="email" />
          </div>
          <div className={classes.control}>
            <label htmlFor="password">Password</label>
            <input type="password" id="password" />
          </div>
          <button>Login</button>
        </form>
      </section>
    </main>
  );
};

export default Auth;
```

`Header.js` 컴포넌트 파일 또한 `authActions`와 `useDispatch`를 사용하여 Logout 버튼을 클릭하면 `authSlice`의 Reducer인 `LOGOUT`을 호출해서 로그인 상태를 업데이트하고 있다.

그리고 `useSelector` Hook을 사용하여 `isAuth` 데이터를 추출하여 State에 따라 조건부 렌더링을 하고 있다.

```js
// Header.js

import { useSelector, useDispatch } from "react-redux";
import { authActions } from "../store/auth";

import classes from "./Header.module.css";

const Header = () => {
  const isAuth = useSelector((state) => state.auth.isAuth);
  const dispatch = useDispatch();

  const logoutHandler = () => {
    dispatch(authActions.LOGOUT());
  };

  return (
    <header className={classes.header}>
      <h1>Redux Auth</h1>
      {isAuth && (
        <nav>
          <ul>
            <li>
              <a href="/">My Products</a>
            </li>
            <li>
              <a href="/">My Sales</a>
            </li>
            <li>
              <button onClick={logoutHandler}>Logout</button>
            </li>
          </ul>
        </nav>
      )}
    </header>
  );
};

export default Header;
```

`App.js` 컴포넌트에서는 `useSelector` Hook을 사용하여 저장소에서 `isAuth`의 데이터를 추출하여 `Auth`와 `UserProfile` 컴포넌트를 조건부 렌더링하고 있다.

```js
import { useSelector } from "react-redux";

import Header from "./components/Header";
import UserProfile from "./components/UserProfile";
import Auth from "./components/Auth";
import Counter from "./components/Counter";

function App() {
  const isAuth = useSelector((state) => state.auth.isAuth);

  return (
    <>
      <Header />
      {!isAuth && <Auth />}
      {isAuth && <UserProfile />}
      <Counter />
    </>
  );
}

export default App;
```

여기서 `useSelector`를 사용한 코드를 보면 길어진 것을 볼 수 있다.

```js
// Header와 Auth 컴포넌트에서 사용된 useSelector이다.
// state의 auth의 isAuth의 데이터를 추출한다.
const isAuth = useSelector((state) => state.auth.isAuth);
```

이것은 여러 개의 `Slice.reducer`를 사용하면 그렇다.

하나의 `Slice.reducer`를 사용하면 아래처럼 store에 등록이 되기 때문에 `state`의 `isAuth`에 바로 접근할 수 있다.

```js
const store = configureStore({
  reducer: authSlice.reducer,
});

const isAuth = useSelector((state) => state.isAuth);
```

하지만 여러 개의 `Slice.reducer`를 사용하면 store에 등록할 때 `Slice.reducer`의 **식별자**로 key를 작성해야하기 때문에 `useSelector`를 사용할 때 데이터에 접근해야하는 과정이 길어진다.

```js
const store = configureStore({
  reducer: {
    counter: counterSlice.reducer,
    auth: authSlice.reducer,
  },
});

// state.auth.isAuth는 [저장소의 전체 데이터(state)] > [authSlice의 reducer(auth)] > [추출 데이터(isAuth)]로 접근이 가능하다.
const isAuth = useSelector((state) => state.auth.isAuth);
```

<br/>

### Slice별 파일 분기

Redux는 하나의 저장소를 가지는데 하나의 파일에 다수의 Slice가 있다면 코드를 읽기도 힘들다.<br>
그래서 이것을 Slice를 개별적인 파일로 분기하여 관리의 편의를 증가시키도록 한다.

```js
// counter.js

import { createSlice } from "@reduxjs/toolkit";

// Counter Slice
const counteInitState = { counter: 0, showCounter: true };
const counterSlice = createSlice({
  name: "counter",
  initialState: counteInitState,
  reducers: {
    increment(state) {
      state.counter++;
    },
    increase(state, action) {
      state.counter = state.counter + action.payload;
    },
    decrement(state) {
      state.counter--;
    },
    toggleCounter(state) {
      state.showCounter = !state.showCounter;
    },
  },
});

export const counterActions = counterSlice.actions;

// 보통 Slice 자체를 export하지 않고 reducer 프로퍼티에 접근하여 export한다.
export default counterSlice.reducer;
```

```js
// auth.js

import { createSlice } from "@reduxjs/toolkit";

// Auth Slice
const authInitState = { isAuth: false };
const authSlice = createSlice({
  name: "authenticated",
  initialState: authInitState,
  reducers: {
    LOGIN(state) {
      state.isAuth = true;
    },
    LOGOUT(state) {
      state.isAuth = false;
    },
  },
});

export const authActions = authSlice.actions;

// 보통 Slice 자체를 export하지 않고 reducer 프로퍼티에 접근하여 export한다.
export default authSlice.reducer;
```

분기한 Slice 파일의 `Slice.reducer`를 가져와서 store 파일에서 사용하면 하나의 저장소를 유지하면서 개별적인 Slice 파일을 관리할 수 있다.

```js
import { configureStore } from "@reduxjs/toolkit";

// 각 Slice 파일에서 export된 Slice.reducer를
import counterReducer from "./counter";
import authReducer from "./auth";

const store = configureStore({
  reducer: {
    // 여기에 사용!
    counter: counterReducer,
    auth: authReducer,
  },
});

export default store;
```

그리고 파일을 분기하면서 **파일의 경로가 변경되었기 때문에 경로를 수정**하는 것으로 마무리하면 된다.
