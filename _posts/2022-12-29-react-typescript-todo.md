---
layout: single
classes: wide
title: "[22.12.30] React 프로젝트에 TypeScript를 우당탕탕 적용하기(1)"
categories: TIL
tag: [TypeScript, toy]
toc: true
author_profile: false
sidebar:
  nav: "docs"
---

지금까지 배운 TypeScript로 추가와 삭제가 가능한 To-do List를 만들어보자.

## React 프로젝트에서 TypeScript 적용하기

우선 React 프로젝트에서 TypeScript를 적용하려고 한다.

React 프로젝트를 생성할 때 보통 아래의 명령어를 가장 많이 사용하곤 한다.

```
npx create-react-app {폴더명}
```

이 명령어를 CRA라고 하는데 여기서 TypeScript를 사용하기 위해선 아래의 명령어를 추가로 붙여줘야한다.

```
npx create-react-app {폴더명} --template typescript
```

위의 명령어로 설치를 진행하면 기존의 CRA와 다르게 파일 확장자가 `.js`가 아닌 `.tsx`로 생성된 것을 볼 수 있다.

<p align="center">
  <img width="200" alt="" src="https://user-images.githubusercontent.com/96808980/209963919-6ed93942-82b8-49f4-ba5d-cc48cdca1e7d.png">
</p>

`.tsx` 파일은 TypeScript와 JSX 문법을 사용할 수 있다.

그래서 이번 to-do List와 관련된 컴포넌트를 생성하는데 모두 `.tsx` 파일로 생성할 것이다.

<br/>

### Todos 컴포넌트 생성하기

우선 `src` 폴더 안에 컴포넌트를 모으는 `components` 폴더를 생성하고 그 안에서 `Todos.tsx` 컴포넌트 파일을 생성한다.

기존의 `function` 키워드를 사용하지 않고 아래의 코드처럼 화살표 함수를 사용하여 컴포넌트를 생성할 것이다.

```js
// Todos.tsx

import React from "react";

const todos = () => {
  return <div>Todos</div>;
};

export default Todos;
```

이 컴포넌트를 to-do 아이템을 모으는 컴포넌트이다.

그래서 리스트 태그인 `ul`로 수정하고 내부의 아이템은 더미 데이터를 이용하여 배열 메서드인 map을 사용하여 리스트 렌더링을 진행할 것이다.

```js
// Todos.tsx

import React from "react";

const Todos = () => {
  return <ul>Todos</ul>;
};

export default Todos;
```

App 컴포넌트에서 더미 데이터의 `props`를 받아서 리스트 렌더링을 해보자.

우선 더미 데이터를 `items`라는 `props`로 Todos 컴포넌트에 전달한다.

```js
// App.tsx

import Todos from "./components/Todos";

function App() {
  const dummy_data = ["리액트 배우기", "타입스크립트 배우기"];

  return (
    <div>
      <Todos items={dummy_data} />
    </div>
  );
}

export default App;
```

그런데 이 상황에서 에러가 발생한다.

이 에러는 Todos 컴포넌트에 들어올 `props`의 타입을 설정해주지 않아서 생긴 에러이다.

<p align="center">
  <img width="933" alt="image" src="https://user-images.githubusercontent.com/96808980/209965905-7771234e-5887-4e21-b38e-bd4af2ceb3f9.png">
</p>

그래서 Todos 컴포넌트에 `props`의 타입을 설정하러 가보자.

```js
// Todos.tsx

const Todos: FC<{ items: string[] }> = (props) => {
  return (
    <div>
      {props.items.map((item) => (
        <li>{item}</li>
      ))}
    </div>
  );
};

export default Todos;
```

작성한 것을 보면 조금 특이한 코드가 보인다.

`FC`는 무엇이며 뒤에 있는 제네릭은 무엇일까?

```js
// Todos.tsx

const Todos: FC<{ items: string[] }> = (props) => {
  // ...
};
```

React에서 TypeScript를 사용할 떄는 해당 컴포넌트의 타입을 알려줘야한다.

