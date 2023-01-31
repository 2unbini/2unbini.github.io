---
title: "Union-Find in Swift (feat. 분리 집합)"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - swift
tags:
    - swift
    - 스위프트
    - 알고리즘
last_modified_at: 2023-01-31
---

## Union(합치기)과 Find(찾기)할 수 있는 알고리즘

Union과 Find만 유의미하게 정의하고 사용할 수 있는 알고리즘 및 자료구조가 있다.

### 분리 집합

교집합이 없는 두 집합을 분리 집합이라고 한다.

교집합이 없는 집합은 무의미해보인다. 그러나 어떤 원소가 두 집합 중 어떤 집합에 속해있는지 알고 싶을 땐 각 집합이 유의미하다.

또, 두 집합을 합쳐 동일한 집합으로 만들어내는 연산을 정의할 수 있다.

### union-find

즉, 이름에서 알 수 있듯, Union(합집합)과 Find(집합 찾기)를 정의하는 것이 이 알고리즘의 내용이다.

```markdown
     A             B
+---   ---+   +---   ---+
|  1   2  |   |  5   6  |
|    3    |   |         |
+---------+   +---------+
```

1, 2, 3을 원소로 갖는 집합 A와 5, 6을 원소로 갖는 집합 B가 있다.

1. Find

find(5)를 하면 B를, find(2)를 하면 A를 도출하도록 `find` 함수를 정의한다.

2. Union

A U B 의 값은 1, 2, 3, 4, 5가 된다. 이 때, 이 합집합의 집합을 A라고 한다.

즉, union(B, to: A)를 하면 A를 도출하도록 `union` 함수를 정의한다.

여기서 집합 A, B는 편의상 나타낸 집합 이름이고, union-find 알고리즘을 정의하고 사용할 때엔 `집합 이름(=부모 노드)을 가진 배열`을 정의해 사용한다.


## 구현

뭐라는겨? 싶다면 구현을 해 보자. (나도 처음엔 뭐라는겨? 싶었다...)

### 집합 배열 초기화

먼저 특정 원소의 집합 이름을 가진 배열을 정의한다.

```swift

var parent: [Int] = []

for node in 0...5 {
    parent.append(node)
}

// node
// [0, 1, 2, 3, 4, 5]
// parent[0] = 0
// parent[1] = 1
// ...
// parent[5] = 5

```

이렇게 하면 원소 자기 자신이 자기 자신의 집합이 된다.

### find

원소의 집합을 찾는 함수를 정의한다.

```swift
func find(_ node: Int) -> Int {
    if parent[node] == node {
        return node
    }
}
```

원소의 집합이 원소 자신과 같으면, 위에 초기화한대로 원소 자기 자신이 자기 자신의 집합이 된다.

다르다면?

```swift
func find(_ node: Int) -> Int {
    if parent[node] == node {
        return node
    }

    return find(parent[node])
}
```

원소 자기 자신이 집합일때까지 쭉 파고들어간다.

그런데 이렇게 하면 최악의 경우 O(n)만큼 parent 배열을 탐색해야 한다. 탐색 횟수를 줄이기 위해 사용하는 알고리즘이니만큼 find 함수를 이렇게 다듬을 수 있다.

```swift
func find(_ node: Int) -> Int {
    if parent[node] == node {
        return node
    }

    parent[node] = find(parent[node])
    return parent[node]
}
```

재귀적으로 타고내려가긴 하더라도, 한 번 타고내려간 node에 대해선 그 집합의 값을 가장 상위 집합의 값으로 치환해주는 것이다.

```markdown
1
ㄴ 2
   ㄴ 3
      ㄴ 4
         ㄴ 5
```

인 경우에, parent[5]는 4고, parent[4]는 3, ..., parent[2]는 1이 된다.

parent[5]를 find 함수를 재귀적으로 타고 내려가면서 찾을 수도 있지만, parent[5] = 1로 치환해 바로 find(1)을 호출해 최상위 집합을 바로 알아낼 수 있는 것이다.

