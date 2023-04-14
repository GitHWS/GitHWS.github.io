---
layout: single
classes: wide
title: "함수형 프로그래밍이란?"
categories: TIL
tag: [React, JavaScript, 함수형 프로그래밍]
toc: true
author_profile: false
sidebar:
  nav: "docs"
---

> 원티드에서 진행하고 있는 '프리온보딩 FE 챌린지'에서 `함수형 프로그래밍`이라는 단어가 언급이 되었다. <br/>
> 나는 함수형 프로그래밍에 대한 이야기만 들어봤을 뿐 개념을 모르고 있었기에 이번 기회에 개발 서적과 다른 개발자분들이 작성한 게시물을 보면서 스스로 정리하는 시간을 가지려고 한다. <br/>

<br/>
 
## **함수형이란?** 🤔

`함수형`이라는 단어를 보면 '함수를 사용해서 프로그래밍하는걸까?'라는 생각이 들 것이다.

그렇다. 말 그대로 `함수`를 사용해서 프로그래밍을 하는 것이다.

`JavaScript`에서 함수는 **1급 시민** 또는 **1급 멤버**라고도 불린다.

> **1급 시민? 1급 멤버?** <br/>
> 함수를 **정수나 문자열 같은 다른 일반적인 값과 마찬가지로 취급할 수 있는 데이터**로 취급하는 것을 의미한다. <br/>
> 즉, 정수나 문자열처럼 함수를 변수에 대입이 가능하고 함수의 인자로 전달하거나 함수를 반환값으로 사용할 수 있는 데이터를 가리킨다.

<br/>

`JavaScript`에서 함수는 1급 시민이므로 `함수형 프로그래밍`을 지원한다고 볼 수 있다.

특히 `ES6`에서는 <u>화살표함수, Promise, 전개(Spread) 연산자</u>가 새롭게 추가되어 함수형 프로그래밍을 더욱 잘 활용할 수 있게 되었다.

그럼 JavaScript에서 `1급 시민`인 함수를 어떻게 사용되는지 살펴보자.

<br/>

### **1. 함수를 변수에 대입 가능**

```js
// ES6 이전
const log = function (message) {
  console.log(message);
};

// ES6 화살표 함수 사용
const log = (message) => console.log(message);
```

위의 코드를 보면 함수를 변수에 대입하고 있는 것을 볼 수 있다.

또한 객체와 배열에도 함수를 넣을 수도 있다.

```js
// 객체에 함수를 프로퍼티로 추가
const obj = {
  message: "함수형 프로그래밍",
  log(message) {
    console.log(message);
  },
};

obj.log(obj.message); // 함수형 프로그래밍
```

```js
// 배열에 함수를 요소로 추가
const message = ["함수형 프로그래밍", (message) => console.log(message)];

message[1](message[0]); // 함수형 프로그래밍
```

<br/>

### **2. 함수의 인자로 함수를 전달 가능**

```js
const insideFn = (logger) => logger("함수형 프로그래밍");

// insideFn을 호출하는데 함수를 인자로 전달하고 있다.
insideFn((message) => console.log(message));
```

<br/>

### **3. 함수가 함수를 반환 가능**

```js
const createScream = function (logger) {
  // createScream이 함수를 반환하고 있다.
  return function (message) {
    logger(message.toUpperCase());
  };
};

// ES6
const createScream = (logger) => (message) => logger(message.toUpperCase());

const scream = createScream((message) => console.log(message));

scream("함수형 프로그래밍 Functional Programming"); // 함수형 프로그래밍 FUNCTIONAL PROGRAMMING
```

여기서 `2`, `3`번의 코드의 경우 **고차 함수**라고 하는데, 다른 함수를 조작하고 함수를 인자로 받거나 반환하는 것이 가능한 복잡한 함수를 의미한다.

<br/>

## **명령형 프로그래밍? 선언적 프로그래밍?** 😵‍💫

함수형 프로그래밍에 대해 말하고 있는데 왜 뜬금없이 `명령형 프로그래밍(Imperative Programming)`과 `선언적 프로그래밍(Declarative Programming)`이란 단어가 나왔을까?

