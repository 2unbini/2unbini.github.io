---
title: "프로토콜, 공식문서 번역"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - swift
tags:
    - swift
    - 스위프트
	- 번역
last_modified_at: 2021-10-19
---

# 프로토콜

프로토콜은 특정한 일이나 어떤 기능을 하는 메소드, 프로퍼티, 그리고 여타 요구사항을 정의하는 청사진이라고 할 수 있다. 프로토콜은 클래스, 구조체, 열거형을 구현할 때 위와 같은 요구사항들을 제공하기 위해 *채택*될 수 있다. 어떤 타입이든 프로토콜의 요구사항을 만족하는 것은 그 프로토콜을 *따른다*고 할 수 있다.

추가로, 무조건 구현해야 하는 특별한 요구사항이 있을 때, 프로토콜을 확장해서 구현하거나 추가적인 기능을 구현할 수 있다.

## 프로토콜 문법

클래스나 구조체, 열거형과 아주 유사하게 프로토콜도 정의할 수 있다.

```swift
protocol SomeProtocol {
    // 프로토콜에 대한 구현
}
```

사용자 정의 타입에선 프로토콜을 채택한다는 뜻으로 이름 뒤에 특정한 프로토콜의 이름을 콜론(:)으로 구분해서 명시한다. 두 개 이상의 프로토콜은 쉼표로 구분해서 나열할 수 있다.

```swift
struct SomeStructure: FirstProtocol, AnotherProtocol {
    // 구조체에 대한 구현
}
```

슈퍼 클래스(부모 클래스)가 있는 경우, 슈퍼 클래스를 맨 처음 써 주고 그 뒤에 채택하는 프로토콜을 써 주면 된다.

```swift
class SomeClasss: SomeSuperClass, FirstProtocol, AnotherProtocol {
    // 클래스에 대한 구현
}
```

## 프로퍼티 요구사항

프로토콜은 프로퍼티가 저장 프로퍼티인지 연산 프로퍼티인지에 대해 구체화하지 않는다. 이는 오직 프로퍼티의 이름과 타입만 정해놓는다. 여기에 더해, 프로토콜은 프로퍼티가 gettable(읽기)인지 gettable/settable(읽기/쓰기)인지 명시한다.

프로퍼티가 gettable/settable(읽기/쓰기 가능)이라면 해당 프로퍼티는 상수 저장 프로퍼티나 읽기만 가능한 연산 프로퍼티가 될 수 없다. 하지만 프로퍼티가 gettable(읽기 가능)이라면 해당 프로퍼티는 어떠한 타입이든지간에 상관이 없으며, 코드 내에서 settable(쓰기 가능) 프로퍼티가 필요하다면 이 특성만 취해서 사용할 수도 있다.

각 gettable, settable의 속성은 `get`, `set` 키워드로 나타낼 수 있으며, 그 예시는 다음과 같다.

```swift
protocol SomeProtocol {
  var mustBeSettable: Int { get set }
  var noNeedToBeSettable: Int { get }
}
```

타입 프로퍼티를 정의할 땐 `static` 을 사용 해 줘야 한다.

```swift
protocol SomeProtocol {
  static var typeProperty: Int { get set }
}
```

> get, set : swift에서 프로퍼티에 대한 읽기/쓰기전용 속성을 줄 때 사용하는 메서드.

### 예시

이름과 나이를 읽을 수 있는 `PersonInfo` 프로토콜이 있다고 해 보자.

```swift
protocol PersonInfo {
  var name: String { get }
  var age: Int { get }
}
```





원문 : [The Swift Programming Language (Swift 5.5) - Protocols](https://docs.swift.org/swift-book/LanguageGuide/Protocols.html)