그래서 Todos의 타입은 FC(FunctionalComponent)라는 의미이다.

그리고 바로 뒤에 나오는 `<{ items: string[] }>`은 해당 컴포넌트가 전달받을 수 있는 props의 타입을 정의한다.

그래서 위처럼 함수형 컴포넌트에 바로 작성해도 되고 `type` 키워드를 사용하는 타입 별칭 기능을 사용해도 된다.

개인적으로 `type` 키워드를 사용하는 것이 가독성에는 좋아보인다.

```js
// Todos.tsx

type TodosProps = {
  items: string[],
};

const Todos: React.FC<TodosProps> = (props) => {
  return (
    <div>
      {props.items.map((item) => (
        <li>{item}</li>
      ))}
    </div>
  );
};

export default Todos;
```

이렇게 작성하면 더미 데이터가 map 메서드를 거쳐 리스트 렌더링이 성공적으로 된다.

<p align="center">
  <img width="334" alt="image" src="https://user-images.githubusercontent.com/96808980/209968146-393b5bff-5e65-476b-8b4d-336855d9fce3.png">
</p>

<br/>

### 객체 타입의 to-do 데이터 모델 정의하기

더미 데이터를 사용했지만 리스트 렌더링을 위한 필수적인 고유의 값을 가진 `id`값도 필요하다.<br/>
그러면 자연스럽게 하나의 text에 id가 붙어서 두 개의 데이터를 담고 있는 객체가 필요하다.

그러면 이제 객체 타입의 to-do 데이터를 미리 정의하여 가져다 사용하는 방법을 사용해보자.

우선 `src` 폴더에 `models` 폴더를 생성하여 안에 `todo.ts` 파일을 생성해준다.
`.ts` 파일을 사용하는 이유는 이 파일 내에서는 JSX 코드를 사용하지 않기 때문이다.

나는 여기서 `class`를 사용할 것이다. 왜냐하면 우리가 사용해야할 to-do 데이터는 객체 타입이며 동시에 동일한 프로퍼티가 들어가야 한다.

그래서 `class`를 사용하여 `new` 키워드를 이용하여 인스턴스를 생성하여 손쉽게 데이터를 생성할 수 있기 때문이다.

우리는 이것을 'model'이라고 한다.

```js
// todo.ts

class Todo {
  // Todo 타입 안의 프로퍼티 타입 지정
  id: string;
  text: string;

  // 인스턴스 생성 시 가지게 될 값을 정의
  constructor(todoText: string) {
    this.id = new Date().toISOString();
    this.text = todoText;
  }
}

export default Todo;
```

> 데이터 모델을 작성해서 export하면 '타입'으로 지정할 수 있다.

이 모델을 이제 App 컴포넌트의 더미 데이터에 사용해보도록 하자.

```js
// App.tsx

import Todos from "./components/Todos";
import Todo from "./models/todo";

function App() {
  const dummy_data = [new Todo("데이터 모델"), new Todo("신기해요!")];

  return (
    <div>
      <Todos items={dummy_data} />
    </div>
  );
}

export default App;
```

Todos 컴포넌트에 전달되는 `props`의 타입도 변경이 되었으니 수정한다.

```js
// Todos.tsx

import Todo from "../models/todo";

type TodosProps = {
  // 생성한 데이터 모델을 타입으로 정의했다.
  // 즉, { id: string, text: string }[]과 동일하다.
  items: Todo[],
};

const Todos: React.FC<TodosProps> = (props) => {
  return (
    <ul>
      {props.items.map((item) => (
        <li key={item.id}>{item.text}</li>
      ))}
    </ul>
  );
};

export default Todos;
```

여기서 변경 사항을 보면 `props`의 타입을 정의하는 `TodosProps`의 `items` 프로퍼티 타입이 `Todo[]`로 변경하였다.

기존에는 문자열 엘리먼트만 더미 데이터 배열에 있었지만 수정 후부터 `Todo` 객체 데이터 모델을 사용하기 때문에 수정한 부분이다.

위처럼 수정 시 아래처럼 결과가 출력되게 된다.

