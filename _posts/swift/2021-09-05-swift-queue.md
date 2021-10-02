---
title: "Swift엔 내장 큐(Queue)가 없다. 만들어서 쓰자."
toc: true
toc_sticky: true
categories:
    - 📂 all
    - swift
tags:
    - swift
    - 스위프트
    - 자료구조
    - 큐
last_modified_at: 2021-09-05
---

# 충격실화

Swift엔 내장된 큐가 없다. 그래서 내 손으로 구현해서 써야 한다.

처음에 든 생각은 '굳이 구현해야될까? 배열로 되지 않을까?' 였고, 큐를 사용하는 BFS 문제를 풀다가 메모리초과 시간초과 등등을 겪고 그냥 구현하기로 마음먹었다.

## 일단 검색

일단 검색했다. 잘 만들어진 큐가 있지 않을까 하여.. 역시나 있었다 잘 짜여진 듯한(?) 큐!

우선, `Swift Algorithm Club`이라는 커뮤니티에서 큐에 대한 개념을 설명하면서 내부적으로 [큐를 구현한 글](https://github.com/raywenderlich/swift-algorithm-club/tree/master/Queue)을 봤다. Swift에서 단순히 배열로 큐를 사용하면 시간복잡도가 얼마나 걸리는지, 그리고 그것이 왜 그런지에 대해 설명이 잘 돼있었다. 하지만 dequeue를 구현하는 부분에서 조금 더 좋은 방식이 있지 않을까 생각했다.

## 이거 좋다

다양한 글을 보면서 사람들이 어떻게 구현했는지 봤고, 이를 통합해 큐를 만들기로 했다.

타입에 구애받지 않도록, 제네릭으로 정의하고 `enqueue`, `dequeue` 두 큐 배열을 두어 시간복잡도 `O(n)`인 `removeFirst()` 대신에 `O(1)`이라고 알려져 있는 `reversed()` 함수를 활용하기로 했다.

# 큐 구현

백문이 불여일견 코드를 보자.

```swift
struct Queue<T> {
    var enqueue: [T]
    var dequeue: [T]
    
    var isEmpty: Bool {
        return enqueue.isEmpty && dequeue.isEmpty
    }
    
    var count: Int {
        return enqueue.count + dequeue.count
    }
    
    mutating func push(_ elem: T) {
        enqueue.append(elem)
    }
    
    mutating func pop() -> T? {
        if dequeue.isEmpty {
            dequeue = enqueue.reversed()
            enqueue.removeAll()
        }
        return dequeue.popLast()
    }
}
```

## struct Queue<T>

제네릭 타입으로 구조체를 정의했다. 이렇게 하면 타입에 구애받지 않고 유연하게 객체를 사용할 수 있다.

위와 같이 `<>`와 같은 형태에 `T, U, V`와 같은 `placeholder`를 사용하여 특정하지 않았지만 '어떤 타입'이라는 것을 표현해 준다. 실제 타입은 해당 placeholder를 사용하는 함수에서 결정된다.

내가 큐를 구현한 것은, 백준 알고리즘 문제를 풀기 위해서였고, 주로 `Int`형 배열을 썼기 때문에 제네릭으로 구현할 필요가 없을 듯 했다. 그런데 이 큐 자료구조가 결정적으로 필요했던 문제에선 `[Int]`의 배열, 즉 Int형 2차원 배열이 필요했기 때문에 제네릭으로 구현해놓은 것이 좋았다. 초기화할 때 결정할 수 있으니까!

또, 구조체로 정의했는데, 클래스로 정의해도 된다. 사실 맨 처음 클래스로 구현된 큐를 봤는데, 굳이 더 많은 비용을 들여서까지 클래스를 써야 되나 싶어서 구조체로 정의했다. 유의미한건진 잘 모르겠지만 백준에서 여러가지 실험을 한 결과, 클래스로 하나 구조체로 하나 메모리는 상관 없고 시간은 클래스가 좀 더 빨랐던 것으로 나왔다. 왜일까.. 테케가 다른가,,? 모르겠다 암튼 구조체나 클래스나 둘 다 가능, 근데이제 클래스는 `init` 필요함.

## isEmpty, count

큐는 선입선출 즉, 처음 들어온 애가 처음 나가는 구조로 되어있다. 하지만 글의 처음에서 말했듯, 스위프트에 내장된 함수 `removeFirst()`는 배열에 담긴 요소의 개수만큼 연산을 해 줘야 하기 때문에 큰 비용이 든다. 그래서 큐를 두 개로 나누어 한 쪽에선 순서대로 push하고, 한 쪽에선 순서대로 푸시된 큐를 `reversed()`해 맨 뒤의 요소를 뽑아내는 방식으로 구성했다.

결론적으로 나는 이 큐 구조체에서 `enqueue`, `dequeue` 두 가지 배열을 가지고 있으므로 `isEmpty`와 `count` 프로퍼티를 두 가지 배열에 대해 모두 생각해 줘야 했다.

## push()

`append()` 함수는 배열의 맨 뒤에 요소를 추가하는 것이기 때문에 그에 대한 연산 딱 하나만 필요하다. 그래서 그냥 `enqueue`에 `append()` 하는 방식으로 `push`를 구현했다.

여기서 `mutating` 키워드가 `func` 앞에 붙어 있는데, 이는 구조체가 값 타입이라 함수가 기본적으로 자기 안의 변수를 바꿀 수 없기 때문에 사용해주었다. 이 키워드 없이 그냥 func push() 이렇게 구현하면 Xcode 가 알아서 '구조체는 수정 못해! mutating쓰셈,,' 라고 말 해준다.

## pop()

해당 함수 역시 변수를 변경하는 동작을 하므로 `mutating` 키워드를 추가해줬다.

큐를 일반적인 배열로 만들어 `removeFirst()` 같은 함수를 쓰면 O(n)만큼의 시간복잡도가 걸리기 때문에 앞서 말한 `reversed()`함수를 써 큐의 순서를 뒤집고, 뒤집은 데서 맨 마지막 요소를 뽑아내 시간복잡도를 낮췄다.

결국, `pop()`은 `dequeue`에서 하고, `dequeue`가 비어 있으면 선입선출로 빠져나갈 애들의 리스트가 없다는 것이므로 일단 그 리스트를 만들어줘야 한다. 이 `dequeue`는 `enqueue`의 순서가 반대로 된 애기 때문에 `dequeue`가 없다면, `enqueue.reversed()`를 해서 순서를 역전시켜 저장 해 준다.

여기서 멈추면 `enqueue`와 `dequeue`에 값이 중복되어 저장된다. 그러므로 `enqueue`에 있는 값을 모두 비워주고, `dequeue`의 마지막 요소(선입선출해야하는 애의 역이면, 마지막 값을 뽑아내는게 맞으니까)를 뽑아서 리턴해주면 된다.

# 더 좋은 방법

을 알고 싶다.

직접 문제를 풀 때 이렇게 구현된 큐를 가지고 문제를 풀면, `pop()` 함수를 쓸 때 조금 번거롭다. 리턴하는 값이 `T?`이기 때문에 이에 대한 처리를 해 줘야 한다. 이에 대한 처리로 `guard let as?` 뭐 이런식으로 했는데, 이게 아직 익숙하지 않기 때문이다.

아니 왜 스위프트엔 내장 큐가 없지?! 왜지 궁금하다...

## 클래스로 정의한다면?

<details>
<summary>📍 전체 소스코드 </summary>
<div markdown="1">

```swift
class Queue<T> {
    var enqueue: [T]
    var dequeue: [T] = []
    
    var isEmpty: Bool {
        return enqueue.isEmpty && dequeue.isEmpty
    }
    
    var count: Int {
        return enqueue.count + dequeue.count
    }
    
    init(_ queue: [T]) {
        enqueue.self = queue
    }
    
    func push(_ elem: T) {
        enqueue.append(elem)
    }
    
    func pop() -> T? {
        if dequeue.isEmpty {
            dequeue = enqueue.reversed()
            enqueue.removeAll()
        }
        return dequeue.popLast()
    }
}
```

</div>
</details>

잘못된 부분이나 더 좋은 방법이 있다면 언제든 알려주세요!!