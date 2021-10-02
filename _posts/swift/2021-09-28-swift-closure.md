---
title: "스위프트 클로저"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - swift
tags:
    - swift
    - 스위프트
last_modified_at: 2021-09-28
---

# 클로저

클로저는 일정 기능을 하는 코드를 하나의 블록으로 모아 놓은 것이다.

특정 기능을 하는 코드라는 점에서 함수는 클로저의 일종이다. 클로저는 변수나 상수가 선언된 위치에서 참조를 획득하고 저장할 수 있다.

### 클로저의 형태

- 전역함수 형태 - 이름 있고, 어떤 값도 획득하지 않음
- 중첩함수 형태 - 이름 있고, 다른 함수 내부의 값을 획득함
- 축약 형태 - 이름 없고, 주변 문맥에 따라 값을 획득할 수 있음

### 클로저 표현 방식

- 매개변수와 반환 값의 타입을 생략할 수 있음 - 문맥을 통해 유추할 수 있기 때문
- 한 줄만 쓰여 있으면 이는 곧 반환값으로 취급
- 축약된 전달인자 이름 사용 가능
- 후행 클로저 문법 사용 가능

### 왜 쓸까?

조금 더 간단한 형태로 함수처럼 사용하기 위해. 

## 기본 클로저

### `sorted(by:)`

해당 메서드의 정의는 다음과 같다.

```swift
public func sorted(by areInIncreasingOrder: (Element, Element) -> Bool) -> [Element]
```

이 메서드는 두 개의 매개변수를 가지고 Bool 값을 리턴하는 클로저를 함수의 인자로 전달받는다.

이 때 반환되는 Bool 값은 첫 번째 인자가 두 번째 인자보다 큰지(앞 순서인지)에 대한 결괏값이다.

> 함수를 메서드의 전달인자로 보내는 것은 함수형 프로그래밍 패러다임에서 익숙한 일이다.
> 

그럼 이 메서드를 사용하기 위해선, `Element` 와 동일한 타입의 요소 두 개를 각각 인자로 받고, 이를 비교해 Bool 값을 리턴하는 함수를 넘겨줘야 한다.

예를 들어, 정수 값을 오름차순으로 정렬하고자 하고, 정렬하고자 하는 정수 배열이 `numbers` 와 같이 정의되어있다면 다음과 같이 해 주면 된다.

```swift
func ascendingOrder(first : Int, second : Int) -> Bool {
	return first < second
}

let numbers: [Int] = [0, -5, 99, 47, -44, 2]
let sortedArray: [Int] = numbers.sorted(by: ascendingOrder)

print(sortedArray) // [-44, -5, 0, 2, 47, 99]
```

여기서 `ascendingOrder` 에 있는 불필요한 매개변수, 반환값, 리턴 키워드 등을 생략해 **`클로저`** 로 만들 수 있다. 이를 클로저로 만들면 다음과 같이 사용할 수 있다.

```swift
let sortedArray: [Int] = numbers.sorted(by: { (first: Int, second: Int) -> Bool in
	return first < second
})

print(sortedArray) // [-44, -5, 0, 2, 47, 99]
```

이를 더 축약해 매개변수와 리턴값 등을 생략해 다음과 같이 쓸 수도 있다.

```swift
let sortedArray: [Int] = numbers.sorted(by: { $0 < $1 })
// 혹은
let sortedArray: [Int] = numbers.sorted { $0 < $1 }
```

극한으로 줄여서 첫 번째 인자와 두 번째 인자를 각각 의미하는 `$0`, `$1` 을 통해 두 요소 간의 대소관계를 리턴한다는 의미이다. 두 번째 축약 형태는 뒤에 나올 '후행 클로저'에 해당한다.

여기에서 가장 중요한 것은, `sorted` 의 인자로 들어간 클로저의 매개변수 개수, 타입, 반환형 등은 무조건 통일되어야 한다는 점이다.

### 연산자 함수

극한의 축약 그 끝은 연산자 함수이다.

스위프트 라이브러리에 정의된 비교 연산자는 다음과 같다.

```swift
public func <<T: Comparable>(lhs: T, rhs: T) -> Bool
```