그 이유는 함수형 프로그래밍은 **선언적 프로그래밍이라는 더 넓은 프로그래밍의 한 가지 사례**라고 볼 수 있다.

그럼 명령형 프로그래밍이 무엇이고, 선언적 프로그래밍은 무엇인지 간단하게 알아보자.

<br/>

### **명령형 프로그래밍이란?**

명령형 프로그래밍이란 '원하는 결과를 달성해 나가는 과정에만 관심을 두는 프로그래밍 스타일'을 의미한다.

<br/>

### **선언적 프로그래밍이란?**

선언적 프로그래밍이란 '필요한 것을 달성하는 과정을 하나씩 기술하기보다 필요한 것이 어떤 것인지 기술하는데 중점을 두는 프로그래밍 스타일'이다.

> 솔직히 나는 이렇게 정의를 봐도 뭔가 애매모호함을 느꼈다.

<br/>

이제 같은 결과를 보여주는 <u>명령형 프로그래밍과 선언적 프로그래밍의 함수</u>를 비교해보자.

우선 **명령형 프로그래밍에서의 함수**를 만들어보자.
아래의 함수는 **문자열의 공백을 '-'으로 변경하는 함수**이다.

```js
// 명령형 프로그래밍
const string = "함 수 형 프로그 래 밍";
const result = "";

for (var i = 0; i < string.length; i++) {
  if (string[i] === " ") {
    result += "-";
  } else {
    result += string[i];
  }
}

console.log(result); // 함-수-형-프로그-래-밍
```

명령형 프로그래밍은 '<u>원하는 결과를 달성하는 과정에만 관심</u>을 두는 프로그래밍 스타일'이라고 했다.

위의 예제 코드에서는,

`string` 변수의 반복문을 통해 **모든 문자를 순회**하고 있다.
순회하면서 공백이라면 `-`을 `result` 변수에 더하기 연산을 하고 공백이 아니라면 `해당 순서의 문자열`을 `result` 변수에 더하기를 한다.

모든 문자를 순회하면서 **각 문자를 순회하는 과정마다 조건에 따라 더하기 연산을 다르게 하고 있다**는 것을 볼 수 있다.

여기서 볼 수 있는 것은 명령형 프로그래밍에서는 코드를 살펴보는 것만으로 해당 로직을 파악하는 것이 쉽지 않다는 것이다.

그래서 명령형 프로그래밍에서는 코드를 읽는 다른 협업자를 위해 로직을 설명하는 주석이 많이 필요하다.

<br/>

이제 **선언적 프로그래밍에서의 같은 역할의 함수**를 보도록 하자.

```js
const string = "함 수 형 프로그 래 밍";
const result = string.replace(/ /g, "-");

console.log(result); // 함-수-형-프로그-래-밍
```

어떤가? 같은 결과를 볼 수 있는데 명령형 프로그래밍에서 본 긴 코드가 확연하게 줄어든 것을 볼 수 있다.

선언적 프로그래밍에서 볼 수 있는 특징은 `result` 변수에서 구체적인 절차를 대신해 사용한 `replace` 메서드에서 볼 수 있다.

현재 `replace` 메서드는 `string` 변수의 문자열에 있는 모든 공백을 `-`로 치환할 것이라는 것을 로직을 설명하는 주석이 없어도 쉽게 추론할 수 있다.

하지만 우리는 `replace`라는 메서드의 로직을 구현하지 않았다.

<br/>

> 어떻게 메서드만 보고 바로 추론할 수 있었을까?

우리는 이것을 `추상화`라고 말할 수 있다.<br/>
선언적 프로그래밍에서의 코드는 어떤 일이 발생해야 하는지 기술하고, 실제로 그 작업을 처리하는 방법을 추상화를 통해 아랫단에 감춰진다.

그래서 선언적 프로그래밍의 코드는 코드 자체가 어떤 일이 발생할지 설명하기 떄문에 **명령형 프로그래밍에 비해 추론하기가 쉽고 가독성도 좋다.**

<br/>

### **명령형 프로그래밍과 선언적 프로그래밍의 DOM 구축 절차**

명령형 프로그래밍과 선언적 프로그래밍에서는 DOM을 구축하는데 절차에도 많은 차이가 있다.

