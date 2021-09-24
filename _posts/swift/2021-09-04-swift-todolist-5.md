---
title: "SwiftUI 로 Todo list 만들기 5"
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
last_modified_at: 2021-09-04
---

# 뷰 쪼개기

[전 편](https://2unbini.github.io/swift/swift-todolist-4)에서 투두 리스트를 JSON 파일 형식으로 저장하고, 불러올 수 있도록 만들어 봤다. 기능적으로는 원하는 바를 일단 달성했기 때문에 `ContentView`에 한 무더기로 뭉쳐 있는 뷰들을 기능에 따라 나누어 조금 더 가독성 있게 만들어보려 한다.

나의 투두 리스트의 여러 뷰들은 다음과 같이 쪼갤 수 있을 것 같다.

- 제목
- 할 일 적는 란
- 투두 리스트
- 저장, 불러오기 버튼

이렇게 네 가지 뷰로 나눈 뒤, 각각을 서로 다른 객체로 만들어 정리해볼 것이다.

스위프트에선 따로 `export`나 `import`를 할 것 없이 그냥 같은 프로젝트 파일 안에 같은 이름으로 정의 해 놓으면 알아서 연결 해 주기 때문에 그냥 나눠 주면 된다. 그 대신, 우리가 나눌 것은 `뷰`이기 때문에, `View` 프로토콜을 따르고, `body`가 있는 형식을 지켜야 한다. Xcode 에서 기본적으로 `SwiftUI View` 파일 형식을 제공하기 때문에, 이를 사용해서 만들어주면 된다.


## View 그룹 만들기

네 개의 뷰를 쪼갤 것이기 때문에 뷰들을 한꺼번에 담아 정리할 그룹 폴더를 만들었다.

왼쪽 내비게이션 창에서 프로젝트 폴더를 선택한 뒤, 우클릭으로 `New Group`을 생성 해 준다. 나는 `View`라고 이름을 지었고, 이 안에 다시 우클릭으로 `New File`을 생성 해 준다. 팝업 창이 뜨며 생성할 수 있는 파일 형식들을 쭉 보여 주는데, 그 중 `USer Interface`의 `SwiftUI View`를 선택하면 된다.

![](/assets/images/swift-todo-5/1.png)


## 파일 쪼개기

쪼갤 뷰의 기능과 화면에 맞게 이름을 만들어준 뒤 파일을 생성하고, 본래 소스코드에서 해당 부분의 뷰와 함수를 잘라 붙여주고 그 자리에 이름에 맞는 뷰를 생성해 주면 끝이다.

### object

뷰를 쪼개기 전, 투두 리스트를 이루고 있는 `TodoList` 객체를 따로 떼어 별도의 파일로 분리했다. 뷰와 동일하게 객체도 따로 export 나 import 할 필요 없이 그냥 써 주면 된다. 나는 objects.swift 라는 파일을 만들어 거기에 구조체를 넣어줬다.

```swift
// objects.swift

import Foundation

struct TodoList: Identifiable, Codable {
    var id = UUID()
    var content: String
    var checked: Bool
}
```

### HeaderView()

가장 쉬운 애부터 시작 해 줬다. 별다른 함수나 변수를 사용하지 않고 단순히 `Text` 뷰에 해당하는 제목을 잘라내 `HeaderView` 파일에 붙여넣어줬다.

```swift
// HeaderView.swift

import SwiftUI

struct HeaderView: View {
    var body: some View {
        Text("What to do Today?")
            .font(.title.bold())
    }
}

struct HeaderView_Previews: PreviewProvider {
    static var previews: some View {
        HeaderView()
    }
}
```

### TextFieldView()

이번엔 함수와 변수가 사용되는 텍스트 필드 뷰다. 우선 여기서 사용된 변수와 함수가 어디까지 영향을 끼치는지 알아봤다.

1. toDoString
	- 텍스트 필드 안의 문자열을 임시적으로 담아두는 변수 => 텍스트 필드 뷰 안에서만 필요 
	- 이 변수를 이용해 `TodoList` 인스턴스를 만들어 줘야 한다. => 전체 뷰 모두에서 공유

2. appendList()
	- toDoString으로 만든 인스턴스를 `todoLists` 배열에 추가 => 전체 뷰 모두에서 공유하는 변수

`toDoString`은 텍스트필드 뷰 안에서만 사용할 수 있도록 `@State private var`로 선언 해 주면 될 것 같은데, `todoLists`는 모든 뷰에서 공유해야 하기 때문에 해당 뷰 안에서만 운용할 수 있는 `@State`로는 부족하다. 그래서 구글에 모든 뷰에서 사용할 수 있는 변수로 검색했고, 그 결과 `@StateObject`를 알게 되었다.

## @StateObject

`@StateObject`는 SwiftUI 2가 나오면서 새로 추가된 객체로, `observable object`를 생성할 수 있는 프로퍼티 래퍼라고 한다. 과연 이게 뭐라는걸까...? 정의를 보면 솔직히 잘 모르겠다. 개요를 보자..!

![](/assets/images/swift-todo-5/2.png)

`@StateObject`를 사용해 `ObservableObject` 프로토콜을 따르는 객체를 뷰, 앱, 혹은 씬에 생성할 수 있으며, 변수의 속성이 변화할 때마다 뷰를 업데이트해준다.

![](/assets/images/swift-todo-5/3.png)

`environmentObject(_ :)` 수정자를 이용해 뷰 계층에 객체를 추가할 수 있고, 이와 같이 `environment object`를 생성하면 `ContentView` 혹은 그 아래에 있는 모든 뷰에 해당 객체를 사용할 수 있다. 이 때, 객체에 `@EnvironmentObject` 키워드를 붙여주면 된다.

가장 상위에 `ObservableObject` 프로토콜을 따르는 객체를 생성한 뒤, 해당 객체를 사용하고 싶은 하위 뷰에 수정자를 통해 넘겨주고, 객체를 사용하고 싶은 뷰에서 `EnvironmentObject` 키워드를 써서 정의한 뒤 사용해주면 모든 뷰에서 하나의 변수를 사용하고, 이를 통해 뷰를 바꿔줄 수 있는 것이다.

그래서 나는 `todoLists` 배열을 `@StateObject`로 선언하고, ContentView 그리고 그 아래에 있는 모든 뷰에서 사용할 수 있게 적절히 `@EnvironmentObject`로 선언해 사용하려고 한다.

### 객체 추가하기

단순히 `todoList` 객체를 ObservableObject로 바꿔주면 될 거라고 생각했지만, 슬프게도 `ObservableObject`는 클래스에만 적용할 수 있었다. 그래서 `todoLists` 배열 자체를 클래스로 만들어 `ObservableObject` 프로토콜을 따르게 했다.

```swift
// objects.swift

class TodoLists: ObservableObject {
	@Published var list: [TodoList]()
}
```

`@Published`는 변수가 바뀌었으니 뷰를 리로드해야한다는 걸 전달해주는 속성 래퍼라고 한다. 우리가 사용할 StateObject가 '어 이거 바뀌었는데?' 하면 Published가 'ㅇㅋ objectWillChange.send()' 해주는 과정이라고 한다. 나는 각 뷰에서 투두리스트 배열이 수정될 때마다 뷰를 리로드해줘야 하기 때문에 이같은 방식으로 구성해주었다.

```swift
// todoListApp.swift

import SwiftUI

@main
struct todoListApp: App {
    
    @StateObject var todoLists = TodoLists()
    
    var body: some Scene {
        WindowGroup {
            ContentView().environmentObject(todoLists)
        }
    }
}
```

`ContentView`의 외부에 있는 `App`에 위와 같이 선언해 주고, 하위 뷰에서 해당 변수를 쓸 수 있도록 넘겨주었다. 이제 저 변수를 필요로 하는 뷰에서 새로 선언 해 주고, 내가 지금까지 쓰던 `todoLists`를 `todoLists.list`로 바꾸어주면 된다.

### 다시 TextFieldView()

`ContentView`에 있는 텍스트필드 뷰 부분, toDoString, appendList()를 모두 가져와 새로 만든 TextFieldView.swift에 붙여넣어준다. 이 뷰에서는 `todoLists` 객체를 사용하기 때문에, `@EnvironmentObject`로 변수를 선언해 주고, 그와 동시에 `todoLists`를 `todoLists.list`로 바꾸어줬다.

```swift
// TextFieldView.swift

import SwiftUI

struct TextFieldView: View {
    
    @State var toDoString = ""
    @EnvironmentObject var todoLists : TodoLists
    
    var body: some View {
        // 이전에 했던 것과 동일
    }
    
    func appendList() {
        let inputList = TodoList(content: toDoString, checked: false)
        
        // todoLists가 todoList 배열 그 자체에서
        // todoList 배열 list를 갖는 객체가 되었기 때문에 수정
        todoLists.list.append(inputList)
        toDoString = ""
     }
}
```

### TodoListView()

TextFieldView()와 동일하게, 리스트를 보여주는 뷰도 `todoLists`를 필요로 하기 때문에 동일하게 선언 해 주고 이름을 바꾸어주면 된다.

```swift
// TodoListView.swift

import SwiftUI

struct TodoListView: View {
    
    @EnvironmentObject var todoLists: TodoLists
    
    var body: some View {
        List {
            // todoLists -> todoLists.list
            ForEach(0..<todoLists.list.count, id: \.self) { i in
                HStack {
                    Button(action: {
                            toggleCheckedState(i)
                        },
                        label: {
                        Image(systemName:
                                // todoLists -> todoLists.list
                                todoLists.list[i].checked == true ?
                            "checkmark.square" :
                            "square")
                        }
                    )
                    // todoLists -> todoLists.list
                    Text(todoLists.list[i].content)
					
	// 아래 코드 생략 ...
    }
    
    // todoLists -> todoLists.list
    func toggleCheckedState(_ i: Int) {
        todoLists.list[i].checked.toggle()
    }
    
    func deleteList(_ i: Int) {
        todoLists.list.remove(at: i)
    }
}
```

### SaveLoadButtonView()

이 것 역시 위의 두 뷰와 동일하게 진행 해 주면 된다.

```swift
// SaveLoadButtonView.swift

import SwiftUI

struct SaveLoadButtonView: View {
    
    @EnvironmentObject var todoLists: TodoLists
    
    var body: some View {
        // 동일
    }
    
    func saveTodoList() {
        // 동일
    }
    
    func loadTodoList() {
        
        let path = getDocumentPath().appendingPathComponent("todolist.json")
        
        do {
            let jsonString = try String(contentsOf: path)
            let jsonData = jsonStringToData(jsonString)
            
            if jsonData == nil {
                print("Error: No JSON Data found")
                return
            }
            
            // todoLists -> todoLists.list
            todoLists.list = jsonData!
        }
        catch {
            print(error.localizedDescription)
        }
    }
    
    func dataToJsonString() -> String? {
        
        let encoder = JSONEncoder()
        encoder.outputFormatting = .prettyPrinted

        do {
            // todoLists -> todoLists.list
            let data = try encoder.encode(todoLists.list)
            return String(data: data, encoding: .utf8)
        }
        catch {
            print(error.localizedDescription)
        }
        
        return nil
    }

    func getDocumentPath() -> URL {
        // 동일
    }
    
    func jsonStringToData(_ jsonString: String) -> [TodoList]? {
        // 동일
    }
}
```

## ContentView에 채워넣기

모든 뷰들이 쪼개졌으니, 이를 ContentView에 순서대로 사용해주면 된다.

```swift
// ContentView.swift

import SwiftUI

struct ContentView: View {
    
    @EnvironmentObject var todoLists : TodoLists
    
    var body: some View {
        VStack {
            HeaderView()
            TextFieldView()
            TodoListView()
            SaveLoadButtonView()
        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView().environmentObject(TodoLists())
    }
}
```

뷰를 나눠줬더니 한결 깔끔하게 정리가 됐다.

View 프로토콜을 따르는 ContentView 아래에 있는 `ContentView_Peviews` 는 말 그대로 프리뷰다. 이를 이용해 에디터 창 옆에 캔버스로 앱을 미리 볼 수 있다. 시뮬레이터를 실행시키지 않고 바로 프리뷰로 투두리스트를 확인하려면, `.environmentObject()`를 사용해 `TodoLists` 객체를 넘겨줘야 한다. 시뮬레이터만 써도 된다면 굳이 할 필요 없다.

잘 작동되는지 확인 해 보면..!!

![](/assets/images/swift-todo-5/4.gif)

다행히 잘 된다 :)