<p align="center">
  <img width="334" alt="image" src="https://user-images.githubusercontent.com/96808980/209972889-50869e69-7e16-4ac6-b1fd-e968c259a3e5.png">
</p>

<br/>

### Form을 이용한 to-do 추가

이제 더미 데이터를 사용하지 않고 직접 입력하여 to-do를 추가해보자.

App 컴포넌트에서 더미 데이터를 삭제하고 `useState`를 통해 `todos`라는 State를 생성한다.

```js
// App.tsx

import { useState } from "react";

import NewTodo from "./components/NewTodo";
import Todos from "./components/Todos";

function App() {
  const [todos, setTodos] = useState([]);

  return (
    <div>
      <NewTodo />
      <Todos items={todos} />
    </div>
  );
}

export default App;
```

그리고 `form`을 사용할 `NewTodo` 컴포넌트를 생성해보자.

```js
// NewTodo.tsx

const NewTodo = () => {
  return (
    <form>
      <label htmlFor="todoInput">TO-DO</label>
      <input type="text" id="todoInput" />
      <button>Add Todo</button>
    </form>
  );
};

export default NewTodo;
```

코드를 위처럼 작성하고 `App` 컴포넌트에 `NewTodo` 컴포넌트를 추가하면 아래와 같은 결과가 나온다.

<p align="center">
  <img width="334" alt="image" src="https://user-images.githubusercontent.com/96808980/209973774-0f32dd73-c71b-479f-ad0a-e9d0cacf98d4.png">
</p>

이제 `input`에 입력되는 값을 받아 리스트에 추가해보자.

우선 데이터를 추가하려면 저장할 State가 있어야하는데 `useState`를 이용하거나 `useRef`를 이용해야한다.

이 둘은 차이가 있으나 본인이 원하는 것으로 사용하면 될 것이다.

나는 `useRef`를 사용할 것이다.

```js
// NewTodo.tsx

import { useRef } from "react";

const NewTodo = () => {
  const todoInputRef = useRef(null);

  return (
    <form>
      <label htmlFor="todoInput">TO-DO</label>
      <input ref={todoInputRef} type="text" id="todoInput" />
      <button>Add Todo</button>
    </form>
  );
};

export default NewTodo;
```

이제 input의 값을 ref로 인해 참조할 수 있다. 이것을 모든 todo를 관리하는 App 컴포넌트의 State에 넘겨줘야한다.

React는 부모에서 자식으로 전달만 가능하고 자식에서 부모에게는 전달하지 못하기 때문에 App 컴포넌트에서 함수를 생성해서 인자로 받아오는 'State 끌어올리기'가 필요하다.

`input`의 값을 State에 추가하는 `addTodoHanlder` 함수를 생성해서 props의 형태로 `NewTodo` 컴포넌트에 전달한다.

```js
// App.tsx

function App() {
  const [todos, setTodos] = useState<Todo[]>([]);

  const addTodoHandlder = (todoText: string) => {
    const newTodo = new Todo(todoText);

    setTodos((prevState) => prevState.concat(newTodo));
  };

  return (
    <div>
      <NewTodo onAddTodo={addTodoHandlder} />
      <Todos items={todos} />
    </div>
  );
}
```

`useState`로 State를 생성했는데 `useState`의 초기값은 `[]`으로 input의 값을 저장하는 역할을 한다.

단, `useState` 또한 어떤 타입인지 명시해야하는데 `Todo[]`으로 명시하여 `{ id: string, text: string }` 요소로 이루어진 배열이라는 것을 알려준다.

그리고 addTodoHanlder는 input의 값을 받기 위해 매개변수 `todoText`로 설정한다.<br/>
전달받은 `todoText`는 문자열만 전달되기 때문에 이것을 `Todo[]`에 들어갈 수 있는 타입으로 변환해야한다.

이럴 때 `todoText`를 데이터 모델의 '인스턴스'를 생성하여 사용하면 된다.<br/>
인자를 받아 데이터 모델의 인스턴스 생성하면 아래와 같이 `Todo[]`의 타입에 맞는 요소가 생성이 되기 때문이다.

