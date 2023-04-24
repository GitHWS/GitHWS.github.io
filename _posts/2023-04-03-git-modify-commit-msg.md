---
layout: single
classes: wide
title: "헉! 커밋 메세지 잘못썼다!"
categories: TIL
tag: [git]
toc: true
author_profile: false
sidebar:
  nav: "docs"
---

github를 사용하면서 commit을 정말 많이 할 것이다.

그런데 만약 commit 메시지에 오타가 생겼다면?

그렇다면 아래처럼 터미널에 작성해보도록 하자.

```
git commit --amend
```

위처럼 작성 후 엔터를 치면 가장 최근의 commit의 내용이 vi터미널에 나올 것이다.

입력 모드(insert)에 접근하기 위해 `i`를 누르고 수정하고 싶은 부분을 수정하고

더이상 수정할 부분이 없다면 `esc`로 입력 모드를 종료하고 `:wq`를 입력하여 수정한 내용을 저장한다.

이렇게 하면 가장 최근의 commit의 메세지가 수정된 것을 볼 수 있다.