`<` 연산자 자체가 함수인 셈인데, 그렇다면 sorted의 인자로 함수를 따로 정의하는 대신, `<` 연산자를 사용해도 되지 않을까? 물론이다. 잘 나온다.

## 후행 클로저

- 함수나 메서드의 마지막 전달인자로 위치하는 클로저로, 함수나 메서드의 소괄호를 닫은 후 작성할 수 있다.
- 전달인자로 클로저 여러 개가 전달될 땐, 그 중 가장 마지막 클로저만 후행 클로저로 사용할 수 있다.
- 매개변수에 클로저가 여러 개 있을 땐, 다중 후행 클로저를 사용할 수 있으며, 중괄호를 열고 닫음으로써 클로저를 표현하고 첫 번재 클로저의 전달인자 레이블은 생략한다.

예시를 통해 무슨 말인지 확인 해 보았다.

```swift
// 후행 클로저

let sortedOne = numbers.sorted() { (first: Int, second: Int) -> Bool in
	return first < second
}

// 소괄호 생략

let sortedTwo = numbers.sorted { (first: Int, second: Int) -> Bool in
	return first < second
}
```

```swift
func someFunction(do: (String) -> Bool,
									onSuccess: (Any) -> Void,
									onFailure: (Error) -> Void) {
	// some function . . .
}

// 다중 후행 클로저

someFunction { (someString: String) in {
	// do closure
} onSuccess: { (result: Any) in {
	// success closure
} onFailure: { (error: Error) in {
	// failure closure
}
```

## 값 획득

클로저는 정의된 위치의 주변 문맥을 통해 상수나 변수를 **획득(Capture)** 할 수 있다. 이 `값 획득` 을 통해 주변에 정의한 상수나 변수가 더 이상 존재하지 않더라도 그 값을 자기 내부에서 참조하거나 수정할 수 있다.

클로저가 비동기 작업에 많이 사용되기 때문에, 현재 상태를 획득해둬야 클로저가 실제로 기능을 수행하게 되는 상황에서 주변의 상수나 변수가 없더라도 제 기능을 할 수 있다.

클로저의 형태 중 하나인 중첩 함수도 값 획득이 가능하다. 자신을 포함하는 함수의 지역변수나 지역상수를 획득할 수 있는 것이다.

```swift
func makeIncrementer(forIncrement amount: Int) -> (() -> Int) {
	var total = 0

	func incrementer() -> Int {
		total += amount
		return total
	}
	
	return incrementer
}
```

 

위와 같이 `total` 을 저장하고, `incrementer` 함수에 의해 `amount` 만큼을 증가시켜주는 중첩 함수가 있다고 해 보자.

이 때, `incrementer` 는 자신을 포함하는 `makeIncrementer` 함수가 가진 변수 `total` 의 값을 획득하여 기억해 둔다.

```swift
let incrementByFive: (() -> Int) = makeIncrementer(forIncrement: 5)

let first = incrementByFive() // 5
let second = incrementByFive() // 10
let third = incrementByFive() // 15
```

`makeIncrementer` 의 인자로 들어간 `amount` 5 만큼, 함수가 호출 될 때마다 증가하고 있는 것을 확인할 수 있다.

makeIncrementer로 생성한 함수는 동일한 값을 넣어 만들어도 각각 서로 다른 값을 저장한다. 즉, 앞서 만든 5 만큼의 함수를 두 번 만들었을 때, 두 함수가 값을 공유하는 것이 아니라 각각의 함수가 값을 각자 저장하고 운용하는 것이다.

```swift
let incrementOne = makeIncrementer(forIncrement: 10)
let incrementTwo = makeIncrementer(forIncrement: 10)

let first_One = incrementOne() // 10
let second_One = incrementOne() // 20

let first_Two = incrementTwo() // 10
```

## 클로저는 참조 타입

함수와 클로저는 `참조 타입` 이므로, 값 획득을 통해 얻어온 변수나 상수를 변화시킬 수 있다.

앞선 예시에서, `total` 변수에 연산이 지속적으로 일어나고, 그 값이 초기화되지 않고 계속 남는 것은 값을 할당하는 것이 아니라 참조를 할당하는 것을 의미한다.

