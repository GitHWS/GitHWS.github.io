---
layout: single
classes: wide
title: "[23.02.09] Style-components 컴포넌트를 별도의 파일로 분리하기"
categories: TIL
tag: [React, Project, Styled-components]
toc: true
author_profile: false
sidebar:
  nav: "docs"
---

> 해당 포스트는 프로젝트의 과정을 기록하기 위한 포스트입니다.

## 어라.. Styled-Components 때문에 파일이 너무 복잡한데..?

프로젝트의 기능을 제외한 나머지 퍼블리싱을 마쳤다.<br/>

```tsx
// LetterContent 컴포넌트

import styled from "styled-components";

import {
  DogRedStampImg,
  DogWhiteStampImg,
  DogOrangeStampImg,
  DogGreenStampImg,
  DogYellowStampImg,
} from "@/components/button/index.style";

// -------- 수많은 Styled-Component로 생성된 컴포넌트...🤯 --------
const LetterGridBox = styled.div`
  //...
`;

const LetterToParagraph = styled.p`
  //...
`;

const LetterContentParagraph = styled.p`
  //...
`;

const LetterFromBox = styled.div`
  //...
`;

const LetterFromParagraph = styled.p`
  //...
`;

const LetterContent = ({ color }: { color: string }) => {
  return (
    <LetterGridBox>
      <LetterToParagraph>To.(수신자)에게</LetterToParagraph>
      <LetterContentParagraph>...내용</LetterContentParagraph>
      <LetterFromBox>
        <LetterFromParagraph color={color}>
          From.<span>(발신자)</span>
        </LetterFromParagraph>
        {color === "red" && <DogRedStampImg />}
        {color === "orange" && <DogOrangeStampImg />}
        {color === "yellow" && <DogYellowStampImg />}
        {color === "green" && <DogGreenStampImg />}
        {color === "navy" && <DogWhiteStampImg />}
      </LetterFromBox>
    </LetterGridBox>
  );
};

export default LetterContent;
```

<p align="center">
  <img width="235" src="https://user-images.githubusercontent.com/96808980/217785642-24f768e9-614b-4194-a94d-1803db4e10e9.png">
</p>

그런데 이 상황에서 갑자기 드는 생각...

> 어..? 이거 파일이랑 컴포넌트가 너무 복잡하게 느껴지는데...?

위의 코드처럼 <u>Styled-Components를 사용하여 생성한 여러 개의 컴포넌트들이 하나의 컴포넌트 파일에 나열</u>이 되어 있고 파일의 구조를 보면 `ReadMyPocket` 디렉토리 안에 `(파일명).tsx` 컴포넌트의 스타일을 담당하는 `(파일명)Style.tsx` 파일이 별로도 존재하고 있다.

여기서 내가 해결하고자 하는 문제는 아래와 같다.

<hr/>

- `ReadMyPocket` 디렉토리의 파일이 너무 복잡하게 느껴진다.
- `ReadMyLetter` 디렉토리 내 파일 내부의 Styled-Components로 생성한 컴포넌트들이 하나의 컴포넌트 파일에 너무 많이 나열되고 있다.

<hr/>

**물론 너무 개인적인 생각일 수 있다.** <br/>
코드에는 취향이 있을 뿐 확실한 정답이 없고 Styled-Components는 CSS in JS로 당연히 저렇게 사용해도 된다.

하지만 개인적인 견해로 메인(?)을 담당하는 하나의 컴포넌트는 **단 하나의 컴포넌트만의 구성을 보여주는 것**만을 담당하는 것이 더 좋다고 생각하기 때문이다.

> '하나의 컴포넌트만의 구성을 보여주는 것'이란 말이 개인적으로 생각한 점이라 뭔가 혼란스러울 수도 있을 것 같다. <br/>
> 예를 들면 `MyLetter`라는 컴포넌트 내에 또 다른 컴포넌트가 생성되어 있으면 복잡하다는 의미라고 할 수 있을 것 같다.

따라서 위의 문제를 해결하기 위해 Styled-Components로 생성한 컴포넌트들을 하나의 파일에 담아서 불러오는 식으로 할 것이다.

<br/>

### 파일 분리하기

우선 나는 각 페이지마다 Styled-Components로 생성된 컴포넌트를 새로 생성한 `styles` 폴더 안에서 `(페이지명).styled.ts` 파일에 넣었다.

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/217799379-82d67124-7c43-4e0b-b3c3-b2ea55459c42.png" alt=""/>
</p>

```ts
// ReadMyLetter

import styled from "styled-components";

// ReadMyLetter 페이지 버튼
export const LetterButtonBox = styled.div`
  //...
`;

// ReadMyLetter 편지지
export const LetterBox = styled.div`
  //...
`;

// 편지 전체 그리드 박스
export const LetterGridBox = styled.div`
  //...
`;

// 편지 수신자(To)
export const LetterToParagraph = styled.p`
  //...
`;

// 편지 내용
export const LetterContentParagraph = styled.p`
  //...
`;

// 편지 발신자(From)
export const LetterFromBox = styled.div`
  //...
`;

export const LetterFromParagraph = styled.p`
  //...
`;
```

기존의 방식과 다른 점은 Styled-Components로 생성된 컴포넌트는 기존에 다른 컴포넌트 파일에 바로 생성하여 사용했지만 Styled-Component의 컴포넌트를 관리하는 `styled.ts` 파일을 따로 만들어서 각 컴포넌트를 `export`해주고 있다는 점이다.

하지만 이렇게 하면 당연히 이런 생각이 들 수도 있다.

