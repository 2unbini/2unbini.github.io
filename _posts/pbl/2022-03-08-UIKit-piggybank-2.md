---
title: "UIKit으로 어플 개발하기 - PiggyBank 개발일지 2"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - swift
    - pbl
tags:
    - swift
    - UIKit
    - 개발일지
    - iOS
    - pbl
last_modified_at: 2022-03-08
---

# 개발일지 2

멋진 어플 개발을 하게 되었다. 일종의 타임캡슐 어플이다.

iOS 인디 개발자님의 피드백을 듣고 지금까지 했던 모든 작업을 롤백하기로 했다... 사실은 개발일지 1 이후에 팝업 뷰를 만들었는데, **iOS스럽지 않은 UI**라는 뾰족한 피드백을 듣고 갈아엎기로 했다.

UI 디자인을 보면서 묘한 느낌을 받았는데 내가 디자인을 잘 몰라서 그런 줄 알았다. 그런데 피드백을 듣고 나니 내가 여태껐 써온 iOS 앱과는 조금 다른 느낌이라 그런 묘한 느낌을 받았다는 걸 깨달았다.

그래서 `iOS스러운 UI`에 집중해 다시 기획하기로 했다.

## iOS스럽게 다시 만들기

### iOS스러운 건 뭘까?

우리 기획과 디자인이 iOS스럽지 않은 가장 큰 이유는 '복잡함'이라고 생각했다.

홈 화면을 예로 들었을 때, 서로 다른 기능을 하는 버튼이 3개 있고, 스와이프해서 내가 가지고 있는 타임캡슐(이후 유리병)을 볼 수 있고, 이 각각의 유리병은 찬 정도에 따라 이미지가 다르며, 개봉했는지, 미개봉상태인지, 완료되기 전에 강제 개봉한 상태인지 등 여러 상태에 따라 액션이 달라진다.

타인에게 우리 앱의 기능이라고 설명할 때, 나의 말이 구구절절 길어지고 설명할 기능이 많아지며, 한번에 이해가 안 간다는 상대방의 표정이 나오는 순간 그건 iOS스럽지 않은 거라고 생각한다.

기본 앱들, 그리고 앱스토어 상위권에서 지속적으로 유저의 사랑을 받는 앱들은 대개 기능이 단순하고, 직관적이며, 깔끔한 UI 디자인을 추구한다.

모든 애플 디자인 가이드를 다 읽은 것은 아니지만, 위 앱들은 애플의 디자인 가이드에 크게 반하지 않는다는 느낌을 받았다.

결국, 개발자 겸 기획자의 입장에서 유저에게 제공하고 싶은 기능, 그리고 그 기능에 대한 제약조건들을 자꾸 추가하다보니 기능이 복잡해졌고, 다른 iOS 앱들이 어떻게 생겼는지, 디자인 가이드에서 뭐라고 하는지 등을 제대로 찾아보지 않아 UI 또한 복잡해졌다고 생각했다.

그래서 모든 복잡함을 덜어내기로 했다.

### 기능 단순화

우리 앱의 가장 핵심 기능인 '추억 저장하기'를 MVP로, 추억을 담을 유리병을 추가하고, 해당 유리병에 쪽지를 쓴 뒤, 개봉 일자가 되면 개봉할 수 있게끔 단순화했다. 이전의 기능에서 주었던 개봉 일자가 되기 전 강제로 개봉할 수 있는 옵션을 삭제했다. 

이전의 기능에서 홈 화면에서 왼편으로 스와이프하면 과거의 유리병들을, 오른편으로 스와이프하면 새 유리병을 추가할 수 있도록 했던 것을 단순화했다. 홈 화면에서는 현재 진행중인 유리병만 보이고, 현재 진행중인 유리병이 없을 땐 새 유리병을 추가할 이미지를 보이게 했다. 

### 화면 단순화

홈 화면에서 각기 다른 기능으로 존재하던 버튼들을 TabBar로 서로 다른 뷰로 연결지었다.

TabBar로 연결지으면서 뷰 간의 관계를 시각적으로 볼 수 있도록 Storyboard를 이용했다. 시각적으로 나타내면 어떤 뷰가 중복되는지, 어떤 뷰가 누구와 연관돼있는지를 파악할 수 있어 화면을 단순화하고 재사용하기에도, 화면끼리 연결짓기에도 좋다는 장점이 있었다.

오토레이아웃, 그리고 뷰 계층에 익숙해지기 위해 코드로 짰는데, 이렇게 하니 화면끼리 연관관계가 한눈에 들어오지 않았다. 모든 UI 요소를 코드로 정의하고, 그에 따른 constraints도 모두 코드로 구현했더니 코드 라인 수도 엄청나게 늘어났다.

