---
title: "앱 아키텍처: 양식(Modality), Apple Human Interface Guidlines"
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

> 이 글은 [Apple Human Interface Guidlines](https://developer.apple.com/design/human-interface-guidelines/ios/app-architecture/modality/)를 번역한 글 입니다.

# 양식(Modality)

양식(Modality)은 종료하기 위해 명시적인 행동이 필요한 임시 모드에서 콘텐츠를 표현하는 디자인 기술입니다. 콘텐츠를 모달로 표현하면 다음과 같은 것을 할 수 있습니다.

- 사람들이 독립적인 작업이나 밀접하게 관련된 옵션들에 집중할 수 있도록 돕습니다.
- 사람들이 중요한 정보를 얻을 수 있도록 보장하며, 필요한 경우 그에 대한 행동을 할 수 있도록 합니다.


![alert](https://developer.apple.com/design/human-interface-guidelines/ios/images/modality/modal-alert.png)| ![modal_view](https://developer.apple.com/design/human-interface-guidelines/ios/images/modality/modal-view.png)
----|----
Alert | Modal View

시스템에 정의된 다양한 모달 경험을 위해, iOS는 [알림](https://developer.apple.com/design/human-interface-guidelines/ios/views/alerts/), [활동 뷰](https://developer.apple.com/design/human-interface-guidelines/ios/views/activity-views/), [공유 시트](https://developer.apple.com/design/human-interface-guidelines/ios/extensions/sharing-and-actions), [작업 시트](https://developer.apple.com/design/human-interface-guidelines/ios/views/action-sheets/)를 제공합니다. 커스텀 모달 콘텐츠를 사용하기 위해서 다음과 같은 스타일을 사용할 수 있습니다.

- **자동**. 기본 보기 스타일을 사용하며, 이는 주로 시트입니다.
- **전체 화면**. 이전 화면을 덮는 것으로, 해당 창을 닫을 수 있는 버튼을 필요로 합니다.
- **팝오버**. 수평으로 된 규칙적인 환경에서는 팝오버를 표시하고, 컴팩트한 환경에서는 시트를 제공합니다.
- **페이지 시트와 형식 시트**. 이전 화면을 부분적으로 덮는 것입니다. [시트](https://developer.apple.com/design/human-interface-guidelines/ios/views/sheets/) 가이드를 참고하세요.
- **현재 컨텍스트**. 특정한 이전 화면을 덮는 것입니다.
- **커스텀**. 커스텀 컨테이너에 있는 콘텐츠를 표현하기 위해 커스텀 애니메이션을 사용합니다.

이에 대한 개발자 가이드는 [UIModalPresentationStyle](https://developer.apple.com/documentation/uikit/uimodalpresentationstyle)을 참고하세요.

> NOTE <br>
> 현재 컨텍스트 모달 뷰 스타일을 사용해 분할 창, 팝오버 또는 전체 화면이 아닌 기타 뷰에서 모달 콘텐츠를 표시하는 경우, 컴팩트한 환경에서 모달 콘텐츠를 사용할 때 시트로 전환해 사용합니다.

**양식을 써야 할 이유가 있을 때 쓰세요.** 모달은 선택을 해야 하거나 현재 수행중인 것과 다른 일을 해야 할 때처럼 사람들의 이목을 집중해야 하는 상황에서만 사용하세요. 모달은 사람들이 현재 상황에서 벗어나게 하고, 취소하는 행동이 동반되기 때문에 확실한 이점이 있을 때에만 쓰는 것이 중요합니다.

**중요하고, 행동 가능한 정보에 대해서 알림을 제공하세요.** 일반적으로, 알림은 무언가 잘못됐을 때 나타납니다. 알림은 현재 상태를 방해하고, 취소를 위한 탭을 필요로 하기 때문에 사람들이 이러한 방해가 정당하다고 느끼는 것이 중요합니다. 가이드는 [알림](https://developer.apple.com/design/human-interface-guidelines/ios/views/alerts/)을 참조하세요.

**일반적으로 모달에서의 할 일은 간단하고, 짧고, 집중적이어야 합니다.** 모달에서 하는 일이 너무 복잡하면 사람들은 모달 콘텍스트에 들어오면서 미뤘던 할 일에 대해 잊어먹게 됩니다. 모달이 앱 안에 있는 다른 앱처럼 느끼지 않도록 조심해야 합니다. 사람들이 원래 작업으로 되돌아가는 방법을 잊어버릴 수 있으므로 특히 모달 작업 안에서 뷰의 계층 구조를 주의해서 표시하세요. 만약, 모달에 하위 뷰가 포함되어야 한다면, 계층 구조에 대한 단일한 경로와, 완료를 위한 명확한 경로를 제공해야 합니다. 작업 완료 이외의 다른 목적으로 '완료' 버튼을 사용해선 안 됩니다.

**몰입해야 하는 콘텐츠나 복잡한 작업을 위해선 전체 화면 스타일의 모달을 사용하세요.** 전체 화면 모달은 방해를 최소화하므로, 영상, 사진, 또는 카메라 화면을 표시할 때 사용하기 좋고, 문서에 표시를 하거나 사진을 편집할 때와 같은 다단계 활동을 가능케 합니다.

**항상 모달 뷰를 없앨 수 있는 버튼을 포함해야 합니다.** '완료'나 '취소'와 같은 버튼을 사용할 수 있습니다. 버튼을 추가하면 모달 뷰가 보조 기술에 접근할 수 있도록 하고, 취소 제스처에 대한 대안을 제공합니다.

**필요한 경우, 사람들이 데이터를 잃지 않도록 하기 위해 모달 뷰를 닫기 전 확인을 받으세요.** 취소 제스처를 사용하거나 뷰를 닫는 버튼을 누르는 등 유저가 생성한 콘텐츠를 잃을 수 있는 행동이 생겼을 때, 액션 시트를 통해 상황을 설명해 사람들이 이를 해결하게 하세요.

**모달 뷰에서의 할 일을 쉽게 식별하도록 하세요.** 사람들이 모달 뷰에 들어온 이상, 그들은 이전 상황에서 벗어나 바로 돌아가지 않을 수 있습니다. 모달 뷰의 작업 이름을 제목으로 제공하거나 그 작업에 대한 설명 혹은 가이드를 추가적인 텍스트로 제공하면 사람들이 앱에서 자신의 위치를 유지하도록 도울 수 있습니다.

**모달 뷰의 모습을 당신의 앱의 모습과 어울리게 만드세요.** 예를 들어, 모달 뷰에 내비게이션 바가 포함돼있다면, 앱에 있는 내비게이션 바와 같은 모양을 사용해야 합니다.

**앱에 잘 적용될 수 있는 모달 트랜지션을 선택하세요.** 앱과 조화를 이루고 일시적으로 컨텍스트를 옮기는 것에 대해 인식할 수 있는 트랜지션 스타일을 사용하세요. 기본 트랜지션은 모달 뷰를 화면의 밑에서부터 수직 위로 올리고 취소됐을 경우 다시 아래로 내려가는 방식입니다. 앱 전반에 동일한 트랜지션 스타일을 사용하세요.

개발자 가이드는 [UIViewController](https://developer.apple.com/documentation/uikit/uiviewcontroller)와 [UIPresentationController](https://developer.apple.com/documentation/uikit/uipresentationcontroller)를 참고하세요.