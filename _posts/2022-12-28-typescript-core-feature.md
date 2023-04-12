---
layout: single
classes: wide
title: "TypeScript의 핵심 기능"
categories: TIL
tag: [TypeScript]
toc: true
author_profile: false
sidebar:
  nav: "docs"
---

> React 프로젝트를 위한 TypeScript 기초 포스트입니다. TypeScript의 세부적인 내용은 다루지 않습니다.

## TypeScript의 핵심 기능

TypeScript는 여러 가지의 기능을 가지고 있다. 이제부터 TypeScript의 기능에 대해서 알아보도록 하자.

### 타입 추론(Type Inference)

기본적으로 TypeScript는 가능한 많은 타입을 유추하려고 한다. 즉 명시적인 타입 표기가 없어도 어떤 타입을 어디에서 사용할지 알아내려고 한다는 것이다.

그것은 '타입 추론'이라고 하는데 말그대로 '지정할 타입을 TypeScript가 추론'하는 것이다.<br/>
예시를 살펴보도록 하자.

이전에 봤었던 number 타입을 지정한 변수 `age`를 가져와보자.
이전 예제에서는 타입을 표기하여 age 변수가 어떤 타입의 값을 받아야하는지 명시하고 있다.

```js
// 타입 표기 기능 사용 시
let age: number;
age = 23;
```

하지만 타입 추론 기능을 사용하면 아래처럼 타입 표기 기능을 사용하지 않고도 가능하다.

```js
let age = 23;
```

위의 코드의 결과는 아래처럼 타입 표기가 자동으로 되어있는 것을 볼 수 있다.

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/209821012-fa67df63-4c43-4c55-aa02-6c3e85186692.png" alt=""/>
</p>

그 이유는 타입 표기가 없을 때 타입 추론의 과정에서 알 수 있다.

#### - 타입 표기가 있을 때 (명시적 타입 지정)

타입 표기가 있을 때 아래의 과정을 거쳐 타입 추론을 하게 된다.

타입 표기의 코드를 탐색하고 발견 시 타입 표기에 작성된 타입을 지정하면 타입 지정이 끝난다.

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/209821826-7b467689-ca32-4640-a1d3-2f032e7ca182.png" alt=""/>
</p>

#### - 타입 표기가 없을 때 (타입 추론 기능)

그럼 타입 표기가 없을 때는 어떤 과정을 거쳐서 추론하게 될까?

우선적으로 타입 표기 코드를 탐색하고 발견하지 못했을 때 **할당된 값**의 타입을 분석하여 이 값의 타입으로 지정한다.

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/209822327-8fcc2d43-b1fb-470b-8732-c051fe5e3b06.png" alt=""/>
</p>

<br/>

#### 타입 추론을 사용하면서 얻는 이점은 무엇일까?

**타입 추론을 활용하면 불필요한 타입 지정을 할 필요가 없어지기 때문에 작성할 코드를 줄여준다.**<br>
그래서 타입 추론 기능을 활용하여 코드를 작성하는게 권장되는 방식이라고 한다.

당연히 명시적 타입 지정을 사용하면 타입 지정이 된 것을 명확하게 볼 수 있다.
단, 명시적 타입 지정을 하는 것은 타입 추론으로 제거할 수 있는 타입 표기 코드를 불필요하게 작성하기 때문에 필요할 때만 사용하는 것을 권장한다.

타입 추론을 거쳐서 타입이 지정었을 때 다른 타입의 값을 할당해도 동일하게 컴파일 에러를 출력한다.

<br>

### 유니온 타입

유니온 타입은 만약 타입을 여러 개 지정하고 싶을 때 사용한다.<br/>
즉, 유니온 타입을 사용하면 값과 타입을 유연하게 정의할 수 있게 된다.

사용법으로는 `|`를 사용하여 여러 개의 타입을 지정할 수 있다.

```js
// id 변수에 string과 number 타입이 지정되었다.
let id: string | number;

// 두 경우 모두 컴파일 에러가 발생되지 않는다.
id = "githws";
id = "1234";

// 지정되지 않은 타입인 boolean은 컴파일 에러가 발생한다.
id = true;
```

여기서 알 수 있는 것은 유니온 타입은 반드시 명시적으로 타입을 지정해야하기 때문에 **타입 추론을 사용할 수 없다.**

<br/>

### 타입 별칭(Type Aliases)

`type` 키워드를 사용하여 기본적인 타입을 생성하고 복잡한 타입을 정의하여 '별칭'으로 사용하는 기능이다.

타입 별칭 기능을 사용할 사용자 정의 타입은 `:`가 아닌 `=`로 지정할 수 있다.

