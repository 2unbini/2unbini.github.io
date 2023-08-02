---
title: "Python의 Generator... Swift에는?"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - study
    - swift
tags:
    - swift
    - python
last_modified_at: 2023-08-02
---

## 파이썬을 학습하다가 제네레이터라는 녀석을 보았다.

와 머야 이거 사기잖아?

내가 이해한 제네레이터는 다음과 같다.

### 제네레이터 in python

메모리에 저장하지 않고 반복되는 값을 생성해주는 말 그대로 `generator`.

`[n for n in range(1, 11)]`과 `range(1, 11)`은 1부터 10까지의 값을 생성해주지만 전자는 list로 각 값이 메모리에 저장되어있는 반면, 후자는 range라는 클래스 그 자체만 저장되어있다.

```python

>>> import sys

>>> a = [n for n in range(1, 11)]
>>> b = range(1, 11)

>>> sys.getsizeof(a)
# 184
>>> sys.getsizeof(b)
# 48

```

a와 b의 메모리 점유율은 다음과 같이 차이가 난다.


## Swift의 Range도 제네레이터일까?

결론: 아니다.

내가 원하는 답을 구글링해서 얻기가 어려워서 챗 GPT에게 물어봤다.

```
In Python, range is a lazy sequence that generates values on-the-fly and follows the concept of lazy evaluation. In contrast, in Swift, a Range represents a fixed sequence of values, and all the values within the range are stored in memory at once. This means that a Range in Swift is not a generator.
```

Swift의 Range는 메모리상에 각 값이 저장되기 때문에 제네레이터라고 할 수 없다고 한다.

python의 repl처럼 swift에서도 그 값을 찾아보고 싶었는데 swift의 MemoryLayout.size(ofValue:)는 실제 메모리 점유율이 아닌 포인터의 크기를 반환하기 때문에 확인할 수 없었다.

또한, Array와 비교해보고 싶었으나 Swift에서 Array는 `reference by value`를 따르지만, 메모리 구조상 Heap에 저장되어있다. 즉, 주소값을 가리킨 상태로 존재하는 것이다. 그래서 두 개의 차이를 명확히 확인하진 못했다.. 이건 아쉽다.


## enumerate와 enumerated()

Python의 enumerate는 range와 동일하게 제네레이터라고 한다.

이 글을 쓰게 된 궁금증이었는데, 과연 Swift의 enumerated()도 제네레이터일까? 하는 의문이 들었다.

enumerated()도 검증이 필요해보여서 Apple의 도큐먼트와 정의 부분을 찾아보았다.

```
    Returns a sequence of pairs (*n*, *x*), where *n* represents a
    consecutive integer starting at zero and *x* represents an element of
    the sequence.
```

인덱스에 해당하는 n은 0부터 시작하는 `연속적인 정수`를 표현하고, x는 `시퀀스의 요소`를 표현한다고 되어 있다.

n, x의 주소값이라거나 기타등등 시각적으로 확인할 수 있는 정보는 없지만, 정의에 쓰여 있는 각 요소에 대한 설명으로 미루어 보았을 때, n은 저장되지 않고 iterated 되는 연속적인 정수로, x는 저장되어 있는 요소로 생각하면 될 듯 싶다.

### 결국 swift의 enumerated()의 n은 generator에 해당하는 부분?

인 것 같다.

## 아쉬운 점

내가 할 수 있는 한 웬만한 리소스를 다 찾아봤는데 뚜렷하게 확인할 수 있는 부분은 찾지 못했다.

Sequence가 Swift에서 명확히 메모리에 저장되는 요소라면, 위에서 든 의문들을 조금이나마 더 해결할 수 있을 것 같다.

### 이 글을 읽는 누군가 확실한 정보를 아신다면 댓글이나 이메일 주시면 큰 도움이 될 것 같습니다!!