```js
// App.tsx

// 만약 todoText에 "빨래 하기"가 전달된다면 ?
const newTodo = new Todo(todoText); // { id: ~ , text: "빨래 하기" }
```

그 다음 `useState`의 setter 함수를 사용해서 기존의 State에 생성한 인스턴스 `newTodo`를 합쳐준다.

기존의 State에 기반하기 때문에 콜백 함수를 인자로 사용하여 `concat`을 진행하였다.

```js
// App.tsx

setTodos((prevState) => prevState.concat(newTodo));
```

완성된 함수는 `NewTodo` 컴포넌트에 props의 형태로 전해주고 `NewTodo`의 props의 타입을 아래처럼 올바르게 수정한다.

우리가 전달하는 `addTodoHandler` 함수는 반환값이 없기 때문에 `void`를 사용한다.

```js
// NewTodo.tsx

import { useRef } from "react";

const NewTodo: React.FC<{ onAddTodo: (text: string) => void }> = (props) => {
  const todoInputRef = useRef(null);

  return (
    <form>
      <label htmlFor="todoInput">TO-DO</label>
      <input ref={todoInputRef} type="text" id="todoInput" />
      <button>Add Todo</button>
    </form>
  );
};

export default NewTodo;
```

이제 'form이 제출이 되었을 때 todos의 상태를 업데이트'해야하기 때문에 form을 위한 submit 함수를 생성한다.

```js
// NewTodo.tsx

 const submitHandler = (event) => {
    event.preventDefault();

    props.onAddTodo(todoInputRef.current!.value);
  };
```

여기서 에러가 발생한다.

<p align="center">
  <img width="634" alt="image" src="https://user-images.githubusercontent.com/96808980/210028383-a708f4df-a61b-4da2-a9a9-0c49ee060787.png">
</p>

이유는 브라우저의 기본 기능(제출 시 새로고침)을 막기 위한 `event` 객체의 타입을 TypeScript가 모르기 때문이다.
`event`의 타입을 명시하기 위해 기존의 정의된 타입인 `FormEvent`를 사용한다.

`FormEvent`는 Form의 Event 객체 타입을 정의할 때 사용하는 기존에 정의된 타입이다. 타입을 지정을 해주면 에러가 해결된다!

그리고 함수에 input의 값에 접근하기 위한 `current!`는 값이 반드시 있을 것이라는 표시이다.

더 확실하게 하기 위해 빈 문자열일 때 `input`에 포커스가 가고 해당 함수를 종료하는 유효성 검사도 만들어주었다.

```js
// NewTodo.tsx

const NewTodo: React.FC<{ onAddTodo: (text: string) => void }> = (props) => {
  const todoInputRef = useRef<HTMLInputElement>(null);

  const submitHandler = (event: React.FormEvent) => {
    // 브라우저의 기본 기능(새로고침)을 막기 위한 preventDefault()
    event.preventDefault();

    // ?는 해당 값이 없을 수 있다는 것을 의미이다.
    if (todoInputRef.current?.value === "") {
      todoInputRef.current.focus();
      return;
    }

    // input에 값을 입력 시 제출할 것이기 때문에 !를 사용했다.
    props.onAddTodo(todoInputRef.current!.value);
  };

  return (
    <form onSubmit={submitHandler}>
      <label htmlFor="todoInput">TO-DO</label>
      <input ref={todoInputRef} type="text" id="todoInput" />
      <button>Add Todo</button>
    </form>
  );
};

export default NewTodo;
```

이렇게 작성하면 빈 문자열일 때 제출하면 input을 포커스가 되고 `submitHandler` 함수가 종료가 되어 빈 문자열이 추가되지 않는다.<br/>

코드를 여기까지 작성하면 정상적으로 추가 가능한 To-do 리스트가 된다!

<p align="center">
  <img width="300" src="https://user-images.githubusercontent.com/96808980/210029768-6944aad8-4ae2-4de3-b641-617dcbe74f13.gif" alt=""/>
</p>