```js
type Children = {
  name: string,
  age: number,
};
```

위의 `Children` 타입을 변수에 지정해보면 아래와 같이 할 수 있다.

```js
// Children은 객체로 객체 타입만 가능
let boy: Children;
// Children은 객체로 객체를 엘리먼트로 가지는 배열 타입만 가능
let girls: Children[];

// 컴파일 에러 없음
boy = {
  name: "Max",
  age: 14,
};

// 컴파일 에러 없음
girls = [
  { name: "Ann", age: 16 },
  { name: "Amy", age: 15 },
];

// 컴파일 에러 발생 - 지정된 프로퍼티도 모두 존재해야 에러가 발생하지 않는다.
girls = [{ name: "Julie" }];
```

타입 별칭 기능을 이용하면 동일한 타입을 반복해서 작성할 필요없이 하나의 커스텀 타입으로 생성하여 재사용할 수 있다는 것이 장점이다.

<br/>

### 타입을 가지는 함수

#### - 반환값이 있는 함수

TypeScript는 함수의 반환 값이 가지는 타입을 통해 함수 타입을 추론한다.

아래의 더하기 함수를 보도록 하자.

```js
function add(a: number, b: number) {
  return a + b;
}
```

위의 더하기 함수는 타입이 `number`로 지정된 매개변수 `a`, `b`의 더하기 연산을 통해 반환값이 결정된다.

그러니 `number`와 `number`를 더하면 당연히 `number` 타입이 나오기에 이 함수의 타입은 `number`가 된다.

이 함수에 커서를 올렸을 때 나오는 툴팁의 가장 끝부분이 해당 함수의 타입이자 반환값 타입이다.

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/209828302-fd098442-af60-4953-b9df-e95749700434.png" alt=""/>
</p>

함수는 입력 뿐만 아니라 출력도 있기 때문에 함수에서 타입을 사용할 때는 매개변수의 타입뿐만 아니라 반환값의 타입도 생각을 해야한다.

#### - 반환값이 있는 함수

반환값이 있는 함수가 있다면 반환값 이 없는 함수도 있을 것이다.<br/>
반환값이 없는 함수의 타입은 특별한 타입인 `void`로 지정이 된다.

`void`는 `undefined`와 `null`과 비슷한 맥락의 타입인데 항상 함수와 사용한다는 것이 특징이다.

```js
function print(value: any) {
  console.log(value);
}
```

위의 함수는 반환값이 없는 함수이기 때문에 `void` 타입으로 지정된 것을 볼 수 있다.

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/209829001-e44e4e69-14cb-413b-8f67-d38ecd857ea0.png" alt=""/>
</p>

<br/>

### 제네릭(Generic)

> 나는 내가 이해한 대로 작성을 해보고 추후에 수정하는 식으로 작성을 하려고 한다.

제네릭은 TypeScript에서 **매우 중요한 기능**이면서 이해하기 난해한 기능 중 하나이다.

우선 아래의 함수를 살펴보자.<br/>
아래의 함수는 배열(array)과 값(value)을 인자로 받아 새로운 배열을 생성하여 반환하는 함수이다.

이 함수에서 매개변수는 모두 `any`로 타입이 지정되어 있다. 그러므로 해당 함수의 반환값 또한 `any` 타입이 된다.

```js
function insertAtBeginning(array: any, value: any) {
  const newArray = [value, ...array];

  return newArray;
}
```

그런데 여기서 매개변수 `array`, `value`에 들어갈 값을 생성해서 어떻게 되는지 알아보도록 하자.

```js
function insertAtBeginning(array: any, value: any) {
  const newArray = [value, ...array];

  return newArray;
}

const demoArray = [1, 2, 3];

// insertAtBeginning(demoArray: number[], -1: number);
insertAtBeginning(demoArr, -1);
```

첫번째 매개변수로 들어갈 `demoArray`의 타입은 타입 추론으로 인해 `number[]`가 된다.
두번째 매개변수로 들어갈 value는 `-1`로 `number`가 된다.

이제 함수 호출의 결과는 변수에 담아서 확인을 해보면 이상한 결과가 나온다.

```js
const updateArray = insertAtBeginning(demoArray, -1);
```

우리는 분명 `number[]`와 `number`인 매개변수를 넣어 반환값을 기대하면 `number[]`가 되야하는데 왜 `any[]`로 타입이 지정이 되었을까?

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/209830416-c7b41be3-c079-440d-9070-60b922c8f9f5.png" alt=""/>
</p>

그 이유는 `any` 타입의 영향 때문이다.
`any`는 TypeScript의 예비적인 타입으로 모든 값의 타입이 값으로 전달될 수 있다.

