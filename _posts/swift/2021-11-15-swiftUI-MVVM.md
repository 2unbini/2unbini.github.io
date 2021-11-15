---
title: "SwiftUI MVVM"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - swift
tags:
    - swift
    - 스위프트
    - SwiftUI
    - MVVM
last_modified_at: 2021-11-15
---

# MVVM이란..

> Model-View-ViewModel로 이루어진 디자인 패턴...

SwiftUI를 검색하면 자동으로 MVVM 패턴이 든다. 기존의 UIKit이 MVC 패턴이었던 것과 달리, SwiftUI는 MVVM 패턴으로 쓰인다. 두 패턴의 차이는 Controller냐 ViewModel이냐의 차이이다.

## View

뷰는 말 그대로 사용자에게 보이는 UI를 뜻한다. 뷰는 모델을 단순히 반영해주는 친구다. 뷰가 별다른 데이터나 기능을 가져서는 안 된다. 이는 모델의 영역!

모델에 있는 데이터가 수정됐을 때, 그 수정된 결과를 반영할 뿐 직접 데이터를 수정하지 않는다.

엥 그럼 @State 같은 애들은 뭐임? -> @State는 그 사용 용도 자체가 뷰의 변화를 감지하고 뷰를 자동으로 바꾸게 하는 것이다. 내부적인 데이터의 상태, 그리고 그 상태를 변화시키는 로직은 모델에 있지만, 그로 인해 뷰에 변경이 생길 때 그 정보를 담을 뿐이다.

## Model

앱 내에서 내가 운용하고자 하는 모든 데이터와 로직이 모델에 있다. UI에 독립적이어야 하며, '어떻게' 보이는지에 집중하는 View와는 달리, Model은 '무엇을' 하는지에 집중한다.

UI에 독립적이기 때문에, 모델을 정의한 파일엔 SwiftUI(혹은 UIKit 등)를 import 하지 않는다.

## ViewModel

뷰와 모델을 연결해주는 중간 다리이다. Model에서 '얘 이거 바뀌었다' 라고 얘기하면, ViewModel은 이를 View에 '야 이거 바뀌었대'라고 알린다. View는 ViewModel이 알려준 사항을 보고 UI를 그에 맞게 변경한다.

반대로, View에서 특정한 행동을 했을 때(예, tap 등) 그에 대한 함수를 실행시키고, 그에 따라 Model을 수정한다.

ViewModel의 핵심적인 역할은 Model이 가지고 있는 데이터를 View 친화적으로 바꾸고, View에서 일어난 동작으로 Model을 오류 없이 수정하는 거라고 생각한다.

# MVVM 패턴에 따라 SwiftUI 코드 짜 보기!

