---
title: "Python max()의 key... feat. 가장 긴 문자열 찾기"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - study
    - python
tags:
    - algorithm
    - python
last_modified_at: 2024-03-07
---

## 가장 긴 문자열 찾기를 위해 max를 그냥 썼다면 오답입니다!!

```python
another_str = "abc"
other_str = "d"

max_str = max(another_str, other_str)
print(max_str)
```

max_str 값은 뭐가 나올까?

"abc"를 예상했지만 틀렸다. "d"가 나온다.

### key

[python 문서](https://docs.python.org/ko/3/library/functions.html#max)를 보면 알겠지만 `max()` 함수에 `default`와 `key`라는 선택 키워드가 추가되어 있다.

`default`는 iterable이 비어 있을 때 기본 값을 부여해 ValueError를 방지할 수 있는 키워드다.

`key`는 function으로, iterable의 크기 비교를 상세하게 지정해줄 수 있는 키워드다.

### str의 크기 비교

파이썬에서 문자열 대소 비교는 앞 글자부터 `ascii` 숫자로 지정된다.

즉, "abc"와 "d"를 비교했을 때, 문자열의 길이로 비교되는 것이 아니라 97과 100의 숫자 비교가 되는 것이므로 "d"가 더 큰 값으로 나온다.

### key=len

문자열 혹은 iterable의 길이를 기준으로 비교하고싶다면 `key=len`을 사용하면 된다.

```python
another_str = "abc"
other_str = "d"

max_str = max(another_str, other_str, key=len)
print(max_str)
```

key 키워드를 추가해주면 내가 원하는 대로 "abc"가 나온다.

### lambda

len은 지정된 키워드여서 바로 썼는데, 만약 원하는 함수식을 쓰고 싶다면 람다 표현식을 쓰면 된다.

알고리즘 문제를 풀 때 딕셔너리의 value 값, 혹은 세트의 두 번째 값 등 기본적으로 기준이 되는 값이 아닌 다른 값을 기준으로 정렬하거나 최대/최소값을 찾고 싶은 경우가 있다. 이 때 람다 식을 사용하면 된다.

```python
dict_arr = [("a", 1000), ("b", 100), ("c", 10), ("d", 1)]

max_val = max(dict_arr, key=lambda x: x[1])
# max_val = ("a", 1000)
```