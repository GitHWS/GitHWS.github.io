---
layout: single
classes: wide
title: "Redux Toolkit 사용하기"
categories: TIL
tag: [React, Redux, Redux-toolkit]
toc: true
author_profile: false
sidebar:
  nav: "docs"
---

## Redux-toolkit 사용하기

본격적으로 Redux-toolkit을 사용해보자.

### Redux-toolkit 패키지 설치하기

우선 Redux-toolkit 패키지 설치를 해야하기 떄문에 아래의 명령어로 설치하도록 한다.

```
npm install @reduxjs/toolkit
```

그리고 만약 기존의 Redux를 사용하고 있었다면 **기존의 Redux를 package.json의 dependency에서 제거**해주도록 하자.
Redux-toolkit을 설치하면 자동적으로 Redux를 포함하기 때문이다.

<br/>

### 전역 상태의 Slice 생성하기

우선 Redux-toolkit을 사용하려면 전역 상태의 Slice를 미리 생성해야한다.

**Slice**는 Redux-toolkit에서 서로 연관된 State끼리 묶어 데이터를 관리하는 방식이라고 생각하면 된다. <br/>
**만약 서로 직접적인 관계가 아닌 State가 여러 조각으로 나눠져 있다면 여러 개의 Slice를 생성해야한다.**

예를 들어 Auth와 관련된 State와 Counter와 관련된 상태가 있다면 Auth Slice와 Counter Slice를 각각 생성해야할 것이다.

> 하지만 해당 예제에서는 counter에 관련된 State만 담고 있어서 counter Slice 하나만 생성하면 된다.
>
> ```js
> // 카운터와 관련된 데이터이므로 하나의 Slice로 관리한다.
> { counter : 0, showCounter: true }
> ```

그래서 설치한 Redux-toolkit 패키지에서 `createSlice` 함수를 가져와서 호출한다.

`createSlice` 함수는 객체를 인자로 하는데 이 객체 안에 Reducer 함수에 있는 다양한 기능을 한번에 단순화시키는 함수이다.

#### createSlice 함수의 인자에 포함되어야 하는 프로퍼티

`createSlice`의 인자인 객체는 반드시 포함해야하는 프로퍼티가 있다.

- **`name`**
  : 해당 Slice의 이름, 즉 해당 <u>**Slice가 어떤 데이터를 담고 있는 Slice인지 식별할 수 있는 값을 말한다.**</u>

- **`initialState`**
  : Slice의 데이터가 갖는 <u>**초기 상태**</u>를 의미한다.

- **`reducers`**
  : Reducers는 <u>**액션에 따라 실행될 메서드를 추가하는 프로퍼티**</u>이다.

```js
createSlice({
  // name : Slice의 식별자
  name: 'counter',
  // initialValue : Slice의 데이터가 갖는 초기 상태
  initialValue: { counter: 0, showCounter: true },
  // reducers : 액션에 따라 실행될 메서드를 추가하는 프로퍼티
  reducers: {
    INCREMENT(){
      // ...
    }
    DECREMENT(){
      // ...
    }
    INCREASE(){
      // ...
    }
    TOGGLE(){
      // ...
    }
  }
})
```

#### **reducers 프로퍼티에서 메서드 내 State 업데이트를 직접 할 수 있다.**

이전에 우리가 Redux를 사용할 때 문제점이 있었는데 그것이 바로 반환하는 State가 '참조 타입'으로 직접 업데이트 시 참조 주소가 변경되어 에러를 발생시킬 수 있어 **데이터의 불변성**의 유지를 위해 수정을 바로 할 수 없었다.

하지만 Redux-toolkit에서는 데이터의 불변성을 유지하지 않고 직접 업데이트가 가능하다.<br/>
Redux-toolkit은 내부적으로 **immer**라는 패키지를 사용하는데 **이 패키지가 불변성을 지키지 않은 코드를 감지**하여 **자동으로 원래 있던 데이터를 복사하여 새로운 상태 객체로 생성하고 모든 데이터를 변경하지 못하게 유지한 후 우리가 변경한 데이터만 오버라이드**해준다.

