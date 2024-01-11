---
layout: single
classes: wide
title: "Vite + PWA로 앱을 만들어보자!"
categories: TIL
tag: [vite, pwa]
toc: true
author_profile: false
sidebar:
  nav: "docs"
---

Pomodoro Timer 프로젝트를 Electron로 Publish하여 Github에 릴리즈를 하는 도중 '코드 사인(Code Signing)' 이슈를 마주쳐 해결 방법을 알아봤지만 방법은 '유료'인 Apple Developer 등록...😢

> 🤔 **코드 사인(Code Signing)이란?** <br/>
> 소프트웨어나 응용 프로그램에 디지털 서명을 적용하는 프로세스.
> 쉽게 말해서 해당 소프트웨어나 응용 프로그램를 다른 사용자에게 배포할 때 사용되는 보증서라고 할 수 있다.

나는 이러한 이슈로 인해 로컬에서만 동작하는 Pomodoro Timer 데스크톱 앱으로 두려고 했지만 애초에 다른 사용자에게 배포하기 위해 프로젝트를 시작했던 것이라 이대로 두기엔 너무 아쉬웠다.

그래서 코드 사인이 필요없는 방법을 찾아보다 한 가지 방법을 찾았다.

<p style="font-size: 20px">그 방법은 바로 PWA!</p>

### PWA(Progressive Web App)란?

<div style="text-align:center">
 <img src="https://miro.medium.com/max/3200/0*zMqsSFxHOUdMcCMW" width="300"/>
</div>

<br/>

PWA은 네이티브 앱과 같은 경험을 제공하기 위한 모던 웹 기술을 사용한 '웹' 애플리케이션이다.

### PWA를 선택한 이유는?

PWA를 선택한 이유는 아래와 같다.

- Electron보다 **간단한 배포 방법**
  - Electron의 경우 Publish 과정을 거친 후 릴리즈를 하는데 이를 다른 사용자가 다운로드하려면 코드 사인이 필요하다.
  - 하지만 PWA는 해당 프로젝트를 빌드 후 배포한 후 해당 URL을 공유하면 누구든지 앱을 다운로드하여 사용할 수 있다.
- **크로스 플랫폼**
  - Electron 데스크톱 앱은 각 운영체제에서 지원하는 설치 파일(zip, dmg 등)을 통해 설치하여 각 운영체제에 맞게 사용할 수 있었다.
  - PWA는 설치 파일이 아닌 웹 브라우저를 통해 설치하기 때문에 모든 운영체제에서 사용할 수 있다.
- **오프라인 환경에서 동작**
  - Electron 데스크톱 앱을 설치 후 오프라인 환경에서 독립적으로 사용할 수 있다.
  - PWA는 브라우저를 통해 설치 시 오프라인 환경에서도 데스크톱 앱처럼 실행할 수 있다.

여러모로 Electron과 공통적인 부분도 있으면서 더 편리하다는 생각이 들어 PWA를 선택했다.

### 내가 생각하는 PWA의 가장 두드러지는 특징

PWA는 주된 목적으로 **사용자의 경험** 초점을 맞추고 있다.

대표적인 특징으로 **반응형**으로 데스크톱 브라우저, 스마트폰, 태블릿 등 브라우저를 지원하는 모든 기기에 맞출 수 있다.

또한 앱의 공유가 단순히 웹 사이트의 URL의 공유로 끝나기 때문에 설치 파일을 다운로드하는 등의 번거로움이 없다.

<br/>

### PWA 구축하기

> 내가 진행한 Pomodoro Timer 프로젝트는 Vite + Vue이다.

Vite 환경에서는 간편하게 `vite-plugin-pwa` plugin의 추가로 PWA를 구축할 수 있다.

```
npm i -D vite-plugin-pwa
```

설치가 완료되었다면 아래와 같이 `vite.config.js` 파일에 plugin을 추가한다.

```js
import { defineConfig } from "vite";
import vue from "@vitejs/plugin-vue";
import { VitePWA } from "vite-plugin-pwa";

// registerType 옵션은 서비스 워커의 등록 유형을 설정한다.
// autoUpdate는 사용자가 앱에 접속할 때마다 사용자의 업데이트 동의 없이 최신 버전의 서비스 워커를 확인하고 업데이트한다.
export default defineConfig({
  plugins: [Vue(), VitePWA({ registerType: "autoUpdate" })],
});
```

VitePWA가 인자로 가지는 `userOptions` 객체에는 다양한 옵션이 들어갈 수 있는데 그 중 필수로 설정되어야하는 옵션이 `manifest` 옵션이다.

```js
export default defineConfig({
  plugins: [
    vue(),
    VitePWA({
      registerType: "autoUpdate",
      manifest: {
        // ...Web App Options
      },
    }),
  ],
});
```