아래의 코드는 명령형 프로그래밍에서 DOM을 구축하는 절차를 보여주는 코드이다.

```js
const target = document.getElementById("target");
// 엘리먼트 생성
const wrapper = document.createElement("div");
const headline = document.createElement("h1");

// 추가적인 설정
wrapper.id = "welcome";
headline.innerText = "Hello World";

// 문서에 추가
wrapper.appendChild(headline);
target.appendChild(wrapper);
```

이 코드를 보면 엘리먼트를 생성하고 추가적인 설정을 한 후 문서에 추가하는 것을 볼 수 있다.

즉, 어떻게 DOM을 구성하는지 순서대로 볼 수 있는 것이다.

<br/>

반면에 선언적 프로그래밍에서는 아래와 같이 DOM을 구성한다.

> React는 선언적이므로 예시 코드로 React 컴포넌트를 사용한다.

<p align="center">
  <img width="700" src="https://user-images.githubusercontent.com/96808980/231354255-76144aa4-cc66-4d4c-ab71-6e0420ed10c8.png" alt="리액트 공식 문서" title="리액트 공식 문서에 선언형이라는 키워드가 표기되어있다.">
</p>

```jsx
const { render } = ReactDOM;

// 렌더링할 DOM을 기술하면서 div의 id를 설정하고, h1의 텍스트를 추가했다.
const Welcome = () => {
  <div id="welcome">
    <h1>Hello World</h1>
  </div>;
};

// Welcome 컴포넌트를 'target'이라는 엘리먼트 내부에 렌더링할 것이란 것을 추론할 수 있다.
render(<Welcome />, document.getElementById("target"));
```

`Welcome` 컴포넌트는 렌더링할 DOM을 기술했고, `render` 함수는 컴포넌트에 있는 지시에 따라 DOM을 구성한다.

또한 `render` 함수의 로직을 쉽게 추론할 수 있으나 <u>DOM이 어떻게 렌더링되는지는 추상화로 인해 해당 함수의 처리 과정이 감춰진다.</u>

즉, 코드에서는 `Welcome` 컴포넌트를 `target` 엘리먼트 내부에 `render` 함수를 호출하여 렌더링할 것이라는 명확하게 볼 수 있다.

<br/>

## **함수형 프로그래밍의 핵심 개념**

이전에 명령형 프로그래밍과 선언적 프로그래밍의 개념을 잡았으니 이제 본격적으로 함수형 프로그래밍에 대해 개념을 정리해보도록 하자.

### **1. 불변성(Immutable)**

함수명 프로그래밍에서는 데이터가 변할 수 없다. 즉, **불변성 데이터**는 절대 바뀌지 않아야한다.

불변성 데이터는 '원본 데이터'를 생각하면 된다.

그래서 함수형 프로그래밍에서는 <u>원본 데이터를 조작하지 않고 원본 데이터를 '복제'하여 복제한 데이터를 사용하여 필요한 작업을 진행</u>한다.

<br/>

**1. 객체의 경우**

예시 코드를 보도록 하자.

```js
// 원본 데이터 === 불변성 데이터
let me = {
  name: "Hyunwoo",
  age: 29,
  gender: "male",
};

// 나이를 +1 하는 함수
const increaseAge = (target, age) => {
  target.age = age;
  return target;
};

// 왜 원본 데이터까지 갱신되었을까?
console.log(increaseAge(me, 30).age); // 30
console.log(me.age); // 30
```

이 예시 코드는 원본 데이터를 유지하지 않는 **불변성을 지키지 않은 코드**이다.

`JavaScript`의 함수의 인자는 <u>실제 데이터에 대한 참조</u>를 의미한다. 즉, 원본 데이터를 `increaseAge` 함수의 호출로 인해 데이터의 변경이 이루어진 것이다.

> 그렇다면 불변성을 지키기 위해선 어떻게 해야할까?

가장 많이 사용하는 방법이라면 `전개 연산자(...)`를 사용하는 것이다.

아래는 전개 연산자를 사용하는 코드이다.

```js
const increaseAge = (target, age) => ({ ...target, age });

console.log(increaseAge(me, 30).age); // 30
console.log(me.age); // 29
```

