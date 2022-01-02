---
title: "앱 아키텍처: 온보딩(Onboarding), Apple Human Interface Guidlines"
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
last_modified_at: 2022-01-02
---

> 이 글은 [Apple Human Interface Guidlines](https://developer.apple.com/design/human-interface-guidelines/ios/app-architecture/onboarding/)를 번역한 글 입니다.

# 온보딩(Onboarding)

온보딩은 새로운 유저를 맞이하고 다시 돌아온 유저를 재연결하게 합니다. 빠르고, 재미있고, 교육적인 선택적 온보딩 경험은 사람들이 앱을 방해받지 않고 최대한 활용하는 데 도움이 됩니다.

![](https://developer.apple.com/design/human-interface-guidelines/ios/images/onboarding/onboarding-screen.png)

**단순히 세팅용이 아닌, 사람들이 당신의 앱을 즐길 수 있도록 돕는 온보딩을 제공하세요.** 사람들은 당신의 앱에 대해 더 많이 배울 기회를 환영하기도 하지만, 동시에 그저 잘 작동하길 바랄 수 있습니다. 온보딩 환경에 세팅이나 구체적인 라이선스 정보를 담지 마세요. [런칭](https://2unbini.github.io/📂%20all/human%20interface%20guidlines/App-Architecture-Launching/)가이드를 참고하세요.

**빨리 시작할 수 있게 하세요.** 시스템이 [런치 스크린](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/launch-screen)을 앱의 첫 화면으로 바꾼 후에, 사람들이 바로 앱에 빠져 즐길 수 있도록 하세요. 만약 튜토리얼이나 인트로 시퀀스를 제공해야 한다면, 사람들이 건너뛸 수 있는 방법을 주고, 사람들이 돌아올 때 자동으로 표시하지 않습니다.

**도움이 필요할 때를 예상하세요.** 사람들이 갇힐 수 있는 시간을 미리 찾습니다. 게임을 예로 들어, 게임이 일시 정지 되거나 캐릭터가 전진하지 않을 때 유용한 팁을 자연스럽게 보여줄 수 있습니다. 사람들이 무언가를 노힌 경우에 튜토리얼을 다시 재생할 수 있도록 합니다.

**튜토리얼의 필수 요소에 집중하세요.** 초보자에게 가이드를 제공하는 것은 좋지만, 교육이 좋은 앱 디자인을 대신할 순 없습니다. 무엇보다도, 앱을 직관적으로 만드세요. 만약 사람들이 너무 많은 가이드를 필요로 한다면, 앱 디자인을 재검토하세요.

**학습을 재미있고 무언가 발견할 수 있도록 만드세요.** 직접 하면서 배우는 것은 지침 목록을 읽는 것보다 훨씬 재미있고 효과적입니다. 애니메이션과 상호 작용을 사용해 점진적이고 상황에 맞게 가르치세요. 대화형처럼 보이는 정적인 스크린샷을 표시하는 것을 지양하세요.