이해하기 쉽게 순서대로 설명해보도록 하겠다.

만약 아래와 같은 reducer 메서드가 있다고 하자. 이 메서드는 Redux-toolkit의 immer 패키지로 인해 직접 State를 변경할 수 있다.

```js
// createSlice 함수 안의 reducers 프로퍼티의 메서드 "INCREMENT"
reducers : {
  INCREMENT(state){
    state.counter++;
  }
}
```

immer는 `INCREMENT` 메서드의 `state.counter++`의 불변성을 지키지 않은 코드를 감지한다.

그 다음 원래 있던 State를 복사하는데 처음엔 State의 초기값을 우선 복사한다. 복사된 State는 새로운 객체로 생성이 된다.

```js
// 기존의 State ==[복사]==> 데이터의 값이 동일한 새로운 객체로 생성
{ counter: 0, showCounter: true };
```

생성이 된 객체에 모든 데이터는 변경을 하지 못하게 유지하고 우리가 변경한 데이터만 새로운 객체로 생성된 State에 오버라이드한다.

```js
{ ...state, counter: state.counter++ };
```

그렇게 데이터의 업데이트가 완료된다.

#### 만약 reducers 프로퍼티에 있는 메서드에 페이로드(추가적인 데이터)가 필요하다면?

위에서 reducers 프로퍼티에 있는 메서드는 매개변수로 `state`와 `action` 객체를 전달받는다고 했었다.
이 `action`을 이용하여 페이로드를 사용할 수 있다.

```js
// reducers 메서드 "INCREASE"에서 action 객체를 사용하여 추가 데이터(amount)의 값을 사용하고 있다.
INCREASE(state, action){
  state.counter = state.counter + action.amount;
}
```

최종적인 createSlice가 전달받은 객체의 형태는 이러하다.

```js
createSlice({
  name: 'counter',
  initialValue: { counter: 0, showCounter: true },
  reducers: {
    INCREMENT(state){
      state.counter++;
    }
    DECREMENT(state){
      state.counter--;
    }
    INCREASE(state, action){
      state.counter = state.counter + action.amount;
    }
    TOGGLE(state){
     state.showCounter = !state.showCounter;
    }
  }
})
```

### Slice를 사용하여 State 연결하기

전역 상태의 Slice를 사용해보자.

우선 <u>**createSlice 함수의 반환값**</u>을 상수에 할당하여 사용할 수 있게 만든다.

```js
const counterSlice = createSlice({
  name: 'counter',
  initialValue: { counter: 0, showCounter: true },
  reducers: {
    INCREMENT(state){
      state.counter++;
    }
    DECREMENT(state){
      state.counter--;
    }
    INCREASE(state, action){
      state.counter = state.counter + action.amount;
    }
    TOGGLE(state){
     state.showCounter = !state.showCounter;
    }
  }
})
```

이제 이것을 Redux의 저장소인 `store`에 등록을 해야한다.<br>
`store`의 인자로 `counterSlice`의 `reducer` 프로퍼티를 전달한다.

```js
const store = createStore(counterSlice.reducer);
```

이 방법은 상태의 Slice가 많아졌을 때 사용하는 것은 좋지 않다. `createStore`는 하나의 Slice의 Reducer만 전달받기 때문이다.

그래서 Slice가 여러 개일때는 `createStore` 대신 Redux-toolkit의 `configureStore` 함수를 가져와서 호출하면 된다.

`configureStore` 함수도 `createStore`와 동일하게 store를 생성하는 역할을 한다. 여기서 차이점은 `configureStore` 함수는 **여러 개의 Reducer를 하나의 Reducer로 쉽게 합칠 수 있다**는 것이다.<br/>

그리고 여러 Slice를 합치기 때문에 인자를 **reducer 프로퍼티를 가지는 객체**로 받는다. 이것은 `configureStore` 함수에서 요구되는 설정이기 때문에 반드시 지켜주도록 한다.

```js
import { createSlice, configureStore } from "@reduxjs/toolkit";

const store = configureStore({
  reducer: counterSlice.reducer,
});
```

