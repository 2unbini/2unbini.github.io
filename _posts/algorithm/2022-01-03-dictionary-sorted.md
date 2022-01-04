---
title: "스위프트 딕셔너리 정렬, Swift Dictionary sorted()"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - swift
tags:
    - swift
    - 스위프트
    - 알고리즘
last_modified_at: 2022-01-03
---

# 딕셔너리 정렬하기

알고리즘 문제에서 [:] 형으로 구성된 딕셔너리를 사용할 때, 값을 정렬하려면 `sorted()`메서드를 사용하면 된다.

직접 해당 컬렉션을 정렬하는 sort()와 달리 sorted()는 정렬된 값을 반환하므로 새로 값을 정의해줘야 한다.

```swift
let dictionary: [Int: Int] = [1: 100, 2: 200, 3: 300]
let sortedDictionary = dictionary.sorted()
```

그런데, 알다시피 딕셔너리는 배열과 달리 키:값 으로 이루어져있다. 그래서 그냥 sorted() 를 때려박으면 이런 오류가 뜬다.

`type 'Dictionary<Int, Int>.Element' (aka '(key: Int, value: Int)') cannot conform to 'Comparable'`

딕셔너리 타입의 값을 비교하는 것은 불가능하므로 sorted 메서드를 내 입맛에 맞게 설정 해 주면 된다.

## key를 기준으로 정렬

key를 기준으로, key 값이 큰 수 대로 정렬하고 싶을 때는 다음과 같이 해 주면 된다.

```swift
let dictionary: [Int: Int] = [1: 100, 2: 200, 3: 300]
let sortedDictionary = dictionary.sorted { (first, second) in
                                          return first.key > second.key }
}
```

비교할 두 친구를 각각 first, second로 이름짓고, 각 key값의 대소관계를 반환한다.

축약하면 다음과 같이 쓸 수 있다.

```swift
let dictionary: [Int: Int] = [1: 100, 2: 200, 3: 300]
let sortedDictionary = dictionary.sorted { $0.0 > $1.0 }
```

`$0`과 `$1`은 각 딕셔너리 원소 하나를 의미하고, `.0`은 그 원소의 첫 번째 친구, 즉 키값을 의미한다.

## value를 기준으로 정렬

value를 기준으로 하는 것은 key를 기준으로 하는 것과 동일하다.

```swift
let dictionary: [Int: Int] = [1: 100, 2: 200, 3: 300]
let sortedDictionary = dictionary.sorted { (first, second) in
                                          return first.value > second.value }
```

또는,

```swift
let dictionary: [Int: Int] = [1: 100, 2: 200, 3: 300]
let sortedDictionary = dictionary.sorted { $0.1 > $1.1 }
```

처럼 해 주면 된다.

## value를 기준으로 정렬하되, 그 값이 같은 경우 key를 기준으로 정렬

그렇다면, 우선적으로 value를 기준으로 정렬하되, 그 값이 같은 경우 key를 기준으로 정렬하고자 한다면?

sorted 내부에 if/else 구문으로 친절히 써 주면 된다.

```swift
let dictionary: [Int: Int] = [1: 10, 2: 10, 3: 30, 4: 40]
let sortedDictionary = dictionary.sorted { (first, second) in
                                          if first.value == second.value {
                                              return first.key > second.key
                                          }
                                          return first.value < second.value }
```

또는,

```swift
let dictionary: [Int: Int] = [1: 10, 2: 10, 3: 30, 4: 40]
let sortedDictionary = dictionary.sorted {
                                          if $0.1 == $1.1 {
                                              return $0.0 > $1.0
                                          }
                                          return $0.1 < $1.1 }
```

로 손쉽게 써 주면 된다!