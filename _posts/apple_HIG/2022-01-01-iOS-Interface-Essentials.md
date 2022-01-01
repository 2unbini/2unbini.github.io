---
title: "인터페이스 필수 요소, Apple Human Interface Guidlines"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - Human Interface Guidlines
tags:
    - iOS
    - Human Interface Guidlines
    - 근본
    - 번역
last_modified_at: 2022-01-01
---

> 이 글은 [Apple Human Interface Guidlines](https://developer.apple.com/design/human-interface-guidelines/ios/overview/interface-essentials/)를 번역한 글 입니다.

# 인터페이스 필수 요소

대부분의 iOS 앱은 일반적인 인터페이스 요소를 정의하는 프로그래밍 프레임워크인 [UIKit](https://developer.apple.com/documentation/uikit)의 요소를 사용해 만들어집니다. 이 프레임워크는 시스템에 상관 없이 앱이 일관적인 모양을 유지할 수 있도록 함과 동시에, 높은 수준의 커스텀을 제공합니다. UIKit 요소들은 유연하고 친숙합니다. 그들은 적용 가능하고, 어떤 iOS 디바이스에서도 잘 보이는 단일 앱을 디자인할 수 있게 하며, 시스템이 외형의 변화를 감지해 알려 줄 때 자동적으로 그 상태를 업데이트합니다. UIKit으로 제공되는 인터페이스 요소를 세 개의 주요 카테고리로 나누면 다음과 같습니다.

[**바.**](https://developer.apple.com/design/human-interface-guidelines/ios/bars/) 사람들에게 앱 상의 어디에 위치해 있는지 말 해 주고, 길을 알려주며, 작업을 시작하고 정보를 전달하기 위한 버튼 또는 기타 요소들을 포함할 수 있습니다.

[**뷰.**](https://developer.apple.com/design/human-interface-guidelines/ios/views/) 텍스트, 그래픽, 애니메이션, 상호작용하는 요소들과 같이 사람들이 앱에서 볼 수 있는 주된 콘텐츠를 포함합니다. 뷰는 스크롤, 추가, 삭제, 정렬 등과 같은 행동을 가능하게 할 수 있습니다.

[**제어.**](https://developer.apple.com/design/human-interface-guidelines/ios/controls/) 작업을 시작하고 정보를 전달합니다. 버튼, 스위치, 텍스트 필드, 진행 상태 표시자 등이 제어 요소의 예시들입니다.

UIKit은 iOS의 인터페이스를 정의하는 것 외에도 앱이 채택할 수 있는 기능을 정의합니다. 예를 들어, 이 프레임워크를 통해 앱은 터치 스크린을 통한 제스터에 응답하고, 그리기, 접근성 및 인쇄와 같은 기능들을 가능하게 합니다.

iOS는 애플 페이, 헬스킷, 리서치킷과 같이 엄청나게 멋진 앱을 만들 수 있는 다른 프로그래밍 프레임워크와 [기술](https://developer.apple.com/design/human-interface-guidelines/#technologies)들과 긴밀하게 통합할 수 있습니다.