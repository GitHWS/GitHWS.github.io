---
layout: single
classes: wide
title: "기존 CRA 프로젝트에서 TypeScript 적용하기 - 에러 마주하기"
categories: TIL
tag: [TypeScript, CRA, error]
toc: true
author_profile: false
sidebar:
  nav: "docs"
---

기존 CRA에서 TypeScript를 적용하던 중 확실히 많은 에러들이 발생했다.

많은 에러가 발생하는 것은 어처피 예상을 했기 때문에 이참에 TypeScript를 적용하는 중 발생하는 에러를 처음부터 해결하면서 포스팅을 하는 것도 좋을 것 같아 기록을 남긴다.

> 물론 TypeScript를 사용한 경험이 있는 이상 JavaScript만을 사용하지 않을 것 같지만...ㅎㅎ

<br/>

## 첫번째 에러.

우선 첫번째 에러는 `MyButton` 컴포넌트를 수정하면서 발생한 에러이다.

> TypeScript를 적용하면서 `.js`에서 `.tsx`로 파일의 확장자를 변환할 때 무조건 볼 수 있는 에러인거 같다.

```
'React'은(는) UMD 전역을 참조하지만 현재 파일은 모듈입니다. 대신 가져오기를 추가해 보세요.
```

```tsx
const MyButton = ({ text, type = "default", onClick }) => {
  const btnType = ["positive", "negative"].includes(type) ? type : "default";

  return (
    <button
      onClick={onClick}
      className={["MyButton", `MyButton_${btnType}`].join(" ")}
    >
      {text}
    </button>
  );
};

export default MyButton;
```

이 에러는 메세지에서 답을 주고 있다.

> 가져오기(import)를 추가해 보세요.

그래서 `React` 모듈을 import했다. 물론 이 방법으로 해결했다.

```tsx
import React from "react"; // 이렇게 하면 에러가 해결된다.

const MyButton = ({ text, type = "default", onClick }) => {
  const btnType = ["positive", "negative"].includes(type) ? type : "default";

  return (
    <button
      onClick={onClick}
      className={["MyButton", `MyButton_${btnType}`].join(" ")}
    >
      {text}
    </button>
  );
};

export default MyButton;
```

### 하지만 React 모듈은 React 17부터 import할 필요가 없다고 들었는데 이걸 컴포넌트마다 추가하는 건 좀...

소규모 프로젝트이기 때문에 매우 적은 컴포넌트를 생성해서 사용하고 있지만 만약 확장이 되어 더 많은 컴포넌트가 생긴다면 일일이 React 모듈을 import하는 것은 정말 번거로운 일이 아닐 수 없다.

