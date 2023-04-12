---
layout: single
classes: wide
title: "React 프로젝트에 TypeScript를 우당탕탕 적용하기(2)"
categories: TIL
tag: [TypeScript, toy]
toc: true
author_profile: false
sidebar:
  nav: "docs"
---

이전 포스트에서 리스트를 추가하는 것을 해봤는데 이제 리스트를 삭제하는 것을 해보도록 하자.

## 리스트 삭제하기

리스트를 삭제하려면 to-do 데이터를 가지고 있는 `App` 컴포넌트에서 `addTodoHandler` 함수를 생성한 것처럼 삭제하는 `removeTodoHandler` 함수도 생성해보자.

삭제를 하기 위해서 to-do의 `id`를 사용할 것이다.<br/>
클릭했을 때 해당 `id`를 `removeTodoHandler`에게 전달하여 이전 State의 to-do의 `id`와 전달받은 `id`가 동일하지 않은 것만 다시 렌더링하는 방식이다.

```js
// App.tsx

function App() {
  const [todos, setTodos] = useState<Todo[]>([]);

  const addTodoHandlder = (todoText: string) => {
    const newTodo = new Todo(todoText);

    setTodos((prevState) => prevState.concat(newTodo));
  };

  // to-do를 삭제하는 함수
  const removeTodoHandler = (todoId: string) => {
    setTodos((prevState) => prevState.filter((todo) => todo.id !== todoId));
  };

  return (
    <div>
      <NewTodo onAddTodo={addTodoHandlder} />
      <Todos items={todos} onRemoveTodo={removeTodoHandler} />
    </div>
  );
}

export default App;
```

`removeTodoHandler` 함수를 `Todos` 컴포넌트에 props로 전달했기 때문에 `Todos` 컴포넌트의 타입도 다시 수정해준다.<br/>
이 함수는 매개변수로 `id`를 받기 때문에 `id`의 타입도 정의해야하고 반환값이 없어 `void`로 타입을 지정한다.

```js
// Todos.tsx

import Todo from "../models/todo";

type TodosProps = {
  items: Todo[],
  // props 타입에 전달받는 onRemoveTodo를 작성해준다.
  onRemoveTodo: (id: string) => void,
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

이제 해당 to-do를 '클릭'시 `id`를 `removeHandler`의 인자로 전달하여 삭제를 진행한다.<br/>
클릭을 이용하는 것이기 때문에 `onClick` 이벤트를 사용하고 이제 id를 어떻게 가져와야할까?

나는 얼마 전에 포스팅한 `bind()` 메서드를 사용했다.

`bind()`는 주로 this를 일치시킬 때 자주 사용하는데 여기선 this의 인자를 null로 하고 두번째 인자를 작성하여 '인자를 전달'하는 용도로 사용했다.

여기서는 `item.id`를 `onRemoveTodo` 함수가 실행이되면서 첫번째 인자인 `todoId`로 가진다.

```js
// Todos.tsx

type TodosProps = {
  items: Todo[],
  onRemoveTodo: (id: string) => void,
};

const Todos: React.FC<TodosProps> = (props) => {
  return (
    <ul>
      {props.items.map((item) => (
        <li key={item.id} onClick={props.onRemoveTodo.bind(null, item.id)}>
          {item.text}
        </li>
      ))}
    </ul>
  );
};

export default Todos;
```

그러면 이제 삭제가 되는 것을 잘 되는 것을 볼 수 있다!

<p align="center">
  <img width="300" src="https://user-images.githubusercontent.com/96808980/210069998-d15f0c1d-5870-4e82-abb0-8806cf96eba4.gif" alt=""/>
</p>

<br />

## 마무리

이번에 처음 사용해본 React + TypeScript 토이 프로젝트였는데 생각보다 많이 헷갈렸던 부분이 많았던 것 같다.

그리고 TypeScript를 사용하면서 '이것까지 타입을 지정해야해?'라는 생각도 많이 들었다.

그런데 의외로 TypeScript에 에러가 발생하면 수정하는게 생각보다 재밌다.. ㅎㅎㅎㅎ