근데 이제 [Stanford CS193p course](https://youtu.be/--qKOhdgJAs)를 곁들인...

## View

카드를 탭 할 수 있는 카드 그리드를 만들었다.

```swift
// ContentView.swift

import SwiftUI

struct ContentView: View {
    
    let emojis = ["❤️", "🧡", "💛", "💚", "💙", "💜"]
    
    var body: some View {
        VStack {
            ScrollView {
                LazyVGrid(columns: [GridItem(.adaptive(minimum: 90))], content: {
                    ForEach(emojis, id: \.self) { emoji in
                        CardView(emoji: emoji)
                            .aspectRatio(2/3, contentMode: .fit)
                    }
                })
            }
            .foregroundColor(.yellow)
        }
        .padding(.horizontal)
    }
}

struct CardView: View {
    
    @State var isFaceUp: Bool = false
    @State var emoji: String
    
    var body: some View {
        ZStack {
            let shape = RoundedRectangle(cornerRadius: 10)
            
            if isFaceUp {
                shape.fill().foregroundColor(.white)
                shape.strokeBorder(lineWidth: 3)
                Text(emoji).font(.largeTitle)
            }
            else {
                shape.fill()
            }
        }
        .onTapGesture {
            isFaceUp = !isFaceUp
        }
    }
}
```

### LazyVGrid

GridItem의 배열을 갖는 그리드로, Vertical Grid를 생성 해 준다. (=> LazyHGrid는 Horizontal Grid)

단순히 [GridItem] 배열을 인자로 넣어도 되지만, 각 그리드의 크기를 유동적으로 정해줄 수 있다. `GridItem(.adaptive())`를 사용하여 최소 너비/높이만큼을 설정해 줄 수 있다.

### .aspectRatio

각 카드의 비율을 가로:세로 = 2:3으로 설정하고 싶을 때, `.aspectRatio`를 사용하면 된다.

### .strokeBorder

어떤 요소에 라인을 줄 때 stroke를 쓴다. 그런데 이는 해당 요소의 바깥에 생성되는 라인이라 그 두께가 커졌을 때 다른 요소에 영향을 받거나 줄 수 있다. 이 때, `strokeBorder`를 사용하면 요소의 안쪽에 선이 만들어진다.

## Model

위와 같이 View를 구성하면 Model에 있어야 할 CardView를 구성하는 emoji 데이터, 카드가 뒤집혀있는지 확인하는 isFaceUp 변수, 그리고 탭 했을 때 변하는 제스처 등이 View에 그대로 드러나게 된다.

이를 분리하기 위해 Card의 content, isFaceUp 등 상태를 가지고 있는 구조체, 그리고 그 카드들의 배열을 담고 있는 구조체가 필요하다. 또한, 데이터의 상태를 변화시킬 함수가 필요하다.

```swift
// MemoryGame.swift

import Foundation

struct MemoryGame<CardContent> {

    private(set) var cards: Array<Card>
    
    mutating func choose(_ card: Card) {
        if let chosenIndex = index(of: card) {
            cards[chosenIndex].isFaceUp.toggle()
        } else {
            print("no index found")
        }
    }
    
    func index(of card: Card) -> Int? {
        for i in 0..<cards.count {
            if cards[i].id == card.id {
                return i
            }
        }
        return nil
    }
    
    init(numberOfPairsOfCard: Int, _ createCardContent: (Int) -> CardContent) {
        cards = Array<Card>()
        
        for pairIndex in 0..<numberOfPairsOfCard {
            let content = createCardContent(pairIndex)
            cards.append(Card(content: content))
            cards.append(Card(content: content))
        }
    }
    
    struct Card: Identifiable {
        var id: UUID = UUID()
        var isFaceUp: Bool = false
        var isMatched: Bool = false
        var content: CardContent
    }
}
```

### Generic CardContent

카드의 내용은 String일 수도, Int일 수도, Image일 수도 있다. 그 내용을 한 가지로 지정하는 대신, 해당 구조체를 초기화할 때 정해주는 방식을 사용할 수 있는데, 이 때 사용하는 것이 `제네릭`이다.

MemoryGame 이라는 모델 자체를 초기화할 때, CardContent의 자리에 원하는 카드의 내용 타입을 넣어 주면 설정된다.

### private(set) var

`private var` 에서 `set`의 범위를 추가해주는 친구. 객체 내부에서만 접근할 수 있는 `private`과 달리, `private(set)`을 하면 객체 외부에서 해당 변수를 읽을 수 있다.

### mutating func

구조체는 값 타입이다. Swift에선 기본적으로 값 타입의 프로퍼티를 내부 메소드로 수정할 수 없다. 하지만 `func` 앞에 `mutating` 키워드를 붙이면 가능하다.

### init(numberOfPairsOfCard: Int, _ createCardContent: (Int) -> CardContent)

모델 내부에 선언해둔 `cards` 배열에 값을 초기화해주는 기능을 한다. 직접 호출해야 하는 다른 메소드와 달리 `init`은 초기화하고자 하는 값 등을 인자로 넣어 주면 객체가 생성될 때 자동으로 값을 생성 해 준다.

첫 번째 인자로 들어간 값은 카드가 몇 쌍인지에 대한 Int 값이고, 두 번째 인자는 각 카드의 `content` 값을 설정해주기 위한 함수이다. createCardContent 함수로 만들어진 CardContent를 content로 하는 Card 객체를 numberOfPairsOfCard 쌍 만큼 cards 배열에 추가해 초기화하는 코드이다.

### Identifiable

말 그대로 식별 가능하다는 뜻이다. View에서 `ForEach`문으로 Card를 추가하게 되는데, 이 때 Card에 식별 가능한 id 값 따위가 있어야 한다. 그래서 Card에 Identifiable 프로토콜을 따르게 하고, 그에 따라 UUID로 각각 식별 가능한 id 를 만들어 주었다.

## ViewModel

Card 객체에 대한 정보를 갖고, 해당 정보를 세팅하고 수정할 수 있는 Model이 생성됐다. 이제 이 모델을 뷰와 연결해줄 ViewModel이 필요하다.

앞서 ViewModel의 핵심적인 역할은 Model이 가지고 있는 데이터를 View 친화적으로 바꾸고, View에서 일어난 동작으로 Model을 오류 없이 수정하는 것이라 말 했던 만큼, Model 데이터를 View에서 사용할 수 있도록, 그리고 View에서 일어난 동작으로 Model을 바꿀 수 있도록 만들었다.

```swift
// EmojiMemoryGame.swift

import Foundation

class EmojiMemoryGame: ObservableObject {
    
    static let emojis = ["❤️", "🧡", "💛", "💚", "💙", "💜"]

    @Published private var model: MemoryGame<String> = createMemoryGame()
    
    var cards: Array<MemoryGame<String>.Card> {
        return model.cards
    }
    
    static func createMemoryGame() -> MemoryGame<String> {
        MemoryGame(numberOfPairsOfCard: emojis.count, { index in
            EmojiMemoryGame.emojis[index]
        })
    }
    
    func choose(_ card: MemoryGame<String>.Card) {
        model.choose(card)
    }
}
```

### static

EmojiMemoryGame 클래스에서 `static` 키워드를 두 번 볼 수 있다. 하트 이모지 배열과 createMemoryGame 함수이다.

cards나 model 변수나 choose 함수와 달리 두 상수와 함수에만 해당 키워드가 붙은 것에 주목하자. emojis와 createMemoryGame는 해당 클래스 내의 다른 변수나 함수에서 사용된다는 것을 확인할 수 있다.

static 은 말 그대로 변하지 않는 고정 값이다. 해당 키워드 없이 createMemoryGame을 선언하면 다음과 같은 에러가 뜬다.

```plainText
Cannot use instance member 'createMemoryGame' within property initializer;
property initializers run before 'self' is available
```

self가 있기 전에 프로퍼티 이니셜라이저가 실행되기 때문에 안 된다는 말인데, model 변수를 초기화하는 데 사용되는 `createMemoryGame()`이 그 자체로 먼저 구성되기 전에 사용될 수 없다는 말로 받아들이면 될 듯 하다.

결국, EmojiMemoryGame 클래스 안의 다양한 프로퍼티, 메서드가 초기화되는 순서가 보장되어있지 않기 때문에, static 키워드를 사용해 바뀌지 않는 고정 값으로 지정해 다른 프로퍼티나 메서드에 사용할 수 있도록 하는 것이다.

### cards 변수

Model을 정의할 때 cards 배열은 이미 구성해놨는데 왜 ViewModel에도 같은 변수를 정의해놨을까?

Model은 외부에서 들여다볼 수 없게 하고, ViewModel에서 해당 데이터와 대응하는 값을 외부로 노출시키는 것이다.

### func createMemoryGame()

model 변수에 직접 MemoryGame을 대입해도 되는데 왜 함수로 뺐을까? 가독성과 유지보수성을 위해서 분리한 듯 싶다.

### func choose()

이 부분이 이번 강의를 들으면서 가장 인상깊었던 부분이다.

나는 항상 ViewModel 혹은 Controller가 어떤 역할을 하는지 확신이 들지 않았다. Model과 View 사이에서 연결해 준다는 것은 알겠는데, 어떻게 그 기능을 한다는건지 확실하게 느껴지지 않았기 때문이다.

그런데, 이 choose() 함수를 구현하면서 ViewModel의 기능을 느끼게 되었다.

choose() 함수를 ViewModel 클래스 안에 구현하기 전엔 isFaceUp을 바꿀 방법이 없었다. 해당 프로퍼티를 바꾸는 로직은 Model에 정의돼있고, 이 모델에 접근하는 방법은 ViewModel에 정의된 `model` 변수를 통해서밖에 없는데, 해당 변수는 `private`이기 때문에 외부에서 접근이 불가능하기 때문이다.

그래서 View와 Model 양쪽에서 접근할 수 있는 ViewModel에 choose라는 함수를 정의해 ViewModel을 통해 Model에 접근할 수 있도록 했다. EmojiMemoryGame 클래스 내에선 model 변수에 접근할 수 있기 때문이다.

### ObservableObject and @Published

마지막으로 Model이 변했을 때, 그 상태를 View에 알려 View의 모습을 바꾸는 ViewModel의 역할을 확인할 차례다. 이를 위해 ObservableObject 프로토콜과 @Published 키워드가 사용된다.

ObservableObject는 해당 객체가 Observable, 즉 관찰 가능하다는 것을 뜻한다. 해당 클래스에 변화가 생겼을 때 `objectWillChange`를 사용해 변화를 알린다.

여기에선 objectWillChange를 쓰는 대신, 변화가 감지될 변수인 `model`에 `@Published` 키워드를 붙였다. 이를 통해 model 변수가 변하면, 자동으로 해당 클래스 외부에 객체가 변화했다는 것을 알리게 된다.

## 다시 View

앞서 만들어둔 View는 Model과 ViewModel, View가 모두 혼합된 친구이다. 이를 잘 분리하면 다음과 같은 코드가 완성된다.

```swift
//ContentView.swift

struct ContentView: View {
    
    @ObservedObject var viewModel: EmojiMemoryGame
    
    var body: some View {
        VStack {
            ScrollView {
                LazyVGrid(columns: [GridItem(.adaptive(minimum: 90))], content: {
                    ForEach(viewModel.cards) { card in
                        CardView(card: card)
                            .aspectRatio(2/3, contentMode: .fit)
                            .onTapGesture {
                                viewModel.choose(card)
                            }
                    }
                })
            }
            .foregroundColor(.yellow)
        }
        .padding(.horizontal)
    }
}

struct CardView: View {
    
    let card: MemoryGame<String>.Card
    
    var body: some View {
        ZStack {
            let shape = RoundedRectangle(cornerRadius: 10)
            
            if card.isFaceUp {
                shape.fill().foregroundColor(.white)
                shape.strokeBorder(lineWidth: 3)
                Text(card.content).font(.largeTitle)
            }
            else {
                shape.fill()
            }
        }
    }
}
```

### @ObservedObject

EmojiMemoryGame 클래스가 ObservableObject라는 것을 알림과 동시에, 해당 객체가 변화할 때마다 뷰를 리로드해주는 역할을 한다.

근데 해당 변수를 잘 보면 viewModel이 초기화되지 않고 그냥 그 타입만 정해져 있다. View 내에서 초기화하지 않고, App이 시작될 때 해당 객체를 생성한 뒤 View로 보내주는 방식을 택했다.

```swift
import SwiftUI

@main
struct MVVMCardGameApp: App {
    
    let game = EmojiMemoryGame()
    
    var body: some Scene {
        WindowGroup {
            ContentView(viewModel: game)
        }
    }
}
```

main문에 해당하는 App 구조체에 EmojiMemoryGame 객체를 초기화하고, ContentView로 전달했다.

# 결론

MVVM 패턴을 이해하려고 시도한지 어언 3개월... 이도 저도 아닌 패턴으로 누더기 코드를 작성하면서 의문이 생긴 와중에 근본 강의를 들으니 그나마 정리가 되는 것 같다.

다음에 코드를 짤 땐 `함수형 프로그래밍`, 그리고 `ViewModel`의 근본적인 기능을 잘 생각하면서 코드를 짜고 싶다.

#### 참고

[Stanford CS193p course 2021](https://youtu.be/--qKOhdgJAs)