> export가 많아진다? 그러면 import하는 부분이 너무 늘어나는거 아닌가?

만약 각각의 컴포넌트를 가져오기 위해 import를 한다면 당연히 import를 하는 코드가 길어질 것이다.<br/>
하지만 아래의 방법을 통해 해당 styled 파일에서 모든 컴포넌트를 가져올 수 있다.

<br/>

### `as`를 사용하자!

이제 하나의 파일에 담긴 Styled-Components의 컴포넌트를 import를 해보자.

여기서 기존에 나였다면 당연히 아래처럼 import를 했을 것이다.

```ts
import {
  LetterButtonBox,
  LetterBox,
  LetterGridBox,
  LetterToParagraph,
  LetterContentParagraph,
  LetterFromBox,
  LetterFromParagraph,
} from "./styles/ReadMyLetter.styled";
```

이렇게 작성하는 것도 좋지만 이렇게 하는 방법은 어떨까?

```js
import * as S from "./styles/ReadMyLetter.styled";
```

이렇게 작성한다면 위의 Styled 파일에 있는 컴포넌트에 `S`라는 명칭으로 접근할 수 있다.<br/>
사용한 예시를 보자면 아래와 같다.

> `as`는 `~로서`라는 뜻이 있듯이 `*(모든 컴포넌트)`를 `S`로서 import하겠다라고 생각하면 될 것 같다.

```tsx
// LetterContent 컴포넌트

import * as S from "./styles/ReadMyLetter.styled";

const LetterContent = ({ color }: { color: string }) => {
  return (
    <S.LetterGridBox>
      <S.LetterToParagraph>To.(수신자)에게</S.LetterToParagraph>
      <S.LetterContentParagraph>
        Lorem ipsum dolor sit amet consectetur adipisicing elit. Culpa, iusto
        dignissimos quia, repellat, quam modi reiciendis recusandae blanditiis
        cumque possimus porro veniam voluptate ea consequatur perspiciatis illo
        nesciunt hic! Ipsum?
      </S.LetterContentParagraph>
      <S.LetterFromBox>
        <S.LetterFromParagraph color={color}>
          From.<span>(발신자)</span>
        </S.LetterFromParagraph>
        {color === "red" && <DogRedStampImg />}
        {color === "orange" && <DogOrangeStampImg />}
        {color === "yellow" && <DogYellowStampImg />}
        {color === "green" && <DogGreenStampImg />}
        {color === "navy" && <DogWhiteStampImg />}
      </S.LetterFromBox>
    </S.LetterGridBox>
  );
};
```

이렇게 사용할 컴포넌트명 앞에 `S`를 붙여 마치 <u>객체의 프로퍼티에 접근</u>하는 것처럼 접근할 수 있다.
근데 이 `S`를 콘솔로 출력해보니 진짜 객체였다.

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/217804891-a5ead7bf-4fd2-464c-ab54-1176aadee982.png" alt=""/>
</p>

`Module`이라는 객체 내 프로퍼티로 Styled-Components로 생성된 컴포넌트가 존재하는 것을 볼 수 있다.

이러한 방식으로 ReadMyPocket 디렉토리의 `Style.tsx` 파일의 컴포넌트들도 하나의 `styled.ts` 파일에 모아 아래와 같이 정리했다.

<p align="center">
  <img src="https://user-images.githubusercontent.com/96808980/217805895-95eb4867-3e29-43e2-b9bf-1eac2c206f31.png" />
</p>

이전의 파일 목록보다 더 깔끔해진 것 같다! 😆

<br/>

### `import * as ... from`를 사용하면 성능 문제가 있을 수도 있다고?? 😱

물론 이전의 파일보다 훨씬 깔끔해진 것 같아 기분이 좋았지만 혹시나 컴포넌트를 하나씩 `import ... from`를 하는 것과 모든 컴포넌트를 `import * as ... from`을 사용하는 것이 차이가 있을까 해서 검색해봤다.

검색 결과는...

> import할 모듈이 모두 사용할 경우가 아니라면, 사용할 항목만 불러오는게 좋음<br/>
> 출처 : [[JS] 모듈 import/export 문법 빠르게 정리하기](https://nuhends.tistory.com/80)

모든 모듈을 사용할 것이 아니라면 사용할 항목만 불러오는게 맞다고 한다.

왜인지 이유를 살펴보니 Webpack과 같은 빌드 툴들은 로딩 속도를 증진시키기 위해 모듈들을 모으는 번들링과 <u>사용하지 않는 리소스를 삭제하는 최적화 작업을 수행</u>한다.

사용하지 않는 리소스를 삭제하는 작업을 `tree-shaking` 작업이라고 한다. **(나무를 흔들어 죽은 잎과 가지를 떨어뜨리는 의미인가?)**

이 작업으로 필요없는 리소스를 제거하는데 시간을 소요하기에 되도록이면 필요한 리소스만 가져와서 사용하는 것이 성능면에서 좋다고 한다.

> 더 `tree-shaking`의 자세한 내용은 아래의 링크에서 읽어봤다!
> 출처 : [트리쉐이킹으로 자바스크립트 사이즈 줄이기](https://yceffort.kr/2021/08/javascript-tree-shaking)

하지만 이 프로젝트는 매우 소규모이기 때문에 현재로선 문제가 없어 유지하기로 했다.

단 성능에 문제가 발생한다면 이것을 수정할 예정이다.

<br/>

### 결론

사용할 모듈만 import해서 사용하는 것이 성능 면에서 좋다.

하지만 해당 프로젝트에서는 이러한 방법을 사용해보고 싶어서 해당 코드를 유지할 것이다! 🤪