현재 `any`가 두 개의 모든 매개변수의 타입으로 지정이 되어있기 때문에 `number[]`와 `number`라는 타입을 인지하지 못하고 `any`로 반환값을 결정하는 것이다.

그렇기 때문에 아래의 코드에서 아무런 에러를 감지하지 못한다.

```js
// 여전히 number[]가 반환값의 타입으로 지정되어있다.
const updateArray = insertAtBeginning(demoArray, -1);

// any 타입의 적용으로 인해 컴파일 에러가 발생하지 않는다.
const stringArray = insertAtBeginning(["a", "b", "c"], "d");
```

그래서 이 문제를 해결하기 위해 존재하는 기능이 바로 '제네릭'이다.

제네릭은 외부의 특정 타입을 지정할 수도 있도록 구현하는 기능이다.<br/>
쉽게는 타입을 마치 함수의 파라미터처럼 사용하는 기능이라고 생각하면 된다.

제네릭 기능을 사용하려면 `<>`를 사용하여 타입을 지정할 수 있다.

```js
function insertAtBeginning<T>(array: T[], value: T) {
  const newArray = [value, ...array];

  return newArray;
}
```

#### - `<T>`는 무엇을 의미하는 것일까?

함수명과 매개변수의 사이에 처음보는 `<T>`가 있는데 이것은 **제네릭 기능을 사용한다는 선언**하는 것이며 `T`는 **제네릭 기능의 이름**으로 임의로 붙인 것이다.

지정한 `T`라는 타입은 **어떠한 타입이라도 될 수 있다.**

무슨 뜻인가 하면, 인자의 값으로 결정이 된다.

위의 예를 가져와서 살펴보면 아래의 함수 반환값은 `number[]`와 `number` 타입의 매개변수로 인해 `number[]`가 된다.

```js
const updateArray = insertAtBeginning(demoArray, -1);
```

이 코드를 제네릭을 사용한다고 했을 때 명시적으로 작성하면 아래와 같다.

```js
const updateArray = insertAtBeginning<number>(demoArray: number[], -1: number): number[];
```

내가 이해한 바로는 **해당 매개변수의 타입으로 인해 반환값의 타입이 결정이 되기 때문에** 제네릭이 공통된 타입을 가져 `number`가 되는 것이라고 이해했다.

> 제네릭은 함수를 호출할 때 넘긴 <u>인자의 타입</u>에 대해 타입스크립트가 추정할 수 있게 된다고 한다. 따라서, 함수의 입력 값에 대한 타입과 출력 값에 대한 타입이 동일한지 검증할 수 있게 됩니다.<br>
> 출처 : [타입스크립트 핸드북](https://joshua1988.github.io/ts/guide/generics.html#%EC%A0%9C%EB%84%A4%EB%A6%AD%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94-%EC%9D%B4%EC%9C%A0)

그래서 함수는 제네릭으로 인해 아래의 코드와 동일하게 될 것이다.

```js
function insertAtBeginning<number>(array: number[], value: number): number[]{
  const newArray:number[] = [value, ...array];

  return newArray;
}
```

만약 이 함수를 호출 시 매개변수의 타입을 변경하여 전달하면 제네릭 또한 유연하게 타입 지정이 된다.

```js
// 매개변수로 string[], string을 함수에 전달하면?
const stringArray = insertAtBeginning(["a", "b", "c"], "d");

function insertAtBeginning<string>(array: string[], value: string): string[]{
  const newArray:string[] = [value, ...array];

  return newArray;
}
```

#### - 제네릭을 사용하면 얻을 수 있는 이점

만약 제네릭이 없다면 함수에 여러 가지 타입을 허용하고 싶다면 `any`를 사용해야할 것이다.

하지만 `any`는 함수의 반환값을 모두 `any`로 바꿔버리며 TypeScript를 사용하는 취지에도 맞지 않다.

이럴 때 제네릭을 사용하면 동일한 로직의 유연하고 재사용 가능한 컴포넌트를 생성할 수 있다.

```js
// 매개변수로 number[], number을 함수에 전달하면?
const demoArray = [1, 2, 3];
const updateArray = insertAtBeginning(demoArray, -1);

// number로 타입 지정
function insertAtBeginning<number>(array: number[], value: number): number[]{
  const newArray:number[] = [value, ...array];

  return newArray;
}

// 매개변수로 string[], string을 함수에 전달하면?
const stringArray = insertAtBeginning(["a", "b", "c"], "d");

// 동일한 로직으로 다른 타입으로 변경하여 함수를 재사용한다.
function insertAtBeginning<string>(array: string[], value: string): string[]{
  const newArray:string[] = [value, ...array];

  return newArray;
}
```
