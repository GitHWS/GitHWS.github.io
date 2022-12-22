---
layout: single
classes: wide
title: "[22.12.22] JavaScript에서 Redux 사용해보기"
categories: TIL
tag: [React, Redux]
toc: true
author_profile: false
sidebar:
  nav: "docs"
---

## JavaScript 환경에서 Redux 사용해보기

Redux는 JavaScript 환경에서도 사용이 가능한데 React에서 무작정 사용하는 것보다 JavaScript에서 연습을 해보고 넘어가도록 하자.

> 해당 포스트는 '1씩 증가/감소 기능'을 Node 환경에서 실행하는 예제로 하고 있습니다.

### Redux 설치
우선 Redux를 사용하기 위해 설치를 해야하므로 아래의 명령어를 실행하여 설치한다.
```bash
npm install redux
```
설치를 하면 package.json의 `dependency`에 redux가 존재할 것이다.

Node.js 환경에서 JavaScript 파일을 실행시킬 것이기 때문에 Node.js의 기본 import 구문으로 redux를 가져온다.
```js
const redux = require("redux");
```

### Redux 데이터 저장소 생성
이제 Redux를 가져왔으니 가장 첫번째로 할 일은 **'저장소 생성'**이다.
Redux는 **하나의 저장소에 애플리케이션의 모든 데이터를 저장**한다.

가져온 Redux의 메서드인 `createStore()`를 사용하여 저장소를 생성한다.
그리고 저장소로서 사용하기 위해 상수(const)에 할당하도록 한다.
```js
const store = redux.createStore();
``` 

여기서 중요한 것은 `createStore()` 메서드는 **인자**로 **Reducer 함수**를 가진다.
왜냐하면 **어떤 Reducer 함수가 해당 저장소를 업데이트하는지 알려주기 위함**이다.

그래서 다음으로 Reducer 함수를 생성한다.

### Reducer 함수 생성
Reducer 함수는 아래와 같이 작성한다.
```js
const counterReducer = (state = { counter: 0 }, action) => {
	if (action.type === "INCREMENT") {
		return { counter: state.counter + 1 }
	}

	if (action.type === "DECREMENT") {
		return { counter: state.counter - 1 };
	}

	return state;
};
```

Reducer 함수를 살펴보면 두 개의 매개변수를 가지는데,
첫번째 매개변수는 **기존의 State의 값**을 가리키는 `state`과  두번째 매개변수는 **Dispatch된** `action` **객체**이다.
`state` 매개변수에는 기본값을 설정해야한다.

조건문을 통해 **`action.type`의 값에 따라 반환되는 값이 달라지고 있다**는 것을 알 수 있다.  그리고 만약 위의 조건문을 모두 만족하지 않을 시 기존의 State 값을 그대로 반환하고 있다.

그렇다.
Reducer 함수는 저장소 내 데이터를 업데이트를 담당한다고 했었다. 
**`action` 객체는 컴포넌트의 Dispatch로 인해 생성이 되는데 생성된 `action` 객체를 Reducer 함수에 전송함으로써 저장소 업데이트를 위해 어떤 작업을 해야할지 알려준다.**

Reducer 함수가 작성이 완료되었다면 `createStore()` 메서드의 인자로 넣어주도록 한다.
```js
const store = redux.createStore(counterReducer);
```

### 저장소를 구독(Subscribe)
컴포넌트가 해당 저장소의 데이터를 가져가서 사용하려면 **구독**를 해야한다.
그래서 아래처럼 **구독 함수를 생성하고 Redux에게 어떤 구독 함수를 사용하는지 인식시켜야한다.**

```js
const counterSubscriber = () => {
	const latesState = store.getState();
	console.log(latesState);
}
```
여기서 **구독 함수는 상태가 업데이트가 될 때마다 트리거**된다.
그리고 저장소의 `getState()` 메서드 호출로 데이터의 업데이트 후 최신 상태의 스냅샷을 제공한다.

이후에 Redux에게 구독 함수를 인식시켜야하는데 `subscribe()` 메서드의 매개변수로 구독 함수를 주면 된다.
```js
store.subscribe(counterSubscriber);
```

### action 객체를 생성하여 Reducer 함수로 전송
`action` 객체는 컴포넌트에서 Dispatch의 과정을 거치면 생성된다고 했다.
그럼 어떻게 Dispatch를 시킬까?

해당 저장소의 `dispatch()` 메서드를 사용하여 Dispatch를 한다.
```js
store.dispatch({ type: "INCREMENT" });
store.dispatch({ type: "DECREMENT" });
```

`dispatch()` 메서드는 인자로 `action` 객체를 가지는데 이 `action` 객체는 기본적으로 `type` 프로퍼티가 존재하고 **'고유의 값'**이 필요로 한다.
이 `action` 객체의 경우 `type`의 고유한 값을 통해 Reducer 함수의 작업을 결정시킬 수 있다.
우리가 위에서 봤던 **Reducer 함수의 조건문**처럼 말이다.
```js
// Reducer 함수 내 조건문
if (action.type === "INCREMENT") {
	return { counter: state.counter + 1 }
}

if (action.type === "DECREMENT") {
	return { counter: state.counter - 1 };
}
```

즉 전달된 `action` 객체의 `type`의 값이 `INCREMENT`일 때 기존 상태의 `counter` 값에 1을 증가시키고, `DECREMENT`일 때 기존 상태의 `counter` 값에 1을 감소시키는 작업이다.

### 실행 결과
이제 Node에서 실행해보면 아래와 같은 결과가 나온다.
```bash
{ counter: 1 }
{ counter: 0 }
```

이 결과는 아래의 코드가 순서대로 실행이 구독 함수에 작성했던 `console.log()`가 실행이 되면서 발생한 값이다.

`{ counter : 1 }`은 기존의 state의 값인 기본값 `0`에 +1이 된 값이며,
`{ counter : 0 }`은 `dispatch({ type : "INCREMENT" })`로 인해 업데이트된 state 값인 `1`에서 -1이 된 값이다.


### 전체 예제 코드

```js
// redux 가져오기
const redux = require("redux");

// 저장소의 생성하기
const store = redux.createStore(counterReducer);

// Reducer 함수 생성하기
const counterReducer = (state = { counter: 0 }, action) => {
	if (action.type === "INCREMENT") {
		return { counter: state.counter + 1 }
	}

	if (action.type === "DECREMENT") {
		return { counter: state.counter - 1 };
	}

	return state;
};

// 구독 함수 생성하기
const counterSubscriber = () => {
	const latesState = store.getState();
	console.log(latesState);
}

// Redux에게 구독 함수 인식시키기
store.subscribe(counterSubscriber);

// type 프로퍼티를 가진 action 객체를 Reducer 함수에 전달하기
store.dispatch({ type: "INCREMENT" });
store.dispatch({ type: "DECREMENT" });
```
