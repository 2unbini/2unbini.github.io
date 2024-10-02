---
title: "CSS @font-face Rule in React Native"
toc: true
toc_sticky: true
categories:
  - 📂 all
  - study
tags:
  - 리액트네이티브
  - RN
last_modified_at: 2024-10-02
---

## RN에서 @font-face 쓸 때 유의사항

```ts
const styles = StyleSheet.create({
  customFontText: {
    fontFamily: "CustomFont",
    fontSize: 16,
  },
});
```

뭐 위와 같이 쓸텐데, 이 때 `font family`에 들어가는 서체의 이름을 잘못 기재하면 영원히 뷰가 렌더링되지 않는다.

### CustomFont.ttf

트루타입 폰트를 사용한다고 했을 때, ttf 파일의 이름을 붙이면 될까?

> "안된다."

리네에서 `font family` 프로퍼티는 폰트의 이름과 맞춰져야 한다.

> 엥? ttf 앞에 붙은 이름이 이름 아녀? <br>
> "아니다."

### Where to find?

macOS를 사용하면 서체 관리하는 앱이 따로 있다. 영어로는 Font Book인데 한글로는 뭐였는지 기억이 안 난다.

암튼 여기에 들어가면 설치된 폰트들의 리스트가 나온다. 특정 폰트의 이름을 확인하고 싶으면 폰트를 활성화시키고 `cmd i`를 누르면 오른쪽에 상세 정보가 뜬다.

![image](/assets/images/all/font-family.png)

저 family name을 써 주면 된다.
