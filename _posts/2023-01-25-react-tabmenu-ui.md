---
layout: single
classes: wide
title: "[23.01.25] React로 탭메뉴 UI 구현하기"
categories: TIL
tag: [React, Project, UI]
toc: true
author_profile: false
sidebar:
  nav: "docs"
---

> 해당 포스트는 프로젝트의 UI를 구현하는 것을 기록하기 위한 포스트입니다.

## 프로젝트의 개요

간단한 편지를 주고 받을 수 있는 앱을 개발하고 있다. <br/>
사용자들 간의 링크 공유를 통해 편지를 작성할 수 있고 보낸 편지를 해당 링크의 사용자의 리스트에서 확인하고 클릭하여 열람할 수 있는 앱이다.

자세한 개요는 프로젝트의 구현 후 배포 링크를 공유하도록 할 것이다.

<br/>

## 구현할 페이지 기능 소개

프로젝트의 디자인을 완료하고 드디어 퍼블리싱를 진행했다.

내가 퍼블리싱을 맡은 파트는 '받은 편지의 리스트', '받은 편지의 열람' 페이지이다.

<p align="center">
<img height="600" src="https://user-images.githubusercontent.com/96808980/214553662-900efca3-97c0-4df2-a456-d18d6fd8cfdf.png" alt="받은 편지 리스트"/>
</p>

그 중 '받은 편지의 리스트' 페이지에는 `전체 복주머니` / `읽은 복주머니` / `안읽은 복주머니` 리스트를 볼 수 있는 '탭 메뉴'가 있다.

실질적으로 React로 프로젝트는 처음 하기 때문에 나중을 위해 UI를 구현한 과정을 기록하려 한다.

## 탭 메뉴 버튼 구현

> TypeScript, styled-components를 사용하여 구현하고 있다.

우선 기능 구현을 하지 않은 JSX의 코드이다.

```js
<WrapperBox>
  <PageDescription>받은 복주머니</PageDescription>
</WrapperBox>
```

위의 이미지에 나와있는 것처럼 탭 메뉴 버튼은 3개이다.<br/>
그래서 `ul` 태그를 이용하여 자식 태그인 `li` 안에 `button` 태그를 넣어 구현할 것이다.

> `li` 태그를 버튼의 용도로 구현해도 되지만 굳이 `button` 태그를 추가한 이유는 `button` 태그를 사용하여 태그를 사용하는 목적을 분명히 하는 것이 좋다고 생각했다.

하나씩 직접 JSX 코드에 작성해도 되지만 나는 `map` 메서드를 사용하여 해당 버튼 컴포넌트를 전개하려고 한다.

아래의 `tabMenuArr`는 객체를 가지는 배열로 `title`이라는 프로퍼티를 가지는데 이것은 <u>버튼의 content</u>로 사용할 것이다.

```js
const tabMenuArr = [
  { title: "받은 복주머니" },
  { title: "읽은 복주머니" },
  { title: "안읽은 복주머니" },
];
```

이제 `ul` 태그와 `button`을 담을 `li` 태그를 styled-components로 생성하도록 하자.

```js
const TabMenuBtnList = styled.ul`
  display: flex;
  justify-content: space-between;
`;

const TabMenuBtn = styled.button`
  width: 100px;
  padding: 12px 0;
  font-weight: bold;
  color: #fff;
  border-radius: 3px;
  background-color: #d9d9d9;

  &.active {
    background-color: #e48254;
  }
`;
```

`TabMenuBtnList`는 `ul` 태그로 `li` 태그를 묶어서 `flex`를 통해 `li` 사이의 동일한 간격(`space-between`)으로 정렬해주는 역할을 컴포넌트이다.

그리고 `TabMenuBtn`는 `button` 태그로 `li` 태그의 자식 요소로 추가할 것이다.<br/>
탭 메뉴를 선택했을 때 `active`라는 className을 추가하여 배경색을 변경할 것이다.

그러기 위해서는 버튼을 클릭했을 때 해당 버튼의 고유의 값을 통해 클릭한 버튼과 클릭하지 않은 버튼을 구별할 필요가 있다.

그래서 해당 컴포넌트 내에 `useState`를 통해 해당 버튼의 고유의 값을 저장한다.

그리고 해당 값을 업데이트하는 함수 `selectMenuHandler`를 선언해준다.

```js
const [active, setActive] = useState(0);

const selectMenuHandler = (index: number) => {
  setActive(index);
};
```

해당 State의 초기값을 `0`으로 한 이유는 `map` 메서드를 사용하는 이유가 될 수 있다.

아래의 코드는 `tabMenuArr`를 `map` 메서드로 렌더링하는 코드이다.

```js
<TabMenuBtnList>
  {tabMenuArr.map((el, index) => (
    <li key={el.title} onClick={() => selectMenuHandler(index)}>
      <TabMenuBtn className={index === active ? "active" : ""}>
        {el.title}
      </TabMenuBtn>
    </li>
  ))}
</TabMenuBtnList>
```

여기서 `map` 메서드의 두번째 인자인 `index`는 순회하는 `el`의 `index`(순서)가 된다.

해당 `li`를 클릭 시 `index`를 전달받은 `selectMenuHandler`가 호출이 되면서 `active` State가 업데이트된다.

이 `active`의 값이 `map` 메서드의 인자인 `index`와 동일할 경우 위에서 설정한 className인 `active`를 추가하고 아니라면 추가하지 않는다.

```js
<TabMenuBtn className={index === active ? "active" : ""}>{el.title}</TabMenuBtn>
```

이렇게 되면 버튼을 클릭할 때마다 클릭한 버튼에 className이 추가되어 스타일이 변경된다.

<p align="center">
<img src="https://user-images.githubusercontent.com/96808980/214558905-ccf3f74f-577b-44e9-bd0e-2744b156f1de.gif" alt="탭메뉴 완성"/>
</p>

<br/>

## 클릭한 메뉴에 따른 컨텐츠 구현

나는 탭 메뉴 버튼을 묶은 `TabMenuBtnList`의 밑에 해당 메뉴에 해당하는 목록을 표시하려고 한다.

해당 기능은 버튼의 `index`의 값을 저장한 State를 사용하여 간단하게 구현할 수 있다.

```js
const [active, setActive] = useState(0);
```

```js
{
  active === 0 && <div>전체 복주머니</div>;
}
{
  active === 1 && <div>읽은 복주머니</div>;
}
{
  active === 2 && <div>안읽은 복주머니</div>;
}
```

이것은 `active` state의 값이 0, 1, 2일 때 `&&` 연산자를 통해 버튼에 해당하는 목록을 가진 컴포넌트를 렌더링하는 방식이다.

지금은 렌더링할 데이터가 없어 `div` 태그를 임시로 이용했다.

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/214561141-b9f9add8-7079-4e7c-a999-65898093820d.gif" alt="탭 메뉴에 따라 바뀌는 컨텐츠"/>
</p>
