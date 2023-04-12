---
layout: single
classes: wide
title: "Redux-toolkit Thunk로 비동기 처리하기!"
categories: TIL
tag: [React, Redux-toolkit, Thunk, 비동기, toy]
toc: true
author_profile: false
sidebar:
  nav: "docs"
---

> 생활코딩 채널의 [Redux-toolkit-thunk 강의](https://www.youtube.com/watch?v=K-3sBc2pUJ4&t=603s)를 보고 스스로 적용한 예제입니다.

## Redux-toolkit로 비동기 처리를 해보자!

Redux를 사용할 때 <u>동기적인 코드</u>만 Reducers에 작성할 수 있다. <br/>
그래서 Redux-toolkit을 이용하면서 어떻게 비동기 처리를 할 수 있을까 찾아보다 **Thunk**라는 것을 알게 되었다.

Thunk를 사용하면 비동기 작업을 Redux-toolkit에서 간편하게 할 수 있다고 한다. 그래서 생활코딩에서 Thunk 강의를 듣고 제일 만만한 [PokeAPI](https://pokeapi.co/)를 사용하여 리스트 렌더링을 해보려고 한다.

<br/>

### Thunk란?

Redux에서 Reducers는 반드시 <u>사이드 이펙트가 없어야하고, 동기적인 작업</u>인 함수여야만 한다. 왜냐하면 Redux의 Store는 비동기 로직에 대해 아무것도 알지 못하기 때문이다.

그래서 모든 비동기 작업의 처리는 Store의 외부에서 이루어져야한다.

하지만 Thunk를 사용하면 Store 내부에서 비동기 처리를 할 수 있도록 도와준다. 그럼 이제 Thunk를 생성해서 비동기 작업을 해보도록 하자.

<br/>

### Slice 준비하기

우선 우리가 사용할 API는 PokeAPI이기 때문에 포켓몬 데이터를 위한 Slice인 `pokemonDataSlice`를 생성하면 아래와 같다.

```js
// store/index.js

import { configureStore, createSlice } from "@reduxjs/toolkit";

const pokemonDataSlice = createSlice({
  name: "pokemonData",
  initialState: {
    // results : 포켓몬의 데이터를 받는 프로퍼티
    results: null,
    // status: 비동기의 상태를 위한 프로퍼티
    status: "Welcome",
  },
  // 비동기 작업을 위한 포스팅이기 때문에 아무런 Reducer를 등록하지 않았다.
  reducers: {},
});

const store = configureStore({
  reducer: {
    pokemon: pokemonDataSlice.reducer,
  },
});

export default store;
```

<br/>

### Thunk 생성하기

Thunk를 생성하려면 `@reduxjs/toolkit`에서 `createAsyncThunk`라는 함수를 가져와서 사용해야한다.

이 함수는 필수적으로 두 가지의 인자를 가지는데 아래와 같다.

- 첫번째 인자로 `action` 타입이다. 문자열로 사용자 임의로 설정한다.
- 두번째 인자로 `payloadCreator` 콜백 함수(비동기 처리를 위한 로직을 가지는 함수)이다.

이제 `createAsyncThunk` 함수를 호출해보자.

```js
// store/index.js

import {
  configureStore,
  createSlice,
  createAsyncThunk,
} from "@reduxjs/toolkit";

// Thunk를 생성하는 함수
const asyncFetch = createAsyncThunk("pokemonDataSlice/asyncFetch", async () => {
  const res = await fetch("http://pokeapi.co/api/v2/pokemon/");
  const data = await res.json();
  console.log(data);
});

const pokemonDataSlice = createSlice({
  name: "pokemonData",
  initialState: {
    results: null,
    status: "Welcome",
  },
  reducers: {},
});

const store = configureStore({
  reducer: {
    pokemon: pokemonDataSlice.reducer,
  },
});

export default store;
```

`createAsyncThunk` 함수를 사용하는 것 말고는 보통 fetch를 하는 것과 다른 점이 없다.

이 함수를 export 하여 `App` 컴포넌트의 버튼에 `onClick` 이벤트로 설정해보자.

```js
// App.js

import { useDispatch } from "react-redux";
// store/index.js의 asyncFetch 함수를 가져왔다.
import { asyncFetch } from "./store";

function App() {
  const dispatch = useDispatch();

  // 기존의 Redux-toolkit과 동일하게 Reducer를 호출하여 사용한다.
  const renderPokemonData = () => {
    dispatch(asyncFetch());
  };

  return (
    <div>
      <button onClick={renderPokemonData}>버튼</button>
    </div>
  );
}

export default App;
```

버튼을 클릭하면 정상적으로 API가 fetch되어 데이터를 받을 수 있다.

이 데이터 중에서 `results`의 value만 사용할 예정이다.

<p align="center">
  <img alt="" src="https://user-images.githubusercontent.com/96808980/211771860-812cf80b-ecbf-49a8-a1ca-6f15c4f1cb8a.png">
</p>

<br/>

### extraReducers 설정하기

Redux는 자체적으로 비동기(fetch API 등)를 지원하지 않아서 `createSlice`의 인자의 reducers 프로퍼티에 작성할 수 없다.

즉 reducers에는 `동기적인 처리`를 위한 Reducer만이 들어갈 수 있다.

그렇다면 비동기 처리를 할 수 있는 Reducer는 어디에 작성해야할까?

바로 `extraReducers`라는 프로퍼티에 작성할 수 있다.<br/>
`extraReducers`는 reducers와 다르게 비동기 처리를 할 수 있기 때문에 이 프로퍼티 내에 비동기 처리 관련 코드를 작성하면 된다.

단, `extraReducers`에는 <u>비동기 처리를 할 때의 3가지 상태(pending, fulfilled, rejected)마다 수동적으로 state를 정의</u>해야한다.

#### extraReducers의 builder

`extraReducers`는 `builder`라는 하나의 객체 형태의 인자를 가진다.<br/>
`builder`는 Slice의 외부에서 정의된 작업에 대한 응답으로 실행될 추가적인 **case Reducer**를 정의할 수 있는 메서드를 제공한다.

그래서 `builder`의 `addCase` 메서드를 사용하여 비동기 Thunk에 의해 전송되는 각각의 작업을 처리할 수 있다.

`addCase`는 Thunk 함수의 실행 상태인 `pending` / `fulfilled` / `rejected` 별 수행할 작업을 정의할 수 있다.

첫번째 인자로 해당 Thunk 함수의 **실행 상태**가 들어가며 두번째 인자로 비동기 처리로 얻은 State의 값을 업데이트한다.

그럼 `extraReducers`를 완성해보자.

```js
import {
  configureStore,
  createAsyncThunk,
  createSlice,
} from "@reduxjs/toolkit";

export const asyncFetch = createAsyncThunk(
  "pokemonDataSlice/asyncFetch",
  async () => {
    const res = await fetch("https://pokeapi.co/api/v2/pokemon/");
    const data = await res.json();
    return data.results;
  }
);

const pokemonDataSlice = createSlice({
  name: "pokemonData",
  initialState: {
    results: null,
    status: "Welcome",
  },
  reducers: {},
  // extraReducers의 인자인 builder로 각 fetch 상태에 따라 State를 업데이트한다.
  extraReducers: (builder) => {
    // Fetching 대기 상태인 Pending
    builder.addCase(asyncFetch.pending, (state, action) => {
      state.status = "Loading...";
    });
    // Fetching 완료 상태인 fulfilled
    builder.addCase(asyncFetch.fulfilled, (state, action) => {
      state.status = "Complete!";
    });
    // Fetching 오류 상태인 rejected
    builder.addCase(asyncFetch.rejected, (state, action) => {
      state.status = "Fail!";
    });
  },
});

const store = configureStore({
  reducer: {
    pokemon: pokemonDataSlice.reducer,
  },
});

export default store;
```

<br/>

### 비동기 데이터 사용하기

위의 코드에서는 비동기 데이터에서 얻지 않은 `status`를 State에 업데이트하고 있다.

이것을 App 컴포넌트에 사용해보고 렌더링해보도록 하자.

사용하려면 기존 Redux-toolkit과 동일하게 `useSelector`를 통해서 State를 추출하여 사용할 수 있다.

```js
import { useSelector, useDispatch } from "react-redux";
import { asyncFetch } from "./store";

function App() {
  const dispatch = useDispatch();

  // 업데이트한 status의 값을 추출한다.
  const fetchStatus = useSelector((state) => state.pokemon.status);

  const renderPokemonData = () => {
    dispatch(asyncFetch());
  };

  return (
    <div>
      <button onClick={renderPokemonData}>버튼</button>
      {/* 추출한 값을 렌더링한다. */}
      <h2>{fetchStatus}</h2>
    </div>
  );
}

export default App;
```

너무 빨리 넘어가는 것 같아서 gif 파일의 프레임을 낮춰서 찍어보면 아래와 같이 비동기 처리가 된다.

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/211777293-34169775-e261-43ba-b464-bdc71438562f.gif" alt=""/>
</p>

State에서 `status` 프로퍼티의 초기값인 'Welcome'이 맨 처음에 나오고 `pending` 상태의 'Loading...' 다음 `fulfilled` 상태의 'Complete!'가 출력된 것을 볼 수 있다.

이제 본격적으로 비동기 처리의 값을 이용하여 리스트를 렌더링해보자.

우선 `fulfilled` 상태에서 API에서 가져온 데이터를 정의해주지 않았기 때문에 `results` 프로퍼티를 State에 업데이트시켜주도록 하자.

```js
extraReducers: (builder) => {
    builder.addCase(asyncFetch.pending, (state, action) => {
      state.status = "Loading...";
    });
    builder.addCase(asyncFetch.fulfilled, (state, action) => {
      // 현재 state의 results 프로퍼티에 action.payload를 할당한다.
      state.results = action.payload;
      state.status = "Complete!";
    });
    builder.addCase(asyncFetch.rejected, (state, action) => {
      state.status = "Fail!";
    });
  },
```

#### action.payload는 어디에?

여기서 `action` 객체의 `payload`는 Thunk 함수의 두번째 인자인 **콜백 함수의 반환값**이 전달되게 된다.

즉, 아래처럼 `data.results`를 반환하고 있기 때문에 `action.payload`에 전달된다.

```js
// store/index.js

export const asyncFetch = createAsyncThunk(
  "pokemonDataSlice/asyncFetch",
  async () => {
    const res = await fetch("https://pokeapi.co/api/v2/pokemon/");
    const data = await res.json();
    return data.results;
  }
);
```

```js
// store/index.js

const pokemonDataSlice = createSlice({
  name: "pokemonData",
  initialState: {
    results: null,
    status: "Welcome",
  },
  reducers: {},
  extraReducers: (builder) => {
    builder.addCase(asyncFetch.pending, (state, action) => {
      state.status = "Loading...";
    });
    builder.addCase(asyncFetch.fulfilled, (state, action) => {
      // Thunk 함수의 두번째 인자인 콜백 함수의 반환값인 data.results가 action.payload가 된다.
      state.results = action.payload;
      state.status = "Complete!";
    });
    builder.addCase(asyncFetch.rejected, (state, action) => {
      state.status = "Fail!";
    });
  },
});
```

그럼 State 업데이트를 완료했기 때문에 App 컴포넌트에 리스트 렌더링을 해보도록 하자.

```js
import { useSelector, useDispatch } from "react-redux";
import { asyncFetch } from "./store";

function App() {
  const dispatch = useDispatch();

  // 포켓몬 데이터의 results를 추출하여 사용한다.
  const pokemonData = useSelector((state) => state.pokemon.results);
  const fetchStatus = useSelector((state) => state.pokemon.status);

  const renderPokemonData = () => {
    dispatch(asyncFetch());
  };

  return (
    <div>
      <button onClick={renderPokemonData}>버튼</button>
      <h2>{fetchStatus}</h2>
      <div>
        {/* pokemonData가 있는 경우엔 ul의 li태그로 리스트 렌더링을 하고 없다면 'Ready'라는 문자를 출력한다. */}
        {pokemonData ? (
          <ul>
            {pokemonData.map((pokemon) => (
              <li key={pokemon.name}>{pokemon.name}</li>
            ))}
          </ul>
        ) : (
          "Ready "
        )}
      </div>
    </div>
  );
}

export default App;
```

결과는 아래처럼 비동기 처리가 되면서 리스트 렌더링이 되고 있는 것을 볼 수 있다.

<p align="center">
  <img width="500" src="https://user-images.githubusercontent.com/96808980/211781572-984753bf-b889-4fd4-820c-967291d6b294.gif" alt=""/>
</p>
