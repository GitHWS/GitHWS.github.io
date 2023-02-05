---
layout: single
classes: wide
title: "[23.02.04] 불필요한 렌더링 해결하기"
categories: TIL
tag: [React, Project, UI]
toc: true
author_profile: false
sidebar:
  nav: "docs"
---

> 해당 포스트는 프로젝트의 UI를 구현하는 것을 기록하기 위한 포스트입니다.

## 불필요한 렌더링 해결하기

이번에 프로젝트에서 담당하는 파트인 '받은 복주머니'에서 불필요한 렌더링이 발생했다.

리렌더링을 할 필요가 없는 컴포넌트가 탭 메뉴가 변경될 때마다 같이 리렌더링되고 있었던 것이다.

<br/>

### 문제 발생 상황

`ReadMyPocket` 페이지 컴포넌트는 페이지 문구 `PageDescParagraph` 컴포넌트와 탭 메뉴 `TabButtonList` 컴포넌트, 받은 복주머니 리스트 `MyPocketList` 컴포넌트로 구성이 되어있었다.

```ts
const ReadMyPocket = () => {
  const [selectedIndex, setSelectedIndex] = useState(0);

  const selectTabButtonHandler = (index: number) => {
    setSelectedIndex(index);
  };

  return (
    <>
      <PageDescParagraph>받은 복주머니</PageDescParagraph>
      {/* 👇 탭 메뉴 버튼 컴포넌트 */}
      <TabButtonList getSelectedIndex={selectTabButtonHandler} />
      {/* 👇 받은 복주머니 리스트 컴포넌트 */}
      <MyPocketList selectedIndex={selectedIndex} />
    </>
  );
};
```

<br/>

이 페이지 컴포넌트는 클릭할 때마다 State를 업데이트하는 `TabButtonList` 컴포넌트의 `active` State를 `selectTabButtonHandler`를 사용하여 페이지 컴포넌트의 State인 `selectedIndex`에 업데이트시키고 있다.

<br/>

### 문제 발생 원인

아래의 `TabButtonList` 컴포넌트에서 사용하는 `active` State는 탭 메뉴 버튼인 `TabButtonItem` 컴포넌트을 클릭할 때마다 각 버튼의 `index`로 업데이트된다.

> index는 map 메서드의 콜백 함수의 두번째 인자인 index를 사용했다.

```ts
const tabMenuTitle = [
  {
    title: "전체 복주머니",
  },
  {
    title: "읽은 복주머니",
  },
  {
    title: "안읽은 복주머니",
  },
];

interface TabButtonListType {
  getSelectedIndex: (index: number) => void;
}

const TabButtonList = (props: TabButtonListType) => {
  const [active, setActive] = useState(0);

  const activeTabButtonHandler = (index: number): void => {
    setActive(index);
    props.getSelectedIndex(index);
  };

  const tabMenuButtons = tabMenuTitle.map((button, index) => {
    return (
      <TabButtonItem key={button.title}>
        <button
          className={index === active ? "active" : ""}
          onClick={() => activeTabButtonHandler(index)}
        >
          {button.title}
        </button>
      </TabButtonItem>
    );
  });
  return <TabButtonListStyle>{tabMenuButtons}</TabButtonListStyle>;
};
export default TabButtonList;
```

```ts
const ReadMyPocket = () => {
  const [selectedIndex, setSelectedIndex] = useState(0);

  // TabButtonList State 끌어올리는 함수
  const selectTabButtonHandler = (index: number) => {
    setSelectedIndex(index);
  };

  return (
    <>
      <PageDescParagraph>받은 복주머니</PageDescParagraph>
      {/* State를 끌어올리는 함수를 props로 전달 */}
      <TabButtonList getSelectedIndex={selectTabButtonHandler} />
      <MyPocketList selectedIndex={selectedIndex} />
    </>
  );
};
```

`TabButtonItem` 컴포넌트를 클릭하면 onClick 이벤트가 호출이 되면서 `activeTabButtonHandler` 함수에 해당 탭 메뉴 버튼의 `index`를 전달하게 되는데 `TabButtonList`의 `active` State와 페이지 컴포넌트의 `selectedIndex` State에 할당이 되게 된다.

이 상황에서 `active` State가 업데이트되면서 `selectedIndex` State의 업데이트가 되어 `ReadMyPocket` 페이지 컴포넌트의 리렌더링이 된다.

페이지 컴포넌트가 리렌더링이 되면서 모든 자식 컴포넌트가 리렌더링된다. 여기서 리렌더링이 필요없는 `PageDescParagraph` 컴포넌트 또한 리렌더링이 된다.

<br/>

### 문제 해결

페이지 자체에서 State를 업데이트를 하기 때문에 모든 자식 컴포넌트의 리렌더링이 발생하는 문제이다.<br/>
그래서 `TabButtonList`와 `MyPocketList` 컴포넌트를 페이지 자체에 배치하지 않고 이 두 컴포넌트를 묶는 `TabMenu`컴포넌트를 생성하여 배치하도록 한다.

페이지 컴포넌트의 필요없는 State와 State 끌어올리는 함수를 삭제하고 새로 생성한 `TabMenu` 컴포넌트에 작성해준다.

```ts
const TabMenu = () => {
  // TabButtonList에서 끌어올린 State를 저장하는 State
  const [selectedTabIndex, setSelectedTabIndex] = useState(0);

  // TabButtonList에서 State를 끌어올리는 함수
  const getSelectedIndexHandler = (index: number) => {
    setSelectedTabIndex(index);
  };

  return (
    <>
      <TabButtonList getSelectedIndex={getSelectedIndexHandler} />
      <MyPocketList selectedTabIndex={selectedTabIndex} />
    </>
  );
};

export default TabMenu;
```

이렇게 하면 수정한 부분이 아래처럼 컴포넌트가 구성된다.

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/216807596-31715b97-f0be-401c-808d-059ab224e8bb.png" alt=""/>
</p>

이렇게 되면 `TabButtonList`에서 버튼을 클릭할 때마다 업데이트되는 State가 `TabMenu` 컴포넌트에서 동일하게 업데이트가 되기 때문에 `TabMenu`에서 리렌더링이 발생하고 `ReadMyPocket`에서는 리렌더링이 발생하지 않아 불필요한 리렌더링이 발생하던 `PageDescParagraph` 컴포넌트는 더이상 리렌더링이 발생하지 않는다!

<br/>

#### 결과

React Dev 툴을 이용하여 렌더링 상황을 살펴보면 아래처럼 리렌더링이 필요한 부분만 변경되는 것을 볼 수 있다.

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/216807740-fecfff9b-2db6-4644-9b06-94d49bc8f42c.gif" alt=""/>
</p>
