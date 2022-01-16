---
title: "SwiftUI Progress Bar 만들기(feat. Indicator)"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - swift
tags:
    - swift
    - 스위프트
    - SwiftUI
last_modified_at: 2022-01-16
---

# Progress Bar를 만들어보자

## 뷰 나누기

![스크린샷 2022-01-16 오후 6.10.29.png](/assets/images/swiftUI/progress-bar-final.png)

만들어야 할 프로그레스 바의 모습은 위와 같다.

의사 코드로 위 뷰를 쪼개보면 다음과 같이 쪼갤 수 있다.

```swift
VStack {
    emojiIndicator
    ZStack {
        wholeProgressBar
        doneProgressBar
    }
}
```

이를 바탕으로 차근차근 뷰를 만들면 된다!

## Progress Bar 만들기

캡슐 형태의 바가 겹쳐진 모양새다. 그래서 ZStack으로 기본 프로그레스 바 위에 진행상태를 표시하는 바를 올려주었다.

```swift
private var progressBar: some View {
    ZStack {
        Capsule()
            .frame(width: progressBarWidth, height: progressBarHeight)
            .foregroundColor(Color.gray)
        Capsule()
            .frame(width: progressBarWidth * 0.2, height: progressBarHeight)
            .foregroundColor(Color(hex: 0xFFCE30))
    }
}
```

![스크린샷 2022-01-16 오후 6.10.29.png](/assets/images/swiftUI/progress-bar-center.png)

그냥 ZStack에 넣으면 이렇게 가운데 정렬이 된다. 우리는 맨 앞에서부터 차오르는 모습을 원하기 때문에, `alignment: .leading` 을 추가해준다.

또, 지금 상태에선 프로그래스 바의 전체 길이에 해당하는 `progressBarWidth` 의 20%에 해당하는 만큼 차 있지만, 이를 내가 원하는 퍼센티지로 바꾸어주면 그 값에 따라 프로그래스 바가 차오르는 것 처럼 만들 수 있다.

```swift
private var progressBar: some View {
    ZStack(alignment: .leading) {
        Capsule()
            .frame(width: progressBarWidth, height: progressBarHeight)
            .foregroundColor(Color.gray)
        Capsule()
            .frame(width: progressBarWidth * percent, height: progressBarHeight)
            .foregroundColor(Color(hex: 0xFFCE30))
    }
}
```

이렇게 해 주면 된다.

> 여기서 progressBarWidth, progressBarHeight는 상위 뷰에서 GeometryReader로 가져온 뷰의 크기에서 원하는 비율로 계산해줄 수 있다.

## Indicator 만들기

앞서 의사 코드로 만들었던대로, 프로그래스 바 위에 이모지 인디케이터를 위치시켜봤다.

이모지는 Text뷰로 생성했다.

```swift
VStack(alignment: .leading) {
    Text("🔥")
    progressBar
}
```

![스크린샷 2022-01-16 오후 6.10.29.png](/assets/images/swiftUI/progress-bar-before.png)

그럼 이제 저 인디케이터의 위치를 프로그래스 바의 퍼센티지에 맞춰 바꿔주면 된다.

해당 뷰의 위치를 변화시키는 `position` 이나 `offset` 을 생각 해 봤는데 값이 조금만 잘못돼도 뷰가 이상하게 변질되기 때문에 다른 방법을 고안했다.

`padding` 의 값이 width, height와 같은 기준이기 때문에, progressBar가 차오르는 만큼 Text에 패딩을 주면 되겠다고 생각했다.

```swift
VStack(alignmnet: .leading) {
    Text("🔥")
        .padding(.leading, progressBarWidth * percent)
    progressBar
}
```

![스크린샷 2022-01-16 오후 6.10.29.png](/assets/images/swiftUI/progress-bar-unpretty.png)

묘하게 안 예쁘다. Text의 위치가 항상 차 있는 프로그래스 바보다 뒤에 있다.

이를 해결하기 위해 다음과 같이 만들어줬다.

```swift
VStack(alignmnet: .center) {
    HStack(spacing: 0) {
        Text("🔥")
            .padding(.leading, defaultPadding + progressBarWidth * percent)
        Spacer()
    }
    progressBar
}
```

Text의 padding 값으로 들어간 `defaultPadding` 은 인디케이터와 프로그래스 바의 상위 뷰인 `VStack` 의 앞쪽 값(빨간색 화살표로 가리키는 곳)이다.

![스크린샷 2022-01-16 오후 6.10.29.png](/assets/images/swiftUI/progress-bar-after.png)

`defaultPadding` 은 `VStack 의 width`  에서 `progressBar 의 width` 를 뺀 길이의 `절반` 에 해당한다.

## 전체 코드

```swift
struct ProgressField: View {
    // percentage of progress
    // can change view by button action or something else
    @State private var percent: CGFloat = 0.2
    
    let size: CGSize
    let fullWidth: CGFloat
    let progressBarWidth: CGFloat
    
    init(size: CGSize) {
        self.size = size
        self.fullWidth = size.width
        self.progressBarWidth = size.width * 0.75
    }
    
    private var progressBar: some View {
        // progress bar's height
        let progressBarHeight: CGFloat = 10.0
        
        return ZStack(alignment: .leading) {
            Capsule()
                .frame(width: progressBarWidth, height: progressBarHeight, alignment: .leading)
                .foregroundColor(Color.gray))
            Capsule()
                .frame(width: progressBarWidth * percent , height: progressBarHeight, alignment: .leading)
                .foregroundColor(Color(hex: 0xFFCE30))
        }
    }
    
    private var indicator: some View {
        // default padding of Text
        let defaultPadding: CGFloat = fullWidth * 0.09
        
        return HStack(spacing: 0) {
            Text(Emoji.Pink.progressIndicatior)
                .font(.system(size: 15))
                .padding(.bottom, 5)
                .padding(.leading, defaultPadding + progressBarWidth * percent)
            Spacer()
        }
    }
    
    var body: some View {
        VStack(alignment: .center) {
            indicator
            progressBar
        }
        .frame(width: fullWidth)
    }
}
```