## 다음은?

이렇게 쪼개는 게 맞는가 싶긴 한데 여튼 ContentView에 뭉텅이로 있던 뷰들을 각 기능에 따라 분리해줬다.

다음은 단순히 버튼을 눌러 내부적으로 json 파일을 저장했던 것을 확장해 `api`로 서버에서 json 파일을 저장하고, 불러오는 것을 해 볼 것이다! (서버와 데이터베이스는 내가 하지 않고, 백엔드 천재 친구와 협업했다.)

### 소스코드

<details>
<summary>📍 전체 소스코드 </summary>
<div markdown="1">

```swift
// objects.swift

import Foundation

struct TodoList: Identifiable, Codable {
    var id = UUID()
    var content: String
    var checked: Bool
}

class TodoLists: ObservableObject {
	@Published var list: [TodoList]()
}
```

```swift
// todoListApp.swift

import SwiftUI

@main
struct todoListApp: App {
    
    @StateObject var todoLists = TodoLists()
    
    var body: some Scene {
        WindowGroup {
            ContentView().environmentObject(todoLists)
        }
    }
}
```

```swift
// ContentView.swift

import SwiftUI

struct ContentView: View {
    
    @EnvironmentObject var todoLists : TodoLists
    
    var body: some View {
        VStack {
            HeaderView()
            TextFieldView()
            TodoListView()
            SaveLoadButtonView()
        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView().environmentObject(TodoLists())
    }
}
```

