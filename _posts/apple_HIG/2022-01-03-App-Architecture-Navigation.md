---
title: "앱 아키텍처: 내비게이션(Navigation), Apple Human Interface Guidlines"
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
last_modified_at: 2022-01-03
---

> 이 글은 [Apple Human Interface Guidlines](https://developer.apple.com/design/human-interface-guidelines/ios/app-architecture/navigation/)를 번역한 글 입니다.

# 내비게이션(Navigation)

사람들은 내비게이션이 그들의 기대에 미치지 않을 때까지 앱의 내비게이션을 인식하지 못하는 경향이 있습니다. 당신의 임무는 앱 자체에 주의를 기울이지 않고도 앱의 구조와 목적을 보여 주는 내비게이션을 구현하는 데 있습니다. 탐색은 자연스럽고 친숙하게 느껴져야 하며, 콘텐츠에서 집중을 멀어지게 하거나 인터페이스를 지배해서는 안 됩니다. iOS에는 세 가지 주요 내비게이션 스타일이 있습니다.

### 계층적 탐색

목적지에 도달할 때까지 한 화면 당 하나의 선택을 하게 합니다. 다른 목적지를 가기 위해선 이전 단계로 되돌아가거나, 처음부터 다시 시작해 다른 선택을 해야 합니다. 설정과 메일은 이러한 탐색 스타일을 사용합니다.

![hierarchical navigation](https://developer.apple.com/design/human-interface-guidelines/ios/images/NavigationHierarchical-Graphic_2x.png)

### 평면 탐색

다수의 카테고리를 바꿔가며 이동합니다. 음악과 앱스토어는 이러한 탐색 스타일을 사용합니다.

![flat navigation](https://developer.apple.com/design/human-interface-guidelines/ios/images/NavigationFlat-Graphic_2x.png)

### 콘텐츠 중심 또는 경험 중심 탐색
 
콘텐츠 간 자유롭게 움직이거나, 콘텐츠 그 자체가 탐색인 경우입니다. 게임, 도서, 그리고 다른 많은 앱들이 이러한 탐색 스타일을 사용합니다.

![content-driven or experience-driven navigation](https://developer.apple.com/design/human-interface-guidelines/ios/images/NavigationExperienceDriven-Graphic_2x.png)

어떤 앱들은 한 개 이상의 탐색 스타일을 결합해 씁니다. 예를 들어, 평면 탐색을 사용하는 앱은 각 카테고리를 계층적 탐색으로 구현하도록 만드는 것입니다.

**항상 정확한 경로를 제공해야 합니다.** 사람들은 언제나 앱 상의 어디에 위치해 있는지, 다음 목적지로 어떻게 가는지 알아야 합니다. 탐색 스타일에 상관 없이, 콘텐츠를 관통하는 경로는 논리적이고, 예측 가능하며, 따라가기 쉬워야 합니다. 일반적으로, 각 화면엔 하나의 경로를 제공합니다. 만약 다수의 맥락에서 화면을 띄워야 한다면, 활동 시트, 알림, 팝오버 또는 모달 뷰를 권장합니다. 더 알아보려면 [활동 시트](https://developer.apple.com/design/human-interface-guidelines/ios/views/action-sheets/), [알림](https://developer.apple.com/design/human-interface-guidelines/ios/views/alerts/), [팝오버](https://developer.apple.com/design/human-interface-guidelines/ios/views/popovers/), [양식](https://2unbini.github.io/📂%20all/human%20interface%20guidlines/App-Architecture-Modality/)을 참고하세요.

**콘텐츠로 향하는 빠르고 쉬운 정보 구조를 디자인하세요.** 최소한의 탭, 스와이프, 화면으로 볼 수 있는 정보 구조로 정리해야 합니다.

**유동성을 위해 터치 제스처를 사용합니다.** 최소한의 마찰로 인터페이스를 이동하게 하세요. 예를 들어, 사람들이 이전 화면으로 돌아가기 위해 옆면을 스와이프할 수 있게 만듭니다.

**표준 탐색 요소를 사용하세요.** 페이지 컨트롤, 탭 바, 세그먼트 컨트롤, 테이블 뷰, 컬렉션 뷰, 분할 뷰와 같은 표준 탐색 컨트롤을 사용하는 것이 좋습니다. 사용자는 이러한 제어에 익숙하며, 당신의 앱을 사용할 때 어떻게 해야 할 지 직관적으로 알고 있을 것입니다.

**내비게이션 바를 사용해 데이터 계층을 탐색합니다.** 내비게이션 바의 제목은 계층 안에서 현재 위치가 어디인지를 보여 주고, 뒤로가기 버튼은 이전 위치로 쉽게 돌아가도록 만들어줍니다. 상세한 가이드는 [내비게이션 바](https://developer.apple.com/design/human-interface-guidelines/ios/bars/navigation-bars/)를 참고하세요.

**탭 바를 사용해 콘텐츠나 기능의 피어 범주를 나타내세요.** 탭 바는 현재 위치에 상관 없이 사람들이 카테고리 간에 빠르고 쉽게 이동할 수 있도록 합니다. 상세한 가이드는 [탭 바](https://developer.apple.com/design/human-interface-guidelines/ios/bars/tab-bars/)를 참고하세요.

**아이패드에서는 탭 바 대신에 분할 화면을 사용하세요.** 분할 화면은 탭 바와 동일하게 빠른 탐색을 제공하면서, 넓은 화면을 더 잘 사용할 수 있게 합니다. 가이드는 [분할 화면](https://developer.apple.com/design/human-interface-guidelines/ios/views/split-views/)을 참고하세요.

**같은 타입의 콘텐츠를 다수의 페이지에 띄워야 할 때 페이지 컨트롤을 사용하세요.** 페이지 컨트롤은 몇 개의 페이지가 있는지, 그리고 뭐가 지금 활성화되어 있는지를 명확하게 표현합니다. 날씨 앱은 구체적인 장소에 맞는 날씨 페이지를 페이지 컨트롤을 통해 보여줍니다. 자세한 가이드는 [페이지 컨트롤](https://developer.apple.com/design/human-interface-guidelines/ios/controls/page-controls/)을 참고하세요.

> TIP <br>
> 세그먼트 컨트롤과 툴 바로 탐색을 할 순 없습니다. 세그먼트 컨트롤을 사용해 정보들을 각기 다른 카테고리로 정렬하세요. 툴 바를 사용해 현재 콘텍스트에서 상호작용할 수 있는 컨트롤을 제공하세요. 이 요소들에 대한 추가적인 정보는 [세그먼트 컨트롤](https://developer.apple.com/design/human-interface-guidelines/ios/controls/segmented-controls/)과 [툴 바](https://developer.apple.com/design/human-interface-guidelines/ios/bars/toolbars/)를 참고하세요.