> 만약 다수의 Slice의 reducer가 있다면 reducer 프로퍼티에 객체 형식으로 추가한다. 그리고 Slice의 고유한 식별자를 key로 지정하여 접근할 수 있도록 한다.
>
> ```js
> const store = configureStore({
>   reducer: {
>     counter: counterSlice.reducer,
>     auth: authSlice.reducer,
>   },
> });
> ```

### action 객체를 전달하기

이제 `action` 객체를 전달해야하는데 우리는 Redux에서의 Reducer 함수의 if문도 없고 우리는 단지 메서드의 이름만 가지고 있어서 각 액션에 어떤 식별자가 호출되는지 모른다.

`action` 객체를 전달하는 것 또한 `createSlice`에서 할 수 있다.
`createSlice`는 서로 다른 reducer에 해당하는 **'고유한 액션 식별자'를 자동으로 생성**해준다.
그래서 액션 식별자를 생성하기 위해 시간을 들이지 않아도 된다.

이제 `action` 객체의 값을 얻기 위해 접근해보자.
우리가 생성했던 `counterSlice`의 `actions` 프로퍼티로 접근하면 `reducer` 필드에 접근할 수 있다.<br>
이 필드에 접근하면 우리가 추가한 메서드를 호출하여 `action` 객체를 생성할 수 있다.<br>

reducer 필드의 메서드를 호출하면 `action` 객체를 생성하기 때문에 이 메서드를 '액션 생성자'라고도 한다.

> 자동으로 생성되는 고유한 액션 식별자는 아래처럼 `action` 객체의 `type` 프로퍼티의 값으로 반환이 된다.
> 해당 `action` 객체는 반드시 `type` 프로퍼티를 가진다.
> <br>
>
> ```js
> // 만약 TOGGLE 메서드의 액션 생성자를 호출한다고 가정하면...
> counterSlice.actions.TOGGLE(); // { type : [자동 생성된 고유한 식별자명] }
> ```

또한 `action` 객체를 다른 컴포넌트에서 사용하기 위해 export를 해주도록 한다.

```js
export const counterActions = counterSlice.actions;
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/209472979-50bf4a83-14c3-4a28-8601-bcd635c6ff31.png" alt=""/>
</p>

### 다른 컴포넌트에서 action 객체 사용하기

export 했던 `counterActions`를 사용할 컴포넌트에서 import 하고

```js
import { counterActions } from "../store/index";
```

dispatch의 인자로 넣어주도록 하는데 <u>**메서드를 호출한 상태**</u>로 인자를 전달해야한다.<br>
왜냐하면 해당 액션 생성자는 반환하는 값이 dispatch할 `action` 객체이기 때문이다.

```js
const incrementHandler = () => {
  // dispatch({ type : [자동 생성 고유한 식별자명], counter: state.counter++ })
  dispatch(counterActions.INCREMENT());
};
```

#### 만약 액션 객체에 추가 데이터인 페이로드가 필요하다면?

액션 객체에 사용할 페이로드가 필요하다면 **호출하는 메서드의 인자로 데이터 타입에 상관없이 전달**하면 된다.<br>
단, <u>해당 인자로 전달된 값은 `action` 객체에 key가 `payload`로 생성이 된다.</u> <br>
<u>이 key명은 Redux-Toolkit에서 사용하는 필드명이기에 변경할 수 없다.</u>

그래서 페이로드의 값을 사용할 때도 `payload`라는 프로퍼티로 접근해야한다.

```js
const increaseHandler = () => {
  // INCREMENT 메서드의 인자로 10을 전달한다.
  dispatch(counterActions.INCREMENT(10)); // { type : [자동 생성된 고유한 식별자명], payload: 10}
};

// counterSlice의 INCREASE reducer
INCREASE(state, action){
  state.counter = state.counter + action.payload;
}
```

### Redux-Toolkit을 사용하지 않았을 때와의 비교

#### - Redux-Toolkit의 적용 여부에 따른 index.js(Redux의 Reducer함수와 Redux-Toolkit의 Slice 비교)

- Redux-Toolkit을 적용하지 않은 index.js(Reducer 함수)

```js
import { createStore } from "redux";

