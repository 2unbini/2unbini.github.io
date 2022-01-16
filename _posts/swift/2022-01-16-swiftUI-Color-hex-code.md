---
title: "SwiftUI Color, Hex Code로 초기화하기"
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

# 킹갓 디자이너의 손길을 받은 색을 그대로 써보자

## 기본 init

```swift
init(uiColor: UIColor)
init(Color.RGBColorSpace, white: Double, opacity: Double)
init(Color.RGBColorSpace, red: Double, green: Double, blue: Double, opacity: Double)
```

등 많은 초기화 방식이 있지만, 그 어디에도 hex는 보이지 않는다.

유일하게 r, g, b 값을 모두 Double 값으로 넣어주는 초기화가 있는데, 깔끔하게 hex code로 받은 친구를 또 연산하는건 너무 귀찮은 일이다.

## extension

그래서 SwiftUI에 정의되어있는 `Color` 객체에 extension으로 새로운 init을 만들어주었다.

```swift
extension Color {
	init(hex: Int, opacity: Double = 1.0) {
		let red = Double((hex >> 16) & 0xff) / 255
		let green = Double((hex >> 8) & 0xff) / 255
		let blue = Double((hex >> 0) & 0xff) / 255

		self.init(.sRGB, red: red, green: green, blue: blue, opacity: opacity)
	}
}
```

이렇게 익스텐션으로 새로운 초기화 방식을 만들어주고, 다음과 같이 써 주면 된다.

```swift
Color(hex: 0xFFCE30)
```

만약, 0x를 붙이지 않고 그냥 FFCE30 자체를 문자열로 받아서 처리하고 싶다면, 위의 동작에서 String → Int 로 바꾸는 작업만 해 주면 된다.

```swift
init(hexString: String, opacity: Double = 1.0) {
	let hex: Int = Int(hexString, radix: 16)!
	...
}

// Usage
Color(hexColor: "FFCE30")
```

+) Int(_:radix:) 는 알고리즘 문제 풀 때 가끔 진수변환에 유용하다...,,

## 추가) 컬러 팔레트를 상수로 정의

받아온 컬러 값을 상황마다 넣어 줄 수도 있지만, 상수로 정의해놓으면 그 값이 무엇인지 직관적으로 알아볼 수 있다.

예를 들어, 우리가 사용할 수 있는 컬러 팔레트가 Default, Yellow, Purple 세 종류고, 각각에 세부적인 색상으로 나뉘어져 있다면 다음과 같이 나누어 정의할 수 있다.

```swift
struct ColorPalette {
    enum Default {
        static var levelOne: Int = 0xFFCE30
        static var levelTwo: Int = ...
        static var levelThree: Int = ...
    }
    
    enum Yellow {
        // same as Default
    }
    
    enum Purple {
        // same as Default
    }
}
```

이렇게 상수로 정의하되, 분류별로 묶어주면 다음과 같이 사용할 수 있다.

```swift
Color(hex: ColorPalette.Yellow.levelOne)
```

`Yellow` 의 `levelOne` 색상을 사용한다는 것을 바로 알 수 있다.