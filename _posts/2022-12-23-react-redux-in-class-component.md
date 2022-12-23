---
layout: single
classes: wide
title: "[22.12.23] 클래스형 컴포넌트에서 Redux 사용해보기"
categories: TIL
tag: [React, Redux]
toc: true
author_profile: false
sidebar:
  nav: "docs"
---

## 클래스형 컴포넌트에서 Redux 사용해보기

이전 포스트에서 함수형 컴포넌트인 Counter를 Redux의 저장소에서 데이터에 접근하여 사용까지 해봤었다.
물론 현재 클래스형 컴포넌트를 사용하는 것은 매우 드물다.

하지만 **완전히는 아니지만 여전히 클래스형 컴포넌트를 사용하는 곳도 있으니** 클래스형 컴포넌트에서는 Redux를 어떻게 사용하는지 알아보도록 하자.


### 클래스형 컴포넌트 Counter

클래스형 컴포넌트인 Counter를 생성하면 아래의 코드와 같다.
```js
import React from "react";

class Counter extends React.Component{
  
  incrementHandler(){};

  decrementHandler(){};

  toggleCounterHandler(){};

  render(){
    return (
      <main className={classes.counter}>
        <h1>Redux Counter</h1>
        <div className={classes.value}>-- Counter Value --</div>
        <div>
          <button onClick={this.incrementHandler}>Increment</button>
          <button onClick={this.decrementHandler}>Decrement</button>
        </div>
        <button onClick={this.toggleCounterHandler}>Toggle Counter</button>
      </main>
    )
  };
};

export default Counter;
```

가장 처음으로 이 컴포넌트가 Redux의 저장소에 구독을 하도록 설정해야한다.
그러기 위해선 `react-redux` 패키지에서 `connect` 함수를 가져온다.

`connect` 함수는 **클래스 기반 컴포넌트를 Redux에 연결**하는데 도움을 준다. 당연히 함수형 컴포넌트에서도 사용이 가능하지만 함수형 컴포넌트에서는 `connect`보다 커스텀 Hook을 사용하는 것이 편리하다.

```js
import React from "react";
import { connect } from "react-redux";
```

클래스형 컴포넌트에서 Redux를 사용할 때는 `connect` 함수를 export할 때 호출하면 된다.

```js
export default connect()(Counter);
```

여기서 `connect` 함수는 호출되면 함수를 반환하며 반환된 함수가 Counter 컴포넌트를 매개변수로 하여 다시 호출한다.
하지만 `connect` 함수에는 두 개의 매개변수가 필요하다.

첫번째 매개변수는 <u>Redux 저장소에서 사용할 데이터를 추출하는 **함수**</u>이며,
두번째 매개변수는 <u>Redux 저장소의 데이터를 업데이트하기 위한 Dispatch된 action 객체를 전달하는 **함수**</u>이다.
이전 포스트에서 `useSelector`와 `useDispatch`와 동일한 동작이라고 생각할 수 있다. 

> 이 두개의 매개변수는 `connect` 함수 다음에 호출되는 함수의 매개변수인 Counter 컴포넌트의 `props`가 된다. 


우선 첫번째 매개변수인 **저장소의 데이터 추출을 위한 함수**를 생성해보자.
이 함수가 반환하는 객체는 **저장소의 모든 데이터를 가져와서 원하는 데이터만 추출한 데이터**이다.

```js
const mapStateToProps = state => {
  return {
    counter : state.counter
  }
}
```

그 다음 두번째 매개변수인 **저장소의 데이터 업데이트를 위한 함수**를 생성해보자.

이 함수는 `dispatch` 함수를 인자로 내려받아 사용자가 정의한 메서드로 구성된 객체를 반환한다.

`dispatch` 함수를 사용할 때 `type` 프로퍼티를 포함시키는 `action` 객체를 Reducer 함수로 전달하여 `type`의 값으로 어떤 작업을 수행하여 업데이트할 것인지 설정한다.

```js
const mapDispatchToProps = dispatch => {
  return {
    increment : () => dispatch({ type : "INCREMENT" }),
    decrement : () => dispatch({ type : "DECREMENT" })
  }
}
```

위에 생성한 두가지 함수를 `connect` 함수의 인자로 넣어주도록 한다.

```js
export default connect(mapStateToProps, mapDispatchToProps)(Counter);
```

위처럼 작성했다면 클래스형 컴포넌트를 함수형 컴포넌트로 포현한다면 아래와 같다.
우리가 생성했던 함수의 반환값은 `connect` 함수가 호출된 다음 함수의 매개변수인 Counter 컴포넌트의 `props`가 된다.

```js
const Counter = { counter, increment, decrement } => {
  // ...
}
```

그럼 이제 위의 과정을 통해 얻은 props를 사용해보자.
여기서 생각해야할 점은 우리는 아직 Redux 저장소를 구독한다는 코드를 작성하지 않았다. 근데 저장소의 데이터를 가져올 수 있다.

왜냐하면 `connect` 함수를 호출하면 해당 컴포넌트의 자동으로 구독을 설정하기 때문이다.


이제 버튼에 `onClick` props를 사용하여 클래스의 메서드를 설정한다.
클릭 이벤트로 메서드를 호출하면 action 객체가 dispatch되어 Reducer 함수에 전달되어 저장소의 데이터가 업데이트된다.

여기서 메서드의 `this`는 `undefined`이기 때문에 `this`를 일치시키기 위해 각 메서드에 `bind(this)` 메서드로 `this`를 `Counter` 컴포넌트로 설정해준다.

```js
class Counter extends React.Component{

  // 버튼의 onClick에 함수를 설정한다.
  incrementHandler(){
    // 이렇게 props의 함수를 호출하면 action 객체가 dispatch되어 Reducer 함수에 전달된다.
    this.props.increment();
  }
  decrementHandler(){
    this.props.decrement();
  }

  toggleCounterHandler(){

  }

  render(){
    return (
      <main className={classes.counter}>
        <h1>Redux Counter</h1>
        <div className={classes.value}>{this.props.counter}</div>
        <div>
          <button onClick={this.incrementHandler.bind(this)}>Increment</button>
          <button onClick={this.decrementHandler.bind(this)}>Decrement</button>
        </div>
        <button onClick={this.toggleCounterHandler}>Toggle Counter</button>
      </main>
    );
  }
}
```

위처럼 코드를 완성하면 함수형인 Counter 컴포넌트의 동일하게 동작하는 것을 알 수 있다.

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/209334419-372c03e6-372c-4628-ac16-0a1ca020e5d4.gif" atl=""/>
</p>