```swift
// HeaderView.swift

import SwiftUI

struct HeaderView: View {
    var body: some View {
        Text("What to do Today?")
            .font(.title.bold())
    }
}

struct HeaderView_Previews: PreviewProvider {
    static var previews: some View {
        HeaderView()
    }
}
```

```swift
// TextFieldView.swift

import SwiftUI

struct TextFieldView: View {
    
    @State var toDoString = ""
    @EnvironmentObject var todoLists : TodoLists
    
    var body: some View {
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
    }
    
    func appendList() {
        let inputList = TodoList(content: toDoString, checked: false)
        
        todoLists.list.append(inputList)
        toDoString = ""
     }
}

struct TextFieldView_Previews: PreviewProvider {
    static var previews: some View {
        TextFieldView()
    }
}
```

```swift
// TodoListView.swift

import SwiftUI

struct TodoListView: View {
    
    @EnvironmentObject var todoLists: TodoLists
    
    var body: some View {
        List {
            ForEach(0..<todoLists.list.count, id: \.self) { i in
                HStack {
                    Button(action: {
                            toggleCheckedState(i)
                        },
                        label: {
                        Image(systemName:
                                todoLists.list[i].checked == true ?
                            "checkmark.square" :
                            "square")
                        }
                    )
                    Text(todoLists.list[i].content)
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
    
    func toggleCheckedState(_ i: Int) {
        todoLists.list[i].checked.toggle()
    }
    
    func deleteList(_ i: Int) {
        todoLists.list.remove(at: i)
    }
}

struct TodoListView_Previews: PreviewProvider {
    static var previews: some View {
        TodoListView()
    }
}
```