홈 뷰를 비롯해 팝업 뷰 등 몇 가지의 뷰를 구성할 때 코드로 짜 봤으니, 지금부턴 조금 더 효율적이고 직관적인 작업을 위해 Storyboard를 사용하기로 했다.

### 동기부여

기능과 화면을 단순화함에 따라, 사용자에게 우리의 앱이 지루하지 않다는 느낌을 주고, 매일 들어와 사용할 수 있는 동기를 부여해야 했다.

이에 따라, 유리병에 쌓이는 쪽지를 애니메이션으로 보여지며, 디바이스를 움직였을 때 그에 따라 유리병 안의 쪽지가 움직일 수 있도록 만들기로 했다.


## 삭제했던 Main.storyboard 부활

### Info.plist

Application Scene Manifest의 하위 항목 중, Scene Configuration - Application Session Role - Item에 `Storyboard Name` 항목을 추가하고 Value에 `Main`을 채워넣었다.

원래 Storyboard를 사용하도록 프로젝트를 만들면 자동으로 되어있는 부분이지만, 스토리보드 없이 코드로 작성하려고 했던 터라 해당 항목을 삭제했었다. 이를 복구시켜주었다. 

### SceneDelegate.swift

```
// If using a storyboard, the `window` property will automatically be initialized and attached to the scene.
```

이전에 스토리보드를 사용하지 않으면서 앱이 우리가 지정한 ViewController 객체로 씬을 구성하게 만들었다. 해당 부분을 모두 삭제하고 초기 상태로 되돌렸다. 스토리보드를 사용한다면 자동으로 씬을 구성해주기 때문이다.

### HomeViewController 연결

Main.storyboard를 생성하면 기본으로 있는 ViewController를 HomeViewController로 연결해줬다. 당연히 ViewController만 연결해주면 될 줄 알았는데, 이렇게 하니 다음과 같은 에러가 발생했다.

> *** Terminating app due to uncaught exception 'NSUnknownKeyException', reason: '[<HomeViewController> setValue:forUndefinedKey:]: this class is not key value coding-compliant for the key mainView.'

`mainView`에 해당하는 값이 없다는 에러인데, 내 기억으로는 mainView를 선언한 적이 없었다.

하지만 내 Main.storyboard에 최초로 만들어져있는 ViewController 안의 UIView가 mainView였다. 내가 안했으니 기억이 안나지... 아무튼 뷰 컨트롤러 내부의 UIView를 내 HomeViewController의 뷰와 연결해주었더니 해결되었다.

## HomeViewController 정리

### 구조

이전에 페이지뷰를 사용해 유리병 간 스와이프로 이동하게 구조를 짰다. 하지만 기능을 변경하게 되면서 더이상 페이지 뷰는 필요가 없어졌다.

다음과 같이 짰던 구조를

```markdown
  button, labels                                      image
        |                                               |
    home view                view                  content view
        |                      |                        |
homeViewController --  pageViewController  -- pageContentViewController
```

다음과 같이 단순하게 바꾸었다.

```markdown
      label
  bottle image  ---------  imageView
        |                      |
    home view                view
        |                      |
HomeViewController --  BottleViewController
```

### 코드

#### UIPageViewControllerDelegate, DataSource

기존에 페이지 뷰를 구성하기 위해 채택했던 UIPageViewControllerDelegate와 DataSource 프로토콜을 삭제하고, 페이지 뷰 컨트롤러와 관련된 모든 코드를 삭제했다.

#### Buttons

또한, 기존에 홈 화면에 표시됐던 버튼들을 모두 탭 바로 이동함에 따라 버튼 요소들을 모두 삭제했다.

#### BottleViewController

페이지 뷰를 사용하면서 진행중인 유리병이 없을 때 새로운 유리병을 추가할 수 있는 로직을 HomeViewController에서 담당해야 했다.

하지만 페이지 뷰 없이 단순하게 진행중인 유리병이 없을 때 유리병 추가할 수 있는 이미지만 띄우게끔 변경했기 떄문에 새로운 유리병 추가 로직 또한 BottleViewController에서 한꺼번에 담당할 수 있게 만들었다.


## 결론

**iOS스러운** 앱에 대해 고민해보는 기회였다.

개발자라고 그냥 무지성으로 기능만 개발해야 하는 건 아니라고 생각한다. 이 기능이 유저에게 어떤 영향을 끼칠지에 대해서 고민하다보면 코드의 질이나 일의 효율성이 높아지게 되는 것 같다.