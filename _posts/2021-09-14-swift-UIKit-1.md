---
title: "오늘 배운 잡다한 Swift 지식"
toc: true
toc_sticky: true
categories:
    - swift
tags:
    - swift
    - 스위프트
    - UIKit
last_modified_at: 2021-09-14
---

# 뭘 배웠지

- MVC(Model-View-Controller) model
- `static var`, `var`
- `extension`
- `UITableViewDataSource`
- Date `description`
- 에러
- 인터페이스 빌더의 클래스 아이덴티티 필드를 업데이트해야 하는 이유는?(미해결)
- 삽질

# MVC

말 그대로 `Model`, `View`, `Controller`가 분리돼 서로의 일을 하는 모델을 말한다.

UIKit은 MVC 패턴을 기반으로 한다. `ViewController.swift`, `Main.storyboard` 등등으로 각자의 컨트롤러, 뷰, 모델의 역할을 하도록 나누어져있기 때문이다.

- 모델 : 데이터와 비즈니스 로직 관리
- 뷰 : 화면 처리(레이아웃)
- 컨트롤러 : 뷰와 모델에 명령을 라우팅

### 모델

모델은 데이터의 상태가 바뀌면 그를 `뷰`에 알린다.

### 뷰

뷰는 모델로부터 받은 데이터를 화면에 표시한다.

### 컨트롤러

컨트롤러는 사용자의 액션에 따라 모델과 뷰를 동작한다.


# static var와 var의 차이

한 줄로 정리하면, 전자는 바뀌지 않는 고정값, 후자는 인스턴스.

`Car`라는 구조체를 정의한다고 했을 때, 모든 자동차는 같은 바퀴 개수를 가지므로 `static` 변수로 선언하고, 모든 자동차는 각자의 번호판을 가지므로 일반 변수로 선언하는 개념이다.

```swift
struct Car {
	static var wheels: Int = 4
	var carNumber: Int
}

// 객체의 고정된 값, 직접 수정하고 모든 인스턴스에 적용
Car.wheels = 3

// 각 인스턴스마다 다르게 생성되는 값
let car1 = Car(carNumber: 1234)
let car2 = Car(carNumber: 5678)
```


# Extension은 저장 프로퍼티를 갖지 않는다

스위프트 언어에 대한 [공식 문서](https://docs.swift.org/swift-book/LanguageGuide/Extensions.html)에서 `Extension`에 대한 설명을 보면 맨 처음으로 `extension`으로 뭘 할 수 있는 지 나와있다.

- 계산된 인스턴스 프로퍼티와 계산된 타입 프로퍼티를 추가할 수 있다.
- 인스턴스와 타입 메서드를 정의할 수 있다.
- 새 생성자를 제공한다.
- 서브스크립트를 정의할 수 있다.
- 중첩 타입을 정의하고 사용할 수 있다.
- 존재하는 타입에 프로토콜을 따르게 할 수 있다.

타입에 새로운 기능을 추가할 수 있는 기능이다. 대신, 이미 있는 기능을 덮어씌울(오버라이드 할) 수 없다.

여기서 일단 내가 배운 것.. `extension`에는 저장 프로퍼티를 담을 수 없다는 것!

위에서 `static var`와 `var`의 차이를 배웠는데, 저장 프로퍼티에 해당하는 `var`는 extension과 함께할 수 없고, 계산된 프로퍼티인 `static var`는 함께할 수 있다.

`UIKit` 공부하려고 튜토리얼 따라하고 있는데, 여기서 임시로 사용할 `testData`를 `extension`으로 선언하여 사용했다. 일반적으로 많이 사용하는 방법인진 모르겠지만, 확실히 컨트롤러나 뭐 어디 다른데에 변수로 써넣는 것보단 훨씬 깔끔한 것 같다.

`extension`으로 기존의 객체는 그 자체로 남겨두고, 필요한 기능이나 타입이나 데이터를 따로 운용할 수 있어서 유용하다고 느낀다.


# UITableViewDataSource

테이블 뷰는 데이터를 보여주기만 할 뿐, 데이터 자체를 수정하거나 운용할 순 없다. 데이터를 수정하려면 [`UITableViewDataSource`](https://developer.apple.com/documentation/uikit/uitableviewdatasource)프로토콜을 채택한 객체를 가지고 해야 한다.

이 프로토콜에 필요한 메소드는 두 개인데, 아래 예시 코드와 같은 친구들이다.

```swift
// Return the number of rows for the table.
override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
	return 0
}

// Provide a cell object for each row.
override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
	// Fetch a cell of the appropriate type.
	let cell = tableView.dequeueReusableCell(withIdentifier: "cellTypeIdentifier", for: indexPath)

	// Configure the cell's contents.
	cell.textLabel!.text = "Cell text"

	return cell
}
```

테이블에 몇 개의 행이 있는지 그 개수를 반환하는 메서드와, 각 행에 들어갈 셀에 정보를 넣고, 해당 셀을 반환하는 메서드 두 가지다.

각 행과 섹션에는 식별할 수 있는 숫자가 있고, 그 숫자로 각 행과 섹션의 위치를 기억하고 불러오는 것이다. 얘들은 `NSIndexPath` 객체들이라고 한다. 인덱스 0부터 시작하고, 섹션이 없는 테이블이라면 섹션의 인덱스는 무시하고 행의 인덱스만 신경쓰면 된다.

왜 굳이 저 두 메서드를 수정하는지 몰랐는데, 데이터를 테이블 뷰에서 직접 수정할 수 있으려면 해당 프로토콜을 써야 하기 때문이라는 걸 알았다. UIKit.. 복자패..


# Date() description

시간을 표시하는 객체인 `Date`는 그냥 문자열이 아니다.

`Date` 객체를 텍스트로 표현하기 위해서 `description` 프로퍼티를 사용하면 된다. 이를 통해 date 값을 텍스트 형태로 가져올 수 있다.

```swift
// dueDate는 Date형 값임
cell.dateLabel.text = task.dueDate.description
```


# 에러

> [Storyboard] Unknown class TaskListViewController in Interface Builder file.

친절하게 쓰여 있듯, 이 에러는 `Storyboard`에서 생긴 문제다.

TaskListViewController 라는 식별되지 않는 클래스가 있다! 라는 말 그대로, Storyboard에서 내가 어떤 뷰에 연결된 컨트롤러를 커스텀 클래스로 설정했을 때 얘가 제대로 인식되지 않는 문제다.

잘못된 경우                                   | 괜찮은 경우
------------------------------------------ | ------------------------------------------
![alt](/assets/images/all/s-error-not.png) | ![alt](/assets/images/all/s-error-ok.png)


오른쪽 그림과 같이 해당 클래스의 모듈이 앱 이름과 같이 되어있는지 확인하자. 나는 왼쪽과 같이 `None`으로 되어 있어서 문제가 생겼다.


# 인터페이스 빌더의 클래스 아이덴티티 필드를 업데이트해야 하는 이유는?

런타임에 뷰 계층을 분석해나갈 때 어떤 클래스를 인스턴스화해야 하는지 판별하기 위해서..라고 한다.

뭔말이지?... 책 보고 공부한 뒤 다시 찾아오겠어,,!ㅜㅜ


# 삽질

Storyboard에 버튼 오브젝트 넣어놓고 변수로 선언 안 해주고 액션에만 연결해줘서 버튼이 안 눌렸다... 낄낄... 땡그라미 잘 보자