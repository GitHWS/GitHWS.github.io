---
layout: single
classes: wide
title: "[22.12.23] bind() 메서드"
categories: TIL
tag: [JavaScript]
toc: true
author_profile: false
sidebar:
  nav: "docs"
---

## bind() 메서드는 무엇인가?


### bind() 메서드의 정의

MDN은 아래처럼 `bind()`의 정의를 내리고 있다.

```js
bind() 메소드가 호출되면 새로운 함수를 생성합니다.
받게되는 첫 인자의 value로는 this 키워드를 설정하고, 이어지는 인자들은 바인드된 함수의 인수에 제공됩니다.
```

```js
func.bind(thisArg[, arg1[, arg2[, ...]]])
```

**`bind()` 함수가 호출되면 새로운 바인딩한 함수를 생성(반환)한다.**<br/>
**바인딩한 함수는 원본 함수를 감싸는 역할**을 하며 이는 ECMAScript 2015에서 말하는 '특이 함수 객체(exotic function object)'이다.<br/>
이 <u>**바인딩된 함수를 호출하면 감싸진 원본 함수가 호출**</u>되게 된다.

`bind()`는 여러 개의 인자를 받을 수 있는데 첫번째 인자는 `this 키워드`이다.
두번째 인자는 새로 바인딩한 함수의 인자가 된다.

`bind()`의 핵심 역할을 하는 첫번째 인자는 **감싸는 원본 함수의 this로 사용할 객체**를 전달하면 된다.

### 예제

'모던 자바스크립트 Deep Dive'의 예제를 살펴보면서 정리해보도록 하자.

```js
function getThisBinding(){
  return this;
}
```

`getThisBinding`이라는 함수는 `this`를 반환하는데 여기서 `this`는 전역에 위치하는 함수로서 `window` 객체가 된다.

```js
const thisArg = { a : 1 };
```

하지만 우리는 `thisArg`를 `this`로 설정해서 사용하고 싶다.
그렇다면 아래처럼 `bind()` 메서드를 사용하여 `this`의 대상을 사용자 마음대로 설정할 수가 있다.

```js
console.log(getThisBinding.bind(thisArg)); // this가 thisArg로 바인딩된 getThisBinding 함수가 반환
```

`bind()` 메서드는 원본 함수를 감싸는 새로운 바인딩된 '**함수**'를 반환하기 때문에 `this`에 접근하려면 한번 더 호출해야한다.
예제에서는 `getThisBinding.bind(thisArg)`가 새로운 바인딩된 함수를 반환하고 이어서 뒤에 있는 `()`로 인해 호출되게 된다.

결과를 보면 `getThisBinding`의 호출로 인해 반환되는 값인 `this`가 `thisArg` 값으로 성공적으로 바인딩된 것을 볼 수 있다.
```js
console.log(getThisBinding.bind(thisArg)()); // { a : 1 }
```

### bind() 메서드, 어디에 쓰면 유용할까?

`bind()` 메서드는 메서드의 `this`와 메서드 **내부의 중첩 함수**나 **콜백 함수**의 `this`가 일치하지 않는 문제를 해결하는데 유용하게 사용할 수 있다.

아래의 예제를 보면 정확하게 알 수 있다.

```js
const person = {
  name: 'Lee',
  foo(callback){
    setTimout(callback, 100)
  }
}

person.foo(function(){
  console.log(`Hi, my name is ${this.name}.`);
})
```

우선 `person` 객체의 `this`는 `person`을 가리킨다.
하지만 `person.foo`의 콜백 함수가 일반 함수로 호출되면서 `this`는 전역 객체인 `window`를 가리키게 된다. 
두 경우의 `this`는 일치하지 않는다. 이렇게 일치하지 않으면 문백상 문제가 발생한다.

따라서 우리는 생각하지 않던 결과인 `'Hi, my name is .'`가 로그에 출력되게 된다.

이 문제를 해결하기 위해서 `bind()`를 사용하면 손쉽게 해결할 수 있다.

```js
// person.foo 메서드가 매개변수로 받은 callback 함수를 바인딩하여 사용할 this를 설정했다.
const person = {
  name: 'Lee',
  foo(callback){
    // 새로운 바인딩된 함수를 반환한다. 이 반환된 함수는 100밀리초 후 실행된다.
    setTimout(callback.bind(person), 100)
  }
}

person.foo(function(){
  console.log(`Hi, my name is ${this.name}.`);
})
```

`bind()` 메서드를 사용하여 매개변수로 들어오는 모든 콜백 함수의 `this`를 `person` 객체로 설정하여 `this`를 일치시켰다.
이로서 결과는 성공적으로 `Hi, my name is Lee.`가 출력이 된다.

