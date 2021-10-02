---
title: "스위프트 알고리즘 클럽 번역, 선형 탐색(검색)"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - kunbon
tags:
    - swift
    - study
    - 알고리즘
last_modified_at: 2021-07-12
---

# 선형 검색

✔︎ 이 글은 [Swift Algorighm Club](https://github.com/raywenderlich/swift-algorithm-club) 의 문서를 번역한 글입니다.

## 선형 검색

목표 : 배열에서 특정한 값을 찾기.

제네릭 오브젝트형 배열이 있다고 가정하자. 선형 검색을 통해서 우리는 배열을 하나씩 하나씩 방문해 우리가 찾는 값을 배열의 각 요소와 비교한다. 두 개가 같으면 멈추고 그 때 배열의 인덱스를 반환한다. 아니면, 배열의 모든 값을 다 볼 때까지 다음 값과의 비교를 반복한다.

## 예시

`[5, 2, 4, 7]` 로 구성된 배열이 있고, 이 배열이 `2` 를 포함하고 있는지 확인해보자.

우리는 배열의 첫 번째 요소인 `5` 로 우리가 찾는 값 `2` 와의 비교를 시작한다. 두 값은 명백히 다른 값이므로 배열의 다음 요소와의 비교를 시행한다.

다음 요소인 `2` 와 우리가 찾는 값 `2` 를 비교했을 때, 두 값은 같다. 우리는 반복을 그만하고 `2` 가 위치한 배열의 인덱스인 1을 반환한다.

## 코드

선형 검색을 스위프트로 구현하면 다음과 같다 :

```swift
func linearSearch<T: Equatalbe>(_ array: [T], _ object: T) -> Int? {
	for (index, obj) in array.enumerated() where obj == object {
		return index
	}
	return nil
}
```

플레이그라운드 상에서 다음과 같이 테스트해보자 :

```swift
let array = [5, 2, 4, 7]
linearSearch(array, 2) 	// This will return 1
```

## 성능

선형 검색의 시간복잡도는 **O(n)** 이다. 우리가 찾는 값을 배열의 각 요소와 비교하기 때문에, 소요되는 시간은 배열의 길이와 비례한다. 최악의 경우, 우리는 배열의 모든 요소를 봐야 할 수도 있다.

최선의 경우엔 **O(1)** 의 시간복잡도를 갖는데, 이는 우리가 찾는 값이 배열의 맨 처음에 위치해 탐색을 시작하자마자 발견돼야 하기 때문에 흔치 않다. 운 좋을 수도 있지만, 대부분의 경우 그렇지 않다. 평균적으로, 선형 검색은 배열의 절반에 해당하는 값들을 봐야 한다.

## 출처

[선형 검색 위키피디아](https://en.wikipedia.org/wiki/Linear_search)

[Written by Patrick Balestra](https://github.com/raywenderlich/swift-algorithm-club/tree/master/Linear%20Search)