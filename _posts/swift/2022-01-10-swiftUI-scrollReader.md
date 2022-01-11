---
title: "SwiftUI ScrollViewReader, scrollTo() 로 시작 위치 지정하기"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - swift
tags:
    - swift
    - 스위프트
    - SwiftUI
last_modified_at: 2022-01-10
---

# ScrollViewReader와 scrollTo 메서드를 사용해 특정 위치에서 앱 시작하기

## 필요성

시작은 언제나 그렇듯... 무언가를 만드는 데서 비롯됐다.

기본 앱 '캘린더'의 월별 뷰를 클론코딩하고자 했고, 기본적으로 2000년부터 2100년까지의 날짜를 불러올 수 있게 하고 앱을 키면 오늘 날짜가 속해 있는 달로 세팅될 수 있도록 만들고자 했다.

이를 위해선 1. 스크롤이 되어야 함, 2. 오늘 날짜로 스크롤 된 상태가 필요 했다.

## 1. 기본 스크롤 뷰 구성

나는 year, month, week, day로 차근차근 쪼개 Stack 뷰를 이용해 합쳐 전체 캘린더를 구성했다.

<details>
<summary> 코드블록 </summary>
<div markdown="1">

```swift
import SwiftUI

struct ContentView: View {
    
    var body: some View {
        MyCalendarView()
    }
}

let calendar = Calendar(identifier: .gregorian)

struct MyCalendarView: View {
    private var years: [Date] {
        // 2000~2100에 해당하는 기간 동안 뷰에 표시할 years 배열 (구현 생략)
        // 참고: 코드 하단 깃허브 주소
    }

    var body: some View {
        ScrollView {
            LazyVStack {
                ForEach(years, id: \.self) { year in
                    Yearly(of: year)
                }
            }
        }
    }
}

struct Yearly: View {
    let year: Date

    init(of year: Date) {
        self.year = year
    }

    private var months: [Date] {
        // year에 해당하는 기간 동안 뷰에 표시할 months 배열 (구현 생략)
        // 참고: 코드 하단 깃허브 주소
    }

    var body: some View {
        LazyVStack {
            ForEach(months, id: \.self) { month in
                let yearLabel: String = String(calendar.component(.year, from: year))
                let monthLabel: String = String(calendar.component(.month, from: month))
                VStack {
                    Text("\(yearLabel)년 \(monthLabel)월")
                        .font(.title3)
                    Divider()
                    Monthly(of: month)
                }
                .padding()
            }
        }
    }
}

struct Monthly: View {
    let month: Date

    init(of month: Date) {
        self.month = month
    }

    private var weeks: [Date] {
        // month에 해당하는 기간 동안 뷰에 표시할 weeks 배열 (구현 생략)
        // 참고: 코드 하단 깃허브 주소
    }

    var body: some View {
        LazyVStack {
            ForEach(weeks, id: \.self) { week in
                Weekly(of: week)
            }
        }
    }
}

struct Weekly: View {
    let week: Date

    init(of week: Date) {
        self.week = week
    }

    private var days: [Date] {
        // week에 해당하는 기간 동안 뷰에 표시할 days 배열 (구현 생략)
        // 참고: 코드 하단 깃허브 주소
    }

    var body: some View {
        LazyHStack() {
            ForEach(days, id: \.self) { day in
                if calendar.isDate(day, equalTo: week, toGranularity: .month) {
                    Daily(of: day)
                }
                else {
                    Daily(of: day).hidden()
                }
            }
        }
    }
}

struct Daily: View {
    let day: Date
    let dayLabel: String

    init(of day: Date) {
        let calendar = Calendar(identifier: .gregorian)
        self.day = day
        self.dayLabel = String(calendar.component(.day, from: day))
    }

    var body: some View {
        Text("Day")
            .hidden()
            .padding(5)
            .overlay(Text(dayLabel))
    }
}
```

</div>
</details>

