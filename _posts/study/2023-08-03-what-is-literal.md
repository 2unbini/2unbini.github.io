---
title: "리터럴(literal) (feat. 10년 묵은 체증 사라짐)"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - study
    - swift
tags:
    - swift
    - python
last_modified_at: 2023-08-03
---

## str 자료형은 불변이다. 생성된 후에 변하지 않고 메모리 어딘가에 남아 있다. 이게 뭔말이고?

42 서울에서 링크드 리스트를 구현할 때부터 가지고 있던 의문.

```python
#python code
a: str = "Apple"
```

'a는 스택 혹은 힙에 저장될텐데, 저 str 타입의 "Apple"이라는 문자열은 어디에 저장되어있는거지?'

리터럴의 개념을 몰랐던 나는 `생성 후 메모리 어딘가에 남아 불변하는`것이 도대체 무슨 말인지 이해하지 못했다.

우선 변하지 않는다는 말을 이해하기 어려웠다. 분명 `a = "Apple"`을 했다가 `a = "Art"`로 바꾸면 변하는 것 아닌가?

**변수**와 **문자열 타입**을 제대로 구분하지 못한 데서 온 의문이었다.

### '메모리 상에 올라가있다'

a가 가리키고 있는 "Apple" 그 자체는 메모리 상에 올라가있다.

왜냐? 말 그대로 a가 가리키고 있어야 하니까. "Apple"이 메모리에 없으면 bad access 에러가 날 것이다.

이 상태에서 `b = "Apple"`을 한 다음, a와 b의 메모리 주소를 찍어 보면 같은 메모리 주소가 나온다.

### '변하지 않는다'

변수 a는 변할 수 있다. 왜냐? 말 그대로 a는 변수니까.

하지만 "Apple"은 변할 수 없다. 그것이 리터럴이니까.

![](https://mblogthumb-phinf.pstatic.net/MjAyMDA5MDZfNzQg/MDAxNTk5MzQ3NjIxNTA1.mLoMRv7v7CNlhXjCoU2wQ5O2mi5yK6CH-H3FHtvceKcg.LdPJtOG-367ZcddayjJhnGYRCop3nMizWozfKz0t3gwg.JPEG.khs20010327/1599347620687.jpg?type=w800)

## 리터럴(literal)?

CS에서 리터럴은 소스코드 내에서 변하지 않는 값이며, 많은 프로그래밍 언어에서 정수, 실수, 불리언, 문자 등 원시 값들을 리터럴로 정의해두고 있다.

다음은 [IBM Literal](https://www.ibm.com/docs/en/developer-for-zos/14.2?topic=SSQ2R2_14.2.0/com.ibm.ent.asm.zos.doc/asmr102056.html)에서 어셈블러가 프로그래머 대신 리터럴을 핸들링해주는 과정을 설명한다.

```
The assembler assembles the data item specified in a literal into a literal pool (See Literal pool).
It then assembles the address of this literal data item in the pool into the object code of the instruction that contains the literal specification. 
Thus, the assembler saves you a programming step by storing your literal data for you. 
The assembler also organizes literal pools efficiently, so that the literal data is aligned on the correct boundary alignment and occupies a minimum amount of space.
```

결국, 메모리의 효율적인 사용 등의 이유로 리터럴을 사용하는 것이다.

프로그래밍 언어마다 다르겠지만 대표적인 언어들을 찾아보니 대부분 literal pool(string pool이라는 개념으로 많이 적혀져 있었다)을 위해 메모리 어딘가에 저장 공간을 마련해두고 리터럴 값들을 이 곳에 저장하고, 동일한 리터럴 값이 참조될 때 그 주소를 반환한다.

Python에서는 이 string Literal을 'interned strings table'에 저장해둔다고 한다. Swift에서도 standard library에 있는 리터럴 테이블에서 해당 값을 꺼내온다. (이 때 타입 추론이 가능해지는 것..!)

그래서 얘네가 도대체 메모리의 어디에 위치하는지 궁금해서 찾아봤다.

어떤데선 힙, 어떤데선 데이터... 결국 Python, Swift 모두 그 위치는 인터프리터만이 안다고 한다.

### 그럼 Swift에서 String이 Heap에 올라간다는데 이건 뭐여?

라는 의문이 따라붙었다.

Swift에서 컴파일타임에 그 크기를 알 수 없는 값들(collection type, string)을 Heap에 저장한다고 한다.

동적 할당한 변수들이 힙에 저장된다는 개념을 놓고 보았을 때, Swift가 자동으로 배열에 대한 메모리를 동적 할당했다고 생각하면 이해가 쉽다. String도 비슷한 맥락으로 볼 수 있을 것 같다.

???: 뭐야? 인터프리터만 안다매? 

걔가 힙이래요. *(아닐 시 꼭 댓글이나 메일 부탁드립니다)*

### bonus: 같이 해결된 의문점

```swift
var a = "Apple pie"
var b = "Apple" + " " + "pie"
var c = "Apple"
c += " pie"

// a, b, c가 가리키고 있는 문자열의 주소는 모두 동일하다.
```

"Apple pie"와 "Apple" + " pie"는 전혀 다른거라 생각했는데, 리터럴 개념과 존재 이유를 알고나니 두개가 왜 같은지 이해됐다.

과정이 어찌됐든 결국 같은 놈을 메모리에서 찾은 거나 다름없으니까.

## 추가로 궁금했던거

그럼 이 리터럴이 충분히 커서 메모리 가용 범위를 넘어서면 어떤 오버플로우가 뜰까?

스위프트의 경우에는 힙에 저장되기 때문에 힙 오버플로우가 날 것이고, 만약 컴파일 타임에 그 값에 대해 크기를 계산할 수 있다면 컴파일이 불가능할 것이고, 컴파일까지 잘 됐다면 런타임에 프로그램이 제대로 동작하지 않을 것이다.

직접 실험해볼 수 없어서 아쉽다. ㅎㅅㅎ..

## 결론

변수/상수/스태틱 != 리터럴

### 참고할만한 링크

- [Swift Docs](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/lexicalstructure/#Literals)