그래서 다른 해결책을 찾아봤는데 [stack overflow](https://stackoverflow.com/questions/64656055/react-refers-to-a-umd-global-but-the-current-file-is-a-module)의 방법을 참고했다.

이 문제를 해결하려면 아래와 같은 조건을 만족해야 한다.

```
- typescript는 최소 4.1버전 이상 👈 typescript@4.9.5로 만족

- react and react-dom은 최소 17버전 이상 👈 react@18.2.0, react-dom@18.2.0으로 만족

- tsconfig.json의 'jsx' compilerOption은 'react-jsx'이나 'react-jsxdev'의 값을 가지고 있어야한다.
```

나는 첫번째, 두번째 조건은 만족하고 있으나 세번째 조건을 만족하지 못했다.

`tsconfig.json` 파일이 없었던 것이다.

그래서 `tsc --init`으로 `tsconfing.json` 파일을 생성하고 아래와 같이 수정했다.

```json
{
  "compilerOptions": {
    // ...

    "jsx": "react-jsx"; /* Specify what JSX code is generated. */,

    // ...
  }
}
```

이렇게 하면 컴포넌트마다 `React` 모듈을 계속해서 import할 필요가 없어져 번거러움도 해결할 수 있다.

<br/>

## 두번째 에러.

```
'HTMLElement | null' 형식의 인수는 'Element | DocumentFragment' 형식의 매개 변수에 할당될 수 없습니다.
'null' 형식은 'Element | DocumentFragment' 형식에 할당할 수 없습니다.
```

두번째 에러는 `index.js`를 tsx로 변환했을 때 발생한 에러이다.

```tsx
import React from "react";
import ReactDOM from "react-dom/client";
import "./index.css";
import App from "./App";
// 👇 이 document.getElemetById('root')에서 발생한 에러
const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

이것은 `ReactDOM.createRoot()`가 전달받을 수 있는 인자의 타입은 아래처럼 정의되어 있다.

```ts
/**
 * Replaces `ReactDOM.render` when the `.render` method is called and enables Concurrent Mode.
 *
 * @see https://reactjs.org/docs/concurrent-mode-reference.html#createroot
 */
export function createRoot(
  container: Element | DocumentFragment,
  options?: RootOptions
): Root;
```

그 중 첫번째 인자인 `root.render()` 메서드가 인자로 전달받은 `<App />`을 렌더링할 위치인 `container`의 타입은 `Element | DocumentFragment`로 지정되어 있다.

> 하지만 `index.tsx`에서 TypeScript는 DOM을 모르기 때문에 `document.getElementById("root")`의 타입을 알 수가 없어 분명히 있는데도 불구하고 `null`의 가능성을 염두해둔다.
>
> 도움받은 링크 : [인프런 질문&답변](https://www.inflearn.com/questions/533756/18%EB%B2%84%EC%A0%84%EC%97%90%EC%84%9C%EC%9D%98-reactdom-render)

그렇기 때문에 `document.getElementById("root")`이 무슨 타입인지 '명시(단언)'를 해줘야하는데 이를 `non-null assertion`이라 한다.

> **어서션(assertion)이란?** <br>
> 값이 TypeScript에서 <u>**예상하는 것과 다른 타입이라는 것을 TypeScript에게 단언하는 것**</u>을 말한다.

`non-null assertion`은 이건 TypeScript에게 "이건 `null`이나 `undefined`가 아니고 무조건 `이 타입`으로 간주해!'라고 하는 것과 같다고 생각하면 된다.

그렇다면 코드로는 어떻게 할 수 있을까?

### as를 사용한 타입 어서션

```tsx
import React from "react";
import ReactDOM from "react-dom/client";
import "./index.css";
import App from "./App";

const root = ReactDOM.createRoot(
  document.getElementById("root") as HTMLElement
);
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

위의 코드에서는 `document.getElementById("root")`의 타입을 `HTMLElement`라고 단언하고 있다.

### !를 사용한 타입 어서션

```tsx
import React from "react";
import ReactDOM from "react-dom/client";
import "./index.css";
import App from "./App";

const root = ReactDOM.createRoot(document.getElementById("root")!);
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

위의 코드의 `!`는 `as HTMLElement`와 동일한 역할을 한다.

이렇게 하면 해당 에러를 해결할 수 있다.

<br/>

## 세번째 에러.

세번째 에러는 'TypeScript를 사용하려면 이런 거까지 생각해야하는구나'라는 것을 생각하게 만들어줬다.

```
'false | Element' 형식은 'Element' 형식에 할당할 수 없습니다.
'boolean' 형식은 'ReactElement<any, any>' 형식에 할당할 수 없습니다.
```

아래의 코드는 `DiaryEditor` 컴포넌트에서 `MyHeader` 컴포넌트를 사용하는 부분이다.

```tsx
<MyHeader
  headText={isEdit ? "일기 수정하기" : "새 일기쓰기"}
  leftChild={<MyButton text="< 뒤로가기" onClick={() => navigate(-1)} />}
  // 👇 여기서 에러가 발생했다!
  rightChild={
    isEdit && (
      <MyButton text="삭제하기" type="negative" onClick={handleRemove} />
    )
  }
/>
```

에러가 발생한 이유는 아주 간단했다. `MyHeader` 컴포넌트의 props 타입은 아래와 같이 지정되어 있었다.

```ts
interface MyHeaderProps {
  headText: string;
  leftChild: JSX.Element;
  rightChild: JSX.Element;
}
```

여기서 `rightChild`를 보면 타입은 `JSX.Element`로 React 컴포넌트, React 프레그먼트, HTML 요소 등을 나타내는 타입이다.

하지만 `MyHeader`의 `rightChild` prop은 <u>**논리 AND 연산**</u>으로 인해 결과값이 `boolean`이 될 수 있음을 알 수 있다.

그런데 이러한 결과를 배제하고 `JSX.Element`만을 타입으로 지정해버린 것..! 아래처럼 적용하면 깔끔하게 에러를 해결할 수 있다!

```ts
interface MyHeaderProps {
  headText: string;
  leftChild: JSX.Element;
  rightChild?: false | JSX.Element;
}
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/236675199-b2148b9a-dcbd-4363-a669-c561cd710af3.png" alt="에러 해결 이미지">
</p>

<br/>
