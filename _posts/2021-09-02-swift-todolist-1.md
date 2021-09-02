---
title: "SwiftUI 로 Todo list 만들기 1"
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
last_modified_at: 2021-09-02
---

## 만들면서 공부하자

만들면서 공부하면 좀 더 빨리 익히는 편인 것 같아 이번에는 투두리스트를 만들기로 했다. 아직 익히고 있는 입장이기 때문에 코드의 비효율이 극에 달할 수 있다. 조금 더 나은 방식이 있다면 주저 없이 댓글로 제안해 주세요!

## 화면 구성하기

바로 작동이 되는 앱을 만들면 정말 좋겠지만, 내 실력이 그렇게 뛰어나진 않기 때문에 우선 화면을 띄워 보기로 했다. 화면을 구성하는 요소가 어떤 것이 있는지 생각하고, 각각에 필요한 뷰를 집어넣기로 했다.

### Todo list 의 구성요소

투두 리스트 화면은 기본적으로 할 일을 적어 넣는 `텍스트 필드`, 해야 할 항목을 띄워 주는 `리스트` 로 구성되어 있다. 각각의 투두에는 할 일의 `내용`, 완료했는지 체크할 수 있는 `체크 박스`, 할 일을 삭제할 `삭제 버튼` 이 기본적으로 있으면 될 것 같다. 필수는 아니지만 그래도 투두 리스트라는 것을 표시하기 위해 맨 위에 `타이틀` 도 추가하기로 했다.

정리하자면,

- 제목 (텍스트)
- 할 일 적어 넣을 공간 (텍스트 필드)
- 할 일 목록 (리스트)
	- 체크 박스 (버튼)
	- 내용 (텍스트)
	- 삭제 버튼 (버튼)

의 구성 요소를 구현하기로 했다.

여기서는 제목과 할 일 적을 텍스트 필드를 정리했다.


### 제목

맨 처음 프로젝트를 생성하고 들어가면 다음과 같이 되어 있다.

```swift
import SwiftUI

struct ContentView: View {
    var body: some View {
        Text("Hello, world!")
            .padding()
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}

```

여기서 `ContentView` 구조체 안의 내용을 채워 주면, 우리가 만드려는 투두 리스트가 된다.

[ContentView]() 에 대해서는 따로 정리해야겠다!

아무튼, 저 안에 내가 원하는 제목 텍스트를 적어 준다.

```swift
struct ContentView: View {
    var body: some View {
        Text("What to do Today?")
    }
}
```

이렇게 하면 기본적인 텍스트만 달랑 생기기 때문에 제목답게 만들어주었다.

```swift
Text("What to do Today?")
    .font(.title.bold())
```

뷰의 속성? 세부사항이나 기능을 추가로 해줄 수 있는 `modifier` 를 사용하면 된다. `Text` 뷰의 폰트 속성을 `title` 로 주고, `bold` 로 글자를 강조했다.

![1](/assets/images/swift-todo-1/1.png)


### 텍스트 필드

다음으로, 제목 아래에 할 일을 적을 텍스트 필드를 만든다.

구글에 'SwiftUI write text and save data' 뭐 이런 식으로 검색했던 것 같다. 어떤 기능 혹은 모양의 뷰를 만들고 싶은데 이름을 모르겠으면 그 기능 혹은 모양을 서술해서 **영어로** 검색하면 스택오버플로우 혹은 애플 디벨로퍼스 행님들이 친절하게 알려 준다.

어쨌든, 텍스트를 입력하고 입력한 텍스트를 저장할 수 있는 뷰는 `TextField` 이다. Xcode 나 Apple Develope 홈페이지에 들어가서 키워드를 검색하면 다음과 같은 정의가 나온다.

![textField](/assets/images/swift-todo-1/2.png)

대충 라벨을 가지고, 값을 바인딩 할 수 있는 텍스트를 담는 그릇이라는 말이다.

입력되는 것이 문자열이라면 입력됨과 동시에 업데이트가 되고, 숫자형과 같은 다른 타입이라면 리턴을 누르거나 하는 액션이 생길 때 업데이트가 된다고 한다. 여기에서 텍스트필드의 인자로 라벨을 나타내는 문자열, 그리고 값을 바인딩(저장하고 뷰를 업데이트할)할 변수가 필요할 거라 유추하여 아래와 같이 코드를 추가했다.

```swift
struct ContentView: View {
    
    @State private var toDoString = ""
    
    var body: some View {
        VStack {
            Text("What to do Today?")
                .font(.title.bold())
            TextField("your task", text: $toDoString)
        }
    }
}
```

어 뭐야 갑자기 급발진!!! 없던 `Vstack`, `@State` 이런 것들이 생겼기 때문이다.

우선 `TextField`만 보면, 첫 번째 인자로 문자열이 들어가고, 두 번째 인자로 변수로 추정되지만 이상한 문자가 붙은 애가 들어간다. 첫 번째 인자로 들어간 문자열은 텍스트 필드의 `라벨` 이다. 텍스트 필드에 입력이 없을 때 기본적으로 띄워 주는 메시지라고 생각하면 된다. 할 일을 적는 란이기 때문에 나는 해당 란에 "your task" 라고 적어 줬다.

