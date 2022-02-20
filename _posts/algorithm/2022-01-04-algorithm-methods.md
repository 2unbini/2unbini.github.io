---
title: "코테문제 풀 때 많이 쓰는 함수들"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - swift
tags:
    - swift
    - 스위프트
    - 알고리즘
last_modified_at: 2022-01-04
---

# 코테 풀 때 자주 쓰는 함수들 정리

문제 풀 때마다 업뎃할 예정!

## 문자열

### String을 배열로 만들기

**각 글자(character) 별로 쪼개고 싶을 때**

```swift
let name: String = "ekwon"
let array: [String] = name.map{ String($0) }
```

**특정 구분자를 기준으로 쪼개고 싶을 때**
```swift
let fullName: String = "Eunbin Seri Kwon"
let array: [String] = fullName.split(separator: " ")
```

**문자열 안에서 특정 문자 삭제하고 싶을 때**
```swift
let str: String = "@Eunbin!Seri^^Kwon@@"
let removedStr: String = str.components(separatedBy: ["@", "!", "^"])
// removedStr => ["", "Eunbin", "", "Seri", "", "", "Kwon", ""]
let filteredStr: String = removedStr.filter { $0 != "" }
// filteredStr => ["Eunbin", "Seri", "Kwon"]
```

**문자열 앞뒤 문자 삭제하고 싶을 때**
```swift
let str: String = "#Eunbin$"
let removedStr: String = str.trimmingCharacters(in: ["#", "$"])
// removedStr => "Eunbin"
```

## 딕셔너리

### Default 값

딕셔너리에 값을 넣지 않으면 nil이 되므로, 조건문에 따라 키가 없을 때 default 값을 넣고, 키가 있으면 값을 추가하는 방식은 너무 귀찮다.

딕셔너리의 default value를 바로 집어넣을 수 있는 방법이 있다. key값이 들어가는 곳에 `, default: DEFAULT_VALUE` 를 넣어 주면 된다.

```swift
var dictionary: [Int: Int] = [:]

for i in 0...10 {
	dictionary[i, default: 0] += i
}
```

## 수학

### 절댓값

```swift
let absolute = abs(1 - 100)
// 99
```

### 최솟값/최댓값
```swift
let minimum = min(100, 1)
let maximum = max(100, 1)
// 각각 1, 100
```

### 제곱근/제곱

제곱근 구하는 함수는 `sqrt`로, `Double`혹은 `Float`형 인자를 받아서 해당 타입에 맞는 제곱근을 반환한다.

```swift
let n: Double = 49
let squareRoot: Double = sqrt(n)
let intSquareRoot: Int = Int(squareRoot)
```

제곱 구하는 함수는 `pow`로, 첫 번째 인자로 제곱할 값을, 두 번째 인자로 몇 제곱인지를 넣어 주면 된다.

```swift
let n = pow(10, 2)
```

### 진수 변환

진수 변환은 String, Int 타입의 init을 통해 하면 된다.

**10진수 -> 2진수**

```swift
let decimal = 5
let binaryString = String(decimal, radix: 2) // "101"
```

**2진수 -> 10진수**

```swift
let binaryString = "101"
let decimal = Int(binaryString, radix: 2) // 5
```

10진수 <-> 8진수, 10진수 <-> 16진수 모두 위 우 개에 radix 값만 바꿔주면 된다.