---
title: "마크다운 문법 (가끔 쓰는데 쓸 때마다 검색하는 것들)"
toc: true
toc_sticky: true
categories:
    - study
tags:
    - 마크다운
    - 블로그
last_modified_at: 2021-09-03
---

## 토글 리스트

<details>
<summary> 토글 리스트 </summary>
<div markdown="1">

원하는 요소 넣기

</div>
</details>

```html
<details>
<summary> 토글 리스트 </summary>
<div markdown="1">

원하는 요소 넣기

</div>
</details>
```

`div`의 `markdown="1"`은 jekyll에서 마크다운임을 인식하기 위한 코드라고 한다. 마크다운 자체에는 토글 리스트가 없어서 html 태그를 이용한 것이다.

[참고 블로그](https://inasie.github.io/it일반/마크다운-expander-control/)

### 인용 문장 나누기(multiple quote line)

> 안녕 <br>
> 하세요 <br>
> 반갑
> 습니다

```
> 안녕 <br>
> 하세요 <br>
> 반갑
> 습니다
```

`>` 써 주고, 뒤에 html 태그 `<br>` 써 주면 문장 나누기가 되고, 안 써 주고 그냥 `>`로 나눠주기만 하면 결과물이 붙어서 나온다.