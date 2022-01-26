---
title: "SwiftUI 로 Todo list 만들기 4"
toc: true
toc_sticky: true
categories:
    - 📂 all
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

# 투두 리스트 저장하고 불러오기 with JSON

[전 편](https://2unbini.github.io/📂%20all/swift/swift-todolist-3/)에서 투두 리스트 액션을 정의 해 봤다. 이번 편에선 투두 리스트를 JSON 파일 형식으로 저장하고, 불러올 수 있도록 만들어 볼 것이다.

화면에 각각 `저장`하고 `불러오기` 할 수 있는 버튼을 만든 뒤, 각각의 액션으로 `JSON` 파일을 생성하고, 파싱하는 것을 추가해볼 예정이다.

## JSON이 뭐지?

`JSON(JavaScript Object Notation)`은 `속성-값 쌍` 또는 `키-값 쌍`으로 이루어진 데이터 오브젝트를 전달하기 위해 인간이 읽을 수 있는 텍스트를 사용하는 개방형 표준 포맷이다. 특히 인터넷에서 자료를 주고받을 때 그 자료를 표현하는 방법으로 알려져 있다. [[위키백과]](https://ko.wikipedia.org/wiki/JSON)

JSON은 이름에서도 유추할 수 있듯, 자바스크립트에서 `객체를 표현`하는 데 사용된 문법이다. 하지만 이 문법은 프로그래밍 언어와 플랫폼과 독립적이므로 자바스크립트 뿐만 아니라 우리가 사용하고 있는 스위프트, 파이썬, 자바, C, C++ 등 다양한 프로그래밍 언어에서 이용할 수 있다. 또한, JSON은 문자열의 형태로 저장돼 운용되므로 네트워크를 통해 자료를 전송하기 좋고, 사람이 읽고 이해하기 편하다는 장점이 있다.

문자열로 저장된 이 JSON 형식의 객체를 스위프트에서 사용하려면 가공이 필요하다. 문자열을 하나하나 읽어들이며 키는 키대로, 값은 값대로 저장하는 것은 우리에게 아주 힘든 일이겠지만...!

다행히 스위프트는 기본적으로 JSON 파일을 읽어들여 객체로 변환 해 주고, 객체를 JSON 형태의 문자열로 만들어주는 객체가 정의되어 있다. 이를 사용해서 우리는 투두 리스트를 JSON 파일로 저장하고, 저장된 파일을 불러와 투두 리스트 배열로 만들 것이다!

## 버튼 만들기

투두 리스트를 저장하고 불러오는 버튼이기 때문에 각각 `Save`와 `Load`로 라벨을 지정해 주었다.

```swift
HStack(spacing: 50) {
    Button("Save", action: )
        .padding(.all, 10)
    Button("Load", action: )
        .padding(.all, 10)
}
.buttonStyle(BorderlessButtonStyle())
```

각각의 버튼에 대해 10만큼의 패딩을 주고, 버튼 간 간격을 50만큼 줬다. 또, 앞에서 `HStack` 안에 버튼이 두 개 이상 있을 때 모든 버튼이 단 한가지의 액션을 하는 에러를 발견했기 때문에 `.buttonStyle(BorderlessButtonStyle())`로 이를 방지해 주었다.

![1](/assets/images/swift-todo-4/1.png)

## 액션 만들기 - Save

이제 `Save`와 `Load`버튼을 눌렀을 때 각각 `todoLists` 배열을 `JSON` 형식으로 변환해 저장하고, `JSON` 형식의 저장된 파일을 불러와 `todoLists` 배열로 만들어 주는 과정을 함수로 정의해주면 된다. 결론적으로 나는,

1. saveTodoList()
	- getDocumentPath()
	- dataToJsonString()
2. loadTodoList()
	- getDocumentPath()
	- jsonStringToData()

와 같이 함수를 만들 것이다.

### saveTodoList()

현재 뷰에 변수로 저장되어 있는 `todoLists` 배열을 `json` 데이터로 변환해 어딘가에 저장하려면 데이터를 저장할 `장소`와 저장할 `데이터`가 필요하다. 장소는 스위프트에 정의된 `FileManager`를, 데이터는 `JSONEncoder()`를 사용하면 된다.


### getDocumentPath()

![2](/assets/images/swift-todo-4/2.png)

공식 문서에 정의된 `FileManager`에 대한 내용이다. 이 객체를 통해 우리는 파일과 디렉토리를 위치시키고, 만들고, 복사하고, 움직이게 할 수 있고, 파일이나 디렉토리에 대한 정보를 얻거나 그 속성을 바꾸는 데 사용할 수 있다.

MacOS의 파일 시스템은 `도메인`에 따라 결정된다. `user`, `local`, `network`, `system` 도메인을 기본적으로 제공하며, 이 도메인에 따라 파일의 위치가 정해진다. 우리는 `FileManager` 클래스를 사용해 도메인에 따른 파일의 위치를 얻고, 그를 수정할 수 있게 되는 것이다.

나는 파일을 저장할 경로가 필요하기 때문에, 이를 통해 해당 경로를 가져올 것이다.

```swift
func getDocumentPath() -> URL {
	return FileManager.default.urls(for: .documentDirectory, in: .userDomainMask)[0]
}
```

공식 문서를 찾아 보니 `urls` 메소드는 요청된 도메인에 있는 지정된 디렉토리에 대한 `URL` 배열을 반환한다고 한다. 이 메소드의 첫 번째 인자엔 찾을 디렉토리의 경로를, 두 번째 인자엔 찾을 파일 시스템 도메인을 입력하면 된다고 쓰여 있다. 즉, **우리가 원하는 도메인 마스크와 디렉토리를 적으면, 그에 대한 `URL`들을 배열에 담아 리턴**한다는 것이다. `Document` 디렉토리는 하나이기 때문에 우리는 배열의 첫 번째 요소만 사용하면 된다.

솔직히 이 부분이 잘 이해가 안 돼서 공식 문서도 찾아 보고(불친절함), 여러 블로그 글도 읽어 봤는데 아직 명확하게 이해할 수 있는 글은 없었다 ㅜㅜ.. 이 부분은 조금 더 공부가 필요한 듯 싶다.


### dataToJsonString()

파일을 저장할 경로를 확보했으면, 다음으로 내가 할 일은 todoLists 배열을 JSON 형태의 문자열로 변환해 해당 경로에 .json 파일로 쓰는 것이다.

![3](/assets/images/swift-todo-4/3.png)

공식 문서에 `JSONEncoder()`를 검색하면 친절하게 어떻게 쓰는지에 대한 예시가 나와 있다. 이대로 따라하면 나만의 멋^^진 JSON 파일이 만들어진다.

여기서 주목할 점은, JSON으로 변환할 데이터가 **`Codable`** 프로토콜을 채택해야 한다는 점이다. 여기선 인코딩을, 그리고 파일을 불러올 땐 디코딩을 할 텐데, 그 때 모두 우리는 변환할 데이터가 `Codable`인 상태로 해야 한다. 그러므로 먼저, `todoList` 구조체에 `Codable` 프로토콜을 추가했다.

```swift
struct todoList: Identifiable, Codable {
	let id = UUID()
	var content: String
	var checked: Bool
}
```

바꿔주면, 에러는 아니지만 경고가 뜬다. 상수 프로퍼티는 초기값으로 선언되기 때문에 디코딩 되지 않는다는 에러다. `let`으로 선언한 id를 `var`로 고쳐주면 된다.

```swift
struct todoList: Identifiable, Codable {
	var id = UUID()
	var content: String
	var checked: Bool
}
```

이렇게 하고 난 뒤, 예시를 따라 1. 인코더를 만들고, 2. 인코더가 반환할 출력물이 예쁘도록 설정해주고, 3. 인코딩한 데이터를 받아, 4. 문자열로 만들어 줬다.

```swift
func dataToJsonString() -> String? {

    let encoder = JSONEncoder()
    encoder.outputFormatting = .prettyPrinted

    do {
        let data = try encoder.encode(todoLists)
        return String(data: data, encoding: .utf8)
    }
    catch {
        print(error.localizedDescription)
    }

    return nil
}
```

인코딩을 할 때 `try`를 하기 때문에 에러 상황에 대한 처리를 `catch`로 해 줬고, 정상적이지 않은 경우 `nil`이 리턴되도록 했다. 전체 함수와 합쳐 받아온 경로에 파일을 써 주었다.

### 다시 saveTodoList()

```swift
func saveTodoList() {
    
    let path = getDocumentPath().appendingPathComponent("todolist.json")
    let jsonString = dataToJsonString()
    
	// dataToJsonString() 함수의 반환형이 String? 이므로, nil인 경우에 대한 처리를 해 줬다.
    if jsonString == nil {
        print("Error: No JSON String found")
        return
    }
    
    do {
        try jsonString?.write(to: path, atomically: true, encoding: .utf8)
    }
    catch {
        print(error.localizedDescription)
    }
}
```

`write` 함수를 사용해 우리가 얻어 온 경로에 utf8 방식으로 인코딩했다. write 함수의 두 번째 인자는 유추하기 힘들었는데, 공식 문서를 보니 첫 번째 인자로 들어온 url 에 바로 쓸 것인지, 아님 보조로 만들어진 url 에 쓴 뒤, 이름을 바꿀 것인지에 대한 Bool 값이었다.

`true` 옵션은 쓰기를 진행하는 동안 어떤 충돌이 일어나도 첫 번째 인자로 준 url은 이상이 없도록 보장한다.

지금은 우리가 가져온 경로에 `json` 파일이 없기 때문에, 파일에 써 주려면 그에 해당하는 파일을 생성한 뒤 그에 대한 경로를 write의 인자로 넘겨줘야 한다. 그래서 `getDocumentPath()`를 통해 경로를 얻어내고, 거기에 `todolist.json`이라는 파일 경로를 확보한 뒤, 그 값을 write에 넘겨 주었다.

이렇게 하고, 위 Button의 액션에 `saveTodoList()`를 넣어 주면, `Save` 버튼을 누름과 동시에 `todolist.json`파일에 해당 리스트가 저장된다. 눈으로 확인하고 싶으면, saveTodoList 함수 안에 `print(path.absoluteString)`를 넣은 뒤, 시뮬레이터로 직접 실행해 json 파일이 저장된 경로로 가 보면 된다.

![4](/assets/images/swift-todo-4/4.png)


아주 깔-끔하게 저장된 것을 확인할 수 있다.

## 액션 만들기 - Load

Save와 정확히 반대의 순서로 진행하면 된다.

1. Json 파일을 받아 디코딩 해 주고,
2. 그 값을 뷰에 선언된 배열에 넣어 주기!

### getDocumentPath()

이 부분은 Save 와 동일하다. 패스!

### jsonStringToData()

특정 위치에 저장 해 놓은 `todolist.json` 파일의 위치를 찾고, 그 파일을 읽어 `todoLists` 배열로 만드는 작업이다. save 와 동일하게 `JSONDecoder`를 사용하면 된다.

```swift
func jsonStringToData(_ jsonString: String) -> [TodoList]? {
    
    let decoder = JSONDecoder()
    let jsonData = jsonString.data(using: .utf8)
    
    if jsonData == nil {
        print("Error: Cannot convert json String to Data")
        return nil
    }
    
    do {
        let returnList = try decoder.decode([TodoList].self, from: jsonData!)
        return returnList
    }
    catch {
        print(error.localizedDescription)
    }
    
    return nil
}
```

인코딩과 마찬가지로, 디코딩 역시 try-catch로 에러 처리를 해 주었고, 문자열 -> 데이터 -> 객체로 변환하는 과정을 거쳤다. 그 과정에서 옵셔널 변수들에 대한 바인딩도 해 주었다.

### loadTodoList()

전체 `loadTodoList()` 함수로 합치면 다음과 같이 완성된다.

```swift
func loadTodoList() {
    
    let path = getDocumentPath().appendingPathComponent("todolist.json")
    
    do {
        let jsonString = try String(contentsOf: path)
        let jsonData = jsonStringToData(jsonString)
        
        if jsonData == nil {
            print("Error: No Array found")
            return
        }
        
        todoLists = jsonData!
    }
    catch {
        print(error.localizedDescription)
    }
}
```

`url`을 가져오고, 그 경로에 있는 json 파일을 문자열로 변환하고, 그 문자열을 `jsonStringToData()`함수를 통해 `todoList` 배열로 만든 뒤, 해당 배열이 유효하면 뷰에서 사용하는 `todoLists` 배열에 할당하는 방식으로 액션을 구성했다.

버튼 액션으로 지정해 주면 잘 동작한다!

![5](/assets/images/swift-todo-4/5.gif)


## 다음은?

`Codable` 객체를 `JSON`형식으로 저장하고, 불러올 수 있게 됐다.

멋진 동작을 하는 투두 리스트지만, 막상 코드를 보면 상당히 난잡해 보였다. 그래서 [다음](https://2unbini.github.io/📂%20all/swift/swift-todolist-5/)으로 `ContentView`에 한꺼번에 모여 있는 여러 뷰들을 나누어 정리해보려고 한다.

### 소스코드

<details>
<summary>📍 전체 소스코드 </summary>
<div markdown="1">

```swift
import SwiftUI

struct ContentView: View {
    
    struct TodoList: Identifiable, Codable {
        var id = UUID()
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
            
            HStack(spacing: 50) {
                Button("Save", action: saveTodoList)
                    .padding(.all, 10)
                Button("Load", action: loadTodoList)
                    .padding(.all, 10)
            }
            .buttonStyle(BorderlessButtonStyle())
            
        }
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
            
            todoLists = jsonData!
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
            let data = try encoder.encode(todoLists)
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