---
layout: single
classes: wide
title: "[22.12.28] TypeScript 설치와 타입 지정"
categories: TIL
tag: [TypeScript]
toc: true
author_profile: false
sidebar:
  nav: "docs"
---

> React 프로젝트를 위한 TypeScript 기초 포스트입니다. TypeScript의 세부적인 내용은 다루지 않습니다.

## TypeScript 사용해보기

TypeScript의 특징과 사용 이유를 봤으니 실제로 사용해보도록 하자.

### TypeScript 설치와 TypeScript 컴파일러의 실행

TypeScript는 아래의 명령을 실행하여 설치해서 사용할 수 있다.
그리고 생성한 파일 확장자는 `.ts`로 한다.

```
npm install typescript
```

설치를 하고 `.ts` 파일을 생성했으면 이제 TypeScript 문법을 사용할 수 있고 'TypeScript 컴파일러'까지 사용할 수도 있다.

코드 작성은 아래처럼 '타입 표기' 기능을 통해 변수나 매개변수의 자료형을 지정할 수 있다.

```ts
// index.ts

function add(a: number, b: number) {
  return a + b;
}

const result = add(2, 5);

console.log(result); // 7?
```

코드를 이렇게 작성하면 로그에 `7`이 출력될 것이라고 생각할 것이다.

> 하지만 HTML 파일에 script를 연결하고 라이브 서버를 실행을 해보면 아무런 반응이 없다.

왜냐하면 **브라우저에서 TypeScript 코드를 실행할 수 없기 때문이다.** <br/>
그래서 TypeScript 설치를 하면서 사용할 수 있는 **TypeScript 컴파일러를 실행해서 브라우저가 읽을 수 있는 파일로 컴파일링을 해야한다.**

TypeScript 컴파일러를 사용하려면 아래의 명령어로 실행시킬 수 있다.

```
npx tsc
```

하지만 이 상태에서 `npx tsx` 명령어를 실행하면 많은 명령어가 출력이 된다.

<p align="cetner">
  <img src="https://user-images.githubusercontent.com/96808980/209784633-9d4fbbf9-af43-4492-bf55-2983ae229d22.png" alt=""/>
</p>

우선 `npx tsc`를 입력했으니 가장 첫번째의 명령어인 `tsc`의 설명을 보도록 하자.

```
Compiles the current project (tsconfig.json in the working directory.)
: 현재 프로젝트(작업 디렉토리의 tsconfig.json)를 컴파일한다.
```

즉 현재 프로젝트 파일에 `tsconfig.json`이 없다면 컴파일을 할 수 없다는 의미이다.
그렇다면 `tsconfig.json` 파일을 생성해야하는데 이것을 생성하려면 아래의 명령어로 생성이 가능하다.

```
tsc --init
```

명령어를 실행하면 `tsconfig.json` 파일이 해당 디렉토리에 생성이 된다.

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/209787499-7450bce3-e5fc-4f20-a423-01d5df8dfeaa.png" alt=""/>
</p>

`tsconfig.json` 파일이 생성된 위치는 **TypeScript 프로젝트의 루트 디렉토리**가 되며, 이 파일에는 프로젝트를 컴파일하는데 필요한 루트 파일이나 컴파일러 옵션을 설정할 수 있다.

> 하지만 만약 `tsconfig.json` 파일을 사용하지 않을 경우에는 `npx tsc { 파일명 }` 명령어로 해당 파일 생성없이 컴파일링 할 수 있다.

이제 컴파일러를 실행시켜보자.

```
npx tsc
```

컴파일러를 실행시키게 되면 `.ts` 파일과 동일한 파일명의 `.js`파일이 생성이 된다.
이 JavaScript 파일은 TypeScript 파일을 컴파일하여 생성된 파일로 코드도 동일한 것을 알 수 있다.

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/209788214-203330a0-27c8-4d0b-b861-821d892392b6.png" alt=""/>
</p>

하지만 다른 점이라면 **'타입 표기' 기능이 사라진 것**을 볼 수 있다.<br>

사라진 이유는 컴파일 과정에서 TypeScript 파일에 있는 타입 표기 기능을 JavaScript는 이해하지 못하기 때문에 컴파일러를 실행하여 컴파일링을 했을 때 타입 표기는 사라지고 JavaScript 파일을 생성한다.

만약 컴파일에 문제가 있다면 에러를 알려준다.

<br/>

### 본격적인 TypeScript 사용하기 - 타입 지정

TypeScript를 사용하려면 '타입'이라는 개념을 알아야한다.

그렇다. 우리가 생각하는 number, string, boolean 같은 것이다.

이것을 이제 어떻게 타입을 지정할 수 있을까?

> 모든 타입을 보고싶다면 각 변수/상수에 커서를 가져다가 기다리면 아래처럼 툴팁이 뜬다.
>
> <p align="center">
>   <img src="https://user-images.githubusercontent.com/96808980/209815124-625175e6-e13d-41ec-8325-f064b50d7cdf.png" alt=""/>
> </p>

#### Primitive Types의 타입 지정

Primitive Type은 number, string, boolean, null, undefined 타입을 포함한다.

변수에 이 Primitive Type을 지정하려면 아래처럼 작성을 하면 된다.

```js
// string
let name: string;

name = "Hyunwoo";

// number
let age: number;

age = 29;

// boolean
let isMan: boolean;

isMan = true;
```

처음 보면 조금 낯선 코드일 수 있으나 `name` 변수에 `:`을 이용하여 타입을 설정하고 이후에 값을 할당(초기화)하는 과정이다.

여기서 주의할 점은 반드시 타입은 '소문자'의 형태로 입력해야한다. 이유는 대문자로 입력시 각 자료형의 '객체'를 가리키기 때문이다.

> null, undefined 타입은 자료형 지정을 하지 않기 때문에 작성하지 않았다.

#### 배열과 객체

배열의 경우에는 아래와 같이 타입을 지정할 수 있다.

자료형과 `[]`을 이어서 타입을 지정하는데 입력한 자료형은 배열 안의 엘리먼트의 타입을 가리킨다.

```js
let array: string[]; // 문자열 엘리먼트만 들어간 배열

array = ["a", "b", "c", "d"]; // 성공!
array = [1, 2, 3, 4]; // 컴파일 에러 발생
```

객체의 경우에는 아래와 같이 타입을 지정할 수 있다.
객체에서 타입 지정은 각각의 프로퍼티마다 가능하다.

```js
let person: {
  name: string,
  age: number,
};

// 성공!
person = {
  name: "Hyunwoo",
  age: 29,
};

// 컴파일 에러 발생
person = {
  name: 29,
  age: "Hyunwoo",
};
```

여기서 <u>엘리먼트로 객체를 가지는 배열</u>은 어떻게 표현할까? 조금 복잡할 수 있지만 생각보다 간단하게 지정할 수 있다.

```js
// 객체의 타입 지정의 뒤에 []을 붙여주면 된다.
let people: {
  name: string,
  age: number,
}[];

people = [
  { name: "Hyunwoo", age: 29 },
  { name: "Haha", age: 27 },
];
```

#### any

any 타입은 **'아무런 타입을 지정하지 않음'을 뜻하고 어떠한 값이든 저장될 수 있다는 것을 의미**한다.

> any는 예비적으로 사용되는 타입이며 TypeScript를 사용하는 주요 목적에 맞지 않기 때문에 사용하지 않는 것이 좋다.

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/209815560-5cc60671-5f07-458d-9718-058193bdb28a.png" alt=""/>
</p>

<br/>
