---
title: "SwiftUI 로 Todo list 만들기 3"
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
last_modified_at: 2021-09-03
---

## 액션 적용하기

[전 편](https://2unbini.github.io/swift/swift-todolist-2)에서 투두 리스트의 모든 화면을 구성 해 봤다. 이번에 할 것은 화면으로만 존재했던 뷰들의 액션을 정의하는 일이다. 내가 정의할 액션은 세 가지다.

1. 텍스트 필드에 입력한 문자열을 `저장`
2. 완료된 항목에 대해 `체크` 표시
3. 지우고자 하는 항목 `삭제`

우리는 앞서 텍스트 필드에 입력된 값을 임시로 저장했고, 임의로 TodoList 객체 배열을 만들어 리스트에 보여지게 했다. 이를 조금씩 변형하면 액션을 만들 수 있을 것 같다.

### 객체 수정하기

화면 구성을 할 땐 몰랐지만 액션을 적용하려고 보니 투두 요소에 속성이 하나 추가로 필요했다. 완료된 항목에 대한 `체크` 를 하려면, 해당 객체가 체크됐는지 안됐는지에 대한 정보가 필요했기 때문이다. 그래서 객체를 먼저 수정하기로 했다.

```swift
struct todoList: Identifiable {
	let id = UUID()
	var content: String
	var checked: Bool
}
```

`checked` 프로퍼티를 추가해 이를 통해 해당 항목이 체크됐는지 아닌지를 판별할 것이다.

### 텍스트 필드의 문자열 저장

`toDoString` 변수에 저장했던 문자열을 `TodoList` 객체로 만들어 해당 객체의 배열에 추가해주면 텍스트 필드의 문자열이 배열의 형태로 저장될 것이다.

이를 위해 우선, `TodoList` 객체의 배열 `todoLists`를 다시 정의 해 준다.

```swift
private var todoLists = [TodoList]()
```

`TodoList` 객체로 이루어진 배열 `todoLists` 가 생성됐다. 이 배열의 요소로 텍스트 필드에 입력된 값을 넣을 것이므로 `toDoString`의 값을 `content`로 갖는 객체를 생성해 줘야 한다. 그런데, 이 객체는 사용자가 텍스트필드에 값을 입력한 직후 생성된 후 추가되어야 하므로 동작을 하는 함수 안에서 생성하도록 했다.

객체를 생성하고, 생성된 객체를 배열에 추가하고, 텍스트필드에 있는 값을 초기화하는 동작을 하는 함수를 만들었다.

```swift
func appendTodoList() {
	let addList = todoList(content: toDoString, checked: false)

	todoLists.append(addList)
	toDoString = ""
}
```

이렇게 우선 함수를 정의 해 주면, `todoLists.append(addList)` 부분에서 오류가 뜬다.

> Cannot use mutating member on immutable value

라고 하며, 자동으로 fix 된 코드를 알려 준다. 이를 fix 하면 `func` 앞에 `mutating` 키워드가 붙는데, 이대로 진행하게 되면 다시 에러를 마주하게 된다.

`append()` 함수를 `todoLists`라는 **구조체 배열**에 사용했다. 구조체는 **`값 타입`** 이므로, 프로퍼티의 값을 인스턴스의 메소드 안에서 바꿀 수 없다.

그런데 이를 가능하게 하는 키워드가 있는데 이 것이 **`mutating`** 이다. 그래서 Xcode 는 해당 함수가 정의된 인스턴스를 `todoList` 구조체로 인식하고 함수에 `mutating` 키워드를 붙여 준 것이다.

하지만 우리가 함수를 정의한 곳은 전체 `ContentView` 라는 구조체 안이다. 지금으로선 `TextField`의 행동으로 함수를 사용하지 않아서 별다른 오류가 더 뜨지 않지만, 만약 함수를 사용하게 되면 다시 동일한 에러가 뜰 것이다. 결국 우리가 바꾸려는 요소는 `ContentView`의 프로퍼티이기 때문이다.

그럼 어쩌냐?! 과거에 썼던 **`@State`** 키워드를 사용하면 된다. 우리가 수정하고자 하는 값은 `todoLists` 배열이므로, 다음과 같이 수정해 주었다.

```swift
@State private var todoLists = [TodoList]()
```

이로써 우리는 바꿀 수 없는 `값 타입`의 인스턴스 프로퍼티를 수정할 수도 있으면서, 자동으로 배열에 요소가 추가될 때마다 뷰를 리로드해줄 수 있게 됐다.


### 텍스트필드에 액션 지정

동작을 하는 함수를 정의했으니, 해당 함수가 실행되는 시점? 순간? 동작?을 지정해주면 된다.

나는 텍스트필드의 입력이 끝나고 키보드의 `Return`을 누르면 해당 액션이 실행되도록 했다.

```swift
TextField(
	"your task",
	text: $toDoString,
	onCommit: {
        appendList()
	}
)
```

라벨과 바인딩할 변수 이외에 `onCommit`이 추가됐다. 이는 텍스트필드에 있는 문자열이 `Return`키를 누르면 `{}` 안의 동작을 하게 한다. 이렇게 해 주면 텍스트 필드에 값을 적고, 리턴(엔터)을 누르면 값이 배열에 저장된다.

![1](/assets/images/swift-todo-3/1.gif)


### 완료된 항목 체크 표시하기

다음은 리스트의 각 할 일 항목의 상태를 변경해 텍스트 앞의 네모 버튼을 누르면 체크 표시가 되도록 하는 액션을 만들어봤다.

나는 따로 체크박스나 이런 요소를 사용하지 않고, 비어 있는 사각형 이미지를 사용했기 때문에 할 일이 완료되어 버튼을 누르면, 사각형에 체크가 된 이미지로 교체되도록 코드를 짰다.

우선, 버튼이 눌렸을 때 해당 항목의 `checked` 프로퍼티가 바뀌어야 하므로 이를 실행하는 함수를 만들었다.

```swift
func toggleCheckedState(_ i: Int) {
	todoLists[i].checked.toggle()
}
```

리스트의 각 항목을 `ForEach`로 각각 보여 주고 있기 때문에, 각 리스트의 위치를 나타내는 인덱스 `i`를 활용해 `checked`를 반대로 바꿔줬다. 이렇게만 하면 내부적으로만 상태가 변하기 때문에, 외부 상태를 바꿔줄 수 있도록 새로운 변수를 만들어 체크 된 상태에 따라 이미지의 `systemName`을 바꿔주었다.

```swift
Button(
	action: {
		toggleCheckedState(i)
	},
	label: {
		Image(systemName:
			todoLists[i].checked == true
			? "checked.square"
			: "square"
		)
	}
)
```

다른 방법으로 `@State` 변수를 만들어 함수 내에서 바꿔주는 것도 생각해 봤는데, 변수 하나를 늘리는 것 보다 직관적으로 보여주는 게 낫지 않나 싶어서 저렇게 했다. 뭐가 맞는 진 모르겠다...

![2](/assets/images/swift-todo-3/2.gif)

### 항목 삭제하기

쓰레기통 버튼을 누르면 해당 항목이 리스트 뷰와 `todoLists` 배열에서 삭제되는 것을 구현해봤다. 앞서 `checked` 상태를 변화시키는 함수와 동일하게 인덱스를 가져와서 해당 항목을 삭제해주는 방식으로 만들었다.

```swift
func deleteList(_ i: Int) {
	todoLists.remove(at: i)
}
```

```swift
Button(
	action: {
		deleteList(i)
	},
	label: {
		Image(systemName: "trash")
	}
)
```

함수를 삭제 버튼에 적용시켜주면 다음과 같이 동작한다.

![3](/assets/images/swift-todo-3/3.gif)

### 버그 발견,,,

마지막으로 앱을 실행시켜봤다. 그런데 버그를 발견했다... ㅜㅜ

삭제 버튼을 맨 마지막에 만들어서 모든 것이 잘 되는 것처럼 보였지만, 사실은 `HStack` 안의 두 버튼이 충돌하고 있었다. 삭제 버튼을 눌러도 삭제가 되고, 체크박스 버튼을 눌러도 삭제가 됐다.

![4](/assets/images/swift-todo-3/4.gif)

별 키워드를 다 써가며 검색한 끝에, 나와 정확히 [동일한 증상(HStack 안의 모든 버튼이 실행되는 오류)을 겪은 사람의 글](https://www.hackingwithswift.com/forums/swiftui/tap-button-in-hstack-activates-all-button-actions-ios-14-swiftui-2/2952)을 찾게 되었다.

각 버튼 뷰에 `.buttonStyle()`을 적용해 주었고, `BorderlessButtonStyle()`을 상세 값으로 넣어 주었다. 해당 수정자는 각 버튼 뷰에 적용해도 되고, 버튼 뷰를 감싸고 있는 HStack 에 적용해도 된다. 나는 HStack 에 넣어줬다.

```swift
HStack {
	Button(
		action: {
			toggleCheckedState(i)
		},
		label: {
			Image(systemName:
				todoLists[i].checked == true
				? "checked.square"
				: "square"
			)
		}
	)
	Text(todoLists[i].content)
	Spacer()
	Button(
		action: {
			deleteList(i)
		},
		label: {
			Image(systemName: "trash")
		}
	)
}
.buttonStyle(BorderlessButtonStyle())
```

![5](/assets/images/swift-todo-3/5.gif)

잘 동작한다..!!

### 다음은?

화면 구성 + 동작까지 할 수 있게 됐다.

그런데 이 앱의 가장 큰 단점은 투두 리스트가 저장되지 않는다는 것이다. 기억하기 위해서 투두 리스트가 존재하는 건데 앱을 켜고 끌 때마다 정보가 리셋되면 의미가 없을 테니! 다음은 `save`와 `load` 버튼을 만들어 투두 리스트 객체를 `JSON` 파일로 저장하고, 불러와 리스트에 업데이트할 수 있도록 해 볼 것이다.

<details>
<summary>📍 전체 소스코드 </summary>
<div markdown="1">

```swift
import SwiftUI

struct ContentView: View {
    
    struct TodoList: Identifiable {
        let id = UUID()
        var content: String
        var checked: Bool
    }
    
    @State var toDoString = ""
    @State private var todoLists = [TodoList]()
    
    var body: some View {
        VStack {
            Text("What to do Today?")
                .font(.title.bold())
            
            HStack {
                Image(systemName: "square.and.pencil")
                TextField(
                    "your task",
                    text: $toDoString,
                    onCommit: {
                        appendList()
                    }
                )
            }
            .textFieldStyle(DefaultTextFieldStyle())
            .frame(width: 300, height: 50, alignment: .center)
            
            List {
                ForEach(0..<todoLists.count, id: \.self) { i in
                    HStack {
                        Button(action: {
                                toggleCheckedState(i)
                            },
                            label: {
                            Image(systemName:
                                todoLists[i].checked == true ?
                                "checkmark.square" :
                                "square")
                            }
                        )
                        Text(todoLists[i].content)
                        Spacer()
                        Button(
                            action: {
                                deleteList(i)
                            },
                            label: {
                                Image(systemName: "trash")
                            }
                        )
                        
                    }
                    .buttonStyle(BorderlessButtonStyle())
                    
                }
            }
        }
    }
    
    func appendList() {
         let inputList = TodoList(content: toDoString, checked: false)
         todoLists.append(inputList)
         toDoString = ""
     }
    
    func toggleCheckedState(_ i: Int) {
        todoLists[i].checked.toggle()
    }
    
    func deleteList(_ i: Int) {
        todoLists.remove(at: i)
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

잘못된 정보나 더 나은 방향이 있다면 언제든 알려 주세요!