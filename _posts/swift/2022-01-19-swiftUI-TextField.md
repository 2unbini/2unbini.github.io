---
title: "SwiftUI, iOS13+ TextField numberPad 텍스트 제한, 리턴 기능 구현"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - swift
tags:
    - swift
    - 스위프트
    - SwiftUI
last_modified_at: 2022-01-19
---

# 목표

- 텍스트 필드의 글자 속성 바꾸기
    - Bold, Color, Size
- 텍스트 필드의 인풋 텍스트에 제한 걸기
    - numberPad → 오직 숫자만 입력, max limit: 365
- 키보드에 리턴 버튼이 없는 경우(numberPad)
    - onTapGesture 활용
    - UITextField 커스터마이징 (→ 완성되면 업로드 예정,,)
    

## 글자 속성 바꾸기

텍스트 필드의 글자 속성을 바꾸기 전에 텍스트 필드의 선언 형태를 보자.

```swift
TextField(title: StringProtocol, text: Binding<String>)
```

placeholder에 해당하는 부분이 `title`, 입력 받는 문자열이 담기는 부분이 `text`이다.

### 처음 생각한 것

텍스트를 입력받을 때(혹은 입력받기 전) placeholder, text에 해당하는 부분의 문자열을 `AttributedString` 으로 가공하는 코드를 추가하면 되지 않을까?

→ UITextField 커스터마이징 해야 함 → SwiftUI로 하는 방법은 없을까?

### Pure SwiftUI Solution

글자 속성 바꾸는 것은 너무나도 쉬웠다. 그냥 TextField 자체에 `.font, .foregroundColor` 모디파이어를 적용하면 된다.

```swift
TextField(title: "365", text: $goalCountText)
	.font(.system(size: 14, weight: .bold))
	.foregroundColor(colors[color.defaultGray.rawValue])
```

이렇게 하면 Placeholder, Text 두 요소를 따로 관리할 필요 없이 foregroudColor → title에 적용, font → textField전체에 적용 되어 깔끔하게 사용할 수 있다.

## 인풋 텍스트 제한 걸기

자동으로 숫자 키보드가 나오게 하려면 다음과 같은 모디파이어를 추가 해 주면 된다.

```swift
TextField(title: "365", text: $goalCountText)
	.keyboradType(.numberPad)
```

`.numberPad` 타입의 키보드를 사용하면 숫자만 입력할 수 있을 것 같지만 실제로는 그렇지 않다.

클립보드에 복사해둔 문자열을 붙여넣으면 숫자가 아니어도 텍스트필드에 입력된다.

이를 해결하기 위해 텍스트가 들어오는 순간마다 텍스트가 유효한지 확인하고, 유효하지 않은 경우 걸러내는 과정이 필요하다.

UITextFieldDelegate에 정의된 `textField(_:shouldChangeCharactersIn:replacementString:)` 는 텍스트 필드에 값이 입력됐을 때 호출된다. 반환값에 따라 새로운 문자열로 대치할지, 기존의 문자열을 유지할지를 결정한다.

### .onReceive와 Publisher

SwiftUI에서 이와 같은 동작을 할법한 메서드는 `.onReceive` 가 있다. `publisher` 가 방출한 이벤트를 받아 `view` 에서 어떠한 `action` 을 하게 된다.

> `publisher` : 이벤트를 발생시켜 subscriber에 데이터를 전달하는 역할을 한다.

어쨌든 .onReceive의 첫 번째 인자로 퍼블리셔를, 두 번째 인자로 처리할 액션을 넣어 주면 된다.

### @Published 변수를 Publisher로 쓰기

```swift
class ChallengeVM: ObservableObject {
	@Published var goal: Goal = Goal()
	...
}

struct Goal {
	var count: String = ""
	var title: String = ""
}
```

```swift
@StateObject private var challengeVM: ChallengeVM = ChallengeVM()

TextField(title: "365", text: $goalCountText)
	.keyboradType(.numberPad)
	.onReceive(challengeVM.goal.count) { _ in
		// Action Field
	}
```

그냥 냅다 갖다 쓰면 안 된다.

이름이 비슷하고, 변화를 감지한다는 면에서 같은 타입일거라 생각했는데 에러 메시지를 보면 `String 타입의 challengeVM.goal.count가 Publisher를 따르지 않는다`고 나온다.

그래서 Apple Document에서 `[Published](https://developer.apple.com/documentation/combine/published)` 키워드를 검색했더니,

> Publishing a property with the `@Published` attribute creates a publisher of this type. You access the publisher with the `$` operator, as shown here:

Publisher의 성격은 가지고 있는데, 그 성격에 접근하려면 `$` 를 써야 한단다.

```swift
.onReceive(challengeVM.$goal.count) { _ in
	// Action Field
}
```

로 해 줬더니, 또 다른 에러들이 생겼다.

> -  challengeVM.$goal.count → Binding<String> 타입임 <br>
> -  onReceive의 인자로 들어오는 Publisher는 Failure가 `Never` 여야 함 <br>
> -  struct, enum, class와 같은 굳어진 타입들만이 해당 프로토콜을 따를 수 있음 <br>

