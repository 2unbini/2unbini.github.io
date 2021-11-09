---
title: "SwiftUI some View"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - swift
tags:
    - swift
    - 스위프트
    - SwiftUI
    - 근본
last_modified_at: 2021-11-09
---

# 근본공부의 필요성

```swift
var body: some View { ... }
```

위는 SwiftUI로 뷰를 생성할 때 필수적으로 마주하는 코드다. 

프로젝트 파일을 하나 만들면 자동으로 생성돼있기 때문에 왜 변수 이름이 `body`고, 이는 단순한 View가 아닌 `some View` 타입인지 생각해보지 않았다.

그런데 같이 공부하던 친구가 '왜 some View를 써야 하는지' 등에 대한 질문에 대답할 수 있어야 진짜 공부를 한 것 아니냐는 말을 했다. 그래서 간단하게 정리하고자 구글링을 했고, 이 과정에서 단순히 각각의 현상이나 이유에 대해서 알 수 있었을 뿐만 아니라 Swift의 문법에 대해서도 공부할 수 있었다.

이번 포스팅을 시작으로 앞으로 SwiftUI 근본에 대한 공부를 하면서 Swift 근본에 대해서도 알아가고자 한다.

## var body

왜 `var body` 일까?

`View` 프로토콜을 따르는 객체는 필수적으로 `body` 라는 연산 프로퍼티를 가져야 하기 때문이다.

이를 확인하는 방법은 아주 간단하다. 프로젝트파일 아무거나 하나 연 다음, View를 오른쪽 클릭 해 'Jump to Definition'을 누르면 친절하게 위 결론이 영어로 쓰여 있다.

![view_definition](/assets/images/swiftUI/view_definition.png)

>  @ViewBuilder : 하나 이상의 자식 뷰를 생성할 때 사용하는 키워드. VStack의 정의에도 해당 키워드가 사용돼 있다. VStack에 두 개 이상(열 개 이하)의 뷰가 생성될 때, 해당 키워드를 통해 TupleView 타입을 반환할 수 있게 된다.



## some View

왜 `some View` 일까?

구현시 구체적인 View의 속성을 정하기 위해 리턴 타입을 정확하게 고정시키지 않는 것이다.

이를 알기 위해선 Swift의 [`opaque return type`](https://docs.swift.org/swift-book/LanguageGuide/OpaqueTypes.html) 에 대해 알아야 한다. 얘는 말 그대로 '불투명한 리턴 타입'이다.

불투명한 리턴 타입은 ''특정 프로토콜을 만족하는 어떠한 인스턴스가 생성될 것이다'' 라고 **불투명하게** 알려줄 뿐, 특정 프로토콜의 모든 속성을 다 구체적으로 명시해 구현해야 할 필요가 없다. 해당 프로퍼티가 정확히 어떤 프로토콜을 따르고, 그렇기 때문에 지정해줘야 하는 모든 요소들을 생각하지 않고, 단순히 내가 구현하고자 하는 View의 모습과 속성만 코드로 작성하면 되는 것이다.

비슷한 류로 `제네릭 타입` 이 있다. 제네릭 타입은 함수 호출자가 그 타입을 지정해주는 반면, 불투명한 리턴 타입은 함수 구현체에서 그 타입이 지정된다는 차이가 있다. 그래서 불투명한 리턴 타입을 `reverse of generic type` 이라고 하기도 한다.

다시 돌아가서, 프로토콜을 따르거나 제네릭 타입으로 만드는 대신 불투명한 리턴 타입을 사용하는 이유는 뭘까?

```swift
var body: some View {
  Text("This is 2unbini")
}
```

단순히 위와 같이 하나의 텍스트 뷰로 구성된 뷰라면 불투명한 리턴 타입을 사용할 필요가 없다. 그냥 body 자체의 타입을 Text 라고 지정해 주면 된다. 단일 뷰이기 때문이다.

그런데 내가 요소들을 쭉 추가해서 단일 텍스트 뷰가 아닌, 텍스트들과 이미지가 섞여 있는 뷰를 만들게 됐다면 어떻게 될까?

```swift
var body: some View {
  VStack {
    Text("This is 2unbini")
    Text("Nice to meet you all!")
    Image(systemName: "heart")
  }
}
```

이런 방식으로 엮여 있는 뷰는 VStack에 의해 `TupleView` 를 반환하게 된다. 이 상황에선 body의 타입을 컴파일하는 시점에서 지정할 수 있도록 하는 것이 코드를 작성하는 데 있어 더 편리하다. 구체적으로 지정하는 순간부터 코드의 변화가 있을 때마다 계속 수정을 해 줘야 하기 때문이다.

## 결론

SwiftUI를 사용해 View 프로토콜을 따르는 뷰 객체를 만들 때 `body` 라는 변수를 정의해야 하는 이유는, View가 그렇게 정의되어 있기 때문이다.

body가 View가 아닌 `some View` 타입인 것은, 해당 뷰의 내부적인 속성에 대해서 구체적으로 코드로 구현한 뒤, 뷰가 만들어지는 순간에 타입을 지정해 타입에 대한 엄격한 제한 없이 뷰를 만들기 위함이다.

*잘못된 부분이나 추가될 내용이 있다면 언제든 알려주세요!*

