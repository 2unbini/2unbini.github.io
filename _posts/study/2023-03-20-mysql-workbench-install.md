---
title: "M1 맥북에서 Mysql, workbench 설치 (feat. homebrew)"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - study
tags:
    - m1
    - mysql
    - workbench
    - homebrew
last_modified_at: 2023-03-20
---

## M1 유저들은 응용 프로그램을 받을 때 항시 주의를 기울인다.

그냥 막 받았다가 인텔 버전이라서 실행 안되고 어쩌구 저쩌구됨.

갓브루로 mysql과 workbench를 설치해보자.

## 엥 홈브루가 뭔디

macOS 전용 패키지 관리자. 맥 유저에게 없어서는 안 될 존재....

### 설치

```zsh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

이걸 그냥 터미널에 갖다 붙여넣고 실행하면 된다.

관련해서 홈브루로 할 수 있는 것들을 보고싶다면 [도큐먼트 페이지](https://brew.sh/index_ko)로 가세용~

## Mysql

### 1. 갓브루 업데이트

```zsh
brew update
```

### 2. 갓브루에서 mysql 찾기

```zsh
brew search mysql
```

요래 하면 설치 가능한 mysql 버전 및 관련 응용 프로그램 리스트가 뜬다.

![](/assets/images/all/brew-1.png)

### 3. mysql 설치

```zsh
brew install mysql
```

특정 버전을 설치하고자 한다면 `mysql@5.6`과 같이 버전 넘버를 써주면 된다.

## workbench 설치

위에 캡쳐 화면에서 Casks 리스트에 `mysqlworkbench`가 있다. 이거 그냥 설치하면 된다.

```zsh
brew install mysqlworkbench
```

### 왜 홈브루로 설치?

m1 유저라 또 설치 전에 미리 Apple silicon 버전 찾아서 검색 몇 번 때려봤는데 역시나 사람들이 이미 m1 관련 이슈들을 많이 겪었더라.

[Is Apple Silicon Ready](https://isapplesiliconready.com/kr/app/MySQL%20Workbench)라는 사이트에도 관련 내용이 적혀 있었다. 로제타 2에서만 돌아가고, 최신 버전 안되고 다운그레이드 버전만 된다는 것,.,

그래서 그냥 일단 저기 명시돼있는 v8.0.21 설치해서 실행했더니 안됐다. 예전에 리액트 네이티브 설치할 때 로제타 설치했었는데 그게 폰제타인지 아님 버전이 낮아서인지 안되길래 그냥 냅다 홈브루로 설치했다.

나보다 홈브루 개발자들이 훨씬 낫겠지 하고 설치했는데 역시나... 갓브루.... 걍 잘 된다.

![](/assets/images/all/workbench.png)

타라~

## ...는 홈브루로 설치했다가 쿼리문 실행하자마자 강종돼서 다운그레이드 시킴🥲

[대충 애플 개발자 포럼 글](https://developer.apple.com/forums/thread/724378)에도 나랑 비슷한 문제를 겪는 사람들이 많았다.

최신버전에서 동일한 문제를 겪는 사람들이 많았고, 내가 설치한 친구를 봤더니 역시나 최신 버전이었다.(2023.03.21 기준 v8.0.31)

다운그레이드 하면 된다고 되어있어서 봤더니 `v8.0.31`로 하란다,,, 역시 업뎃빠른 포럼이 제일이여,,,, 🥲

다운그레이드해서 새로 설치하고 돌려보니 이번엔 잘 된다!

선구자들이 있어서 나는 행복해...