에러들을 생각 해 봤을 때,

1. goal에 $표시가 붙었으니 `$goal.count`로 떼어 놓고 보면, String 타입에 바인딩 오퍼레이터를 붙인 격이다. 그래서 나오는 에러라고 생각된다.
2. onReceive 정의를 다시 봤더니, `P.Failure == Never` 라는 부분이 있었다.
    
    publisher는 subscriber와 함께 데이터를 주고받으며 이벤트를 발생시키고 처리하는데, 이 때 subscriber에게 전달하는 `값(Output)` 외에 실패했을 경우 `에러`를 발행할 수 있는 `Failure` 라는 타입을 가지고 있다. 이 `Failure` 가 `Never` 라는 것은, publisher가 에러를 발행하지 않는다는 것을 의미한다.
    
    근데 지금 publisher로 전달한 `challenge.$goal.count` 가 제대로 작동하고 있지 않거나, 작동한다 할 지라도 Failure가 Never인 상태가 아닌 것이다. 즉, Failure == Never 인 상태로 가공 해 줘야 한다.
    
3. 일단 class와 struct로만 이루어진 객체라서 저 말이 왜 나온 지 모르겠지만, 전체적으로 에러일 수 있는 상황을 다 보여주는거라고 느꼈다. 그래서 일단 패스.

결국, Failure가 Never인 상태의 Publisher를 인자로 넘겨 줘야 한다는 것이다.

### Just

일단 `challenge.$goal.count` 로 @Published 변수를 Publisher 타입으로 변환하는 것 까진 했다고 치고,

위에서 나온 2번 에러를 해결하기 위해 `Failure == Never` 로 만들어주는 Publisher 초기화 방법을 찾아봤다.

Publisher 타입엔 다양한 친구들이 있는 것 같은데, 다양한 블로그 포스팅과 기타 등등을 참조한 결과, `Just` 가 가장 기본적인 형태의 Publisher Initializer인 것을 알아냈다. 게다가 내가 원하는 `Failure == Never` 의 상태도 만들 수 있었다.

[문서에](https://developer.apple.com/documentation/combine/just) 따르면, Just는 실패할 수 없고, 항상 값을 생산해낸다고 한다. 내가 원하는 형태로 Publisher를 만들 수 있는 것 같다.

```swift
.onReceive(Just(challengeVM.$goal.count)) { _ in
	// Action Field
}
```

이렇게 해 줬더니 에러가 사라졌다.

### 입력받은 문자열에 제약조건 주기

드디어 이제.. 입력받은 문자열에 제약조건을 추가할 수 있게 됐다.

1. 오직 숫자만 입력
2. max limit: 365

이 두 가지를 구현했다.

```swift
.onReceive(Just(challengeVM.$goal.count)) { _ in

	// Number가 아닌 문자는 걸러내고, 걸러낸 문자열과 입력받은 문자열이 다르면 걸러낸 문자열로 대치
	let filteredString = challengeVM.goal.count.filter { $0.isNumber }
	if filteredString != challengeVM.goal.count {
		challengeVM.goal.count = filteredString
	}

	// 걸러낸 문자열을 숫자로 만들었을 때, max limit인 365보다 크면 입력받은 문자열은 "365"로 고정
	let filteredCount = Int(filteredString) ?? 0
	if filteredCount > 365 {
		challengeVM.goal.count = "365"
	}
}
```

이렇게 하면 numberPad 키보드에서 내가 원하는 한도 만큼의 숫자만 입력받을 수 있다.

## 키보드에 리턴 버튼이 없을 때

numberPad는 숫자만 입력받는 키보드가 기본 세팅되어 편리하지만, 입력을 끝낼 수 있는 버튼이 따로 없어 이를 추가해주어야 한다.

추가하는 방법으로 이 두 가지가 대표적이다.

1) 키보드에 툴바 추가하기, 2) 키보드 외 공간 터치하면 키보드 없애면서 값 저장하기

SwiftUI로 키보드에 툴바 추가하는 방법은 아주 간단하다.

TextField에 `.toolBar` 를 붙여 주면 된다. 하지만 `iOS 14.0` 부터 지원한다는 슬픈 소식. 그리고 키보드에 툴바 붙이는 `.keyboard` 는 `iOS 15.0` 부터 지원한다는 슬픈 소식 하나 더...

### iOS 15.0 +

```swift
TextField(title: "365", text: $goalCountText)
	.keyboradType(.numberPad)
	.toolbar(content: {
     ToolbarItem(placement: .keyboard) {
	     Spacer()
	     Button("Done") {
	       // Submit Button
	     }
		 }
	})
```

이렇게 해 주면 되는데 iOS 13+, 아니 일단 iOS 14+부터도 순수 SwiftUI로 keyboard에 붙일 툴 바를 만들 수 없다. 결국 UIKit에 있는 UITextFieldDelegate를 이용해 뷰를 만들어 사용하거나, 약간의 꼼수 격으로 키보드 외부 공간을 탭 했을 때 키보드가 꺼지도록 만들게 해야 한다.

### SwiftUI에서 UIKit 뷰 사용하기

