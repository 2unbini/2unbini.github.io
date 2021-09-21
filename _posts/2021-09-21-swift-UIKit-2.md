---
title: "Swift, UIKit 프로젝트 기본 구조와 앱 생명주기"
toc: true
toc_sticky: true
categories:
    - swift
tags:
    - swift
    - 스위프트
    - UIKit
last_modified_at: 2021-09-21
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

앱과 장면의 동작에 대한 코드인 `AppDelegate`, `SceneDelegate` 이 두 가지 파일은 말하자면 시스템의 영역이기 때문에 우리가 수정할 필요가 없다. 대신, 뷰와 뷰의 동작을 담당하는 `Controller`와 `storyborad`를 적절하게 수정하여 내가 원하는 방식으로 앱의 인터페이스와 기능을 구성할 수 있다.

## 시스템 프레임워크

내가 첫 번째 언어로 배운 프로그래밍 언어인 C언어에선, 프로그램이 시작되는 가장 첫 번째 함수를 `main`함수라고 명명했다. 이 메인 함수 안에 내가 실행하고자 하는 프로그램의 기능을 추가하여 프로그램을 구동시켰다.

Objective-C는 이런 C 언어의 특징을 이어받아 main 함수에서 프로그램의 기능을 시작하게 했다. Swift 역시 이 흐름의 연장선에 있기 때문에 main문이 있다. 그런데, C나 Objective-C와는 달리 Swift는 메인 함수가 따로 없고 `@main`이런 식으로 적혀만 있다.

![@main](/assets/images/uiKit-1/life1.png)

> `@`는 어노테이션(annotaion)으로, 어떠한 공통적인 특성이나 동작을 마치 변수 이름처럼 지정해 사용하는 것으로, `@main`이라고 시작되는 부분이 C의 main()문의 기능을 하는 것과 같다고 볼 수 있다.

## 커스텀 영역

- Controller
- Storyboard

# iOS 앱의 생명주기

## Swift의 main, UIApplicationMain()

`UIApplicationMain`은 어플리케이션 객체와 이에 대한 델리게이트를 생성하고, 이벤트 사이클을 만드는 함수라고 한다. [공식 문서](https://developer.apple.com/documentation/uikit/1622933-uiapplicationmain/)

이 함수는 시스템 프레임워크와 커스텀 코드를 엮어 앱 객체를 생성하는 프로세스 핸들링을 하고, UI와 기능을 읽어 구현하고, 이벤트 루프를 실행하기 때문에 앱 그 자체로 볼 수 있다.