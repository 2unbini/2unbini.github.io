---
title: "SwiftUI @State 초기화"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - swift
    - kunbon
tags:
    - swift
    - 스위프트
    - SwiftUI
    - 근본
last_modified_at: 2021-12-09
---

# @State

SwiftUI에서 어떤 변수가 변화하면 그에 따라 뷰의 내용도 변화하게끔 만들 때, `@State`를 사용한다.

`@State`로 선언한 변수를 정의할 때 바로 초기화하지 않으면 뷰를 사용할 때 해당 변수에 대한 내용을 뷰의 인자로 넣어 주어야 한다. 무슨 말이냐면,

```swift
struct ContentView: View {
    
    @State var text: String
    
    var body: some View {
            VStack {
                Text(text)
                TextField("field", text: $text)
            }
        }
    }
}
```

이런 상황에서, App에서 ContentView를 호출할 때 `ContentView()`라고만 하면 `Missing argument for parameter 'text' in call` 이라는 에러가 뜬다.

에러를 무시하기 위해서 `@State var text: String = ""`과 같은 방식으로 선언과 동시에 초기화해버리는 방법도 있지만, 이번엔 View의 `init`으로 초기화하는 방식을 배웠다.


### 필요성

나는 구조체로 묶인 데이터를 `init`을 통해 받고, 각각의 요소들을 `@State`에 초기화하고 싶었다.

그런데, 이 때 `@State var text: String = ""`와 같이 선언과 동시에 초기화해둔 뒤 init에서 값을 대입하면 그 값이 제대로 대입이 되지 않았다. 그래서 서치를 해 봤더니,

```swift
_text = State(initialValue: "default")
```

와 같이 사용해 주면 되었다.


### init(initialValue:)

공식 문서에 [State](https://developer.apple.com/documentation/swiftui/state)를 검색하면, 하단 Topic 파트에 State를 생성하는 생성자들이 나와 있다.

그 중, `Creates the state with an initial value`의 역할을 하는 친구를 활용하면 State를 초기화할 수 있다.

그런데 `init`을 활용해 변수를 초기화하는 것은 [좋은 방법은 아니라고](https://forums.swift.org/t/state-messing-with-initializer-flow/25276/3) 한다. 일반적으로 데이터는 뷰 외부에서 구성되고, 뷰에선 단순히 그 데이터를 보여주는 것이기 때문에 `@State private var viewModel = SignInViewModel(...)` 다음과 같이 뷰 모델을 통해 데이터 모델을 바로 초기화하는 방법을 추천한다.

오케이,, 그럼 변수 명 앞에 붙는 `_`얘는 뭘까..?


### _

`The compiler synthesizes storage for the instance of the wrapper type by prefixing the name of the wrapped property with an underscore (_)`

스위프트 컴파일러가 무언가 값을 담는 [프로퍼티 래퍼](https://docs.swift.org/swift-book/ReferenceManual/Attributes.html)를 확인하는 용도로 `_`를 사용한다고 한다. 즉, 내가 선언한 text가 프로퍼티 래퍼라는 것을 컴파일러에게 알려주기 위해 쓰는 것이다.


## 결론

@State로 선언한 프로퍼티 래퍼를 init으로 초기화할 수 있다.

그런데 그보단 ViewModel을 만들어서 바로 초기화해주는 것이 좋다.

그리고 스위프트 컴파일러는 상당히 다양한 방식으로 스위프트 언어를 해석한다. 이를테면, @, _와 같은 친구들...

*잘못된 부분이나 추가할 사항이 있다면 언제든 말해주세요!*
