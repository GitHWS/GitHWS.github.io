---
layout: single
classes: wide
title: "MERN 스택 프로젝트 계획하기"
categories: TIL
tag: [React, Node, Express, MongoDB]
toc: true
author_profile: false
sidebar:
  nav: "docs"
---

> MERN 스택을 사용한 프로젝트를 계획하기 위한 포스팅이다.

디자인은 어느정도 완료(퍼블리싱 단계에서 수정 예정)했고 이제 프로젝트마다 소요되는 예상 기간을 생각해보기 위해 해당 포스트를 작성한다.

<img width="845" alt="image" src="https://user-images.githubusercontent.com/96808980/225525081-b875e6f7-0b43-4cdd-be83-518d9c23a226.png">

<br>

## 디렉토리 구조

> 기능 구현과 함께 추가할 예정이다.

src 디렉토리의 구조를 아래와 같이 설정한다.

```
src
├── assets
│   └── images
│   └── fonts
├── components
│   ├── common
│   └── { MyComponent }
├── pages
└── styles
```

- **assets**

  : 폰트, 컴포넌트 내부에서 사용될 이미지 등 필요한 파일을 포함하는 디렉토리

- **components**

  : 재사용 가능한 컴포넌트들이 위치하는 디렉토리, components 디렉토리는 다수의 파일이 저장될 수 있으므로 추가적인 하위 디렉토리를 생성하는 식으로 함.

  - **common**
    : 공통으로 사용하는 컴포넌트를 저장하는 디렉토리, Button이나 Input 등의 페이지마다 공통적으로 사용될 UI를 저장하도록 함.

  - **MyComponent**
    : 라우트 페이지마다 사용될 컴포넌트를 저장하는 디렉토리, 디렉토리명을 페이지 타이틀로 네이밍하도록 함.

- **pages**

  : 라우팅을 적용할 때 페이지 컴포넌트를 해당 디렉토리에 저장하도록 함.

- **styles**

  : Global Style과 Reset Style과 같은 Style 관련 파일을 저장하는 디렉토리

<br/>

## 프로젝트 Task 생성

> 단계의 시작마다 업데이트할 예정

1. **전체 UI 퍼블리싱(**03.18** 완료 예정)**
