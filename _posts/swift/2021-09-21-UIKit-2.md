---
title: "Swift, UIKit 프로젝트 기본 구조와 앱 생명주기"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - swift
tags:
    - swift
    - 스위프트
    - UIKit
last_modified_at: 2021-09-22
---

# Swift 스토리보드-UIKit 프로젝트 구조

모델-뷰-컨트롤러 모델을 따라 스토리보드 인터페이스를 사용해 프로젝트 파일을 생성하면, 다음과 같은 파일들이 생성된다.

- AppDelegate.swift
- SceneDelegate.swift
- ViewController.swift
- Main.storyboard
- LaunchScreen.storyboard
- Info.plist

앱 전반의 동작에 관여하는 `AppDelegate`, 씬(장면)의 동작에 관여하는 `SceneDelegate`, 앱과 장면 위에서 실제로 표현될 여러 가지 뷰에 대한 상태와 동작 변화를 담당하는 `ViewController`, 그리고 인터페이스를 담당하는 `storyboard`파일들, 앱이 실제로 동작하기 직전, 앱의 정보를 받아오는 `Info.plist`로 이루어져 있다.

이 프로젝트 파일엔 앱의 전반적인 구조를 담당하는 `시스템 프레임워크`와, 뷰나 뷰의 동작 등을 제어하는 수정 가능한 영역인 `커스텀 코드` 영역이 있다. 우리는 시스템 프레임워크를 임의로 바꿀 필요가 없고, 실제로 바꿀 일도 없다.

앱과 장면의 동작에 대한 코드인 `AppDelegate`, `SceneDelegate`, 뷰와 뷰의 동작을 담당하는 `Controller`와 `storyborad`를 적절하게 수정하여 내가 원하는 방식으로 앱의 인터페이스와 기능을 구성할 수 있다.

## 시스템 프레임워크

내가 첫 번째 언어로 배운 프로그래밍 언어인 C언어에선, 프로그램이 시작되는 가장 첫 번째 함수를 `main`함수라고 명명했다. 이 메인 함수 안에 내가 실행하고자 하는 프로그램의 기능을 추가하여 프로그램을 구동시켰다.

Objective-C는 이런 C 언어의 특징을 이어받아 main 함수에서 프로그램의 기능을 시작하게 했다. Swift 역시 이 흐름의 연장선에 있기 때문에 main문이 있다. 그런데, C나 Objective-C와는 달리 Swift는 메인 함수가 따로 없고 `@main`이런 식으로 적혀만 있다.

![@main](/assets/images/uiKit-1/life1.png)

> `@`는 어노테이션(annotaion)으로, 어떠한 공통적인 특성이나 동작을 마치 변수 이름처럼 지정해 사용하는 것으로, `@main`이라고 시작되는 부분이 C의 main()문의 기능을 하는 것과 같다고 볼 수 있다.

스위프트에선 `UIApplicationMain`이 main의 동작을 한다.

## 커스텀 영역

UI를 변경하고, 각각의 뷰에 원하는 기능을 집어넣고 싶을 때 우리는 `Storyboard`와 `Controller`를 만든다. 이렇게 원하는 대로 바꿀 수 있는 부분을 커스텀 영역이라고 한다.

물론, 앱이 실행되는 초기 상태에 무언가 데이터를 불러오거나 별다른 동작이 필요하면 `AppDelegate`의 `application` 메서드에 코드를 작성할 수 있다.

### Controller

뷰 컨트롤러엔 각각의 뷰를 제어하기 위한 객체가 있는데, 이 클래스의 내부에 뷰의 상태나 기능을 제어하는 코드를 작성 해 주면 된다.

뷰 컨트롤러는 `UIViewController`를 상속받으며, 여기엔 뷰 로딩이 완료됐을 때 시스템에 의해 자동으로 호출되는 `viewDidLoad`와 같은 메서드가 정의되어 있다. 뷰의 초기 상태를 지정해줄 때 `viewDidLoad`메서드에 내용을 집어넣는다.

![viewDidLoad](/assets/images/uiKit-1/life3.png)

프로젝트를 생성하면, 자동으로 생성된 메인 뷰 컨트롤러에 viewDidLoad 메서드가 오버라이드 되어 있다. 주석처리 된 부분을 내가 원하는대로 수정하면 된다.

### Storyboard

컨트롤러에 의해 조작되고, 사용자에게 보여져 실제 동작과 사용자를 이어주는 각각의 뷰는 인터페이스 빌더를 통해 만들어진다.

인터페이스 빌더의 `storyboard`는 여러 뷰들의 흐름을 보여 줌으로써, 어떤 뷰를 통해 어떤 뷰로 넘어가는지, 어떤 기능이 있는지를 쉽게 알 수 있게 한다.

storyborad 파일을 열어 보면, 미리보기처럼 각각의 뷰를 볼 수 있고, 어시스턴트 창을 열어 각각의 뷰를 제어하는 컨트롤러와 바로 연결할 수 있게 되어 있다.


# iOS 앱의 생명주기

![lifecycle](/assets/images/uiKit-1/life2.png)

앱이 실행되고 끝나는 과정에서 시스템 영역과 커스텀 영역이 어떻게 상호작용하는지 보여 주는 유명한 그림이다.

## Swift의 main, UIApplicationMain

`UIApplicationMain`은 어플리케이션 객체와 이에 대한 델리게이트를 생성하고, 이벤트 사이클을 만드는 함수라고 한다. [공식 문서](https://developer.apple.com/documentation/uikit/1622933-uiapplicationmain/)

이 함수는 시스템 프레임워크와 커스텀 코드를 엮어 앱 객체를 생성하는 프로세스 핸들링을 하고, UI와 기능을 읽어 구현하고, 이벤트 루프를 실행하기 때문에 앱 그 자체로 볼 수 있다.

위의 그림에서 볼 수 있듯, 사용자가 앱 아이콘을 터치해 앱이 시작되면, `UIApplicationMain`이 실행되고, 이 메인은 `UIApplication`객체를 생성하고, `AppDelegate`를 생성한 뒤, 해당 클래스에 있는 `application` 메소드를 실행시켜 앱이 처음 구동될 때 개발자가 써 놓은 코드를 실행하게 한다.

`AppDelegate`는 `UIApplication`의 권한을 위임받아 커스텀 코드와 상호작용하여 코드를 실제 뷰와 컨트롤러로 보여지게, 그리고 동작하게 한다. 앱 실행동안 유지되며, 앱이 종료되면 소멸하고, 단 한개의 인스턴스만 생성한다.

## Event Loop

앱이 꺼지기 직전까지 쭉 이벤트 루프를 돌면서 뷰를 생성하고, 동작하게 한다. 앱을 구성하는 여러 뷰와, 각각의 뷰를 제어하는 컨트롤러가 사용자와 상호작용하는 것 같다.

## 종료

종료되기 직전 `AppDelegate`의 `applicationWillTerminate` 메소드가 실행되고, 해당 메소드 안에 적혀 있는 커스텀 코드에 따라 종료 직전 처리해야 할 일들을 모두 하고나면, 앱이 진짜_최종_종료된다.

# 정리

- `UIApplicationMain`이 스위프트에선 메인 함수에 해당한다.
- `AppDelegate`의 다양한 메서드로 앱의 생명주기에 맞춰 원하는 코드를 실행시킬 수 있다.
- `Controller`를 통해 뷰를 제어하고, `storyboard`로 UI를 그릴 수 있다.

잘못된 내용이나 더 구체적인 내용이 있다면 언제든 알려주세요!