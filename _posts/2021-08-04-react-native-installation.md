---
title: "M1, React Native 설치"
toc: true
toc_sticky: true
categories:
    - study
tags:
    - 리액트네이티브
    - 기초
    - 공부
last_modified_at: 2021-08-04
---

<br/>

늙고 소중한(했던) 나의 13년형 맥북 프로를 보내고...
젊고 귀여운 나의 20년형 맥북 에어와 함께 리액트 네이티브를 시작해보았다.

구구절절이지만 대충 시뮬레이터 돌려보고 기타등등 하려면 내 낡은 카탈리나를 Big Sur 로 업데이트 해야 했는데, 업데이트를 하면 내 맥북은 벽돌이 되기 때문에 더러운 꼴 안 보고 그냥 질러버렸다.

그럼 젊고 귀여운 20년형 맥북은 잘 되나? 에러의 향연 끝에 되긴 된다.

- [iOS 시뮬레이터](https://2unbini.github.io/study/react-native-android-simulator)
- [안드로이드 시뮬레이터](https://2unbini.github.io/study/react-native-android-simulator)

## 리액트 네이티브 설치

를 위해서는 `npm` 이 필요한데 이 `npm` 은 풀네임이 node package manager 이다.

이 말은 `node` 가 필요하다는 것일테고, 그럼 `node` 를 설치하려면 뭐가 필요하냐?

`homebrew` 킹갓브루가 필요하다.

그래서 **`homebrew` → `node` → `react native`** 순으로 설치 해 줄건데,

이 과정에서 **필요한 툴, 의존성 관리 도구, ide,** 그리고 결과적으로 `M1` 환경에서 손쉬운 개발을 하게 해 줄 **`iTerm` 과 `Rosetta`** 를 함께 설치 할 것이다.

### Xcode 설치

나의 새 맥북 구매를 하게끔 한 장본인.
앱스토어 가서 그냥 다운 받으면 된다. 시간이 오래 걸리니 얘를 가장 먼저 다운로드 하는 것이 좋다.

Xcode 설치가 안 돼 있으면, 즉 Apple 개발자 도구에 대한 다운로드나 설정이 완료되지 않으면, 뒤의 과정에서 오류가 생길 수 있으니 반드시 설치해놓기로 하자.


### Rosetta 설치

- Rosetta 는 M1과 같이 ARM 기반의 칩을 사용하는 환경에서 x86 아키텍처를 사용하여 디자인된 Intel 기반 칩용 앱을 실행할 수 있는 lifeline 이라고 한다.

```
/usr/sbin/softwareupdate --install-rosetta --agree-to-license
```

  → Rosetta 설치 없이 실행하려면 [이 블로그](https://medium.com/@davidjasonharding/developing-a-react-native-app-on-an-m1-mac-without-rosetta-29fcc7314d70) 참조!


### iTerm 설치

- 쉽게 생각하면 맥에 기본적으로 있는 [terminal.app](http://terminal.app) 과 동일한 기능을 하는 프로그램이다.
- 터미널로 그냥 시작하지 않고 iTerm을 다운받는 이유는, 당신이 M1을 사용하기 때문이다.
- 터미널은 AppleSilicon 에서 작동할 수 있도록 남겨 두고, iTerm 을 Rosetta 로 돌릴 수 있도록 나누어 주는 것이다. 구분을 위해서 다운받는 것이니 터미널에 Rosetta를 돌리게 하고 iTerm에 AppleSilicon을 돌리게 하는 것도 당연히 된다.

> [iTerm](https://iterm2.com/)

위 링크에 들어가서 다운받자.

![](https://images.velog.io/images/seri_ous/post/3ef34e3e-46a4-4a78-ab8b-12d30ee902dc/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202021-08-04%20%EC%98%A4%EC%A0%84%201.30.34.png)


설치한 뒤, Finder에서 iTerm을 응용 프로그램 디렉토리로 옮긴다.

command + i혹은 마우스 우클릭 + 정보 가져오기 로 iTerm 정보를 띄우고,

Rosetta를 사용하여 열기를 클릭해준다.

### Homebrew 설치

- 킹갓브루는 맥에서 다양한 툴을 설치할 수 있는 패키지 매니저다.
- 킹갓브루는 한글로 된 홈페이지도 있다.

> [Homebrew](https://brew.sh/index_ko)

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

로 homebrew 를 설치해주고,

```
brew --version
```

으로 제대로 잘 깔렸는지 확인 해 준다.

homebrew를 설치했으니 이제 본격! 설치를 해 보자.

차례로 설치해 줄 것들은 `node`, `watchman`, `cocoapods`, `ffi` 이다.


### node 설치

- 모두가 알고 있는 그 node.js 가 맞다.

```
brew install node
```

설치해주고,

```
node -v
npm -v
```

로 확인 해 준다.

node가 정상적으로 설치 됐으면 npm도 잘 설치 됐을 것이다.


### watchman 설치

- 특정 폴더나 파일을 감시해서 변경됐을 때 어떠한 동작을 하는 툴이다.
- react native에서 코드를 수정했을 때 바로 적용할 수 있게끔 해 준다.

```
brew install watchman
```

```
watchman --version
```

근데 버전 확인해보면 일반적으로 우리가 아는 그 버전 형태가 아니라 날짜로 나오는 경우가 있는데, 홈브루 버그인 듯 싶다. → [관련 링크](https://github.com/facebook/watchman/issues/915)


### cocoapods 설치

- 늙은 13년형 맥북으로 이거 설치하려다가 온갖 잡새가 날아들어서 에러 파티를 일으킨 적이 있다.
- 하지만 이번엔 다르다. 새 맥북은 꼬일 일이 없다.
- 아무튼 cocoapods는 의존성 관리자로, iOS 개발을 할 때 뗄래야 뗄 수 없다고 한다.

```
sudo gem install cocoapods
```

갑자기 gem? 그냥 해 주자. 왜냐면 맥에는 ruby가 기본으로 깔려 있기 때문에 그냥 해 주면 된다. 혹시나 확인하고 싶다면 아래와 같이 입력하자.

```
ruby -v
```

그리고 내가 방금 설치한 cocoapod 의 버전을 확인해준다.

```
pod --version
```

확인.

### ffi 설치

- 왜 하는 지 잘 모르겠어서 건너뛰었다가 뒤에서 에러를 맛 보고 다시 그냥 깔았다.
- 얘 때문에 에러가 나고 안나고 하는지는 솔직히 모르겠다.

```
sudo gem install ffi
```

### React Native 설치

대망의 그 것.

```
npm install -g react-native-cil
```

```
react-native --version
```

설치 완료.

### 설치는 끝났다.

M1 맥북 아키텍쳐때문에 설치에 어려움을 겪는 글을 너무 많이 봐서 쫄았는데, 이 대로 하면 별 에러 없이 잘 된다.

사실 RN 개발 환경은 그렇게 큰 에러가 안 난다. 근데 이제 시뮬레이터를 곁들이면 ...

... [다음 글](https://2unbini.github.io/study/react-native-iOS-simulator)에서 알아보도록 하자...