전개 연산자를 통해 실제 데이터의 모든 프로퍼티를 전개하고 새로운 `age`의 값을 오버라이드한다.

이렇게 하면 `{}` 안에 참조하는 `실제 데이터(원본 데이터)의 모든 프로퍼티들을 전개`하고 인자로 받는 새로운 `age`의 값을 전개된 기존 `age` 프로퍼티에 오버라이드하여 반환하게 된다.

이 외에도 [Object.assign](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)의 사용하여 새로운 객체를 반환하는 방법도 있으니 참조하길 바란다.

<br/>

**2. 배열의 경우**

```js
const colorArray = ["빨강", "노랑", "초록"];

const addColor = (newColor, colors) => {
  colors.push(newColor);
  return colors;
};

console.log(addColor("파랑", colorArray)); // ["빨강", "노랑", "초록", "파랑"]
console.log(colorArray); // ["빨강", "노랑", "초록", "파랑"]
```

배열에서의 `push` 메서드는 원본 데이터에 영향을 주기 때문에 **불변성을 지키지 않는다.**

`push` 뿐만 아니라 `pop`,`shift`,`unshift` 등은 원본 데이터에 영향을 주는 **불변성을 지키지 않는 함수**이다.

> 그렇다면 배열에서는 불변성을 지킬 수 있을까?

대표적으로 새 배열을 반환하는 `map`, `filter`, `concat` 등의 메서드와 `전개 연산자`가 있을 수 있다.

```js
// concat 메서드를 사용하여 colors 인자가 참조하는 원본 배열에 newColor를 추가한 새로운 배열을 반환한다.
const addColor = (newColor, colors) => colors.concat(newColor);

console.log(addColor("파랑", colorArray)); // ["빨강", "노랑", "초록", "파랑"]
console.log(colorArray); // ["빨강", "노랑", "초록"]
```

```js
// 전개 연산자를 사용하여 원본 배열을 복제하고 newColor를 추가한 배열을 반환한다.
const addColor = (newColor, colors) => [...colors, newColor];

console.log(addColor("파랑", colorArray)); // ["빨강", "노랑", "초록", "파랑"]
console.log(colorArray); // ["빨강", "노랑", "초록"]
```

두 코드 모두 원본 데이터에 영향을 주지 않은 것을 볼 수 있다.

위와 같은 식으로 원본 데이터의 불변성을 지킬 수 있다.

<br/>

### **2. 순수 함수(Pure Function)**

`순수 함수`란 **파라미터에 의해서만 반환값이 결정되는 함수**를 말한다.

순수 함수는 무조건 **하나 이상의 변경 불가능한 인자를 받아야하며 인자가 같을 시 동일한 값이나 함수를 반환**해야한다.

순수 함수에는 **부수 효과**가 없다.

> **부수 효과**(Side Effect)? <br/>
> 전역 변수를 설정하거나, 함수 내부나 애플리케이션에 있는 다른 상태를 변경하는 것을 말한다.

우선 순수 함수가 아닌 것을 먼저 살펴보도록 하자.