```swift
// SaveLoadButtonView.swift

import SwiftUI

struct SaveLoadButtonView: View {
    
    @EnvironmentObject var todoLists: TodoLists
    
    var body: some View {
        HStack(spacing: 50) {
            Button("Save", action: saveTodoList)
                .padding(.all, 10)
            Button("Load", action: loadTodoList)
                .padding(.all, 10)
        }
        .buttonStyle(BorderlessButtonStyle())
    }
    
    func saveTodoList() {
        
        let path = getDocumentPath().appendingPathComponent("todolist.json")
        let jsonString = dataToJsonString()
        
        if jsonString == nil {
            print("Error: No JSON String found")
            return
        }
        
        print(path.absoluteString)
        
        do {
            try jsonString?.write(to: path, atomically: true, encoding: .utf8)
        }
        catch {
            print(error.localizedDescription)
        }
    }
    
    func loadTodoList() {
        
        let path = getDocumentPath().appendingPathComponent("todolist.json")
        
        do {
            let jsonString = try String(contentsOf: path)
            let jsonData = jsonStringToData(jsonString)
            
            if jsonData == nil {
                print("Error: No JSON Data found")
                return
            }
            
            todoLists.list = jsonData!
        }
        catch {
            print(error.localizedDescription)
        }
    }
    
    func getDocumentPath() -> URL {
        return FileManager.default.urls(for: .documentDirectory, in: .userDomainMask)[0]
    }
    
    func dataToJsonString() -> String? {
        
        let encoder = JSONEncoder()
        encoder.outputFormatting = .prettyPrinted

        do {
            let data = try encoder.encode(todoLists.list)
            return String(data: data, encoding: .utf8)
        }
        catch {
            print(error.localizedDescription)
        }
        
        return nil
    }
    
    func jsonStringToData(_ jsonString: String) -> [TodoList]? {
        
        let decoder = JSONDecoder()
        let jsonData = jsonString.data(using: .utf8)
        
        if jsonData == nil {
            print("Error: Cannot convert json String to Data")
            return nil
        }
        
        do {
            let gotList = try decoder.decode([TodoList].self, from: jsonData!)
            return gotList
        }
        catch {
            print(error.localizedDescription)
        }
        
        return nil
    }
}

struct SaveLoadButtonView_Previews: PreviewProvider {
    static var previews: some View {
        SaveLoadButtonView()
    }
}
```

</div>
</details>

잘못된 정보나 더 나은 방향이 있다면 언제든 알려 주세요!