### union

집합을 찾는 함수를 만들었으니 합집합 연산을 하는 함수도 만들어보자.

```swift
func union(_ num2: Int, to num1: Int) {
    let set1 = parent[num1]
    let set2 = parent[num2]

    parent[set2] = set1
}
```

합집합이 될 원소를 num1, num1 집합에 포함시킬 원소를 num2라고 하자. set1은 num1의 집합, set2는 num2의 집합이다.

set2의 집합에 set1을 대입해주면 된다.

```markdown
// parent[i]
//    |
//    i      의 구조

1    2    3    4    5
|    |    |    |    |
1    2    3    4    5

```

의 예시에서, union(3, to: 2)를 하면

parent[3] = 2

가 되고, 이는 다음과 같은 구조로 바뀌게 된다.

```markdown
// parent[i]
//    |
//    i      의 구조

1    2    2    4    5
|    |    |    |    |
1    2    3    4    5

// 즉,

1       2       4    5
|    +--|--+    |    |
|    |     |    |    |
1    2     3    4    5

```

## 예시 문제

어떤 알고리즘 문제 책에 소개되어있는지 union-find 알고리즘을 검색하자마자 나오는 대표적인 백준 문제가 있다.

### [10775 공항](https://www.acmicpc.net/problem/10775)

1, 2, 3, ... , G 총 G 개의 게이트가 있는 공항이 있다.

이 공항의 각 게이트에는 하나의 비행기만 도킹할 수 있고, 입력으로 들어온 g(i) 비행기는 i 이하의 게이트에 도킹될 수 있다. 게이트에 도킹할 수 없는 경우 공항이 폐쇄된다.

이 때, 도킹할 수 있는 비행기의 최대값을 구하는 문제다.

예를 들어 1, 2, 3, 4, 5 게이트가 있을 때, 첫 입력으로 2가 들어왔다면, 2번 게이트에 비행기를 도킹한다. 다음 입력으로 2가 들어왔다면, 1번 게이트에 비행기를 도킹한다. 다음 입력으로 또 2가 들어왔다면, 2 이하의 게이트 중 도킹할 수 있는 게이트가 없으므로 공항이 폐쇄된다.

이 흐름대로 Union-Find를 활용하면 `parent`(아래 코드에선 gate)가 0이 되어 도킹 실패, 공항 폐쇄가 될 때까지 비행기의 개수를 세어 주면 된다.

```swift
let G = Int(readLine()!)!
let P = Int(readLine()!)!
var gate: [Int] = []
var input: [Int] = []
var count: Int = 0

// gateNumber에 대한 gate 집합 초기화
for gateNumber in 0...P+1 {
    gate.append(gateNumber)
}

// 입력받기
for _ in 1...P {
    input.append(Int(readLine()!)!)
}

// num2의 집합을 num1의 집합으로 합치는 union 함수 정의
func union(_ num2: Int, to num1: Int) {
    let set1 = find(num1)
    let set2 = find(num2)
    
    gate[set2] = set1
}

// num의 집합 찾는 find 함수 정의
func find(_ num: Int) -> Int {
    if gate[num] == num {
        return num
    }
    
    gate[num] = find(gate[num])
    return gate[num]
}

// 입력 순회
for number in input {
    
    // 입력된 게이트의 집합
    let gateNum = find(number)
    
    // 게이트의 집합이 0이면 더이상 도킹 불가
    // 0이 아니면 현재 게이트보다 1 작은 게이트의 집합으로 합치기
    if gateNum != 0 {
        union(gateNum, to: gateNum - 1)
        count += 1
    } else {
        break
    }
}

// 최대 비행기 개수 도출
print(count)
```


## 참고

[이것이 자료구조+알고리즘이다 with C언어 - 박상현, 한빛미디어](https://m.hanbit.co.kr/media/books/book_view.html?p_code=B9034896671)

[10775번 질문의 답변](https://www.acmicpc.net/board/view/55692)