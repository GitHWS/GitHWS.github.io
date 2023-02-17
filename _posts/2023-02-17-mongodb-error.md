---
layout: single
classes: wide
title: "[23.02.17] Attempted to check out a connection from closed connection pool 에러"
categories: TIL
tag: [React, Node, Express, MongoDB]
toc: true
author_profile: false
sidebar:
  nav: "docs"
---

> Udemy '【한글자막】 React, NodeJS, Express 및 MongoDB - MERN 풀스택 가이드' 강의를 공부하면서 발생한 에러를 정리하는 포스트입니다.

<br/>

## 에러 발생 상황

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/219660080-9ee92f1c-515a-4fa0-9609-87409758efa2.png" alt="에러가 발생한 코드"/>
</p>

위의 코드의 설명을 하자면,

1. MongoDB를 설치하여 `MongoClient`를 가져온다.
2. 상수 `url`에 내 MongoDB를 연결할 `<connection string uri>`를 할당한다.
3. `createProduct`라는 `비동기` 함수는 상수 `newProduct`에 요청 객체(req)의 `name`과 `price`의 값을 `name`, `price` 프로퍼티에 각각 할당한다.
4. `MongoClient`를 `url`을 인자로 하여 상수 `client` 클라이언트 인스턴스를 생성한다.
5. `client.connect()`로 MongoDB에 연결한다.
6. try...catch문 실행이 되는데 에러가 발생하지 않으면 해당 `client`의 `products` 컬렉션에 요청 객체로 인해 값이 저장된 `newProduct`를 저장한다.
7. 만약 에러가 발생하면 에러 메세지를 JSON 형태로 반환한다.
8. 이후 `client`를 `close()` 메서드로 닫는다.
9. ...이하 생략

<br/>

MongoDB에 문서를 생성하여 데이터를 추가하는 중이였는데 해당 에러가 발생했다.

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/219656338-189d143b-06eb-49a2-8bab-d9435b71bbf1.png" alt="Attempted to check out a connection from closed connection pool 에러"/>
</p>

> **PoolClosedError [MongoPoolClosedError]: Attempted to check out a connection from closed connection pool<br/>**
> 닫힌 연결 풀에서 연결을 체크아웃하려고 시도했다.

자세하게 무슨 상황인지 몰라 구글링을 했다.

그런데 해당 에러의 게시물은 많이 찾아볼 수 없어서 해결하기가 어려웠다. 하지만 [StackOverFlow 페이지](https://stackoverflow.com/questions/72155712/mongoruntimeerror-connection-pool-closed)에 비슷한 문제를 겪었던 케이스가 있었다.

해당 케이스의 질문자는 '데이터를 삽입하기 전에 닫히는 것처럼 보인다.'라고 말하고 있으며 답변으로는 아래와 같이 작성 시 에러를 해결할 수 있다고만 작성되어있다.

```js
setTimeout(() => {
  client.close();
}, 1500);
```

`setTimeout`을 이용하여 데이터를 삽입하기 전에 클라이언트가 닫히는 것을 1.5초 기다렸다가 닫는 것이다.

그렇게 되면 당연히 1.5초를 기다리는 동안 데이터가 삽입이 될 것이며 1.5초가 지나면 클라이언트가 닫히게 하는 해결 방법인 것이다.

하지만 해당 작업이 만약 1.5초가 지나면 동일한 문제가 발생하지 않을까해서 해결법을 다시 생각해봤다.

<br/>

### 해결 과정

우선 해당 에러의 원인은 **데이터를 컬렉션에 삽입하기 전에 클라이언트가 닫히기 떄문이다.**

그렇다면 클라이언트가 데이터를 컬렉션에 삽입하기 전까지 닫히지 않게 하면 된다.

우선 컬렉션에 데이터를 넣는 작업을 하는 상수 `result`가 무슨 값을 반환하는지 먼저 확인했다.

```js
const result = db.collection("products").insertOne(newProduct);
```

위의 상수 `result`는 `insertOne` 메서드가 반환하고 있다.

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/219666201-cb595c58-3326-465c-9d15-6eee55603b5d.png" alt="insertOne 메서드 설명"/>
</p>

해당 메서드는 `Promise`를 반환하고 있다. Promise는 `pending` 과정을 거쳐 성공 시 `fulfilled`가 되거나 실패 시 `rejected` 상태를 반환한다.

즉 `pending` 과정에서 **시간이 소요된다**는 것이다!

이제 `result`를 콘솔로 찍어보고 값이 올바르게 전송이 되나 살펴보도록 하자.

```js
const result = db.collection("products").insertOne(newProduct);
console.log(result);
```

아래처럼 `Postman`을 사용하여 `createProduct`에 해당하는 미들웨어에 요청을 보냈다.

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/219667043-584854ed-ae82-4311-9447-4dce02439311.png" alt="포스트맨으로 POST 요청을 보낸 이미지"/>
</p>

결과는 앞에서 말했듯 에러가 발생하고 Promise인 `result`는 아래처럼 **pending** 상태가 콘솔에 출력된다.

```
Promise { <pending> }
```

즉, `pending` 상태일 때 클라이언트의 `close()`메서드가 호출되어 **MongoDB와의 연결이 먼저 닫히는 것**이다.

그렇기 때문에 `close()` 메서드에 `setTimeout`을 사용했을 때 가능했던 이유는 `pending` 상태의 Promise가 처리되는 것이 1.5초가 넘지 않았기 때문이다.

하지만 `setTimeout`에서 설정하는 타임은 상황에 따라 적절하지 않을 수 있어 해당 코드를 사용하지 않고 아래의 코드를 사용했다.

```js
const result = await db.collection("products").insertOne(newProduct);
```

`await` 키워드를 추가해서 해당 작업이 끝날 때까지 기다렸다가 MongoDB 클라이언트를 닫는 것!

그러면 굳이 `setTimeout`을 사용할 필요없이 해결할 수 있다.

동일하게 위의 이미치처럼 POST 요청을 보내서 해당 코드의 결과를 보면 MongoDB에 성공적으로 저장된 것을 볼 수 있다.

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/219671780-f99435cc-1ed0-44ea-b0be-a7405c7350ac.png" alt="MongoDB 데이터 저장 성공"/>
</p>
