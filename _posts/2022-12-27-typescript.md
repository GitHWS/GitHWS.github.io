---
layout: single
classes: wide
title: "TypeScript란 무엇인가?"
categories: TIL
tag: [TypeScript]
toc: true
author_profile: false
sidebar:
  nav: "docs"
---

> React 프로젝트를 위한 TypeScript 기초 포스트입니다. TypeScript의 세부적인 내용은 다루지 않습니다.

## TypeScript란 무엇인가?

TypeScript는 <u>JavaScript를 기반</u>으로 한 확장된 프로그래밍 언어로 <u>JavaScript의 '슈퍼셋'</u> 언어이다.

쉽게 말하자면 JavaScript 문법에 TypeScript 기능을 추가한 것이라고 생각하면 된다.

> **Q. 그럼 TypeScript는 JavaScript의 라이브러리인가?**
>
> A. 아니다. TypeScript는 React 같은 JavaScript 라이브러리가 아니기 때문에 JavaScript의 기존의 기능을 기반으로 새로운 기능을 만들거나 확장하지 않는다. 단지 JavaScript의 주요 문법보다 더 확장된 문법을 가진다.

TypeScript의 가장 중요한 특징은 <u>정적 타입(Static typing)</u>의 특징을 가진다.
이러한 정적 타입 기능이 추가된 이유는 JavaScript가 <u>동적 타입(Dynamically typed)</u>의 언어이기 때문이다.

### 동적 타입과 정적 타입의 차이

동적 타입은 **자료형(type)이 컴파일 시에 결정되지 않고 런타임에 결정**한다. 즉 값을 할당함으로서 타입이 정해진다. 그래서 문법적으로 매우 유연하게 사용할 수 있다. 단 유연한 만큼 타입을 예측할 수 없어 에러에 취약하고 최적화가 되지않아 성능이 떨어진다는 단점이 있다.

```js
const string = 1; // string 변수는 숫자 1을 할당 받았기 때문에 자료형이 Number가 된다.
```

정적 타입은 **컴파일 시 자료형이 결정된다.** 컴파일 단계에서 값이 지정한 자료형이 아니라면 컴파일 에러를 발생시킨다. 정적 타입은 동적 타입보다 유연하진 않지만 동적 타입보다 컴파일 단계에서 모든 가능성이 미리 확인되기 때문에 에러에 안전하고 코드는 최적화 되고 메모리 사용을 최소화할 수 있다.

```js
let string: string; // string 변수의 자료형을 string으로 지정한다.
string = 1; // 컴파일 단계에서 자료형을 확인하여 지정한 자료형이 일치하지 않기 때문에 에러가 발생한다.
```

<br>

예를 들어 **간단한 더하기 함수**를 예를 들어보자.

### 동적 타입의 JavaScript에서는 어떨까?

```js
function add(a, b) {
  return a + b;
}

const result = add(2, 5); // Number 타입의 매개변수를 더하면?

console.log(result); // 7
```

여기서 JavaScript는 당연히 매개변수 2와 5를 전달 받아 함수를 호출하여 결과값인 7을 반환하여 로그에 출력한다.

하지만 여기서 문자열인 '2'와 '5'를 전달받으면 어떻게 될까?

```js
function add(a, b) {
  return a + b;
}

const result = add("2", "5"); // String 타입의 매개변수를 더하면?

console.log(result); // '25'
```

JavaScript는 문자열에 더하기 연산을 하면 문자열을 연결하는 동작을 수행한다. 그래서 '25'라는 문자열이 출력된다. <br/>
물론 에러가 나지 않아서 편리한 기능이긴 하지만 우리가 생각하기에 문자열에 더하기 연산이 에러가 나지 않는다는 것은 매우 이상한 기능이다.

### 정적 타입의 TypeScript에서는 어떨까?

TypeScript에서는 매개변수의 타입을 지정할 수 있다. 이것을 '타입 표기' 기능이라고 한다.

타입 표기를 하고 상수 `result`에 더하기 함수에 자료형이 number인 매개변수 2, 5를 전달하여 반환값을 저장한다.<br>
컴파일 과정에서 '타입 표기'를 확인하고 지정한 자료형이 매개변수로 전달되었는지 확인하면 number인 2, 5가 전달되었기 때문에 에러없이 정상적으로 반환값을 저장하여 로그에 출력이 된다.

```js
// 매개변수 a, b의 자료형을 number로 설정했다.
function add(a: number, b: number) {
  return a + b;
}

// 컴파일 단계에서 지정한 자료형을 확인하여 올바르게 number 타입의 값이 전달하면?
const result = add(2, 5);

console.log(result); // 7
```

하지만 만약 매개변수로 다른 타입이 들어가면 컴파일 에러가 발생하게 된다.

```js
const result = add("2", "5"); // 컴파일 에러 발생
```

<br>

### 그럼 유연한 동적 타입 언어를 쓰는 것이 좋지 않나?

절대 그렇지 않다.<br> 물론 동적 타입 언어을 사용하면 문법적으로 유연하게 사용할 수 있다.<br>
단, 유연하게 사용하는 만큼 우리가 연산 과정에서 예측하지 못하는 결과를 받을 수도 있으며 에러의 발생 가능성이 높다.

또한 런타임에서 자료형이 결정되기 때문에 자료형을 예측할 수 없어 최적화가 되지 않아 그만큼 메모리를 정적 타입 언어보다 더 사용한다.

<br>

### 그래서 TypeScript를 왜 사용하는가?

정적 타입 언어인 TypeScript는 특정한 타입이 아니라면 컴파일 과정에서 에러를 발생시킨다.<br>
이로서 동적 타입 언어처럼 런타임에 오류의 원인을 찾을 필요없이 코드를 작성할 때 자료형으로 발생되는 에러와 의도치 않은 함수 사용을 미리 예방할 수 있다.

이것이 TypeScript를 사용하는 궁극적인 목표라고 할 수 있다.
