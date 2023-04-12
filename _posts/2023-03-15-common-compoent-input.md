---
layout: single
classes: wide
title: "공통 Input 컴포넌트 구성을 어떻게 해야할까?"
categories: TIL
tag: [React, Styled-components, react-hook-form]
toc: true
author_profile: false
sidebar:
  nav: "docs"
---

> MERN 스택을 사용한 개인 프로젝트를 계획하기 위한 포스팅이다.

# 공통 Input 컴포넌트 어떻게 구성해야할까?

### 공통 Input 컴포넌트에 필요한 컴포넌트(태그)들

- `div` : `label`과 `input` 또는 `textarea`를 묶어 두 줄로 레이아웃을 구현하기 위해 필요
- `label` : `label`의 htmlFor 속성을 이용하여 `input`과 연결하기 위해 필요
- `input` or `textarea` : 사용자 입력을 받기 위해 필요

### 공통 Input 컴포넌트에 필요한 props

> **InputField 컴포넌트 구성** > `div` (InputField로 설정)
> ⎣ `label` (Label로 설정)
> ⎣ `input` 또는 `textarea` (UserInput)

- `label` : 라벨 텍스트, htmlFor
- `input` : `label`의 htmlFor과 동일한 값, type, placeholder(옵셔널), element(input or texture)
- `textarea` : `label`의 htmlFor과 동일한 값, placeholder(옵셔널), element(input or textarea)

### 공통 Input 컴포넌트

```ts
// InputBox는 InputLabel 컴포넌트와 UserInput 컴포넌트를 묶는 역할을 하고 props를 전달하는 역할을 하도록 한다.
const InputBox = styled.div`
  display: flex;
  flex-direction: column;
  gap: 5px;
`;

// Input과 함께 사용할 label 태그
const Label = styled.label`
  // ...스타일
`;

// 사용자 입력 Input 태그
const Input = styled.input`
  // ...스타일
`;

// 사용자 입력 textarea 태그
const Textarea = styled.textarea`
  // ...스타일
`;
```

<br/>

##### InputField.tsx

##### InputField의 Props 타입

```ts
interface InputFieldProps {
  // element에 따라 UserInput의 태그 결정되기 때문에 '유니온 타입'을 사용한다.
  element: "input" | "textarea";
  // label 컨텐츠 타입이다.
  label: string;
  // React.HTMLInputTypeAttribute로 input에 type 어트리뷰트의 모든 문자열 값을 유니온 타입으로 사용한다.
  type: React.HTMLInputTypeAttribute;
  // placeholder가 필요한 사용자 입력 태그에만 사용하기 때문에 '옵셔널 타입'으로 설정한다.
  placeholder?: string;
}
```

> 참고 🤔 :
> [javascript - Input types in Typescript interface - Stack Overflow](https://stackoverflow.com/questions/59504750/input-types-in-typescript-interface) > `HTMLInputTypeAttribute`라는 `input` 태그의 유틸리티 타입이 있다고 한다. 해당 타입은 input 태그에 사용되는 type 어트리뷰트의 모든 값을 가지는 유니온 타입이다.

<br/>

##### InputField.tsx

```tsx
// type을 "email" 등 input type 값으로 전달해서 UserInput의 id는 Label의 htmlFor와 동일한 값이여야 한다.
const InputField = ({ element, label, type, placeholder }: InputFieldProps) => {
  return (
    <InputBox>
      <Label htmlFor={type}>{label}</Label>
      <UserInput
        element={element}
        id={type}
        type={type}
        placeholder={placeholder}
      />
    </InputBox>
  );
};
```

<br/>

> 왜 같은 값을 가지는데 id와 type을 분리하는가? 🤔

나도 처음엔 id와 type을 분리하지 않고 하나의 props으로 전달했었다. 하나의 값을 사용하기 때문에 하나의 prop으로 전달해서 사용하는 것이 더 효율적이지 않을까 생각했기 때문이다.

하지만 같은 값이라도 props의 용도에 따라 분리하면 얻는 이점에 대해서 생각할 수 있었다.

이렇게 분리하면 장점은 아래와 같다.

```
1. 가독성 : 같은 값의 prop이라도 더 명확한 의미로 prop을 사용할 수 있다.

2. 예측 가능성 : prop 이름으로 용도를 예측하기가 용이하다.

3. 유지보수성 : 추후 코드가 변경이 되었을 때 같은 이름으로 사용된 prop의 영향을 최소화할 수 있다.
```

그래서 `UserInput` 컴포넌트에서 전달받은 `id`, `type` prop은 같은 값을 전달하나 분리한 이유는 `type` 값을 사용하는 '용도'가 다르기 때문에 분리한 것이다.

<br/>

##### UserInput의 Props 타입

```ts
interface UserInputProps {
  // 부모 컴포넌트로부터 전달받는 값을 'input'과 'textarea'로 제한한다.
  element: "input" | "textarea";
  // InputLabel 컨텐츠 타입
  label: string;
  // 부모 컴포넌트로부터 type의 값을 HTMLInputTypeAttribute 타입으로 받기 때문에 설정한다.
  type: React.HTMLInputTypeAttribute;
  // 부모 컴포넌트의 placeholder의 값은 옵셔널이기 때문에 있을 때 모든 string 타입으로 전달받고 없다면 undefined로 지정된다.
  placeholder: string | undefined;
}
```

##### UserInput.tsx

```tsx
import { useForm } from "react-hook-form";

// UserInput으로 전달받은 element prop에 따라 사용자 입력 태그(input / textarea)를 결정한다.
const UserInput = ({ element, id, type, placeholder }) => {
  const { register } = useForm();

  return (
    <>
      {element === "input" ? (
        <Input
          id={id}
          type={type}
          placeholder={placeholder}
          {...register(id)}
        />
      ) : (
        <Textarea id={id} {...register(id)} />
      )}
    </>
  );
};
```

### 사용 예시

```tsx
// element, type, label, placeholder가 모두 필요한 경우
<InputField element="input" type="email" label="Email" placeholder="example@example.com"/>

// element, type, label가 필요한 경우 (placeholder는 필요없음)
<InputField element="input" type="password" label="Password" />

// element, label가 필요한 경우 (type, placeholder는 textarea 태그를 사용하기 때문에 필요없음)
<InputField element="textarea" label="Bio" />
```
