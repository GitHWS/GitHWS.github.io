---
layout: single
classes: wide
title: "[22.12.22] Redux란?"
categories: TIL
tag: [React, Redux]
toc: true
author_profile: false
sidebar:
  nav: "docs"
---

## Redux란 무엇인가?

Redux는 **‘Cross-Component State’** 또는 **‘App-Wide State’**를 관리에 도움을 주는 시스템이다.

> 1. **Cross-Component State** : 다수의 컴포넌트에 영향을 주는 State
> 2. **App-Wide State** : 애플리케이션의 전반적인 컴포넌트에 영향을 주는 State

간단하게 **애플리케이션을 업데이트하고 화면에 표시하는 데이터를 관리하게 도와주는 라이브러리**이다.

## 왜 Redux를 사용하는가?

Cross-Component와 App-Wide에서 데이터를 넣고 전체 `props`를 업데이트하는 것은 매우 번거롭다.

그래서 이것을 위해 React의 **‘Context'**를 사용하여 Cross-Compoent와 App-Wide State를 손쉽게 관리를 했었다.

이 Context의 기능을 Redux를 통해서도 사용할 수 있다.

하지만 여기서 들 수 있는 의문점,

> **“Context를 이미 사용할 수 있는데 왜 굳이 Redux를 사용해야하나?”** 🤔

그 이유는 Context의 단점에 있다.

### Context의 단점 👎

- **사용 시 설정이 복잡할 수 있고 상태 관리가 매우 복잡해진다.**

작거나 중간 규모의 애플리케이션의 경우를 제외한 대규모 프로젝트의 경우 다양한 Context로 인해 전체 앱에 영향을 미치며 상태 관리가 매우 어려워진다.

하나의 Context를 사용하여 다수의 데이터(State)를 관리하려고 해도 하나의 Context가 다수의 작업을 하기에 유지와 보수가 매우 어려워진다.

<p style="text-align:center">
  <img src="https://user-images.githubusercontent.com/96808980/209133613-134ab0da-242b-4d65-9388-3033d0953415.png" alt="" width="400px"/>
</p>

또한 위의 상황을 인지하여 다수의 Context를 사용하여 각 데이터마다 Context를 나누면 각각의 `Provider`를 사용하면서 아래처럼 JSX 코드의 많은 중첩이 발생한다.

```js
<AuthProvider>
  <ThemeProvider>
    <UserInputProvider>
      <ModalDisplayProvider>
      </ModalDisplayProvider>
    </UserInputProvider>
  </ThemeProvider>
</AuthProvider>
```

- **성능의 문제가 발생한다.**

Context는 **저빈도의 업데이트에서 매우 좋은 선택**이나 **데이터의 변경이 매우 잦은 경우에는 좋지 않은 성능**을 낸다.

또한 Context는 `Provider`를 설정한 영역에만 사용이 가능하기 때문에 **유동적인 State 전파(Propagation)를 사용하기가 번거롭다.**

그에 반해 **Redux는 매우 유동적인 상태 관리 라이브러리**이다.


## Redux의 동작 방식 ⚙️

### 1. 중앙 데이터 저장소 생성

Redux는 **하나의 중앙 데이터 저장소**이다.

오직 **하나의 저장소를 생성하여 전체 애플리케이션의 모든 State를 저장**한다.

하나의 데이터 저장소에 데이터를 저장해서 컴포넌트에서 사용할 수 있다는 의미이다.

<p style="text-align:center">
  <img src="https://user-images.githubusercontent.com/96808980/209135839-fc3a8a5f-28d8-43db-b7ef-03a500c3e4e7.png" alt="" width="400px"/>
</p>


### 2. 저장소와 컴포넌트의 연결(Subscribe)

다음 이 저장소를 **컴포넌트가 구독(Subscribe)**하여 데이터가 변경될 때마다 저장소가 컴포넌트에게 알려준다.

이 때 컴포넌트는 필요한 데이터를 얻게 되고 사용할 수 있게 된다.

<p style="text-align:center">
  <img src="https://user-images.githubusercontent.com/96808980/209135857-7e2e59d5-c8f3-4822-9dd1-ca29362a44fc.png" alt="" width="400px"/>
</p>


### 3. 저장소의 데이터를 업데이트하는 Reducer 함수

저장소의 데이터를 업데이트할 때 Redux는 `Reducer`의 개념을 활용한다.

Redux의 **저장소의** **데이터 업데이트를 위해** `Reducer함수`**를 설정**해야한다.

이때 `Reducer함수` 는 저장소 데이터의 업데이트를 담당하게 된다.

<p style="text-align:center">
  <img src="https://user-images.githubusercontent.com/96808980/209135873-6263d90d-8d10-41e4-996d-d65fe14f1b6f.png" alt="" width="400px"/>
</p>


### 4. Reducer 함수에게 수행할 작업을 알려주기(Dispatch, action 객체)

여기서 컴포넌트에서 `Reducer 함수`를 통해 **저장소의 데이터를 업데이트를 하려면 어떤 작업을 해야하는지 알려주는** `action` **객체가 필요**하다.

이 과정을 `dispatch`라고 하는데 `dispatch`를 통해 `action` 객체를 `Reducer함수`에 전달하여 어떤 작업을 수행할 것인지 설명한다.

작업을 수행 시 저장소 내 데이터가 업데이트가 된다.

<p style="text-align:center">
  <img src="https://user-images.githubusercontent.com/96808980/209135894-fadc395d-b712-4193-a38e-39425de40299.png" alt="" width="400px"/>
</p>