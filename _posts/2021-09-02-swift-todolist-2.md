---
title: "SwiftUI 로 Todo list 만들기 2"
toc: true
toc_sticky: true
categories:
    - swift
tags:
    - swift
    - 스위프트
    - todolist
    - 투두리스트
    - 프로젝트
    - iOS
last_modified_at: 2021-08-24
---

## 화면 구성하기

[전 편](https://2unbini.github.io/swift/swift-todolist-1)에서 제목과 텍스트 필드 화면을 구성했다. 이번엔 투두 리스트의 리스트 화면을 구성해볼 것이다.

- 할 일 목록 (리스트)
	- 체크 박스 (버튼)
	- 내용 (텍스트)
	- 삭제 버튼 (버튼)

앞서 리스트 각각의 요소에 필요한 것들을 위와 같이 정리했다. 지난 번의 기억을 복습해 보면, 큰 틀에서 `List` 와 같은 뷰로 잡아주고, 각각의 요소에 대해 `HStack` 으로 버튼과 텍스트를 구성해 주면 되겠다는 생각이 든다.

### 리스트

단도직입적으로 구글 창에 'SwiftUI List' 라고 치거나, Xcode 에서 'List' 뷰를 검색하면 바로 결과가 나온다. 리스트는 말 그대로 `List`다.

![list](/assets/images/swift-todo-2/1.png)

공식 문서의 예제로 나와 있듯, `List` 안에 `Text` 뷰를 넣어 세로로 정렬된 리스트를 만들 수 있다. 하지만 더 일반적으로 식별 가능한 요소의 배열을 하나씩 선택해가며 리스트에 보여주는 방식을 사용한다고 나와 있다. 텍스트 필드를 통해 입력받은 할 일을 배열에 저장하고, 그 요소를 리스트에 보여 주는 방식으로 진행하면 될 것 같다.

```swift
List(todoLists) {
	HStack {
		Button()
		Text($0.content)
		Button()
	}
}
```

이런 방식으로 구성해봤다.

### 객체 정의

이렇게 하려면 식별 가능한 `ID`를 가진 `todoLists` 배열이 있고, 각각의 요소를 `$0` 로 가져와서 `content`로 정의된 할 일 문장을 텍스트로 표현할 수 있어야 한다. 위의 공식 문서 예제에서도 볼 수 있듯, 리스트로 각 요소를 가져올 수 있으려면 `Identifiable` 프로토콜을 따르는 객체를 정의해야 한다.

```swift
struct TodoList: Identifiable {
	let id = UUID()
	var content: String
}
```

`UUID`를 통해 애플이 제공하는 고유 아이디를 부여받을 수 있다. `Int`와 같이 숫자형으로 만든 뒤, 각각의 입력마다 id 값을 줘도 되지만, 투두 리스트를 삭제하고 추가하는 과정에서 중복된 숫자가 들어갈 수도 있으니 자동으로 부여되는 방식을 따르기로 했다.

여기서는 화면 구성을 먼저 해 볼 예정이기 때문에, 텍스트 필드로 입력받은 문자열을 바로 객체화해 배열에 넣는 대신, 배열을 임시로 만들어 구현해보기로 했다.

```swift
private var todoLists = [
	TodoList(content: "SwiftUI 공부하기"),
	TodoList(content: "블로그 글 쓰기"),
	TodoList(content: "백준 문제 풀기")
]
```

이렇게 만들어준 뒤, 선언을 해 주고 실행을 해 보면..!!!

![error](/assets/images/swift-todo-2/2.png)

Button 은 아직 제대로 된 애들이 아니라 주석 처리를 해 놨는데, 예상대로라면 할 일이 하나씩 떠야 하는데 에러가 뜬다. `HStack` 없이 리스트 아래에 바로 `Text`가 들어가면 오류가 없지만, `HStack`이 들어가는 순간 오류가 뜨는 것을 보면 이 두 뷰가 뭔가 충돌을 일으키는 것 같다.

구글링을 통해 알아낸 해결법은, `ForEach` 를 사용하는 것이다.

```swift
List {
	ForEach(0..<todoLists.count, id: \.self) { i in
		HStack {
			Button()
			Text(todoLists[i].content)
			Button()
		}
	}
}
```

`ForEach` 를 사용해 `i`에 0 부터 `todoLists` 배열의 개수만큼의 수를 차례로 넣어 주고, `i`를 인덱스로 삼아 `todoLists[i]` 의 요소를 텍스트 뷰에 올리는 것이다. 첫 번째 인자로 들어간 것은 범위이고, 두 번째 인자로 들어간 것은 각각을 식별할 수 있는 `id` 이다.

각각을 하나의 요소로 분리해 뷰에 나타내는 과정에서 `id`가 필수적으로 사용되는 것 같다. id를 안 쓰면 또 에러가 남. 이에 대해선 더 자세한 공부가 필요할 듯 하다.

여기까지 하면, 아래와 같이 리스트 형태로 텍스트가 표현된다.

![3](/assets/images/swift-todo-2/3.png)

### 버튼

텍스트가 나왔으니, 텍스트 앞 뒤로 붙어 각각 `체크`와 `삭제` 기능을 할 버튼을 만들어 봤다. 우선 `Button` 뷰에 대한 정의를 봐 보자.

![4](/assets/images/swift-todo-2/4.png)

특정한 액션을 하고, 라벨이 있는 버튼을 만들 수 있으며, 액션은 유저가 버튼을 클릭하거나 탭 했을 때 무언가를 할 수 있는 메소드나 클로저라고 한다. 여기선 화면 구성만 해 볼 예정이므로, Button의 라벨만 설정해볼 예정이다.

공식 문서의 예제를 통해 버튼의 라벨은 `{}` 안에 또 다른 뷰를 추가하거나, 그 대신 문자열을 쓰면 된 다는 것을 알았다. 나는 전 글에서 썼던 `systemName`을 다시 이용해 아이콘을 불러왔다.

```swift
Button(action: {}, label: {
	Image(systemName: "square")
})
Button(action: {}, label: {
	Image(systemName: "trash")
})
```

SwiftUI 로 앱 개발을 할 땐 Xcode 를 무조건 쓸 텐데, 모든 IDE 가 그렇듯, Xcode에도 멋진 자동완성 기능이 있다. `Button` 뷰를 입력하고 `(` 만 치면 해당 뷰가 받을 수 있는 모든 경우의 수를 쫙 리스트로 내려 준다. 그 중 내가 가장 필요한 것을 골라 엔터를 쳐 주면 자동완성이 된다. 나는 액션과 라벨이 필요하므로 위의 형식으로 작성했다.

두 버튼 뷰를 추가해서 보면 아래와 같이 리스트 뷰를 완성할 수 있다.

```swift
List {
	ForEach(0..<todoLists.count, id: \.self) { i in
		HStack {
			Button(action: {}, label: {
				Image(systemName: "square")
			})
			Text(todoLists[i].content)
			Button(action: {}, label: {
				Image(systemName: "trash")
			})
		}
	}
}

```

![5](/assets/images/swift-todo-2/5.png)

쓰레기통 모양이 행의 맨 오른쪽 끝으로 가면 더 예쁠 것 같아서 `Text`와 `Button` 사이에 `Spacer()` 를 추가해 줬다.

```swift
List {
	ForEach(0..<todoLists.count, id: \.self) { i in
		HStack {
			Button(action: {}, label: {
				Image(systemName: "square")
			})
			Text(todoLists[i].content)
			Spacer()
			Button(action: {}, label: {
				Image(systemName: "trash")
			})
		}
	}
}

```

![](/assets/images/swift-todo-2/6.png)

### 다음은?

이로써 투두 리스트의 모든 구성 요소가 화면에 잘 표현됐다.

다음으로 할 것은 화면으로만 존재했던 뷰들의 [액션을 정의]()하는 일이다. 텍스트 필드에 입력한 문자열을 `Return` 과 동시에 리스트에 올리고, 리스트에 올라간 할 일을 경우에 따라 `삭제`하거나 완료된 항목에 대해 `체크`할 수 있는 것들이 기본적으로 우리가 갖춰 놓은 화면에 대한 액션이다.

잘못된 정보나 더 나은 방향이 있다면 언제든 알려 주세요!