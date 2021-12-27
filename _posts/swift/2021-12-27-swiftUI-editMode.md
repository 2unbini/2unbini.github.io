---
title: "SwiftUI editMode로 편집/삭제 버튼 만들기"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - swift
tags:
    - swift
    - 스위프트
    - SwiftUI
last_modified_at: 2021-12-27
---

# EditButton() 말고 custom Button을 만들어 사용하고 싶었다.

왜? 냐 하면...

편집 버튼을 눌렀을 때, 기본으로 지정되어 있는 `.onDelete`나 `.onMove`말고 다른 일들을 추가적으로 더 처리하고 싶었기 때문이다.

## editMode

그러려면 일단 버튼을 딱 눌렀을 때, editMode를 `.active`하게 바꿔주고, 버튼을 다시 눌렀을 때 `.inactive`하게 해 주면 된다.

SwiftUI에서 이 editMode는 `EnvironmentValue`로, `@Envrionment(\.editMode) var editMode`처럼 정의해서 변수처럼 쓰거나, `.environment(\.editMode)`와 같이 적용하고자 하는 뷰에 직접 사용할 수 있다.

> EnvironmentValues <br>
> SwiftUI는 몇 가지의 값을 앱의 뷰에 노출시키는데, 이 값을 읽어들이려면 `@Environment` 프로퍼티 래퍼와 그 값의 key path를 사용해 정의해주면 된다. <br>
> 이 프로퍼티는 뷰의 레이아웃을 동적으로 제어하기 위해 사용되며, 변경된 값은 하위 뷰를 포함한 뷰에 적용된다.

## 구현

다른 뷰로 넘어가는 NavigationLink를 editMode가 .active 되어 있을 땐 onTapGesture의 내용이 먹히도록 구현하고자 했다.

내비게이션 링크로 다른 뷰로 넘기는 것과 동시에, 현재 뷰에서 각 행에 대한 정보를 수정하고 삭제하고 싶었기 때문이다. 그래서 다음과 같이 구현했다.

```swift
struct ProjectListView: View {

    @Environment(\.editMode) var editMode: Binding<EditMode>?
    @State private var isEditing: Bool = false
    @State private var showAddSheet: Bool = false
    @State private var project: Project
    
    var body: some View {
        NavigationView {
            List {
                ForEach(projects, id: \.self) { project in
                    NavigationLink(destination: EmptyView()) {
                        Text(project.name)
                    }
                    .onTapGesture {
                        if isEditing {
                            self.project = project
                        }
                    }
                }
                .onDelete(perform: removeSelectedProject)
            }
            .toolbar {
                ToolbarItem(placement: .navigationBarLeading) {
                    Button(isEditing ? "완료" : "편집") {
                        editMode?.wrappedValue.toggle()
                        isEditing.toggle()
                    }
                }
                ToolbarItem(placement: .navigationBarTrailing) {
                    if !isEditing {
                        Button("추가") {
                            showAddSheet.toggle()
                        }
                        .sheet(isPresented: $showAddSheet, content: {
                            AddSheet()
                        })
                    }
                }
            }
            .sheet(item: $project) { project in
                EditSheet(editWith: project)
            }
        }
        .navigationViewStyle(StackNavigationViewStyle())
    }
    
    private func removeSelectedProject(at indexSet: IndexSet) {
        ...
    }
}

extension EditMode {
    mutating func toggle() {
        self = self == .active ? .inactive : .active
    }
}
```

### 문제 1: 편집 버튼을 눌러도 뷰에 변화가 없다.

![expected](/assets/images/swiftUI/expected-editmode.png)

이렇게 되길 바랐는데,

![real](/assets/images/swiftUI/no-button-editmode.png)

이렇게 .. 아무것도 보이지 않았다.

