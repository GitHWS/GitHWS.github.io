---
layout: single
classes: wide
title: "[23.02.11] 지저분한 JSX 수정하기 및 데이터가 없을 때 메세지 렌더링하기"
categories: TIL
tag: [React, Project]
toc: true
author_profile: false
sidebar:
  nav: "docs"
---

> 해당 포스트는 프로젝트의 과정을 기록하기 위한 포스트입니다.

## 지저분한 JSX 발견! 😡

프로젝트의 코드를 살펴보던 중 또 다른 문제를 발견했다.

바로 <u>받은 복주머니를 리스트 렌더링하는 컴포넌트</u>의 JSX 코드가 너무 복잡하다는 것!

아래의 코드는 `MyPocketList` 컴포넌트의 JSX 코드 부분이다.

```tsx
// MyPocketList 컴포넌트

const MyPocketList = ({ selectedTabIndex }: MyPocketListType) => {
  return (
    <S.MyPocketList>
      {selectedTabIndex === 0 &&
        DUMMY_LIST.map((item) => (
          <MyPocketItem
            key={item.id}
            id={item.id}
            isRead={item.isRead}
            color={item.color}
            author={item.author}
          />
        ))}
      {selectedTabIndex === 1 &&
        DUMMY_LIST.filter((item) => item.isRead === true).map((item) => (
          <MyPocketItem
            key={item.id}
            id={item.id}
            isRead={item.isRead}
            color={item.color}
            author={item.author}
          />
        ))}
      {selectedTabIndex === 2 &&
        DUMMY_LIST.filter((item) => item.isRead === false).map((item) => (
          <MyPocketItem
            key={item.id}
            id={item.id}
            isRead={item.isRead}
            color={item.color}
            author={item.author}
          />
        ))}
    </S.MyPocketList>
  )
```

현재 API가 완성되지 않은 상태라 임시로 데이터를 만들어 렌더링을 하고 있었다.

이 컴포넌트는 아래처럼 탭 메뉴의 `index`인 `selectedTabIndex`를 넘겨받아 값에 따라 맞는 리스트 렌더링 결과를 보여준다.

- `selectedTabIndex`가 `0`일 경우, 전체 복주머니 리스트를 보여준다.
- `selectedTabIndex`가 `1`일 경우, 읽은 복주머니 리스트를 보여준다. => (isRead === true)
- `selectedTabIndex`가 `2`일 경우, 안읽은 복주머니 리스트를 보여준다. => (isRead === false)

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/216807740-fecfff9b-2db6-4644-9b06-94d49bc8f42c.gif" alt=""/>
</p>

그런데 아래의 코드를 보면 **너무 길면서 동일한 코드가 계속해서 반복**되고 있다.

> 받은 복주머니의 데이터를 받아서 `map` 메서드로 리스트 렌더링하는 부분이다!

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/218260391-14c31443-57ae-4d1c-9518-f5e6e66b1a03.png" alt=""/>
</p>

다른 부분은 `isRead`의 값에 따라 `filter` 메서드를 통해 만족하는 데이터를 추출하여 `map` 메서드로 반환된 결과를 리스트 렌더링한다.

그래서 나는 공통된 부분이 많으니 한번 묶어서 하는 것이 더 좋지않을까 생각했다.

<br/>

### 해결해보자! 🔥

우선 위의 공통된 부분의 맥락은 다 동일하다는 점으로 접근했다.

> 받은 복주머니 데이터를 받아서 리스트 렌더링을 해준다.

그렇다면 <u>처음부터 받은 데이터에서 원하는 데이터를 추출하여 전달한다면 `map` 메서드로 렌더링만 해주면 된다는 의미</u>이다!

그럼 한번 코드를 수정해보자!

<br/>

#### 리스트 렌더링 용도의 컴포넌트 생성!

하나의 복주머니를 가리키는 `MyPocketItem` 컴포넌트를 리스트 렌더링한 `MyPocketItems` 컴포넌트를 생성했다.

이 컴포넌트는 `MyPocketList` 컴포넌트에서 **받은 복주머니 데이터(임시 데이터)**를 props로 받는다.

```tsx
// MyPocketItems 컴포넌트

type MyPocketItemsProps = {
  tabIndex: number;
  pocketData: {
    id: number;
    color: string;
    author: string;
    isRead: boolean;
  }[];
};

// 임시 데이터인 pocketData를 props로 받고 있다.
const MyPocketItems = ({ pocketData }: MyPocketItemsProps) => {
  return (
    //...
  );
};
```

<br/>

#### 원하는 데이터만 추출해서 보내주기!

현재 이 컴포넌트는 `pocketData`의 받은 복주머니의 전체 데이터를 받고 있다.

하지만 이제 이것을 필요한 데이터만 추출하여 전달하도록 할 것이다.

`MyPocketList` 컴포넌트에서 props로 전달할 때 `filter` 메서드로 필요한 데이터만 추출하여 전달한다.