내가 읽고 있는 개발 서적[(Leaning React)](http://www.yes24.com/Product/Goods/58543289)의 예시 코드는 아래와 같다.

> 오래된 책이라 var 키워드를 쓰는 예제이다. 🥲

```js
var frederick = {
  name: "Frederick Douglass",
  canRead: false,
  canWrite: false,
};

// selfEducate 함수는 frederick의 canRead와 canWrite를 업데이트하는 함수이다.
function selfEducate() {
  frederick.canRead = true;
  frederick.canWrite = true;
  return frederick;
}

selfEducate();
console.log(frederick); // { name: "Frederick Douglass", canRead: true, canWrite: true }
```

위의 코드에서 `selfEducate` 함수는 순수 함수가 아니다.

그 이유는 아래와 같다.

1. 인자를 전달받지 않고 있다는 것
2. 2.이 함수를 호출하면 `frederick`이라는 변수의 값을 변경(**부수 효과의 발생**)</u>한다.

이것을 이제 순수 함수로 수정해보도록 하자.

```js
const selfEducate = (person) => ({ ...person, canRead: true, canWrite: true });

selfEducate(frederick);

console.log(selfEducate(frederick)); // { name: "Frederick Douglass", canRead: true, canWrite: true }
console.log(frederick); // { name: "Frederick Douglass", canRead: false, canWrite: false }
```

우선 `selfEducate` 함수의 인자를 전달하고 변수의 값을 변경하여 원본 데이터의 불변성을 위반하는 것을 전개 연산자를 사용하여 원본 데이터를 복제 후 변경될 필드의 값을 오버라이드하고 반환했다.

원본 데이터을 변경하는게 아닌 복제된 데이터를 변경하기 때문에 부수 효과도 없다.

이로써 원본 데이터의 불변성을 지키면서 순수 함수로 수정했다.

<br/>

#### **DOM 변경에서의 순수 함수**

DOM에서의 순수 함수가 아닌 경우이다.

```js
function Header(text) {
  let h1 = document.createElement("h1");
  h1.innerText = text;
  document, body.appendChild(h1);
}

Header("Hello world");
```

위의 함수는 왜 순수 함수가 아닌가?

`Header` 함수는 하나 이상의 인자를 받고 있지만 `h1`이라는 **DOM을 변경**하고 있으며, **아무 값도 반환하지 않고** 있기 때문에 순수 함수가 아닌 것이다.

하지만 `React`에서는 아래와 같이 <u>UI를 순수 함수로 표현</u>한다.

```jsx
const Header = (props) => <h1>{props.title}</h1>;
```

이 함수는 위에서 본 `Header` 함수와 다르게 부수 효과를 발생시키지 않고 엘리먼트를 반환한다.

또한 이 함수는 엘리먼트를 만드는 일만 할 뿐, DOM을 변경하는 책임은 다른 부분이 담당해야 한다.

> 순수 함수는 애플리케이션 상태에 영향을 미치지 않기 때문에 코딩이 편해진다는 장점이 있다.<br/>
>
> 함수를 만들 때 <u>3가지 규칙</u>을 따르면 순수 함수를 만들 수 있다.
>
> 1. 파라미터 최소 **1개 이상**을 전달받을 것 <br/>
> 2. 순수 함수는 **값이나 다른 함수를 반환**할 것<br/>
> 3. 순수 함수는 **인자나 함수 밖에 있는 다른 변수를 변경하거나 입출력을 수행하지 말 것**

> **참고) 순수 함수는 테스트하기 쉽다.** <br/>그 이유는 순수 함수는 자신의 환경 또는 어떤 것도 변화시키지 않기 때문에 복잡한 테스트 준비나 정리가 필요하지 않다. <br/>순수 함수를 테스트할 때는 함수에 전달되는 '인자'만 제어하면 되며, 인자에 따른 결과값을 예상할 수 있다.

<br/>

### **3. 데이터 변환**

함수형 프로그래밍에서는 어떻게 기존의 데이터를 바꿀 수 있을까?

함수형 프로그래밍은 `함수`를 사용하여 원본을 변경한 복사본을 만들어낸다.

그렇다. 1번에서 말했던 `불변성`을 지키면서 데이터를 바꿔야하기 때문에 당연히 원본 데이터를 그대로 유지하면서 복사 데이터를 이용해야 하는 것이다.

함수를 사용해서 데이터를 변경하면 코드가 조금 덜 명령형이 되며 그에따라 복잡도도 감소한다.

> 그렇다면 **원본 데이터를 복사한 데이터를 이용하는 함수**를 사용하는 것이 포인트가 될 수 있을 것 같다.

예를 들어 `Array.map`이나 `filter`, `reduce` 등이 있겠다.

위의 함수들은 모두 `JavaScript 내장 함수`이며 '반환값'을 `새로운 값으로 반환`하고 `원본 데이터에 영향을 미치지 않는 함수`들이다.

<br/>

### **4. 고차함수(HOF, High Order Function)**

고차함수는 '다른 함수를 조작할 수 있는 함수'이다. 즉, 함수가 함수를 인자로 받거나 반환값으로 사용할 수 있다는 의미이다. 또한 인자로 받음과 동시에 반환값으로 사용할 수도 있다.

<br/>

**1. 다른 함수를 인자로 받는 고차함수**

