---
layout: single
classes: wide
title: "Notification API 사용하기!"
categories: TIL
tag: [vite, pwa]
toc: true
author_profile: false
sidebar:
  nav: "docs"
---

> Vue-Pomodoro-Timer 프로젝트에서 Notification 기능을 추가한 방법이다. 많은 부분이 틀릴 수 있으니 주의하길 바란다.

Vue-Pomodoro-Timer 프로젝트를 PWA로 구축했다.

이 프로젝트는 단순히 포모도로 타이머를 구현하는 것이라 많은 기능은 없지만 대표적인 기능으로 **사용자가 설정한 작업 시간 또는 휴식 시간이 끝난다면 모드 자동 전환과 함께 Notification을 제공**한다.

즉, **Push 알람이 기능을 제공**한다는 것이다.

[Firebase Cloud Message](https://firebase.google.com/docs/cloud-messaging?hl=ko)와 같은 서비스도 있지만 굳이 서버 요청이 필요없는 JavaScript의 `Notifications API`를 사용했다.

아래의 코드는 `Notifications API`를 사용한 부분이다.

> 🤔 이 코드는 수정할 예정이다. <br/>
> 왜냐하면 현재 모드(currentMode)가 변경되었을 때 Notification의 활성화 여부를 사용자에게 물을 필요없이 현재 모드에 적절한 Notification 인스턴스만 생성하면 되기 때문이다.

```ts
watch(currentMode, (value) => {
  Notification.requestPermission().then((perm) => {
    if (perm === "granted") {
      new Notification("Vue-Pomodoro-timer", {
        body:
          value === "work"
            ? `Let's work hard for ${currentWorkMin.value} minutes!`
            : `Take ${currentBreakMin.value} minutes to relax!`,
      });
    }
  });
});
```

위의 코드를 간단히 설명하자면 아래와 같다.

- watcher를 이용하여 `currentMode` 반응형 데이터를 감시
  - `currentMode`의 값이 변경될 때마다 해당 로직을 실행
- `Notification` 객체에서 `requestPermission` 메서드를 호출
  - `requestPermission`은 `Promise`를 반환
  - `then`을 통해 `Promise`의 값을 성공적으로 반환하면 콜백 함수를 호출
- `Notification.requestPermission()`으로 반환된 값 `perm`이 `granted`라면 새로운 `Notification` 인스턴스를 생성
  - 생성된 `Notification`의 매개변수
    - `title`: 첫번째 문자열 인자로 Notification의 `제목`을 설정
    - `body`: 두번째 객체 인자로 Notification의 `내용`을 설정

<br/>

### PWA에서 Notification 기능을 추가하려면?

PWA에서 Notification 기능을 추가하려면 몇 가지 설정이 필요하다.

- 기기의 **브라우저 알림 기능 활성화**
- 브라우저의 **알람 기능 활성화**

<br/>

#### ⚙️ 기기의 브라우저 알림 기능 활성화

첫번째로 기기의 브라우저(Chrome) 알림 기능을 활성화하자.

나는 맥북을 이용하고 있기 때문에 **설정 > 알림 > Google Chrome > 알림 허용 활성화**순으로 이동하여 설정했다.

<img src='https://github.com/GitHWS/vue-pomodoro-timer/assets/96808980/7e125634-2008-4df2-a2fa-ef8a99f409ea' alt="디바이스에서 브라우저 알림 기능 활성화"/>

> 알림 허용 활성화와 알림 형태를 '배너' 또는 '알림'으로 하도록 하자.

이렇게 설정함으로써 해당 기기는 브라우저로부터 Notification을 받을 수 있다.

<br/>

#### ⚙️ 브라우저의 알림 기능 활성화

브라우저(Chrome) 환경의 알람 기능을 활성화해보자.

브라우저 환경에서의 알림 기능은 현재 모드에 따라 `Notification` 인스턴스를 생성하던 위의 코드와 동일한 방법으로 작성한다.

```ts
// main.ts
const requestNotificationPermission = async () => {
  const perm = await Notification.requestPermission();

  if (perm !== "granted") {
    throw new Error("Notification permission not granted.");
  } else {
    new Notification("Vue-pomodoro-timer", {
      icon: "icons/pwa-64x64.png",
      body: "The notification feature is set up.",
    });
  }
};

requestNotificationPermission();
```

- `requestNotificationPermission` 함수는 비동기 함수로 호출 시 `Notification.requestPermission()`가 반환하는 `Promise` 함수의 값을 `perm`에 할당
  - `perm`의 값이 `granted`가 아니라면 에러를 발생시킴
  - `perm`의 값이 `granted`라면 `title`과 그 외 `options`이 설정된 Notification 인스턴스를 생성

이 코드로 인해 브라우저는 브라우저 알림 기능의 활성화 여부를 사용자에게 요청한다.

<div style="text-align:center">
  <img src="https://github.com/GitHWS/vue-pomodoro-timer/assets/96808980/f5942a14-dba0-4e8e-95d0-e0ff0f8b5302" alt="브라우저 알림 기능 활성화 요청" />
</div>

여기서 `허용`을 클릭하게 되면 `perm`의 값이 `granted`가 되고 `차단`을 클릭하면 `denied`가 된다.

```ts
// NotificationPermission의 타입값
type NotificationPermission = "default" | "denied" | "granted";
```

`허용`을 클릭하면 작성한 코드의 `title`과 `body`가 설정된 `Notification`이 나타난다.

<div style="text-align:center">
  <img src="https://github.com/GitHWS/vue-pomodoro-timer/assets/96808980/720c74db-f7b9-4a14-a5fc-125a435aa473" alt="Notification이 나타남"/>
</div>
