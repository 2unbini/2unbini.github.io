---
title: "Swift Linked List"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - swift
tags:
    - swift
    - 스위프트
    - 자료구조
last_modified_at: 2023-01-18
---

# Linked List

## Linked List(링크드 리스트)

### 뭐임?

자기 자신의 값과 자신 앞/뒤에 있는 값에 대한 위치 정보를 알고 있는 자료구조.

```C
typedef int Type;

typedef struct node
{
    Type value;
    Node* next;
    Node* previous;
} Node;
```

C 언어에서 포인터는 메모리의 위치를 가리킨다.

링크드 리스트의 핵심은 이 포인터가 앞과 뒤의 노드를 가리키고있어 멀리서 봤을 때 노드들이 서로 연결된 형태를 이룬다는 것이다.

```
----A----      ----B----      ----C----
|  val  |      |  val  |      |  val  |
---------      ---------      ---------
| p | n | ---> | p | n | ---> | p | n | ---> NULL
---------      ---------      ---------
  |   ^          |   ^          |
  |   +----------+   +----------+
 NULL
```

여기서 previous 값을 가지고 있냐 아니냐에 따라 Doubly Linked List와 Single Linked List가 구분된다.

또 한 가지 링크드 리스트가 있는데, 맨 앞 노드와 맨 뒤 노드가 서로 연결되어 동그란 맘 속에 피어난 how is the life? 모양으로 이루어진 Circular Doubly Linked List(환형 링크드 리스트)가 있다.

맨 앞 노드를 head(머리), 맨 뒤 노드를 tail(꼬리)라고 하는데 위에 있는 A, B, C 구조에서 A와 C가 각각 head, tail이 된다. NULL을 가리키고 있는 A의 p가 C의 n을, C의 n이 A의 p를 가리키게 만들어놓으면 진정으로 '난 너고 넌 나야'가 완성된다.

### 왜씀?

선형으로 데이터를 이룰 때 사용한다.

선형으로 데이터를 이루는 가장 기본적인 자료구조로 `배열`이 있다. 배열과 링크드 리스트는 삽입, 삭제, 탐색 연산에서 차이를 보인다.

우선 배열은 `index`를 알고 있으면 바로 값에 접근할 수 있다. 반면, 링크드 리스트는 head(0)부터 index까지 노드를 하나하나 다 거쳐가야 하므로 탐색 연산에서 배열이 링크드 리스트보다 쉽고 빠르다.

하지만 배열은 삽입, 삭제 연산을 할 때에 공간을 늘리거나 줄이고, 이미 배열돼있는 데이터를 한칸씩 움직여야 하므로 많은 시간과 공간이 필요하지만 링크드 리스트는 이전 데이터와 다음 데이터를 연결해주기만 하면 되므로 경제적이다.

## C 말고 Swift 가져와라

C 언어에는 `포인터`라는 밉지만 무적의 개념이 있다.

얘가 미운건 처음에 이해하기도 어렵고 다루기도 어려워서인데, 무적이라고 하는건 또 이런 개념을 알아먹기에 가장 좋은 근본이기 때문이다.

암튼 C의 포인터를 Swift에서 설명하려면 어떤 것에 치환하면 될까? 생각했을 때 `값 타입, 참조 타입`이 생각났다.

### class로 Node 정의하기

포인터는 메모리의 위치를 가리키고 있다. Swift에서 참조 타입(Reference Type)이 이와 같은 맥락이라고 생각했다.

Swift의 대표적인 참조 타입인 class는 그 인스턴스가 하나만 생성된다. 서로 다른 변수들이 하나의 class 인스턴스를 가리킨 상태에서 그 값을 바꾸면 각각의 바뀐 값들이 유지되지 않고 뒤죽박죽 된다. struct 인스턴스를 서로 다른 변수들이 가리키는 상태에서 값을 변경하면 각 변수가 가리키는 인스턴스에 대한 복사본이 만들어져(CoW, Copy on Write) 각각의 값을 유지하는 것과 다른 참조 타입의 특징이다.

결국, 메모리의 위치를 가리키고, 이것이 값이 변함에 따라 복사되지 않고 원본을 유지하려면 `class`를 사용해 Node를 정의하면 된다.

```swift
class Node {
    var value: Int
    var next: Node?
    var previous: Node?

    init(value: Int) {
        value = value
    }
}
```

