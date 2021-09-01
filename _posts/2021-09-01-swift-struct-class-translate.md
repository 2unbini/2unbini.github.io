---
title: "구조체와 클래스, 공식문서 번역"
categories:
    - swift
tags:
    - swift
    - 스위프트
    - 번역
last_modified_at: 2021-09-01
---

# 구조체와 클래스

구조체와 클래스는 일반적인 목적의, 유연한 구조를 가진 코드를 구성하는 기본 요소다. 구조체와 클래스에 기능을 추가하기 위해 상수, 변수, 함수 등을 선언하는 것과 같은 이유로 프로퍼티와 메소드를 정의한다.

다른 프로그래밍 언어와 달리, 스위프트는 사용자 정의 구조체와 클래스를 만들기 위해 인터페이스와 구현을 따로 나눌 필요가 없다. 스위프트에선 구조체와 클래스를 하나의 파일에 정의하고나면 그에 대한 외부 인터페이스는 자동적으로 만들어져 다른 코드에서 사용할 수 있게 된다.


> NOTE : 클래스의 인스턴스는 일반적으로 *객체*로 알려져 있다. 하지만, 스위프트의 구조체와 클래스는 다른 언어에 비해 더 기능에 가깝고, 이 챕터에서 클래스나 구조체 타입의 인스턴스들에 적용되는 기능에 초점을 맞춰 설명한다. 이런 이유로 *인스턴스* 라는 용어가 더 일반적으로 사용됐다.

<br/>

## 구조체와 클래스 비교하기

스위프트의 구조체와 클래스는 많은 공통점이 있다.

- 값을 저장하기 위해 프로퍼티를 정의한다.
- 기능을 제공하기 위해 메소드를 정의한다.
- 서브스크립트 문법을 사용해 값에 접근하기 위해 서브스크립트를 정의한다.
- 초기 상태를 설정하기 위해 이니셜라이저를 정의한다.
- 기본적으로 구현된 것에 기능을 추가할 수 있다.
- 특정 종류에 기본적인 기능을 부여하기 위해 프로토콜을 따른다.