SwiftUI 뷰 계층에서 UIKit 뷰를 사용하기 위해선 `UIViewRepresentable` 프로토콜을 따르는 뷰를 생성해야 한다.

일단 이 방법으로 하다가 버튼이 제대로 동작하지 않아 위에 말한 다른 방법으로 우선 구현 했다.

다음에 다시 시도해보고 포스팅 할 예정이다.

### onTapGesture 이용하기

텍스트필드 외의 공간을 탭 했을 때, 키보드가 꺼지고, 그와 동시에 지금까지 작업하던 것들을 저장할 수 있게 만들면 리턴 키의 역할을 유사하게 구현할 수 있겠다고 생각했다.

1. TextField 외의 공간에 onTapGesture 선언하기
2. onTapGesture 내부 기능 구현하기
    1. 지금까지 입력된 내용 저장하기 → challengeVM에 저장하는 메서드 추가
    2. 키보드 없애기 → resignFirstResponder 사용

의 순서로 의사 코드를 작성하면 다음과 같다.

```swift
.onTapGesture {
	challengeVM.saveData()
	textField.resignFirstResponder()
}
```

> `First Responder`: 사용자의 터치 이벤트를 처리해 내부에 전달하는 UIWindow 객체는 터치 이벤트가 발생했을 때 가장 먼저 응답할 객체를 가리키는 포인터를 가지고 있다. 그 포인터가 바로 First Responder. <br>
> <br>
> 텍스트 필드를 눌렀을 때 First Responder가 키보드를 가리키게 되고, 그에 따라 키보드가 팝업된다. <br>
>코드상으로 수정하지 않으면 키보드가 First Responder 인 상태를 계속 유지하기 때문에 키보드 밖의 그 어떤 곳을 눌러도 키보드가 사라지지 않는다.<br>
> <br>
> 결국, 키보드 밖의 부분을 터치했을 때 First Responder를 해제해주면 되는 것이다. `resignFirstResponder()` 메서드를 사용하면 된다.

**데이터 저장 메서드 구현**

```swift
class ChallengeVM: ObseravableObject {
	@Published var goal: Goal = Goal()

	func saveData() {
		UserDefaults.standard.set(goal.count, forKey: "userGoalCount")
		UserDefaults.standard.set(goal.title, forKey: "userGoalTitle")
	}
}
```

임시로 UserDefaults를 이용해 데이터를 저장했다.

**resignFirstResponder**

`resignFirstResponder` 메서드를 쓰기 위해 현재 포커싱되고 있는 textField에 접근해야 한다.

하지만 선언형으로 이루어진 SwiftUI에서 그 객체에 접근할 수 없는데다가, 해당 메서드는 UIResponder 클래스에 정의되어있기 때문에 이를 상속받지 않으면 의미가 없다.

그래서 현재 터치 이벤트에 의해 First Responder가 된 객체를 찾아 resignFirstResponder 를 적용하기로 했다.

```swift
UIApplication.shared.sendAction(#selector(UIResponder.resignFirstResponder), to: nil, from: nil, for: nil)
```

해당 코드를 써 주면 된다.

UIApplication에 의해 `UIResponder.resignFirstResponder` 가 실행되는 것이다.

그낭 위 문장을 써도 되지만, `UIApplication` 의 익스텐션으로 함수를 새로 만들어 정리했다.

```swift
extension UIApplication {
	func endEditing() {
		sendAction(#selector(UIResponder.resignFirstResponder), to: nil, from: nil, for: nil)
	}
}
```

```swift
.onTapGesture {
	challengeVM.saveData()
	UIApplication.shared.endEditing()
}
```

> UIApplication.shared : 싱글톤 앱 인스턴스로, 앱이 실행될 때 만들어지는 공유 앱 인스턴스

## 결론

1. iOS 15+ 부터는 순수 SwiftUI로 만들 수 있는 것들이 많지만 그 아래는 다양한 시도를 해 봐야 한다.
2. keyboard의 numberPad는 키보드만 바뀔 뿐, 입력받는 폼 자체에 제한이 걸리는 것이 아니므로 그 제약조건은 따로 설정해줘야 한다.
3. 제약조건 설정시 onReceive 메서드를 쓰는데, 여기엔 Publisher가 필요하고, 그러려면 Combine 공부를 해야 한다.
4. iOS 14 이하의 키보드에 툴바 달려면 `UIViewRepresentable` 을 사용해 UIKit 뷰를 삽입해야 하는데, 조금 꼼수(키보드 외부 공간 터치)를 쓰면 최선은 아니라도 리턴 기능을 구현할 순 있다.

### 참고
[Apple Documentation](https://developer.apple.com)

[programming with swift: numbers only textfield](https://programmingwithswift.com/numbers-only-textfield-with-swiftui/)

[medium, harrythegreat: Combine 입문](https://medium.com/harrythegreat/swift-combine-입문하기-가이드-1-525ccb94af57)

[hacking with swift: dissmiss keyboard](https://www.hackingwithswift.com/quick-start/swiftui/how-to-dismiss-the-keyboard-for-a-textfield)