[참고 gist](https://gist.github.com/mecid/f8859ea4bdbd02cf5d440d58e936faec)

기본 뷰를 구성하면 2000년 1월부터 2100년 12월까지 쭉 스크롤할 수 있는 뷰가 생성된다.

나는 앱을 켰을 때 오늘에 해당하는 달이 뜨게 하고자 했기 때문에, 스크롤 뷰의 특정 id를 가진 뷰로 이동할 수 있는 `scrollTo()`메서드를 사용하기로 했다.

## 2. 특정 위치로 이동하기

[Apple Developer에 ScrollView를 검색](https://developer.apple.com/documentation/swiftui/scrollview)하면 스크롤 뷰를 코드상에서 어떻게 사용할 수 있는지 나온다.

이와 동시에, 프로그래밍적으로 스크롤하기 위해선 [`ScrollViewReader`](https://developer.apple.com/documentation/swiftui/scrollviewreader)를 사용해 하나 이상의 스크롤 뷰를 감싸야 한다고 나와 있다.

### ScrollViewReader

스크롤할 수 있는 대리자(proxy)를 통해 '알고 있는' 자식 뷰로 이동할 때 사용하는 뷰 라고 한다.

쉽게 말해서, 내가 특정 위치에 있는 자식 뷰로 이동하기 위해선 대리자와 그 자식 뷰의 식별자를 알아야 한다는 것이다.

스크롤 뷰 리더의 콘텐트 뷰 빌더는 [ScrollViewProxy](https://developer.apple.com/documentation/swiftui/scrollviewproxy) 인스턴스를 인자로 받고, 이 대리자(proxy)의 `scrollTo(_:anchor:)` 메서드를 사용해 특정 위치로 스크롤하게 한다.

### scrollTo(_:anchor:)

첫 번째 인자로 스크롤되어 도착할 자식 뷰의 식별자를, 두 번째 인자로 정렬에 관한 값을 넣어 주면 된다.

이는 proxy에 의해 스크롤 뷰의 모든 요소들을 탐색해 첫 번째 인자로 들어온 id값과 동일한 맨 첫번째 자식 뷰로 스크롤되게 하는 메서드이다.

### 코드에 적용

1. ScrollView를 ScrollViewReader로 감싼다.
2. ScrollView가 나타날 때, 내가 원하는 위치로 scrollTo()한다.
3. 이를 위해선 각각의 monthly 뷰에 id가 있어야 한다.

#### 1. ScrollViewReader로 감싸기

```swift
struct MyCalendarView: View {
    ...

    var body: some View {
        ScrollViewReader { proxy in
            ScrollView {
                LazyVStack {
                    ForEach(years, id: \.self) { year in
                        Yearly(of: year)
                    }
                }
            }
        }
    }
}
```

ScrollView를 감싸주면 된다.

#### 2. scrollTo() 한다.

ScrollView가 시작될 때 특정 위치로 옮기고자 하기 때문에, `viewWillAppear()`에 해당하는 `.onAppear(perform:)` 함수에 해당 내용을 넣어 줬다.

UIKit에서 `viewDidLoad()`에 해당하는 메서드가 SwiftUI에도 있을까 해서 찾아봤는데 없었다. 뷰가 표시되는 시점에 해당 코드가 실행되기 때문에 조금 부자연스럽지만 지금 상황에선 최선의 선택이라고 생각했다.

```swift
struct MyCalendarView: View {
    ...

    var body: some View {
        ScrollViewReader { proxy in
            ScrollView {
                LazyVStack {
                    ForEach(years, id: \.self) { year in
                        Yearly(of: year)
                    }
                }
            }
            .onAppear {
                proxy.scrollTo(id, .top)
            }
        }
    }
}
```

#### 3. id 넣어주기

scrollTo의 id 자리에 내가 원하는 id를 넣어주면 끝이다.

나는 오늘에 해당하는 Montly 뷰를 보여줄 것이므로, MontlyView에 id를 하나씩 부여해주고, 도착하고자 하는 뷰의 id를 scrollTo의 인자로 넣어 주면 된다.

MontlyView에 id를 부여하는 것은 `id(_)` 메서드를 사용하면 된다.

처음에 이 id를 뭐로 넣어야 할 지 고민이 많이 됐다. 특정 년도의 특정 달인데다가, 년도와 달이 서로 다른 뷰로 분리되어 있어 연속된 Int나 String 값을 넣기가 어려웠다.

그래서 에라 모르겠다 하고 Montly를 구성하는 `month` Date를 id로 넣어 줬는데, 식별자로서 인식이 되었다.

id의 인자로 들어가는 id는 Hashable 프로토콜을 따른다. 즉, Hashable 프로토콜을 따르는 어떠한 값이든 식별자로 작용할 수 있다는 것이고, 이에 따라 `Date`를 검색 해 봤더니 `Equatable` 프로토콜을 따르고 있었다. Hashable은 Equatable을 상속하므로, Date 역시 id가 될 수 있다는 것..1!!

```swift
struct Yearly: View {
    ...

    var body: some View {
        LazyVStack {
            ForEach(months, id: \.self) { month in
                ...
                VStack {
                    ...
                    Monthly(of: month)
                        .id(month)
                }
                .padding()
            }
        }
    }
}
```

그래서 Montly 각 뷰에 month를 id로 부여했다. 여기서 또 빼먹으면 안 되는 점은, `ForEach의 id`이다. ForEach의 식별자로서 역할할 뿐만 아니라, 내가 원하는 id로서의 month를 제대로 부여하기 위해 꼭 필요하다.

## 3. 끝인줄 알았지?

scrollTo 메서드의 첫 번째 인자로 오늘의 날짜를 `Date()`로 만들어 넣어 주면 될 것 같지만 안 된다.

id로 넣은 month와 Date()가 타입은 같지만 시간이 다르기 때문이다.

### 찾고자 하는 id를 Date()가 아닌 id로 부여한 Date 형식으로 맞추자

print()로 id와 Date()를 각각 찍어 보면, 같은 날을 기준으로 했음에도 불구하고 완전히 다르게 나온다.

![](/assets/images/swiftUI/montly-id.png)

months 배열을 어떻게 구성하느냐에 따라서 month의 값이 달라지겠지만, DateInterval과 DateComponent를 이용해 2022년 한 해 동안의 달 배열을 구하면, 2021년 12월 31일을 시작으로 2022년 11월 31일까지의 배열이 도출된다. 이 과정에서 내가 사용한 calendar의 포맷이나, 시스템 세팅이나 기타 등등이 한국 시간대가 아닌 다른 시간대로 지정되어 있어서 그럴 거라고 생각된다.

아무튼, 그래서! months 배열을 구성한 것과 동일하게 오늘에 해당하는 달 날짜를 만들어 주어야 찾을 수 있다. 시스템 세팅에 맡기면 될까? 하여 오늘의 year와 month를 이용해 날짜를 하나 만들었다.

```swift
let today = Date()
let year = calendar.component(.year, from: today)
let month = calendar.component(.month, from: today)
let initialScrollDateId: Date = calendar.date(from: DateComponents(year: year, month: month)) ?? Date()
```

이를 출력 해 보니 id로 만들어진 값과 동일한 Date 값이 나왔고, 이를 id의 위치에 넣어 완성해줬다.

```swift
.onAppear {
    proxy.scrollTo(initialScrollDateId, .top)
}
```

## 결론

SwiftUI에서 특정 위치로 스크롤하는 것은 `ScrollViewReader`와 `scrollTo`를 사용하면 된다.

이 과정에서 뷰에 id를 부여해 줘야 하는데, `id()`메서드를 사용하고 이 안에 들어갈 식별자는 Hashable 프로토콜을 따르는 타입/구조체 등 뭐든 된다.

*더 좋은 방법이나 수정되어야 할 부분이 있다면 알려주세요!*