---
layout: single
classes: wide
title: "CRA 프로젝트에 TypeScript 적용 시 설치 경고"
categories: TIL
tag: [dependency, TypeScript, CRA, npm]
toc: true
author_profile: false
sidebar:
  nav: "docs"
---

기존 create-react-app으로 생성한 프로젝트에 TypeScript를 적용하려고 [create-react-app Docs](https://create-react-app.dev/docs/adding-typescript/)의 지시에 따라 진행하였다.

아래와 같은 코드를 입력하여 dependency 설치를 시작하였다.

```bash
npm install --save typescript @types/node @types/react @types/react-dom @types/jest
```

나는 평소대로 잘 되겠거니 하며 설치 완료를 기다리고 있었는데 내 눈에 보인 건 터미널의 `npm WARN` 표시들...

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/236459199-d13d8963-812e-4bd9-b90b-7ff2b8a920a9.png" alt="TypeScript 설치 에러">
</p>

무슨 이유로 아래와 같은 에러가 뜨는지 메세지의 일부를 살펴보았다.

<br/>

## 경고 메세지에 담긴 의미는??

이 에러는 <u>npm이 dependency를 해결하는 동안 발생한 경고 메세지</u>라고 한다.

```bash
Could not resolve dependency:
peerOptional typescript@"^3.2.1 || ^4" from react-scripts@5.0.1
```

위의 경고 메세지를 살펴보면 현재 `typescript@5.0.4`를 사용하고 있지만 `react-scripts@5.0.1`에서 요구하는 `typescript@^3.2.1 || ^4` 버전과 호환이 되지 않는다는 것을 말하고 있다.

<br/>

### 여기서 잠깐! 내가 궁금해서 찾은 peerOptional의 의미

> 나는 [이 블로그](https://velog.io/@johnyworld/Peer-Dependencies-%EC%97%90-%EB%8C%80%ED%95%98%EC%97%AC)에서 내용을 참고했다.

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/236477574-3bd7f6ab-f235-4a8a-83df-5d8b8bc90a79.png">
</p>

**peerOptional가 무슨 뜻인지 몰라 검색해보았다 🔥** <br/>
peer(동료) + optional(선택적인)이라는 '선택적 동료'라는 뜻인거 같은데 검색을 해보니 **peer dependency**와 관련이 있었다.

**peerDependency는 또 뭐야? 🤔** <br/>
우리는 의존성 추가를 위해 아래와 같이 터미널에 명령어를 입력해본 경험이 있을 것이다.

```bash
npm i --save-dev {name}
```

여기서 `--save-dev`라는 것은 '빌드'나 '개발 중'에 필요한 패키지로 런타임에서는 필요로 하지 않는 의존성을 말한다. 우리는 이런 의존성을 `package.json`의 `devDependencies`라는 데이터 필드(?)에서 볼 수 있다.

이것처럼 우리가 추가하는 의존성에도 종류가 있는데 그 중 `peer dependency`가 이번 경고에 가장 큰 포인트라고 할 수 있다.

`peer dependency`는 <u>실제로 패키지에서 직접 require(import) 하지는 않더라도 호환성이 필요한 의존성</u>이라고 할 수 있다.

예를 들어, `React@18 기반의 프로젝트(A)`에서 `라이브러리(B)`를 개발한다고 했을 때, `B`는 `A`의 <u>React 18버전</u>을 의존할 수 밖에 없다.

즉, `B`는 `A`의 **React@18 버전을 peer Dependency로 두고 있다**고 할 수 있다.

이해하기 너무 어렵다고 생각이 든다면

`이 라이브러리 사용하려면 이 버전을 사용해주세요!`

하는 것과 동일한 것이라고 할 수 있을 것 같다.

> 나는 **게임을 실행하기 위해 필수 프로그램(실행 프로그램 등)을 설치하는 것과 같은 맥락인 것 같다**라고 생각했다.

나의 경우 `react-scripts@5.0.1`를 사용하려면 `typescript@3.2.1 이상부터 typescript@4.x.x`을 사용해주세요!라고 하는 것이다.

> **peer dependency와 peerOptional dependency의 차이?**
>
> `peer dependency`의 경우 **특정 버전만**을 호환하기 때문에 어떠한 종속성을 사용하기 위해 **필수**적인 종속성이며, <br/>
>
> `peerOptional dependency`의 경우 **특정 범위의 버전 내**에서 어떠한 종속성의 일부 기능을 사용하기 위해 **선택적으로 필요한 종속성**이라고 한다.

게시물을 작성한 이유에서 조금 멀어진 것 같은데, 아무튼 `react-scripts`가 호환(지원?) 가능한 `typescript`의 버전이 맞지 않아 충돌이 발생한 에러라고 볼 수 있을 것 같다.

<br/>

## 그럼 이 문제를 어떻게 해결해야할까?

버전 문제라는 것을 알 수 있기 때문에 이 문제를 해결하려면 해당 '버전을 맞추는 것'이다.

### 간단하게 해결하려면 원하는대로 버전을 맞춰주자

나는 react-scripts의 호환성을 위해 아래의 명령어를 통해 TypeScript의 버전을 다운그레이드했다.

```bash
npm i typescript@4
```

> **참고**
>
> 명시적으로 버전을 작성하거나(e.g. 4.9.5) 해당 버전에서 가장 최신을 설치하려면 버전의 가장 앞 번호(Major Version)만 작성하면 설치할 수 있다.

위와 같이 패키지 뒤에 `@`를 입력하고 원하는 버전을 입력하면 해당 버전으로 설치가 가능하다.

나는 4번대 버전이기만 하면 상관이 없기 때문에 `@4`로 가장 최신인 `4.9.5` 버전을 설치했다.

<br/>

### --force 옵션을 사용하는 방법도 있지만... 흠..

나의 경우 CRA 프로젝트을 완료하고 `JavaScript`를 `TypeScript`로 마이그레이션하는 과정에서 이러한 경고 메세지를 볼 수 있었다.

마이그레이션을 처음 해보는 '마린이'였기 때문에 이런 경고 메세지가 쭉 출력될지 몰랐기 때문에 좋다고 `npm i typescript`을 입력해버린 것이다.

하지만 이 상황을 버전을 맞추지 않고 '강제'로 충돌을 무시하고 할 수도 있다고 한다.

나의 경우 아래와 같을 것이다.

```bash
npm i typescript --force
```

하지만 이런 상황을 chat-gpt에게 물어봤을 때 '종속성 충돌을 무시하고 강제로 설치를 진행하므로 부작용이 발생할 수 있다.'라고 하니 되도록이면 지양하는 것이 좋을 것 같다.

<br/>

### --force 말고 다른 방법이 있다구?! package.json에 `overrides` 필드 추가하기

나는 되도록이면 최신 버전을 사용하는게 더 좋지 않을까라는 생각에 해결방법을 더 검색해보았다.

그랬을 때 [create-react-app(github)](https://github.com/facebook/create-react-app/issues/13080)의 3월 22일 issues에서 나와 동일한 에러를 겪은 개발자분들이 꽤나 있었다.

<p align="center">
  <img width="600" src="https://user-images.githubusercontent.com/96808980/236487924-9470b901-9176-436d-84fd-beb36b2c2a37.png"/>
</p>

다른 개발자분들 중 해결하신 분이 있나~ 내려서 comment를 보니 아래와 같은 해결방법이 있었다.

<p align="center">
  <img width="600" src="https://user-images.githubusercontent.com/96808980/236488445-859cb709-57e3-4020-aa16-ccf37491a890.png"/>
</p>

> CRA seems to work fine with TypeScript 5. So, if you need a temporary workaround for the error and happy to continue working with it, you can achieve that by creating a new overrides section in your package.json (if not existing already), and adding the same typescript version in dependences to it.
>
> CRA는 TypeScript 5에서 잘 작동하는 것 같다. 따라서 이 에러에 대한 임시 해결 방법이 필요하고 계속 작업할 수 있는 경우 package.json에 새로운 'overrides' 섹션을 만들고(아직 존재하지 않는 경우에만) 종속성에 동일한 타입스크립트 버전을 추가하여 해결할 수 있다.

나의 경우 `package.json`에 아래와 같이 작성을 했다.

```json
// package.json
{
  "dependencies": {
    "@testing-library/jest-dom": "^5.16.5",
    "@testing-library/react": "^13.4.0",
    "@testing-library/user-event": "^13.5.0",
    "@types/jest": "^29.5.1",
    "@types/node": "^20.0.0",
    "@types/react": "^18.2.5",
    "@types/react-dom": "^18.2.3",
    "@types/react-router-dom": "^5.3.3",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-router-dom": "^6.10.0",
    "react-scripts": "5.0.1",
    "typescript": "^5.0.4",
    "web-vitals": "^2.1.4"
  },
  // 새로운 overrides 필드를 추가하여 한번 더 typescript 버전을 추가했다.
  "overrides": {
    "typescript": "^5.0.4"
  }
}
```

npm Docs의 `overrides`는 아래와 같이 설명하고 있다.

> If you need to make specific changes to dependencies of your dependencies, for example replacing the version of a dependency with a known security issue, replacing an existing dependency with a fork, or making sure that the same version of a package is used everywhere, then you may add an override.
>
> Overrides provide a way to replace a package in your dependency tree with another version, or another package entirely. These changes can be scoped as specific or as vague as desired.
>
> 종속성의 버전을 교체하거나, 기존 종속성을 교체 또는 모든 곳에서 동일한 버전의 패키지가 사용되도록 하는 등의 '종속성의 종속성'을 세부적으로 변경해야하는 경우 'overrides'를 추가할 수 있습니다.
>
> 'overrides'는 종속성 트리의 패키지를 다른 버전 또는 완전히 다른 패키지로 대체할 수 있는 방법을 제공합니다. 이러한 변경 사항은 원하는 만큼 구체적으로 또는 모호하게 범위를 지정할 수 있습니다.

<br/>

나의 프로젝트(위의 package.json)로 예를 들어보자.

`react-scripts@5.0.1`의 `typescript@5.0.4`처럼 의존적 관계일 때,

`overrides` 필드를 추가하고 그 안에 원하는 버전의 패키지를 추가하면 `react-script`이 의존하는 버전에 관계없이 `typescript` 패키지는 항상 `@5.0.4`버전을 설치하겠다라는 의미이다.

<br/>

내 생각이지만 `typescript@5.0.4`**를 설치할꺼야, 그러니까 무조건 써야해!** 라는 의미와 같은 것 같다.

하지만 이것 또한 **임시 해결 방법**일 뿐 완전한 해결 방법은 아니기 때문에 **안정적으로 종속성의 호환 버전을 맞추는 것이 더 좋은 선택**이라고 생각한다.

<br/>

## 나의 결과

나는 안정적인 것을 원하기 때문에 기존의 `typescript@5.0.4`에서 `typescript@4.9.5`로 다운그레이드를 하는 것으로 결론을 내렸다.

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/236495386-cdde0b01-5fd8-4717-af60-9f97fc4441ff.png" alt="">
</p>

다운그레이드를 한 후 안정적으로 모두 잘 동작하였으며 npm의 경고 메세지도 출력되지 않았다.
