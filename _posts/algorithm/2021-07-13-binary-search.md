---
title: "스위프트 알고리즘 클럽 번역, 이진 탐색(검색)"
toc: true
toc_sticky: true
categories:
    - kunbon
tags:
    - 📂 all
    - swift
    - study
    - 알고리즘
last_modified_at: 2021-07-13
---

# 이진 검색

✔︎ 이 글은 [Swift Algorighm Club](https://github.com/raywenderlich/swift-algorithm-club) 의 문서를 번역한 글입니다.

## 이진 검색

목적 : 배열에서 요소를 빠르게 찾기.

숫자 배열이 주어지고, 이 중 특정한 숫자가 배열에 있는지, 있다면 몇 번째 인덱스에 있는지 알고싶다고 가정하자.

대부분의 경우에, 스위프트의 `Collection.index(of:)` 함수로 충분할 것이다 :

```swift
let numbers = [11, 59, 3, 2, 53, 17, 31, 7, 19, 67, 47, 13, 37, 61, 29, 43, 5, 41, 23]

numbers.index(of:43) // returns 15
```

`Collection.index(of:)` 내장 함수는 **[선형 검색(linear search)](https://2unbini.github.io/categories/kunbon/linear-search)** 으로 진행된다. 코드는 다음과 같이 생겼다:

```swift
func linearSearch<T: Equatable>(_ a: [T], _ key: T) -> Int? {
    for i in 0..<a.count {
        if a[i] == key {
            return i
        }
    }
    return nil
}
```

그리고 이 함수를 이렇게 쓰면 된다:

```swift
linearSearch(numbers, 43) // returns 15
```

그럼 뭐가 문젤까? `linearSearch()` 함수는 당신이 찾고자 하는 요소를 찾을 때까지 배열의 전체를 처음부터 끝까지 돈다. 최악의 경우엔 값이 배열에 없어 지금까지의 행동이 무의미하게 끝나버린다.

평균적으로 선형 검색 알고리즘은 배열의 절반을 확인해야 한다. 배열이 크다면, 속도가 굉장히 느려질 것이다.

## 분할 정복

위 방식에 속도를 더하는 방식은 *이진 탐색(binary search)*이다. 비법은 값을 찾을 때까지 배열을 반으로 쪼개는 데 있다.

크기가 `n` 인 배열에서 선형 검색이 **O(n)** 의 시간복잡도를 갖는 반면, 이진 검색은 **O(log n)** 뿐이 걸리지 않는다. 예를 들어, 요소의 개수가 1,000,000인 배열에서 단 20번만(`log_2(1,000,000) = 19.9`) 탐색하면 원하는 값을 찾을 수 있다. 요소의 개수가 10억개인 경우에도 단 30번만 거치면 찾을 수 있다. (하긴, 10억 개의 요소를 가진 배열을 언제 마지막으로 썼겠냐만..)

보기엔 좋아보이지만 이진 탐색엔 배열이 **정렬 돼 있어야 한다는** 단점이 있다. 물론, 실제론 문제가 되진 않는다.

이진 검색은 이렇게 동작한다 :

- 배열을 반으로 쪼갠 뒤 당신이 찾는 *키값*이 절반의 왼쪽 편에 있는지, 오른쪽 편에 있는지 판별한다.
- 키값이 어디에 있는지 어떻게 아는가? 여기서 배열을 정렬해야 하는 이유가 나온다. 정렬된 배열에서 단순히 `<` 나 `>` 연산자로 비교하면 된다.
- 키값이 왼쪽 절반에 있다면, 거기서 똑같은 동작을 반복하면 된다 : 왼쪽 절반을 반으로 쪼개 더 작은 조각으로 만든 뒤 키값이 어느 범위에 있는지 판별한다. (오른쪽 절반에 대해서도 똑같이 적용한다.)
- 키값을 찾을 때까지 반복한다. 더이상 배열을 쪼갤 수 없으면, 배열에 키값이 없다고 결론짓는다.

왜 "이진" 검색이라는 지 알 수 있다 : 매 단계마다 배열을 반으로 쪼개기 때문이다. 이 *분할정복* 과정은 찾는 키값이 어디에 있는지 빠르게 좁혀나갈 수 있다.

## 코드

재귀를 활용해 짜여진 이진 검색 스위프트 코드는 다음과 같다 :

```swift
func binarySearch<T: Comparable>(_ a: [T], key: T, range: Range<Int>) -> Int? {
    if range.lowerBound >= range.upperBound {
        // 찾는 값이 배열에 없음
        return nil

    } else {
            // 배열을 자를 기준 계산
            let midIndex = range.lowerBound + (range.upperBound - range.lowerBound) / 2
            
            // 찾는 값이 왼편에 있을 때
            if a[midIndex] > key {
                return binarySearch(a, key: key, range: range.lowerBound..<midIndex)
            
            // 찾는 값이 오른편에 있을 때
            } else if a[midIndex] < key {
                return binarySearch(a, key: key, range: midIndex + 1..<range.upperBound)
            
            // 찾는 값 발견!
            } else {
                return midIndex
            }
     }
}
```

플레이그라운드에 위 코드를 복사한 뒤 아래와 같이 실행해볼 수 있다 :

```swift
let numbers = [2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67]

binarySearch(numbers, key: 43, range: 0 ..< numbers.count)  // gives 13
```

`numbers` 배열이 정렬되어 있는 것에 주목하자. 그렇지 않으면 이진 검색 알고리즘은 제대로 동작하지 않을 것이다.

이진 검색은 배열을 절반으로 쪼개면서 진행된다고 했지만, 새로운 두 개의 배열을 실제로 만들지는 않는다. 스위프트의 `range` 객체를 사용해 쪼개진 상태를 추적해간다. 초기엔, 이 범위가 `0..<numbers.count` 와 같이 전체를 감당한다. 우리가 배열을 쪼개면 쪼갤수록 이 범위는 점차 작아진다.

> **주의** : `range.upperBound` 는 언제나 마지막 요소 + 1을 가리킨다는 점을 주의해야 한다. 예를 들어, 배열에 19개의 요소가 있는 배열의 범위가 `0..<19` 일 때, `range.lowerBound = 0` 이고, `range.upperBound = 19` 가 된다. 하지만 우리는 배열의 인덱스를 0부터 세기 때문에 마지막 요소의 인덱스는 19가 아닌 18이다. 범위와 함께 작업할 때 이를 꼭 기억하자 : `upperBound` 는 항상 마지막 요소의 인덱스보다 하나 더 많다는 점을.

## 예시를 단계별로 따라가보자

구체적으로 알고리즘이 어떻게 동작하는지 보면 도움이 될 것이다.

위의 예시에서 배열은 19개의 숫자를 갖고, 정렬된 상태에서 아래와 같은 모습을 보인다 :

```swift
[ 2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67 ]
```

`43` 이 해당 배열의 어디에 위치하는지를 아는 것이 목적이다.

배열을 반으로 쪼개기 위해서, 우리는 배열의 중간에 위치한 요소의 인덱스를 알아야 한다.

```swift
let midIndex = range.lowerBound + (range.upperBound - range.lowerBound) / 2
```

처음엔, 범위에서 `lowerBound = 0` 이고, `upperBound = 19` 이다. `midIndex` 가 `0 + (19 - 0) / 2 = 9` 라는 것을 알 수 있다. 사실 9.5지만, 우리는 정수를 사용하기 때문에 값을 내림한다.

다음 순서부터, `*` 표시는 가운데 요소를 보여 준다. 아래서 확인할 수 있듯, 각 부분에 있는 숫자의 개수는 동일하며, 우리는 정 가운데를 기준으로 배열을 쪼갤 수 있다.

```swift
[ 2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67 ]
                                  *
```

이제 이진 탐색으로 어떤 절반을 사용할 지 판별한다. 관련된 코드 조각은 다음과 같다 :

```swift
if a[midIndex] > key {
    // 왼쪽 절반을 사용
} else if a[midIndex] < key {
    // 오른쪽 절반을 사용
} else {
    return midIndex
}
```

이 경우에, `a[midIndex] = 29` 이다. 이는 찾고자 하는 값보다 작기 때문에 우리는 찾고자 하는 값이 절대 왼쪽 편에 위치하지 않으리라 결론내릴 수 있다. 어쨌든 왼쪽 편은 29보다 작은 수들로 구성돼있기 때문이다. 따라서 찾는 값은 무조건 오른쪽 편에 있을 것(혹은 배열에 없거나)이다.

이제 우리는 이진 검색을 반복하면 되는데, `midIndex + 1` 부터 `range.upperBound` 까지의 배열에서만 하면 된다 :

```swift
[ x, x, x, x, x, x, x, x, x, x | 31, 37, 41, 43, 47, 53, 59, 61, 67 ]
```

왼쪽 편에 대해서는 신경 쓸 필요가 없기 때문에 `x` 로 표시했다. 지금부터는 인덱스가 10으로 시작하는 오른쪽 편만 보면 된다.

새로운 중간 값을 계산하면, `midIndex = 10 + (19 - 10)/2 = 14` 가 되고, 이를 이용해 반으로 쪼개면 다음과 같아진다.

```swift
[ x, x, x, x, x, x, x, x, x, x | 31, 37, 41, 43, 47, 53, 59, 61, 67 ]
                                                 *
```

확인할 수 있듯, `a[14]` 의 값이 오른편의 중간값이 된다.

찾고 있는 값이 `a[14]` 보다 큰가 작은가? `43 < 47` 이기 때문에 작다. 이번엔 왼쪽편을 보고, 오른쪽 편에 대해선 무시하도록 하자 :

```swift
[ x, x, x, x, x, x, x, x, x, x | 31, 37, 41, 43 | x, x, x, x, x ]
```

이제 새로운 `midIndex` 는 다음과 같다 :

```swift
[ x, x, x, x, x, x, x, x, x, x | 31, 37, 41, 43 | x, x, x, x, x ]
                                     *
```

찾는 값이 `37` 보다 크므로, 오른쪽 편으로 반복한다 :

```swift
[ x, x, x, x, x, x, x, x, x, x | x, x | 41, 43 | x, x, x, x, x ]
                                        *
```

다시, 찾는 값이 더 크므로 다시 한 번 쪼갠 뒤 오른쪽 편을 취한다 :

```swift
[ x, x, x, x, x, x, x, x, x, x | x, x | x | 43 | x, x, x, x, x ]
                                            *
```

이제 끝났다. 찾는 값이 배열의 요소와 같아졌으므로, 우리가 찾고 있는 값 `43` 이 배열의 `13` 인덱스에 있음을 찾았다. 오예!

많은 일을 한 것 같지만, 실은 배열에서 키값을 찾기 위해 4번의 과정밖에 거치지 않았고, 이는 `log_2(19) = 4.23` 이라는 값으로도 확인할 수 있다. 선형 검색으로는 14번의 과정을 밟아야 했을 것이다.

우리가 `43` 대신 `42` 를 검색해야 했다면 어떤 일이 일어날까? 이 상황에서, 우리는 배열을 더이상 쪼갤 수 없다. `range.upperBound` 가 `range.lowerBound` 보다 작아진다. 이는 키값이 배열에 없다는 것을 의미하며, `nil` 을 반환한다.

> **주의**: 이진 검색을 구현할 때 많이들 `midIndex = (lowerBound + upperBound) / 2` 로 계산한다. 이는 사소한 버그를 일으킬 수 있는데, 배열의 크기가 엄청 커서 `lowerBound + upperBound` 가 정수의 최댓값을 넘어서는 오버플로우를 일으킬 때 발생한다. 이는 64비트 CPU 에서는 잘 일어나지 않지만, 32비트 환경에서는 분명히 일어날 수 있다.

## 반복 vs 재귀

이진 검색은 같은 로직을 더 작은 서브 배열에 계속 적용하기 때문에 보통 재귀의 형태를 가진다. 하지만, 꼭  `binarySearch()` 함수가 재귀 함수여야 하는 것은 아니다. 어떨 땐 재귀 알고리즘을 단순한 루프로 바꾸는 것이 더 효과적이다.

이진 검색의 반복 구현은 다음과 같다 :

```swift
func binarySearch<T: Comparable>(_ a: [T], key: T) -> Int? {
    var lowerBound = 0
    var upperBound = a.count
    while lowerBound < upperBound {
        let midIndex = lowerBound + (upperBound - lowerBound) / 2
        if a[midIndex] == key {
            return midIndex
        } else if a[midIndex] < key {
            lowerBound = midIndex + 1
        } else {
            upperBound = midIndex
        }
    }
    return nil
}
```

확인할 수 있듯, 재귀함수로 구현된 것과 아주 유사하다. 가장 큰 차이점은 `while` 반복문을 사용한다는 점이다.

이와 같이 사용할 수 있다 :

```swift
let numbers = [2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67]

binarySearch(numbers, key: 43)  // gives 13
```

## 결론

배열이 먼저 정렬돼야 한다는 점이 문제일까? 경우에 따라 다르다. 정렬하는 것 - 이진 검색을 곁들인 것 - 은 단순한 선형 검색보다 더 느릴 수 있음을 기억하자. 이진 탐색은 딱 한 번 정렬하고, 많은 검색을 할 때 최고로 좋은 방법이다.

## 출처

[위키피디아](https://en.wikipedia.org/wiki/Binary_search_algorithm) 참조행!

[Written for Swift Algorithm Club by Matthijs Hollemans](https://github.com/raywenderlich/swift-algorithm-club/tree/master/Binary%20Search)