이 말은, 같은 클로저를 가리키는 두 서로 다른 클로저는 참조하는 대상이 같기 때문에 두 클로저 모두 값 변화에 영향을 끼칠 수 있다는 것이다.

```swift
let incrementThree = makeIncrementer(forIncrement: 10)
let incrementFour = incrementThree

let first_Three = incrementThree() // 10
let first_Four = incrementFour() // 20
```

## 탈출 클로저

함수의 전달인자로 전달한 클로저가 함수 종료 후에 호출될 때, 클로저가 함수를 **탈출(escape)**한다고 표현한다. 이 때, 클로저를 매개변수로 갖는 함수를 선언할 때 매개변수 이름의 콜론(:) 뒤에 `@escaping` 키워드를 사용하여 클로저가 탈출하는 것을 허용한다고 명시할 수 있다.

비동기 작업을 할 때, 함수가 종료된 뒤 호출해야 하는 클로저는 `탈출 클로저` 로, 그렇지 않은 경우는 `비탈출 클로저` 로 사용하면 된다. `@escaping` 키워드에 의해 자동으로 탈출과 비탈출 클로저가 정해지므로, 탈출 클로저를 쓰는 경우가 아니라면 그냥 비탈출 클로저로 생각하고 쓰면 된다.

클로저가 함수를 탈출할 수 있는 경우 중 하나는 함수 외부에 정의된 변수나 상수에 저장되어 함수가 종료된 후에 사용할 경우다. 이 때, 클로저를 함수의 인자로 전달할 때 **컴플리션 핸들러**로 전달한다.