지금은 value가 `Int`형인데, 제네릭(Generic)을 이용하면 value의 타입을 노드 생성시에 정해줄 수 있다.

```swift
class Node<T> {
    var value: T
    var next: Node?
    var previous: Node?

    init(value: T) {
        value = value
    }
}
```

Node 객체에서 previous를 빼면 그냥 기본 링크드 리스트가, previous를 추가하면 더블 링크드 리스트가, 더블 링크드 리스트 구조에서 head와 tail이 각각 nil이 아닌 서로의 next, previous를 가지고 있으면 환형 링크드 리스트가 된다.

근데 이 구조는 retain cycle을 만들어내기 딱 좋다.

스토리보드에서 라벨이나 버튼같은걸 코드에 정의할 때 `@IBOutlet weak var label: UILabel!` 이런 코드가 생성된다.

UILabel도, 그리고 이 라벨이 정의될 ViewController도 참조 타입이므로 weak이나 unowned 프로퍼티를 쓰지 않으면 서로 강하게 결합한다. 강하게 결합한 객체들은 서로가 가리키는 화살표를 삭제하지 않으면 영원히 메모리에 남아 무언가를 가리키고 있게 된다.

즉, Node가 삽입 삭제 연산을 반복하면서 인스턴스를 사이에 강한 결합이 일어나고, 이를 개발하는 과정에서 적절하게 해제하지 않으면 메모리 누수가 날 수 있다는 것이다.

그럼 어캄,,? weak 냅둬서 뭐혀유

```swift
class Node<T> {
    var value: T
    var next: Node?
    weak var previous: Node?

    init(value: T) {
        value = value
    }
}
```

이렇게 previous(혹은 next)를 `weak`하게 결합해 한 놈이 사라지면 다른 넘들도 사라질 수 있게 만들면 된다.

### 관련 연산 구현

에 관한 내용은 너무 많은 블로그와 아티클과 튜토리얼과 자료들이 넘쳐난다.

[Swift Algorithm Club - Linked List](https://github.com/kodecocodes/swift-algorithm-club/tree/master/Linked%20List) 혹은 더 친절한 설명으로 [구웨현코](https://www.kodeco.com/947-swift-algorithm-club-swift-linked-list-data-structure#toc-anchor-005)

를 보고 구현하면 된다.

## 생각해보기

### 링크드 리스트의 개수 세기 O(n) -> O(1)

내가 참고한 책을 봐도, 여기 저기 흩어진 코드 조각들만 봐도 링크드 리스트의 노드 개수를 구하기 위해서 tail까지 n번 연산을 반복했다.

```swift
class LinkedList<T> {
    private var head: Node<T>?

    public var isEmpty: Bool {
        return head == nil
    }

    public var count: Int {
        guard var currentNode = head
        else { return 0 }

        var count: Int = 1
        
        while let next = node.next {
            node = next
            count += 1
        }

        return count
    }
}
```

여기서도 node.next가 nil이 아닐 때까지 while문을 반복한다.

이걸 바로 알아내는 방법은 없을까?

count를 연산 프로퍼티가 아닌 저장 프로퍼티로 만들어두고, 노드가 삽입, 삭제되는 연산을 할 때마다 그 값을 바꿔주면 되지 않을까?

### 비슷한 맥락으로

링크드 리스트, 더블 링크드 리스트에선 tail에 접근하려면 세상 끝까지 반복문을 돌아야 했다.

LinkedList 객체에 tail을 저장해놓으면 되지 않을까?

물론 메모리에 count, tail만큼의 공간이 필요하겠지만... 아직 시간과 공간의 차이가 얼마나 성능에 큰 영향을 끼치는지 감이 잘 안 와서 모르겠다. 그래서 ~않을까? 로 끝난거임ㅋㅋㅎㅎ.. 죄송합니다.. 아시는 분 있으면 댓글이나 메일 주시면 정말 감사하겠습니다...


## 참고자료

[이것이 자료구조+알고리즘이다 with C언어 - 박상현, 한빛미디어](https://m.hanbit.co.kr/media/books/book_view.html?p_code=B9034896671)

[Swift Algorithm Club - Linked List](https://github.com/kodecocodes/swift-algorithm-club/tree/master/Linked%20List)

[구웨현코](https://www.kodeco.com/947-swift-algorithm-club-swift-linked-list-data-structure#toc-anchor-005)