두 번째 인자로 들어간 `$toDoString` 은 예상했다시피 변수다. 앞서 `TextField`가 값을 바인딩할 수 있다는 정의를 생각 해 보면, 이 변수는 텍스트필드에 입력되는 문자열을 실시간으로 받아 저장해 둔다. `$` 표시는 **값을 쓸 수 있다**는 것을 의미한다. 이 `toDoString`이 선언된 곳을 보면, 단순히 `var toDoString` 이라고 선언되어있지 않고 `@State` 라는 키워드가 붙어 있는 것을 확인할 수 있다.

우리가 지금 코드를 써 넣는 `ContentView` 는 **구조체**다. 구조체는 `값 타입`으로, `참조 타입` 인 클래스와 다르게 외부에서 값을 변경할 수 없다. 하지만, **`@State`** 키워드를 사용하면 해당 구조체 내에서 값을 변경하고, 이를 새로 업데이트 할 수 있게 된다. `@State` 는 해당 변수 즉 해당 프로퍼티 값이 쭉 변할 수 있다는 것을 의미한다. 또한, `@State`가 붙은 프로퍼티는 프로퍼티가 속해 있는 뷰가 사라지기 전까지 쭉 존재하며, 프로퍼티의 **상태가 변화하면 자동으로 뷰를 리로드**한다.

> [`값 타입`과 `참조 타입`](https://2unbini.github.io/swift/swift-struct-class-translate)
> 값 타입은 변수의 `복제본`, 참조 타입은 변수의 `원본`이라고 할 수 있다.
> 값 타입은 복제본이기 때문에 말 그대로 원본에 영향을 주지 않는다. 하지만 참조 타입은 원본 그 자체이므로, 다른 이름의 변수로 만들고 그 값을 바꾸면 원본 자체가 바뀐다.
> Swift에서 구조체와 열거형 및 모든 기본 타입(배열, 딕셔너리, 문자열 튜플 등)은 값 타입이고, 클래스는 참조 타입이다.


결론적으로, `@State` 는 특정한 뷰 안에서 어떤 상태를 업데이트하고 그에 따라 뷰를 리로드하고자 할 때 사용되는 것이다. `TextField` 뷰 내부적으로 바인딩 할 변수를 입력받고, 투두 리스트의 입력에 따라 리스트를 업데이트 할 예정이므로 `@State` 키워드를 사용한 것이다.

다음으로 `VStack` 은 쉽게 말해 Vertical Stack, 세로(수직) 스택이다. 결과물을 보면 알 수 있듯, **뷰들을 세로로 정렬**하고 싶을 때 해당 뷰를 사용한다.

이와 관련해서 `HStack`, `ZStack`이 있는데, 각각 가로(수평) 스택, Z 축을 기준으로 중첩되는 스택이다.

![3](/assets/images/swift-todo-1/3.png)

이렇게만 하면 뭔가 안 예쁘니까 텍스트 필드를 조금 가공 해 주었다.

```swift
struct ContentView: View {
    
    @State var toDoString = ""
    
    var body: some View {
        VStack {
            Text("What to do Today?")
                .font(.title.bold())
            HStack {
                Image(systemName: "square.and.pencil")
                TextField("your task", text: $toDoString)
            }
			.textFieldStyle(DefaultTextFieldStyle())
            .frame(width: 300, height: 50, alignment: .center)
        }
    }
}
```

`VStack`의 반대 격인 `HStack`를 이용해 텍스트 필드와 이미지를 가로 정렬 시켰고, 그에 대한 크기를 `.frame` 모디파이어로 설정 해 주었다.

`Image` 뷰의 인자로 이미지 경로가 들어가면, 해당 경로에 있는 이미지가 띄워지는데 저기서처럼 `systemName`을 이용하면 시스템에 저장된 특정한 아이콘 이름을 통해 아이콘의 이미지를 불러올 수 있다. `systemName` 의 목록을 확인하고 싶다면, 애플에서 제공하는 [**SF Symbol**](https://developer.apple.com/sf-symbols/) 을 다운받아 사용하면 된다.

이렇게 하면 왼쪽에 뭔가 이미지가 붙고, 300 * 50 의 크기를 가지며, 중앙 정렬이 돼 있는 텍스트 필드가 생성된다.

![4](/assets/images/swift-todo-1/4.png)


### 다음은?

제목과 텍스트필드까지 만들었다. 다음은 실제 투두가 표시되는 [리스트 화면을 구현](https://2unbini.github.io/swift/swift-todolist-2)해 볼 차례다.

<details>
<summary>📍 전체 소스코드 </summary>
<div markdown="1">

```swift
import SwiftUI

struct ContentView: View {
    
    @State var toDoString = ""
    
    struct TodoList: Identifiable {
        let id = UUID()
        var content: String
    }
    
    private var todoLists = [
        TodoList(content: "SwiftUI 공부하기"),
        TodoList(content: "블로그 글 쓰기"),
        TodoList(content: "백준 문제 풀기")
    ]
    
    var body: some View {
        VStack {
            Text("What to do Today?")
                .font(.title.bold())
            
            HStack {
                Image(systemName: "square.and.pencil")
                    TextField("your task", text: $toDoString)
            }
            .textFieldStyle(DefaultTextFieldStyle())
            .frame(width: 300, height: 50, alignment: .center)
        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```

</div>
</details>

잘못된 정보나 더 나은 방향이 있다면 언제든 댓글 주세용!