컴플리션 핸들러가 뭐지..? 하고 찾아봤는데, [화면 전환](https://2unbini.github.io/swift/UIKit-3/)할 때 배웠던 `present` 메서드의 세 번째 인자가 컴플리션 핸들러였다. 화면전환과 같은 비동기 작업에서, 현재 진행중인 작업이 끝남과 동시에 실행하고자 하는 액션을 넣고, 그를 작동하게 하는 부분이다. 즉, 현재 돌아가고 있는 작업이 끝나고 뒤따라 진행될 기능을 제어하는 부분이라고 생각하면 될 것 같다.

그렇다면, 컴플리션 핸들러로 클로저를 전달한다는 뜻은, 함수 실행과 동시에 클로저가 실행되는 것이 아니라, 함수가 모두 끝난 후에 클로저가 실행되어야 한다는 말이다. 이 때, 클로저가 탈출한다는 것은 함수가 작업을 마치기 전엔 클로저를 실행하지 않는다는 것과 동일하다고 생각하면 될 것 같다.

```swift
var completionHandlers: [() -> Void] = []

func someFunctionWithEscapingClosure(completionHandler: @escaping () -> Void) {
	completionHandlers.append(completionHandler)
}
```

```swift
typealias VoidVoidClosure = () -> Void

let firstClosure: VoidVoidClosure = {
	print("First Closure")
}

let secondClosure: VoidVoidClosure = {
	print("Second Closure")
}

// 1
func returnOneClosure(first: @escaping VoidVoidClosure, second: @escaping
	VoidVoidClosure, shouldReturnFirstClosure: Bool) -> VoidVoidClosure {
	return shouldReturnFirstClosure ? first : second
}

let returnedClosure = returnOneClosure(first: firstClosure, second:
	secondClosure, shouldReturnFirstClosure: true)

returnedClosure() // First Closure

var closures = [VoidVoidClosure]()

// 2
func appendClosure(closure: @escaping VoidVoidClosure) {
	closures.append(closure)
}
```

### 1번

1의 경우 `@escaping` 키워드가 각각 `first`, `second` 인자에 붙었다.

이유는, `returnOneClosure` 의 리턴 값으로 각 VoidVoidClosure가 반환될 수 있기 때문이다.

### 2번

2의 경우, `closures` 배열에 추가될 클로저에 `@escaping` 키워드가 붙었다. 이 또한, 해당 클로저가 함수 외부의 변수에 저장될 수 있기 때문에 탈출 클로저로 명시한 것이다.

즉, 함수 외부로 전달되어 함수 외부에서 사용하게 되거나, 외부 변수에 저장될 수 있을 때 탈출 클로저를 쓴다는 걸 알 수 있다.

### self

타입 내부 메서드의 매개변수 클로저에 `@escaping` 키워드를 사용하는 경우, 클로저 내부에서 해당 타입의 프로퍼티, 메서드, 서브스크립트 등에 접근하려면 명시적으로 `self` 키워드를 사용해야 한다.

```swift
typealias VoidVoidClosure = () -> Void

func functionWithNoescapeClosure(closure: VoidVoidClosure) {
	closure()
}

func functionWithEscapingClosure(completionHandler: @escaping VoidVoidClosure)
	-> VoidVoidClosure) {
	return completionHandler
}

class SomeClass {
	var x = 100

	func runNoescapeClosure() {
		functionWithNoescapeClosure { x = 200 }
	}

	func runEscapingClosure() -> VoidVoidClosure {
		return functionWithEscapingClosure { self.x = 300 }
	}
}

let instance = SomeClass()
instance.runNoescapeClosure() // x == 200

let returnedClosure = instance.runEscapingClosure()
returnedClosure() // x == 300
```

### withoutActuallyEscaping

`withoutActuallyEscaping(_:do:)`

이름 그대로 비탈출 클로저를 탈출 클로저처럼 사용할 수 있는 함수.

첫 번째 전달 인자로 탈출 클로저처럼 사용할 비탈출 클로저가 들어가고, 두 번째 전달 인자 'do'는 첫 번째 인자인 비탈출 클로저를 전달받아 실제로 사용할 탈출 클로저를 전달한다.

## 자동 클로저

함수의 전달인자로 전달하는 표현을 자동으로 변환해주는 클로저를 **자동 클로저**라고 한다.

자동 클로저는 전달인자를 갖지 않고, 호출되었을 때 자신이 갖고 있는 코드의 결괏값을 반환한다.

자동 클로저는 클로저가 호출되기 전까지 클로저 내부의 코드가 동작하지 않기 때문에, 연산하는 데 지연을 주고 싶을 때 사용할 수 있다.

```swift
var peopleInLine = ["ekwon", "gpark", "sun-kim"]
print(peopleInLine.count) // 3

let peopleProvider: () -> String = {
	return peopleInLine.removeFirst()
}

print("Now serving \(peopleProvider())...") // "Now serving ekwon..."
print(peopleInLine.count) // 2
```

이렇게 보면, 지금까지 봐온 클로저의 기능과 형태와 비슷한 것 같다. 그런데, 이 클로저를 함수의 전달인자로 전달한다고 했을 때 기존의 클로저와 자동 클로저의 작동 방식에 약간의 차이가 생긴다.

```swift
var peopleInLine = ["ekwon", "gpark", "sun-kim"]

// 1
func servePerson(_ peopleProvider: () -> String) {
	print("Now serving \(peopleProvider)...")
}

// 2
func servePersonAuto(_ peopleProvider: @autoclosure () -> String) {
	print("Now serving \(peopleProvider)...")
}

servePerson { peopleInLine.removeFirst() } // "Now serving ekwon..."
servePersonAuto(peopleInLine.removeFirst()) // "Now serving gpark..."
```

### 1 은 그냥 클로저, 2는 자동 클로저

1은 지금껏 봐온대로, 함수의 인자로 클로저가 들어가므로 `servePerson` 함수에 바로 클로저를 써 주면 된다.

그런데 2의 함수 인자에는. `@autoclosure` 와 같은 키워드가 붙어 있는데, 이를 통해 함수에 클로저를 넣지 않아도 함수의 인자로 넣은 연산의 결과 `String`이 바로 `peopleProvider` 로 들어가게 된다.

이 String 타입의 값이 자동 클로저 매개변수에 전달되면, 이를 `매개변수가 없는 String 값을 반환하는 클로저` 로 변환해준다. 이렇듯 자동으로 클로저로 변환해주기 때문에 **자동 클로저**라고 하는 것이다.

### 자동 클로저는 기본적으로 비탈출 클로저

자동 클로저를 탈출 클로저로 사용하고 싶다면, `@autoclosure` 뒤에 `@escaping` 키워드를 붙여주면 된다.

## 결론

결국, 클로저는... 쓸 데가 많다!

## 참고

스위프트 프로그래밍 3판 (한빛미디어, 야곰)