```js
const invokeIf = (condition, fnTrue, fnFalse) =>
  condition ? fnTrue() : fnFalse();

const showWelcome = () => console.log("Welcome");

const showUnauthorized = () => console.log("Unauthorized");

invokeIf(true, showWelcome, showUnauthorized); // Welcome
invokeIf(false, showWelcome, showUnauthorized); // Unauthorized
```

위의 코드에서 `invokeIf` 함수는 인자로 받는 `condition`에 따라 삼항 연산자를 통해 인자로 받는 `fnTrue`, `fnFalse` 중 하나의 함수를 호출하는 간단한 코드이다.

**2. 다른 함수를 반환하는 고차함수**

다른 함수를 반환하는 고차함수는 '비동기적인 실행 컨텍스트를 처리할 때' 유용하다.

이런 함수를 반환하는 고차함수를 사용하면 필요할 때 재활용할 수 있는 함수를 만들 수 있다.

여기서 **커링**(Currying)이라는 개념이 필요하다.

> **커링(Currying)?** <br/>
> 커링이란 어떤 연산을 수행할 때 <u>필요한 값 중 일부를 저장하고 나중에 나머지 값을 전달받는 기법</u>이다. 이를 위해 다른 함수를 반환하는 함수를 사용하며 이를 '커링된 함수'라 부른다.

우선 커링의 예제를 살펴보도록 하자.

```js
const userLogs = (userName) => (message) =>
  console.log(`${userName} -> ${message}`);

const log = userLogs("grandpa23");

log("attempted to load 20 fake member");

// Promise를 반환하는 getFakeMember
getFakeMember(20).then(
  (members) => log(`successfully loaded ${members.length} members`),
  (error) => log("encountered an error loading members")
);

// 불러오기 성공 시
// grandpa23 -> attempted to load 20 fake member
// grandpa23 -> successfully loaded 20 members

// 불러오기 실패 시
// grandpa23 -> attempted to load 20 fake member
// grandpa23 -> encountered an error loading members
```

여기서 `userLogs`는 고차함수이다.

`userLogs`는 호출 시 만들어지는 다른 함수(`log`)를 반환하고 `log` 함수를 호출하면 메세지 앞에 'grandpa23'이라는 문자열이 덧붙여진다.

여기서 '덧붙여진다'라는 것이 커링된 함수라는 것을 나타내고 있다.

커링의 정의는 '연산 수행 시 필요한 값 중 일부를 <u>저장</u>하고 나중에 <u>나머지 값</u>을 전달받는 기법'이다.

즉, `userLogs` 함수의 인자인 `userName`이 먼저 전달(**저장**)이 되면 생성한 함수(`log`)를 반환하고, 반환되는 함수의 인자인 `message`(**나머지**)가 전달이 되어야만 `userLogs`가 반환한 함수를 활용할 수 있는 것이다.

<br/>

### **5. 재귀(recursion)**

`재귀`란 '자기 자신을 호출하는 함수를 만드는 기법'이다.

루프를 모두 재귀로 바꿀 수도 있고 일부 루프의 경우엔 재귀로 표현하는 것이 더 간단하다.

<br>

**1. 루프를 재귀로 표현**

아래의 코드는 10부터 0까지 거꾸로 카운트하는 코드이다.

```js
for (let i = 10; i >= 0; i--) {
  console.log(i);
}
```

위의 코드의 경우 반복문을 사용한 카운트 코드이다. 이 코드를 같은 기능을 하는 재귀 함수로 변경하면 아래와 같다.

```js
const countDown = (value, fn) => {
  fn(value);
  return value > 0 ? countDown(value - 1, fn) : value;
};

countDown(10, (value) => console.log(value)); // 10 9 8 7 6 5 4 3 2 1 0
```

`countDown` 함수를 호출 시 첫번째 인자 `10`이 value로, 콜백 함수인 `(value) => console.log(value)`가 fn으로 전달이 되면서 함수가 실행된다.

`value`가 0보다 크다면 재귀로 인해 `value`를 1씩 감소시켜 함수 자신을 또 다시 호출하고 아니라면 `value`를 반환한다.

**2. 비동기 작업에서의 재귀**

비동기 작업에서도 재귀를 사용할 수 있다.