더 많은 정보는 [프로퍼티](https://docs.swift.org/swift-book/LanguageGuide/Properties.html), [메소드](https://docs.swift.org/swift-book/LanguageGuide/Methods.html), [서브스크립트](https://docs.swift.org/swift-book/LanguageGuide/Subscripts.html), [초기화(이니셜라이제이션)](https://docs.swift.org/swift-book/LanguageGuide/Initialization.html), [익스텐션](https://docs.swift.org/swift-book/LanguageGuide/Extensions.html), [프로토콜](https://docs.swift.org/swift-book/LanguageGuide/Protocols.html)을 확인하자.


클래스는 구조체가 갖지 않은 더 많은 기능을 가지고 있다.

- 상속을 통해 다른 클래스의 특성을 상속받을 수 있다.
- 타입 캐스팅을 통해 런타임에 클래스 인스턴스의 타입을 확인할 수 있다.
- 정리자(디이니셜라이저)를 사용해 클래스의 인스턴스에 대한 모든 정보(메모리)를 정리할 수 있다.
- 참조계수를 통해 클래스 인스턴스에 한 개 이상의 참조를 할 수 있다.


더 많은 정보는 [상속](https://docs.swift.org/swift-book/LanguageGuide/Inheritance.html), [타입 캐스팅](https://docs.swift.org/swift-book/LanguageGuide/TypeCasting.html), [정리자(디이니셜라이제이션)](https://docs.swift.org/swift-book/LanguageGuide/Deinitialization.html), [자동 참조 계수(Automatic Reference Counting)](https://docs.swift.org/swift-book/LanguageGuide/AutomaticReferenceCounting.html)을 확인하자. 


클래스가 가진 추가적인 기능은 복잡도를 증가시켜 더 많은 비용이 들게 한다. 일반적인 가이드라인 상에서 구조체를 선호하는데, 이는 구조체가 더 식별하기 쉽기 때문이며, 클래스는 정말 필요하고 클래스를 사용하기 적절한 때에 사용한다. 이는 현실에서 사용자 정의 데이터 타입을 정의할 때 구조체와 열거형을 많이 쓴다는 의미가 된다. 더 자세한 비교는 ['구조체와 클래스 중 뭘 선택하는가'](https://developer.apple.com/documentation/swift/choosing_between_structures_and_classes)를 확인하자.


> NOTE: 클래스와 액터는 행동과 특성에 있어 많은 유사성이 있다. 액터에 대한 정보는 [Concurrency](https://docs.swift.org/swift-book/LanguageGuide/Concurrency.html) 에서 볼 수 있다.

<br/>

## 정의 구문(문법)

구조체와 클래스는 유사한 정의 구문을 가진다. 구조체는 `struct` 키워드로, 클래스는 `class` 키워드로 시작한다. `{}` 안에 각 구조체와 클래스의 정의를 적어 넣으면 된다.

```swift
struct SomeStructure {
	// 구조체 정의
}

class SomeClass {
	// 클래스 정의
}
```

> NOTE : 구조체와 클래스와 같은 새로운 타입을 정의할 땐 `대문자 카멜 케이스` 로 이름을 정한다. 스위프트의 기본 타입들(String, Bool, Int ...)과 같이 말이다. 프로퍼티와 메소드의 이름은 `소문자 카멜 케이스` 로 지으면 된다.

<br />

구조체와 클래스를 정의하는 예시는 다음과 같다.

```swift
struct Resolution {
		var width = 0
		var height = 0
}

class VideoMode {
		var resolution = Resolution()
		var interlaced = false
		var frameRate = 0.0
		var name: String?
}
```

위 예시에서 화면의 해상도를 설명하는 `Resolution` 이라는 구조체를 정의했다. 이 구조체는 너비와 높이의 두 저장 프로퍼티를 갖는다. 저장 프로퍼티란 구조체나 클래스의 부분으로 값을 저장하는 상수나 변수를 일컫는다. 이 두 프로퍼티는 초기값 `0` 을 갖는 `Int` 형으로 정의되어있다.


또한 비디오 모드와 디스플레이에 대한 정보를 담는 `VideoMode` 라는 클래스도 정의되어 있다. 이 클래스는 네 개의 저장 프로퍼티를 갖는다. 첫 번째 `resolution` 은 `Resolution` 타입의 인스턴스다. 다른 세 가지의 프로퍼티는 각자 Bool, Double, Optional String 타입으로 정의돼있다. 여기서 `name` 프로퍼티는 옵셔널 타입이기 때문에 자동적으로 `nil` 또는 `no name value` 로 정의된다.

<br/>

## 구조체와 클래스 인스턴스

`Resolution` 구조체와 `VideoMode` 클래스는 그 자체로 특정 해상도나 비디오 모드를 설명할 수 없다. 이를 위해서 구조체 또는 클래스의 인스턴스를 만들어야 한다.

인스턴스를 생성하는 구문은 클래스와 구조체가 유사하다.

```swift
let someResolution = Resolution()
let someVideoMode = VideoMode()
```

구조체와 클래스 모두 새 인스턴스 생성에 초기화 구문이 사용된다. 위에서 `Resolution()` `VideoMode()` 와 같이 써준 것이 가장 간단한 초기화 구문이다. 이를 통해 구조체와 클래스에 정의된 내용과 그것들의 초기값이 담긴 새 인스턴스를 생성할 수 있다. 클래스와 구조체 초기화는 [Initialization](https://docs.swift.org/swift-book/LanguageGuide/Initialization.html)에 더 자세히 쓰여 있다.

<br/>

## 프로퍼티에 접근하기

점을 사용해 인스턴스의 프로퍼티에 접근할 수 있다. 공백 없이 마침표(.)로 인스턴스 이름과 프로퍼티 이름을 분리한다.

```swift
print("someResolution의 너비는 \(someResolution.width) 이다.")
// someResolution의 너비는 0이다.
```

여기서 someResolution의 width 프로퍼티의 초기값이 0으로 설정돼있기 때문에 위와 같은 출력값이 나올 것이다.

서브프로퍼티로 들어갈수도 있는데, VideoMode의 resolution 프로퍼티의 width 프로퍼티에 접근하는 것도 가능하다는 뜻이다.

```swift
print("someVideoMode의 너비는 \(someVideoMode.resolution.width) 이다.")
// someVideoMode의 너비는 0이다."
```

프로퍼티 변수에 새로운 값을 할당할 때도 점을 사용한다.

```swift
someVideoMode.resolution.width = 1280
print("someVideoMode의 너비는 \(someVideoMode.resolution.width) 이다.")
// someVideoMode의 너비는 1280이다."
```

<br/>

## 구조체 타입의 Memberwise Initializer

모든 구조체는 새로운 구조체 인스턴스를 생성할 때 memberwise initializer 를 사용해 자동으로 프로퍼티들을 초기화할 수 있다. 프로퍼티의 초기화 값은 이름으로 초기화할 수 있다.

```swift
let vga = Resolution(width: 640, height: 480)
```

구조체와 달리 클래스는 기본적으로 memberwise initializer가 없다. 초기화에 대한 내용은 [Initialization](https://docs.swift.org/swift-book/LanguageGuide/Initialization.html) 을 확인하자.

<br/>

# 구조체와 열거형은 값 타입이다

값 타입이라는 것은 변수나 상수에 할당될 때, 혹은 함수에 전달될 때 그 값이 복사된다는 것을 의미한다.

스위프트의 모든 기본 타입들 - 정수형, 소수형, 불리언, 문자열, 배열, 딕셔너리들 -은 모두 값 타입으로, 구조체 뒤에 그 내용을 숨기고 있을 뿐이다.

모든 구조체와 열거형은 스위프트에서 값 타입이다. 이는 모든 생성된 구조체와 열거형 인스턴스들, 그리고 그것이 가진 프로퍼티로 가진 어떠한 값 타입들은 코드 상에서 항상 복사된다는 것을 뜻한다.

> NOTE : 배열, 딕셔너리, 문자열과 같이 기본 라이브러리에 정의된 컬렉션들은 복사하는 데 사용되는 비용을 줄이기 위해 최적화를 한다. 복사본을 바로 만드는 대신, 오리지널 인스턴스와 어떠한 복사본이든 저장되어 있는 메모리 공간을 공유한다. 컬렉션형의 어떠한 복사본이 수정되면, 수정 직전에 그 값들이 복사된다. 이 동작은 바로 복사가 되는 것처럼 보이게 한다.

```swift
let hd = Resolution(width: 1920, height: 1080)
var cinema = hd
```

위 예시는 hd라는 Resolution형 변수를 각각 width = 1920, height = 1080으로 초기화한 뒤, cinema 라는 변수 초기화에 사용한 것이다. Resolution이 구조체이기 때문에 존재하는 인스턴스의 복사본이 cinema에 할당된다. 지금은 hd 와 cinema 가 모두 같은 값을 갖지만, 이 두 인스턴스는 완벽히 다른 것들이다.


다음으로 디지털 영화 상영을 위해 사용되는 2K 해상도로 cinema의 너비 프로퍼티를 바꿔보자.


```swift
cinema.width = 2048
```

cinema 의 width 프로퍼티를 확인해보면 다음과 같이 수정된 것을 볼 수 있다.


```swift
print("cinema의 너비는 \(cinema.width) 픽셀이 됐다.")
// cinema의 너비는 2048 픽셀이 됐다.
```

하지만 hd의 width 프로퍼티는 여전히 이전의 값을 가지고 있다.


```swift
print("hd의 너비는 \(hd.width) 픽셀이다.")
// hd의 너비는 1920 픽셀이다.
```

cinema 가 hd의 값을 부여받을 땐 hd의 값이 cinema 인스턴스에 복사된다. 이 결과 두 분리된 인스턴스는 동일한 값을 지니게 된다. 하지만, 이 두 인스턴스는 분리돼있기 때문에 cinema의 너비를 2048로 바꾸는 것이 hd 의 값에는 아무런 영향을 미치지 않는다.


![value.png](/assets/images/swift-1/sharedStateStruct_2x.png)


열거형에도 동일하게 적용된다.


```swift
enum CompassPoint {
		case north, south, east, west
		mutating func turnNorth() {
				self = .north
		}
}

var currentDirection = CompassPoint.west
let rememberedDirection = currentDirection

currentDirection.turnNorth()

print("현재 방향은 \(currentDirection)이다.")
print("저장된 방향은 \(rememberedDirection)이다.")
// 현재 방향은 north 이다.
// 저장된 방향은 west 이다.
```

rememberedDirection이 currentDirection 의 값을 부여받을 땐 값이 복사된다. currentDirection 의 값을 변경한다고 해서 rememberedDirection의 값도 변경되는 것은 아니다.

<br />

# 클래스는 참조 타입이다

값 타입과 달리 참조 타입은 복사되지 않는다. 복사본 대신, 같은 인스턴스가 참조된다.

```swift
let tenEighty = VideoMode()

tenEighty.resolution = hd
tenEighty.interlaced = true
tenEighty.name = "1080i"
tenEighty.frameRate = 25.0
```

위 예시는 tenEighty라는 이름의 VideoMode의 인스턴스를 선언한 것이다.

tenEighty 를 alsoTenEighty 라는 이름의 새 상수에 할당하고, alsoTenEighty의 프레임 레이트를 바꿔보자.

```swift
let alsoTenEighty = tenEighty
alsoTenEighty.frameRate = 30.0
```

클래스는 참조 타입이므로 tenEighty와 alsoTenEighty는 같은 VideoMode 의 인스턴스다. 이 둘은 하나의 같은 인스턴스의 두 가지 다른 이름인 것과 같다.

![reference.png](../assets/images/swift-1/sharedStateClass_2x.png)


tenEighty의 프레임 레이트를 확인해보면 새로운 프레임 레이트가 30.0으로 업데이트된 것을 확인할 수 있다.

```swift
print("tenEighty의 프레임 레이트는 \(tenEighty.frameRate)이다.")
// tenEighty의 프레임 레이트는 30.0이다.
```

이는 참조 타입이 식별하기 더 어렵다는 것을 보여 주는 예시가 된다. tenEighty와 alsoTenEighty가 코드 상에서 멀리 떨어져 있다면, 비디오 모드가 바뀐 부분을 찾기 어려울 것이다. tenEighty를 쓸 때마다 alsoTenEighty 까지 신경 써 줘야 한다는 것이다. 대조적으로, 값 타입은 같은 값들을 주고 받는 코드들이 소스파일 상에서 가까이에 있기 때문에 식별하기 더 쉽다.

tenEighty와 alsoTenEighty가 변수가 아닌 상수로 선언되어 있는 것에 주목하자. 그럼에도 불구하고 tenEighty.frameRate와 alsoTenEighty.frameRate 를 수정할 수 있는 것은 그 각각은 자기자신을 바꾸는 것이 아니기 때문이다. tenEighty와 alsoTenEighty는 VideoMode 인스턴스를 "저장"하지 않는다. 대신, 둘은 모두 어딘가에 있을 VideoMode 인스턴스를 참조하고 있을 뿐이다. 이는 해당 VideoMode 인스턴스의 frameRate를 바꾸지, VideoMode 를 참조하고 있는 상수의 값을 바꾸는 것은 아니다.

> 말이 어렵지만, 클래스의 인스턴스를 담는 상수의 이름은 단지 생성된 인스턴스를 참조하고 있다는 것을 기억하면 된다. 상수 자체의 값은 일단 저장돼있지 않을 뿐더러 없는 값을 수정하는 일은 하지 않을 것이다. 즉, 상수가 가리키고 있는 인스턴스의 값이 바뀌는 것이기 때문에 `let` 으로 선언해도 문제가 없는 것.

<br />

## 식별 연산자

클래스는 참조 타입이기 때문에 두 개 이상의 상수와 변수로 하나의 인스턴스를 참조할 수 있다. (구조체와 열거형에선 같은지의 여부가 참이 아닌데, 이는 그것들이 값 타입으로 상수나 변수에 할당될 때, 혹은 함수에 전달될 때 복사가 되기 때문이다.)

가끔 두 상수 혹은 변수가 정확히 같은 클래스의 인스턴스를 참조하고 있는지 찾아내는 것이 유용할 때가 있다. 이를 위해 스위프트는 두 가지 식별 연산자를 제공한다.

- 동일하다 (===)
- 동일하지 않다 (!==)

두 상수 혹은 변수가 동일한 인스턴스를 참조하고 있는지 위의 연산자를 이용해 확인할 수 있다.

```swift
if tenEighty === alsoTenEighty {
	print("tenEighty와 alsoTenEighty는 같은 VideoMode 인스턴스를 참조하고 있다.")
}

// tenEighty와 alsoTenEighty는 같은 VideoMode 인스턴스를 참조하고 있다.
```

identical(===)과 equal(==)은 명확히 다르다. Identical은 두 클래스 타입의 상수 혹은 변수가 정확히 동일한 클래스 인스턴스를 찹조하고 있다는 것을 뜻한다. Equal은 두 인스턴스가 같거나 같은 값을 가지고 있다는 것을 의미한다.

사용자 정의 구조체와 클래스를 정의할 때 두 인스턴스가 동일한지에 대한 판별은 코드를 짜는 사람에 달려 있다. ==와 != 연산자를 정의하는 과정은 [Equivalence Operators](https://docs.swift.org/swift-book/LanguageGuide/AdvancedOperators.html#ID45) 에 정리되어 있다.

<br />

## 포인터

C, C++, Objective-C 를 해 봤다면 이 언어들은 메모리 주소를 참조하기 위해 포인터를 쓴다는 점을 알 것이다. 참조 타입의 인스턴스를 참조하는 스위프트의 상수 혹은 변수들은 C의 포인터와 유사한데, 정확히 메모리 주소를 가리키는 포인터는 아니며, 참조하고 있다는 것을 표시하기 위해 애스터리스크(*)를 사용할 필요도 없다.

대신, 이 때의 참조는 스위프트의 어떠한 다른 상수 혹은 변수와 동일하게 정의되어 있다. 표준 라이브러리에선 메모리 주소를 가리키는 포인터를 필요로 할 때 사용할 수 있는 포인터와 버퍼 타입을 제공한다. 이는 [Manual Memory Management](https://developer.apple.com/documentation/swift/swift_standard_library/manual_memory_management) 문서를 참조하자.

<br />

---

원문 : [The Swift Programming Language (Swift 5.5) - Structures and Classes](https://docs.swift.org/swift-book/LanguageGuide/ClassesAndStructures.html)