```tsx
// MyPocketList 컴포넌트

const MyPocketList = ({ selectedTabIndex }: MyPocketListType) => {
  return (
    <S.MyPocketList>
      {selectedTabIndex === 0 && (
        <MyPocketItems pocketData={DUMMY_LIST} />
      )}
      {selectedTabIndex === 1 && (
        <MyPocketItems
          {/* 🔥 isRead !== false인 데이터만 추출하여 전달한다! */}
          pocketData={DUMMY_LIST.filter((item) => item.isRead !== false)}
        />
      )}
      {selectedTabIndex === 2 && (
        <MyPocketItems
          {/* 🔥 isRead === false인 데이터만 추출하여 전달한다! */}
          pocketData={DUMMY_LIST.filter((item) => item.isRead === false)}
        />
      )}
    </S.MyPocketList>
  );
};
```

이것을 통해 이제 `MyPocketItems` 컴포넌트에서는 받은 데이터를 `map` 메서드를 통해 리스트 렌더링만 해주면 끝이다!

```tsx
// MyPocketItems 컴포넌트

const MyPocketItems = ({ tabIndex, pocketData }: MyPocketItemsProps) => {
  return (
    <>
      {/* 🔥 복주머니가 있을 때 */}
      {pocketData.map((item) => (
        <MyPocketItem
          key={item.id}
          id={item.id}
          color={item.color}
          author={item.author}
          isRead={item.isRead}
        />
      ))}
    </>
  );
};
```

이렇게 작성하면 각 탭에 따라 추출된 데이터의 리스트 렌더링이 된다!

해결 완료! 😆

<br/>

### 데이터가 없을 때도 생각을 해야한다..!

디자인할 때 '데이터가 없을 때' 어떻게 할 것인지 생각을 하지 못했다.

그래서 이왕 코드도 수정한 거 이것도 같이 추가하면 좋겠다라는 생각이 들었다.

우선 데이터가 없을 때 다른 점이 있다.

> 전체 복주머니, 읽은 복주머니, 안읽은 복주머니 탭마다 데이터가 다르다.<br/>
> 그리고 각 탭마다 데이터가 없을 때 나타나는 텍스트도 달라야할 것 같다.

- 전체 복주머니에 데이터가 없다면 **모든 탭**의 데이터가 없어야한다.
- 전체 복주머니 중 `isRead` 값이 `true`인 복주머니만 존재한다면 **안읽은 복주머니 탭**에만 데이터가 없어야한다.
- 전체 복주머니 중 `isRead` 값이 `false`인 복주머니만 존재한다면 **읽은 복주머니 탭**에만 데이터가 없어야한다.

<br/>

#### 탭 메뉴의 index를 전달하기

그래서 `MyPocketItems` 컴포넌트에 어떤 탭이 선택되었는지에 대한 값을 받아야한다. 그래서 `MyPocketList` 컴포넌트에서 탭의 index의 상태인 `selectedTabIndex`를 props로 전달한다.

이로서 `MyPocketItems`는 선택되는 탭에 따라 조건부 렌더링이 가능해졌다.

```jsx
const MyPocketList = ({ selectedTabIndex }: MyPocketListType) => {
  return (
    <S.MyPocketList>
      {selectedTabIndex === 0 && (
        <MyPocketItems tabIndex={selectedTabIndex} pocketData={DUMMY_LIST} />
      )}
      {selectedTabIndex === 1 && (
        <MyPocketItems
          tabIndex={selectedTabIndex}
          pocketData={DUMMY_LIST.filter((item) => item.isRead !== false)}
        />
      )}
      {selectedTabIndex === 2 && (
        <MyPocketItems
          tabIndex={selectedTabIndex}
          pocketData={DUMMY_LIST.filter((item) => item.isRead === false)}
        />
      )}
    </S.MyPocketList>
  );
};
```

#### 데이터가 없을 때 선택되는 탭마다 텍스트 적용하기

받은 복주머니의 데이터인 `pocketData`의 `length`에 따라 데이터의 유무를 파악하여 `0`이라면 선택된 탭의 index인 `tabIndex`마다 삽입될 텍스트를 넣어준다.

현재 임시로 `li`에 텍스트를 적용했다.

그리고 데이터가 있는 경우에는 받은 데이터를 리스트 렌더링한다!

```tsx
const MyPocketItems = ({ tabIndex, pocketData }: MyPocketItemsProps) => {
  return (
    <>
      {pocketData.length === 0 && tabIndex === 0 && (
        <li>받은 복주머니가 없습니다!</li>
      )}
      {pocketData.length === 0 && tabIndex === 1 && (
        <li>읽은 복주머니가 없습니다!</li>
      )}
      {pocketData.length === 0 && tabIndex === 2 && (
        <li>읽지 않은 복주머니가 없습니다!</li>
      )}

      {pocketData.length !== 0 &&
        pocketData.map((item) => (
          <MyPocketItem
            key={item.id}
            id={item.id}
            color={item.color}
            author={item.author}
            isRead={item.isRead}
          />
        ))}
    </>
  );
};
```

이로서 지저분한 코드를 더 짧은 코드로 수정할 수 있었다.

더 줄일 수 있는게 있을 것 같아서 더 찾아보고 다시 기록하도록 하겠다!