`mainfest` 옵션은 PWA의 외관과 동작을 정의하는 JSON 형태의 메타데이터이다.
이를 설정하여 아이콘과 앱의 이름, 배경색, 디스플레이 모드 등의 설정을 할 수 있다.

> mdn에 manifest 옵션에서 설정할 수 있는 필드를 볼 수 있다. <br/>
> 참고 : [MDN - Web App Manifests](https://developer.mozilla.org/en-US/docs/Web/Manifest)

### PWA 구축 시 최소 요구 사항

PWA는 URL을 통해 접속 시 브라우저에서 다운로드를 진행할 수 있다.
하지만 다운로드를 가능하게 하려면 엔트리포인트(index.html)의 `<head>` 섹션과 Web App Manifest의 최소한의 옵션을 설정해야한다.

> 엔트리포인트의 head 섹션에 있어야하는 최소한의 항목 : [VitePWA Minimal Requirements:Entry-Point](https://vite-pwa-org.netlify.app/guide/pwa-minimal-requirements.html#entry-point) <br/>
> Web App Mainfest의 최소한의 옵션 : [VitePWA Minimal Requirements:Web App Manifests](https://vite-pwa-org.netlify.app/guide/pwa-minimal-requirements.html#web-app-manifest)

위의 링크에서 항목을 살펴보면 `icon`이 있다는 것을 알 수 있다.

<img src="https://github.com/GitHWS/vue-pomodoro-timer/assets/96808980/2606989b-8045-4748-afe3-7e50170cad5c" alt="Manifest의 icon 최소 요구 사항" />

최소한의 아이콘 파일을 준비해야하는데 기본적으로 **512x512 크기의 icon 이미지**(png, svg와 같은)가 필요하다.

하지만 디자이너가 아닌 개발자가 겨우 만든 아이콘을 일일이 여러 크기로 조절하여 만들기는 매우 번거롭다.

그렇기 때문에 이미 개발자가 만들어놓은 생성기인 PWA Assets Generator를 사용하면 된다.

<br/>

### PWA Assets Generator

> 참고 : [assets-generator](https://github.com/vite-pwa/assets-generator)

생성기를 설치 후 `pwa-assets.config.js(ts)` 파일을 생성한 후 아래와 같이 작성한다.

```js
import {
  defineConfig,
  minimalPreset as preset,
} from "@vite-pwa/assets-generator/config";

export default defineConfig({
  preset,
  images: ["public/icons/icon.svg"],
});
```

`images` 옵션의 값에는 사용할 assets 파일의 경로를 모두 작성해주면 된다.
나는 public/icons 디렉토리에 icon.svg라는 512x512 크기의 아이콘 이미지 파일이 존재하기때문에 위와 같이 입력했다.

설정을 마쳤으면 `package.json`에서 아래의 script를 추가한다.

```json
{
  "scripts": {
    // ...
    "generate-pwa-assets": "pwa-assets-generator"
  }
}
```

이 script를 실행시키면 아래와 같이 public/icons 디렉토리에 PWA를 위한 assets 이미지 파일이 생성된다.

![PWA assets 생성 결과](https://github.com/GitHWS/vue-pomodoro-timer/assets/96808980/0bdfe252-d336-404c-805c-9a5bcb161ee8)

이제 이 assets을 기반으로 **엔트리 포인트의 설정**을 하면 된다!

<br/>

### 엔트리 포인트 설정

엔트리 포인트인 index.html의 `<head>` 태그 안에 PWA를 위한 필수 항목(메타 데이터)의 설정이 필요하다.

<img src="https://github.com/GitHWS/vue-pomodoro-timer/assets/96808980/56e569ca-f1ea-4dad-a79c-7d9e662348e9" alt="엔트리 포인트 최소 설정 항목"/>

```html
<head>
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Pomodoro Timer</title>
  <meta name="description" content="Pomodoro Timer" />
  <link rel="icon" href="/favicon.ico" />
  <link rel="apple-touch-icon" href="/apple-touch-icon-180x180.png" />
  <link rel="mask-icon" href="/maskable-icon.png" color="#FFFFFF" />
  <meta name="theme-color" content="#34495e" />
</head>
```

여기서 `apple-touch-icon`이란 **iOS 환경**에서 특정 웹사이트를 홈 화면에 추가했을 때 표시되는 아이콘을 가리킨다. 일반적으로 홈 화면에 추가하는 웹사이트의 캡쳐 이미지가 기본적으로 설정이 되지만 PWA를 위해 icon 이미지를 직접 설정한다.

또한 `mask-icon`이라는 것도 볼 수 있는데 이는 대부분의 브라우저에서 사용되는 아이콘으로 주로 브라우저의 탭이나 홈화면에서 볼 수 있는 아이콘이다.

<br/>

### 설정을 마치고 빌드

위의 설정을 마치고 빌드하면 위와 같이 dist 디렉토리 내에 파일이 생성된 것을 볼 수 있다.

<img src="https://github.com/GitHWS/vue-pomodoro-timer/assets/96808980/12d3e718-42e0-462d-ad35-47102dd58c1d" alt="빌드 결과"/>

우리가 `vite.config` 파일에서 설정했었던 mainfest 옵션은 위와 같이 manifest 파일로 별도로 생성되어 자동으로 index.html에 연결되어있는 것을 볼 수 있다.

```html
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Pomodoro Timer</title>
  <meta name="description" content="Pomodoro Timer" />
  <link rel="icon" href="/favicon.ico" />
  <link rel="apple-touch-icon" href="/apple-touch-icon-180x180.png" />
  <link rel="mask-icon" href="/maskable-icon.png" color="#FFFFFF" />
  <meta name="theme-color" content="#34495e" />
  <script type="module" crossorigin src="/assets/index-35deca68.js"></script>
  <link rel="stylesheet" href="/assets/index-bbac3b0c.css" />
  <link rel="manifest" href="/manifest.webmanifest" />
  <script id="vite-plugin-pwa:register-sw" src="/registerSW.js"></script>
</head>
```

이로써 PWA의 구축이 완료되었다고 할 수 있다.
이것을 개발자 도구에서 Lighthouse를 이용하여 검사해보면 아래와 같은 결과를 확인할 수 있다.

<img src="https://github.com/GitHWS/vue-pomodoro-timer/assets/96808980/3ffa1b70-7205-49c6-96a7-f67a9e56a0da" alt="lighthouse에서 PWA 구축 결과 확인"/>

### 🤔 **난 왜 '체크' 표시가 아니라 '더하기' 표시가 뜨죠?**

<div style="text-align:center"><img src="https://github.com/GitHWS/vue-pomodoro-timer/assets/96808980/7cb685e6-f455-4089-92aa-e0b60316e6ea" alt="PWA 더하기 표시"/></div>

가끔씩 '체크' 표시가 아닌 '더하기' 표시인 경우가 있는데 이것은 PWA로 구축이 되었으나 PWA가 최적화되지 않았다라는 것이다.

그러므로 항목을 확인하여 조건에 맞게 추가하면 된다.

나의 경우 아래와 같이 '매니패스트에 마스크 가능한 아이콘이 없음'이라는 항목이 최적화가 되지 않아 이 부분을 수정해서 체크 표시를 띄울 수 있었다.

<img src="https://github.com/GitHWS/vue-pomodoro-timer/assets/96808980/b2cc0d11-cb55-42a6-b98b-592c203ac5b8" alt="최적화 항목 살펴보기"/>

<br/>

### 그렇다면 PWA를 로컬에 설치해보자!

> 나는 빌드된 파일을 Vercel을 통해 간단히 배포한 상태였다. <br/>
> 배포 링크 : https://vue-pomodoro-timer-eight.vercel.app/

Electron으로 설치했었던 혼자 사용했던 데스크톱 앱을 이제 배포한 URL을 통해 로컬에 설치해보자.

우선 URL로 접속하면 아래와 같이 화면이 보일 것이다.

<img src="https://github.com/GitHWS/vue-pomodoro-timer/assets/96808980/43822ff7-48dc-42e7-b8f6-02f98036294e" alt="배포한 URL에 접속" />

여기서 주소 표시줄의 우측을 보면 여러 아이콘 중 가장 좌측의 아이콘을 클릭하면 설치를 진행할 수 있다.

<img src="https://github.com/GitHWS/vue-pomodoro-timer/assets/96808980/bbfff259-3b64-4d91-8e02-e8ff20319be3" alt="PWA 설치 확인 창"/>

여기서 설치를 하면 Mac의 경우 런치패드에 설치된 것을 확인할 수 있다.

<div style="text-align:center">
  <img width="200" src="https://github.com/GitHWS/vue-pomodoro-timer/assets/96808980/8753d6fd-5f26-4c91-948e-d8bb397c0db5" alt="MacOS의 런치패드에 설치된 PWA"/>
</div>

이것을 실행하면 **짠!** 독립적인 창으로 로컬에서 실행할 수 있다!

아직 많은 부분을 PWA의 대표적인 특징인 반응형을 만족시키진 않고 있지만 추후 계속해서 수정해나갈 생각이다!

<img src="https://github.com/GitHWS/vue-pomodoro-timer/assets/96808980/106716a5-9752-479e-9dbb-2c323d190162" alt="PWA 실행 결과"/>