```js
const countDown = (value, fn, delay = 1000) => {
  fn(value);
  return value > 0 ? setTimeout(() => countDown(value - 1, fn), delay) : value;
};

const log = (value) => console.log(value);
countDown(10, log);
```

10에서 0까지 1씩 감소하면서 카운트다운을 하는 이전과 '비동기' 작업인 `setTimeout`이 있다는 것만 다른 함수이다.

위의 코드는 `value`가 0보다 클 때 `value`를 1씩 감소시켜 1초가 지난 후에 `countDown` 함수를 다시 호출하고 아닐 시엔 `value`를 반환한다.

<br/>

**3. 데이터 구조 검색을 위한 재귀**

어떤 폴더의 모든 하위 폴더를 검색하면서 파일 이름을 모두 추려내고 싶다면 재귀를 사용할 수 있다.

또한 HTML DOM에서 자식이 없는 엘리먼트를 찾고 싶을 때도 재귀를 사용할 수 있다.

아래의 코드에서는 재귀로 객체에 있는 값을 찾아내는 것이다.

```js
const dan = {
  type: "person",
  data: {
    gender: "male",
    info: {
      id: 22,
      fullname: {
        first: "Dan",
        last: "Deacon",
      },
    },
  },
};

// deepPick("data.info.fullname.first", dan)를 호출 시
const deepPick = (fields, object = {}) => {
  // [first, ...remaining] = [data, info, fullname, first];
  const [first, ...remaining] = fields.split(".");

  // remaining.length === 3 이므로 deepPick("info.fullname.first", dan[data])로 재귀
  // 재귀 호출은 값을 반환할 때까지 반복되기 때문에 remaining.length가 0일 때까지 계속된다.
  // 즉, 객체의 내부 프로퍼티를 한 단계씩 아래로 접근하면서 처리한다.
  return remaining.length
    ? deepPick(remaining.join("."), object[first])
    : object[first];
};

console.log(deepPick("type", dan)); // person
console.log(deepPick("data.info.fullname.first", dan)); // Dan
```

위의 예시를 보면 deepPick 함수에 `remaining.length`에 따라 재귀가 되는 것을 볼 수 있다.

재귀 호출은 값을 반환할 때까지 반복된다.

<br/>

> 단, 재귀 함수는 루프보다 코드가 짧아 가독성이 좋으나 <u>**스택 오버 플로우**</u>를 일으킬 수 있다. <br/>

함수를 호출할 때 **함수의 입력 값(매개변수), 반환값, 그리고 리턴됐을 때 돌아갈 위치값 등을 스택에 저장**한다. <br/>
재귀 함수를 사용하면 함수가 끝나지 않은 채 연속적으로 함수를 호출하기 때문에 스택에 메모리가 쌓이게 된다. <br/>
해당 스택의 최대 크기보다 이상의 메모리가 쌓이게 되면 <u>**스택 오버 플로우**</u>가 발생한다. 또한 잦은 점프로 인해 성능 저하 위험도 가지고 있다.

그래서 이런 재귀 함수의 단점을 해결하기 위해 <u>**꼬리 재귀(Tail recursion)**</u>이라는 최적화 방법이 있다.

꼬리 재귀의 경우 아래의 링크를 참고하길 바란다.

