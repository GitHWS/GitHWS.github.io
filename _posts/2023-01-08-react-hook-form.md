---
layout: single
classes: wide
title: "React-hook-form을 이용한 간단한 로그인 구현하기"
categories: TIL
tag: [React, React-Hook-Form, toy]
toc: true
author_profile: false
sidebar:
  nav: "docs"
---

이번에 프로젝트를 하면서 사용하게 될 React-hook-form 라이브러리의 기초를 연습하기 위해 간단한 로그인 폼을 구현해보기로 했다.

### React-Hook-Form의 사용 목적

React-Hook-Form은 간단하게 아래로 설명하고 있다.

> Performant, flexible and extensible forms with easy-to-use validation.

번역하면 '사용하기 쉬운 유효성 검사를 통해 성능이 뛰어나고 유연하며 확장 가능한 폼'이라고 한다.

간단하게 설명을 훑어보고 로그인 폼을 구현해보도록 하자.

[React-Hook-Form 바로가기](https://react-hook-form.com/)

#### 간결한 코드로 불필요한 리렌더링을 제거

이전에 form을 구현하려면 많은 수의 State가 필요로 했지만 이 라이브러리를 사용하면 짧은 코드로 동일한 기능을 가진 form을 구현할 수가 있다.<br>
또한 State가 업데이트될 때 발생하는 불필요한 리렌더링 또한 제거하여 최적의 성능을 낼 수 있다고 한다.

#### 빠른 마운팅

React-Hook-Form은 다른 라이브러리(Formik, ReduxForm)보다 빠른 마운팅 결과를 보여줄 수 있다고 한다.

여기서 가장 포인트는 **불필요한 리렌더링을 제거할 수 있다는 것**이 가장 매력적인 부분이 아닐까 생각된다.<br/>
form을 관리하는데 생성되는 State로 인해 `value`와 `onChange`/`onInput`으로 연결된 input에 입력을 할 때마다 발생하는 리렌더링을 줄일 수 있다는 것은 그만큼 React의 부담을 덜어주는 것이라고 생각할 수 있겠다.

<br/>

### React-Hook-Form 설치하기

사용하려면 아래의 명령어를 사용하여 라이브러리를 설치하도록 하자.

```
npm install react-hook-form
```

<br/>

### useForm 사용하기

이 라이브러리를 사용하려면 입력 필드(input)를 우선 '등록'해야한다.

그러기 위해서는 `useForm()`이라는 Hook을 React-Hook-Form에서 가져와서 사용해야한다.

```js
import { useForm } from "react-hook-form";
```

이 Hook은 아래처럼 다양한 함수를 가지고 있는데 이것에 맞춰서 작성하면 된다.

<p align="center">
  <img width="400" alt="image" src="https://user-images.githubusercontent.com/96808980/211183859-14ab8676-2624-4b71-84ab-0e8d47935c30.png">
</p>

<br/>

### register 메서드 사용하기

맨 처음 사용할 것은 `register`라는 메서드이다.

`register` 메서드는 말 그대로 어떤 필드 사용할 것인지 등록하는 것이다.

인자로는 `name`과 option인 `validation` 조건이 들어간다.

아래처럼 `register`의 첫번째 인자를 'id'로 했다면 해당 input 필드는 React-Hook-Form에 'id'로 등록이 된 것이다.

두번째 인자는 객체 형태로 전달하는데 validation 조건인 `required`를 적용했다. 이것을 `true`로 활성화 시 반드시 입력 값이 있어야한다.

이것말고도 최소/최대 길이 등 다양한 validation 조건을 설정할 수 있기 때문에 필요에 맞게 적용해보도록 하자.

```js
function App() {
  const { register } = useForm();

  return (
    <form>
      <input {...register("id", { required: true, maxLength: 10 })} />
      <button>Login</button>
    </form>
  );
}
```

<br/>

### State와 React-Hook-Form로 인한 렌더링의 차이

input을 등록했으니 이제 해당 값을 읽어오는 방법을 알아보자.

기존에는 아래처럼 input의 입력 값을 관리하는 State와 onChange props가 받을 함수가 필요하다.

```js
import { useState } from "react";

function Normal() {
  // input의 값을 저장하기 위한 State
  const [value, setValue] = useState("");

  // input의 값을 State에 업데이트하는 함수
  const changeInputHandler = (event) => {
    setValue(event.target.value);
  };

  return (
    <form>
      <input value={value} onChange={changeInputHandler} />
      <button>Login</button>
    </form>
  );
}

export default Normal;
```

하지만 React-Hook-Form을 사용하면 이것 또한 필요없이 값을 읽어올 수 있다.
React-Hook-Form을 사용하면 아래와 같이 간단한 코드로 동일한 기능을 구현할 수 있다.

```js
import { useForm } from "react-hook-form";

function App() {
  const { register, watch } = useForm();

  // watch로 해당 input 필드를 구독하여 값의 변화를 감지할 수 있다.
  console.log(watch("id"));

  return (
    <form>
      <input {...register("id")} />
      <button>Login</button>
    </form>
  );
}

export default App;
```

위의 두 개의 코드를 보면 동일한 기능이지만 React-Hook-Form에서는 State가 전혀 사용되지 않았다.<br/>
그래서 React-Hook-Form의 코드는 input에 입력을 해도 리렌더링이 발생하지 않는다.

이 두 코드의 차이를 보도록 하자.

위는 React-Hook-Form을 사용한 `App` 컴포넌트의 input, 아래는 State를 사용한 `Normal` 컴포넌트의 input이다.<br/>
출력되는 로그를 보면 아래 input에 사용된 State가 변경되었을 때 해당 컴포넌트가 리렌더링되면서 많은 로그를 출력하지만 React-Hook-Form을 이용한 input은 리렌더링이 되지 않아 출력이 안되는 것을 볼 수 있다.

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/211196008-2d54245a-0757-4ce0-abe3-a7619f5ec8e7.gif" alt=""/>
</p>

<br/>

### watch 사용하기

watch 메서드는 등록된 필드의 값의 변화를 감지할 수 있다.

인자로는 등록된 필드의 `name` 전달받는다. 전달받은 `name`의 필드의 값의 변화를 감지하여 반환한다.

```js
import { useForm } from "react-hook-form";

function App() {
  // watch 메서드를 객체 구조 분해로 사용한다.
  const { register, watch } = useForm();

  // name이 'id'로 등록된 input 필드의 값을 로그에 출력한다.
  console.log(watch("id"));

  return (
    <form autoComplete="off">
      <h3>React-Hook-Form 사용한 input</h3>
      <input {...register("id")} />
      <button>Login</button>
    </form>
  );
}

export default App;
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/211197052-a68617dc-34ef-4f37-b65e-b4d7817f93bb.gif" alt=""/>
</p>

위의 이미지에서 볼 수 있듯이 React-Dev-Tool이 리렌더링을 감지하고 있는 것을 볼 수 있다.
`watch`를 사용하면 해당 input 필드의 값 변경으로 인해 사용하는 컴포넌트가 리렌더링이 된다.

입력값을 반환하기 때문에 해당 값에 접근할 때 사용하면 편리하다.

<br/>

### handleSubmit으로 Form에 입력된 데이터를 제출하기

Form을 사용하는 이유는 당연히 사용자가 입력한 데이터를 전송하기 위해서 일 것이다.
그래서 이번에 입력된 데이터를 제출을 해보도록 하자.

우선 `useForm`에서 구조 분해를 통해 `handleSubmit`이라는 메서드를 추출한다.

`handleSubmit`은 말 그대로 Submit을 할 수 있는 메서드로 form 태그에 작성하면 된다.

form의 onSubmit prop에 `handleSubmit`을 호출하는데 인자로 Submit 함수를 전달받는다.

```js
import { useForm } from "react-hook-form";

function App() {
  const { register, handleSubmit } = useForm();

  // handleSubmit의 인자가 되는 Submit 함수
  const submitForm = (data) => {
    console.log(data);
  };

  return (
    <form autoComplete="off" onSubmit={handleSubmit(submitForm)}>
      <h3>React-Hook-Form 사용한 input</h3>
      <input
        type="text"
        {...register("id", { required: true, maxLength: 10 })}
      />
      <button>Login</button>
    </form>
  );
}

export default App;
```

위처럼 코드를 작성하면 button을 클릭했을 때 정상적으로 해당 input 필드명을 key로 가진 객체의 형태로 제출된다.

<p align="center">
  <img width="465" alt="image" src="https://user-images.githubusercontent.com/96808980/211199713-7074c5a6-26d5-45fa-adf8-2824c087a1fe.png">
</p>

<br/>

### formState를 사용한 error 메세지 출력하기

제출을 했으니 이제 validation에 충족하지 않을 때 에러를 출력해보도록 하자.

`useForm`에는 우리가 설정한 validation에 충족하지 않는 값을 감지할 수 있는 `formState`의 `errors` 객체가 있다.

> 이 기능 말고도 다양한 기능이 있으니 공식 문서를 참고하여 살펴보길 바란다.

`errors` 객체는 등록된 input 필드명을 `key`로 하여 에러의 정보가 등록된다.

그럼 아래의 코드를 실행해보자.

```js
import { useForm } from "react-hook-form";

function App() {
  const {
    register,
    handleSubmit,
    // formState에서 errors를 사용하면 validation의 충족 여부를 알 수 있다.
    formState: { errors },
  } = useForm();

  const submitForm = (data) => {
    console.log(data);
  };

  return (
    <form autoComplete="off" onSubmit={handleSubmit(submitForm)}>
      <h3>React-Hook-Form 사용한 input</h3>
      <input type="text" {...register("id", { maxLength: 10 })} />
      <button>Login</button>
      // 👇 필드명 id에 대한 error가 존재하면 메세지를 출력한다.
      {errors.id && <p>ERROR!!!</p>}
    </form>
  );
}

export default App;
```

위의 예시로는 필드명이 'id'인 input의 validation 조건은 `maxLength`가 `10`이여야 한다.

그럼 11자를 작성하고 버튼을 클릭해보자.

<p>
  <img alt="image" src="https://user-images.githubusercontent.com/96808980/211200187-2e575652-e880-4acf-b128-8dcd498ab9a3.png">
</p>

11자를 입력하고 제출을 했더니 제출은 진행되지 않고 설정한 에러 메세지가 출력된 것을 볼 수 있다.

필드명이 'id'인 input의 값이 validation에 충족하지 못했을 때 `errors` 객체를 로그에 출력하면 아래와 같다.

```js
{
  id: {
    type: "maxLength";
    message: '',
    ref: input,
  }
}
```

위의 코드는 논리연산자 `&&`으로 에러 메세지의 출력 여부를 관리하고 있다.

만약 에러가 발생하지 않으면 input의 필드명을 가진 객체가 존재하지 않기 때문에 `false`로, 에러가 발생한다면 input의 필드명을 가진 객체가 추가되어 `true` 값을 가진다.

현재는 'id' 필드의 `maxLength` validation 조건이 충족되지 않았으므로 에러 데이터가 추가되었고 그로 인해 `errors` 객체에서 id 프로퍼티에 접근할 수 있게 되어 에러 메세지를 출력된 것이다.

<br/>

### id와 동일하게 비밀번호 input 만들기

```js
import { useForm } from "react-hook-form";

function App() {
  const {
    register,
    handleSubmit,
    formState: { errors },
  } = useForm();

  const submitForm = (data) => {
    console.log(data);
  };

  console.log(errors);

  return (
    <form autoComplete="off" onSubmit={handleSubmit(submitForm)}>
      <h3>React-Hook-Form 사용한 input</h3>
      <div>
        <label htmlFor="id">아이디</label>
        <input
          type="text"
          id="id"
          {...register("id", { required: true, maxLength: 10 })}
        />
        {errors.id && <p>ERROR!!!</p>}
      </div>
      <div>
        <label htmlFor="pw">비밀번호</label>
        <input
          type="password"
          id="pw"
          {...register("pw", { required: true, minLength: 5 })}
        />
        {errors.pw && <p>ERROR!!!</p>}
      </div>
      <button>Login</button>
    </form>
  );
}

export default App;
```

위처럼 입력했을 때 모든 필드에 에러를 발생시키면 아래와 같이 `errors` 객체에 값이 들어간다.

<p align="center">
  <img alt="image" src="https://user-images.githubusercontent.com/96808980/211201672-5a7c382f-8560-4e0c-baec-2b653dbab2ec.png">
</p>

지금은 프로젝트를 위한 React-Hook-Form 기초로 포스팅을 했지만 아직 다양한 기능들이 많기 때문에 프로젝트를 진행하면서 사용하는 새로운 기능도 포스팅할 예정이다.

새로운 React-Hook-Form을 사용하면서 편리함을 제대로 경험한 것 같아 너무 재미있었다!