구글에 editMode not working부터 별 검색어로 다 검색 해 봤는데, 다들 잘 나오고 나만 안 돼... 라고 슬퍼하려던 중, [블로그 포스팅](https://medium.com/geekculture/swiftui-and-the-intermittent-editmode-b714c923f536)을 발견하게 되었다.

그와, 나와, 우리 모두의 진단(?)결과, `environmentValue`인 `editMode`를 내가 사용하고 있는 List 뷰에서 찾지 못하고 있는 것이 문제였다.

그래서 직접 뷰 모디파이어로 List에 `.environment(\.editMode, editMode)`를 넣어 주었더니 버튼이 정상적으로 나왔다!

### 문제 2: .inactive 일 때 NavigationLink의 Label이 안 눌림.

행복한 결말일 줄 알았는데, 또 다른 문제가 있었다.

편집 모드가 아닐 때, NavigationLink의 라벨 부분이 눌리지 않는 현상이 발생한 것이다...!! ㅠㅠ

![](/assets/images/swiftUI/not-working-editmode.gif)

#### 시도 1: NavigationLink(isActive:destination:label) 사용

어떤 변수의 상태가 `true`일 때 NavigationLink를 작동시키도록 해 보았다.

하지만 이는 복수 개의 NavigationLink가 있을 때 제대로 작동하지 않는 녀석이었다.

즉, 나는 ForEach로 복수 개의 NavigationLink가 리스트에 올라와 있는데, `isActive`가 선택된 딱 한 녀석이 아닌, 불특정 다수(로 예상되는)의 NavigationLink를 실행시켜 동작이 이상하게 되었다.

#### 시도 2: listStyle, navigationViewStyle, 순서 바꿔보기

예전에 버튼 두 개가 리스트의 한 행 안에 있을 때, 하나의 버튼 액션만 실행이 됐던 것이 떠올라 listStyle, navigationViewStyle을 적용해보고, 바꿔보고,

.onTapGesture의 위치가 이상한 걸까 해서 순서도 바꿔봤지만, 해결되지 않았다.

#### 시도 3: List 안에서 조건문 나누기

전전긍긍하던 중, 그냥 아예 뷰 안에서 조건을 나눠 .active일 땐 NavigationLink가, .inactive일 땐 해당 링크와 같은 모습의 라벨이 나올 수 있도록 만들었다.

```swift
    ...
    ForEach(projects, id: \.self) { project in
        if isEditing {
            Text(project.name)
                .onTapGesture {
                    self.project = project
                }
        }
        else {
            NavigationLink(destination: EmptyView()) {
                Text(project.name)
            }
        }
    }
    ...
```

위와 같이 조건문으로 나눴더니....!!!!!

![](/assets/images/swiftUI/nice-working-editmode.gif)

됐다 ㅠㅠㅠㅠ 흑흑 이 망할 문제 때문에 며칠을 별의 별 검색어로 검색을 다 했는지 모르겠다...

근데 물론 이 방법에는 슬픈 이야기가 있다.

editMode로 바뀌는 부분에 애니메이션이 있으면 좋을 것 같아서 editMode가 toggle되는 부분에 withAnimation을 넣었더니...

![](/assets/images/swiftUI/animation-bug-editmode.gif)

ㅎㅎ..

커스텀한 에딧버튼이 문제인가 해서 EditButton()을 써 봐도 똑같은 결과가 나왔다... 이건 나중에 언젠가 어떻게든 해결되겠지...

일단 텍스트 부분이 터치가 안 되는 게 더 큰 문제라고 생각해서 위와 같은 방식으로 때웠다.

## 결론

editMode는 environmentValue로 뷰의 편집 상태를 추적할 때 사용하는 값이며, @Environment나 .environment로 뷰에서 사용할 수 있다.

하지만 현재 상태에서 그냥 변수로 선언해서 사용하는 것 만으로는 뷰에 그 상태를 제대로 전달하지 못하는 것 같다. 그러므로 꼭 사용하는 뷰에 .environment로 알려주어야 제대로 쓸 수 있다.

편집 모드에서 NavigationLink와 onTapGesture의 동작이 제대로 되지 않는 문제가 있었는데, 뷰 안에서 조건문을 나눠 편집 모드와 편집 모드가 아닐 때의 화면과 동작을 구분하면 된다. (근데 이제 다른 오류를 곁들인...)

그러므로,,, 위와 같은 오류에 대한 해결법을 아시는 분은 제발 제게 가르침을 주세요..1!!!

*더 좋은 방법이나 잘못된 부분이 있다면 댓글 주세요!*