출처 : [꼬리 재귀 최적화](https://velog.io/@yesdoing/%EA%BC%AC%EB%A6%AC-%EB%AC%BC%EA%B8%B0-%EC%B5%9C%EC%A0%81%ED%99%94Tail-Call-Optimization%EB%9E%80-2yjnslo7sr)

<br/>

### **6. 합성(Composition)**

함수형 프로그래밍에서는 로직을 구체적인 작업을 담당하는 여러 작은 순수 함수로 나눈다.

그 과정에서 모든 작은 함수를 한 곳에 합칠 필요가 있다.

구체적으로 **각 함수를 서로 연쇄적으로 또는 병렬적으로 호출하거나 여러 작은 함수를 조합해서 더 큰 함수로 만드는 과정을 반복해서 전체 애플리케이션을 구축**해야한다.

`합성`의 경우 여러 가지 다른 구현과 패턴, 기법이 있다. 대표적인 예로 체이닝(Chaining)이 있다.

JavaScript에서는 `점(.)`을 사용하여 이전 함수의 반환값에 다음 함수(메서드)를 적용할 수 있다.

예를 살펴보도록 하자.

```js
const template = "hh:mm:ss tt";
const clockTime = template
  .replace("hh", "03")
  .replace("mm", "33")
  .replace("ss", "33")
  .replace("tt", "PM");

console.log(clockTime); // "03:33:33 PM"
```

위의 예에서 체이닝을 살펴보면 `replace` 메서드가 연속적으로 4번 사용되는 것을 볼 수 있다.

`clockTime`의 적용 과정을 보면 아래와 같다.

```js
const clockTime = template
  .replace("hh", "03") // "03:mm:ss tt" 반환
  .replace("mm", "33") // 이전 replace 반환값 사용 => "03:33:ss tt" 반환
  .replace("ss", "33") // 이전 replace 반환값 사용 => "03:33:33 tt" 반환
  .replace("tt", "PM"); // 이전 replace 반환값 사용 => "03:33:33 PM" 반환
```

<br/>

또한 합성의 목표는 '단순한 함수를 조합하여 **고차 함수**를 만들어 내는 것'이다.

아래의 예를 살펴보자.

```js
const civilianHours = (clockTime) => ({
  ...clockTime,
  hours: clockTime.hours > 12 ? clockTime.hours - 12 : clockTime.hours,
});

const appendAMPM = (clockTime) => ({
  ...clockTime,
  ampm: clockTime.hours >= 12 ? "PM" : "AM",
});

const both = (date) => appendAMPM(civilianHours(date));
```

`both` 함수는 두 함수(appendAMPM, civilianHours)에 값을 주입한다.

여기서 `civilianHours` 함수의 반환값은 `appendAMPM`의 매개변수가 되어 하나의 함수인 `both`를 호출하여 처리할 수 있다.

단, 이런 구문은 이해하기가 어렵고 유지보수나 확장이 어렵다.

그러므로 이럴 때는 작은 함수를 큰 함수로 조합하는 `compose` 함수를 사용하는 것이 좋다.

```js
const civilianHours = (clockTime) => ({
  ...clockTime,
  hours: clockTime.hours > 12 ? clockTime.hours - 12 : clockTime.hours,
});

const appendAMPM = (clockTime) => ({
  ...clockTime,
  ampm: clockTime.hours >= 12 ? "PM" : "AM",
});

const compose =
  (...fns) =>
  (arg) =>
    fns.reduce((composed, f) => f(composed), arg);

const both = compose(civilianHours, appendAMPM);

both(new Date());
```

이 방법을 사용하면 원하는 위치에 함수를 추가할 수 있어 확장이 가능하고 합성 순서 또한 변경하기 쉽다.

여기서 `compose` 함수는 함수를 인자로 받아 또 다른 함수를 반환하기에 '고차 함수'라 할 수 있다.

`compose` 함수는 전개 연산자로 인해 인자로 전달받은 함수들을 `fns`라는 배열로 만들고, `arg`를 인자로 받는 함수를 반환한다.

반환되는 함수의 `arg`에 값을 전달하면 `fns` 배열에 `reduce` 메서드가 호출되며 `arg`로 받은 값이 전달된다.

`arg`의 `reduce`의 초기값이 되며 각 이터레이션(반복)마다 `fns` 배열의 함수와 이전 값을 변환 함수(`reduce`의 콜백 함수)를 사용해 축약한 값을 전달한다.

여기서 `reduce`의 변환 함수는 이전 이터레이션의 결과값인 `composed`와 `f`를 인자로 받아 `f`에 `composed`를 적용하여 반환한다.

결국 마지막 함수가 호출되면서 최종 결과를 반환한다.

> 나는 해당 개발 서적을 보면서 이해하기가 어려운 부분이 많았다. 🥲<br/>
> 그래서 다른 예시로 내가 이해하기 위해 찾아봤던 링크를 공유한다. <br/>
> 링크 : [순수함수의 합성](https://velog.io/@teo/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%EC%88%9C%EC%88%98%ED%95%A8%EC%88%98%EC%9D%98-%ED%95%A9%EC%84%B1%ED%8E%B8)
