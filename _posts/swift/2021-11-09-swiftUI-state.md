---
title: "SwiftUI @State"
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
last_modified_at: 2021-11-09
---

# @State

SwiftUI로 뷰를 만들고, 변수의 값이 바뀌면 뷰의 내용도 그에 따라 업데이트하고 싶을 때 처음 마주하는 키워드가 `@State` 일 것이다. 나 역시 무작정 투두 리스트 만들기를 할 때 텍스트 필드 뷰에 연결된 변수가 바뀔 때마다 뷰가 업데이트되길 바라며 해당 키워드를 사용했다.

@State의 용례만 봐도 이 친구가 뷰를 리로드해주는 특별한 변수라는 것을 알 수 있다. [공식 문서](https://developer.apple.com/documentation/swiftui/state) 에 쓰여진 State의 개요를 보면, '`State` 값이 변경되면, 뷰는 바디를 다시 연산하여 그 모습을 바꾼다'고 되어 있다. 즉, @State로 선언한 변수는 값이 변경될 때마다 해당 뷰의 모습을 새로고침하는 것이다.



### Property Wrapper

프로퍼티 래퍼는 프로퍼티의 get/set 코드가 여러 프로퍼티에서 중복되어 사용될 때, 이를 특정한 키워드로 정의해 가져다 쓸 수 있게 하는 것이다. 정의하고자 하는 클래스, 구조체, enum 등의 앞에 `@propertyWrapper` 를 써 주면, 정의된 클래스, 구조체, enum 등을 프로퍼티 래퍼로 사용할 수 있다.

공식 문서에 정의된 State는 'A Property Wrapper Type' 이라고 한다.

```swift
@frozen @propertyWrapper struct State<Value>
```

실제로 정의된 부분을 보면 `@propertyWrapper` 키워드가 사용된 것을 알 수 있다.

내부적으로 어떻게 구현돼있는지 확인하지 못했지만, 뷰의 상태를 변경 해 주는 로직이 `State` 구조체에 담겨 있지 않을까 하는 생각이 든다.



### Private

공식 문서에 이런 구절이 적혀 있다. 'You should only access a state property from inside the view’s body, or from methods called by it.'

State 프로퍼티를 뷰의 바디 내부나 이를 통해 호출되는 메서드에서만 접근할 수 있어야 한다는 말인데, State가 영향을 미치는 범위를 생각 해 보면 이 말을 이해할 수 있다. 뷰 내부에서 사용되어 해당 뷰의 모습을 업데이트하기 때문에, 이를 뷰 바깥에서 사용할 순 없는 노릇이다.

그래서 `@State` 를 사용해 변수를 선언할 땐, `private` 키워드를 함께 쓰도록 권장하고 있다.



### projectedValue

내가 `@State` 키워드를 처음 사용하게 된 것은 앞에서도 말했듯 TextField를 통해 값을 입력받고 이 내용을 뷰에 업데이트할 때다. 이 때 나는 State 뿐만 아니라 `$`가 붙는 이상한 색다른 친구를 만났다. 속성을 보니 `Binding`이라고 적혀 있었고, 이를 검색 해 보니 뷰 끼리 연결해 변수를 주고받을 때 사용한다는 것을 알게 되었다.

텍스트 필드에 값을 입력하고, 이를 통해 뷰를 업데이트하고자 할 때, 나는 `@State`와 `$`를 모두 사용했다. 다른 뷰에서 변수에 접근할 수 있도록 `$` 키워드로 변수를 바인딩하고, 해당 변수가 변경됨에 따라 뷰가 업데이트될 수 있도록 그 변수를 `@State` 로 정의했다. 이렇게 State 값을 바인딩하는 것을 [`projectedValue`](https://developer.apple.com/documentation/swiftui/state/projectedvalue) 라고 한다.



## 결론

변수를 하나 두어 이를 변경함에 따라 뷰를 리로드하려면 `@State` 라는 프로퍼티 래퍼를 사용하면 된다.

이는 뷰 내부적으로 혹은 뷰 안의 메서드에 의해서만 변경되기 때문에, `private` 키워드로 그 사용 범위를 제한하며, String이나 Bool, Int와 같이 단일 뷰에서 사용할 수 있는 기본적인 타입의 변수로 주로 사용된다.

복수 뷰에서 State 변수를 사용하고자 한다면, `$` 로 바인딩해서 사용하자.



*잘못된 부분이나 추가할 사항이 있다면 언제든 말해주세요!*