const initialState = { counter: 0, showCounter: true };

// Redux-Toolkit을 적용하지 않은 counterReducer 함수
const counterReducer = (state = initialState, action) => {
  if (action.type === "INCREMENT") {
    return { counter: state.counter + 1, showCounter: state.showCounter };
  }

  if (action.type === "INCREASE") {
    return {
      counter: state.counter + action.amount,
      showCounter: state.showCounter,
    };
  }

  if (action.type === "DECREMENT") {
    return { counter: state.counter - 1, showCounter: state.showCounter };
  }

  if (action.type === "TOGGLE") {
    return { counter: state.counter, showCounter: !state.showCounter };
  }

  return state;
};

const store = createStore(counterReducer);

export default store;
```

- Redux-Toolkit을 적용한 index.js(Slice)

```js
import { createSlice, configureStore } from "@reduxjs/toolkit";

const initialState = { counter: 0, showCounter: true };

// Redux-Toolkit을 적용한 counter Slice
const counterSlice = createSlice({
  name: "counter",
  initialState: initialState,
  reducers: {
    INCREMENT(state) {
      state.counter++;
    },
    DECREMENT(state) {
      state.counter--;
    },
    INCREASE(state, action) {
      state.counter += action.payload;
    },
    TOGGLE(state) {
      state.showCounter = !state.showCounter;
    },
  },
});

const store = configureStore({
  reducer: counterSlice.reducer,
});

export const counterActions = counterSlice.actions;

export default store;
```

보시다시피 Redux-Toolkit을 사용하여 Slice를 사용한 것의 코드가 더 가독성이 좋게 간결해진 것을 볼 수 있다.

#### - Redux-Toolkit을 적용 여부에 따른 Counter.js

- 적용하지 않은 Counter.js

```js
// Redux-Toolkit을 적용하지 않은 Counter.js
import { useSelector, useDispatch } from "react-redux";

import classes from "./Counter.module.css";

const Counter = () => {
  const dispatch = useDispatch();

  const counter = useSelector((state) => state.counter);
  const show = useSelector((state) => state.showCounter);

  const incrementHandler = () => {
    dispatch({ type: "INCREMENT" });
  };

  const increaseHandler = () => {
    dispatch({ type: "INCREASE", amount: 10 });
  };

  const decrementHandler = () => {
    dispatch({ type: "DECREMENT" });
  };

  const toggleCounterHandler = () => {
    dispatch({ type: "TOGGLE" });
  };

  return (
    // ...
  )
};

export default Counter;
```

- 적용한 Counter.js

```js
// Redux-Toolkit을 적용한 Counter.js
import { useSelector, useDispatch } from "react-redux";
import { counterActions } from "../store/index";

import classes from "./Counter.module.css";

const Counter = () => {
  const dispatch = useDispatch();

  const counter = useSelector((state) => state.counter);
  const show = useSelector((state) => state.showCounter);

  const incrementHandler = () => {
    dispatch(counterActions.INCREMENT());
  };

  const increaseHandler = () => {
    dispatch(counterActions.INCREASE(10));
  };

  const decrementHandler = () => {
    dispatch(counterActions.DECREMENT());
  };

  const toggleCounterHandler = () => {
    dispatch(counterActions.TOGGLE());
  };

  return (
    // ...
  )
};

export default Counter;
```

`action`의 `type` 프로퍼티의 값을 **자동으로 생성된 고유의 식별자명로 인해 번거롭게 직접 작성할 필요가 없으며 액션 생성자의 호출하는 reducer 메서드명에 따라 자동으로 생성된 고유한 식별자명을 가진 `action` 객체가 생성이 되기 때문에 식별자를 잘못 입력하는 실수를 예방할 수 있다.**

또한 페이로드의 추가도 액션 생성자의 호출하는 메서드의 인자로 전달하기 때문에 매우 간단해진 것을 볼 수 있다.
