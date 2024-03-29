---
title: "정보처리기사 실기 공부 day6, 프로그래밍 언어 활용"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - kunbon
    - 정보처리기사
tags:
    - 정보처리기사
    - study
last_modified_at: 2021-10-06
---

# 주의할 것 정리

### C
- for 반복문에서 i, j의 위치
- switch 문에서 break 없으면 다음 case로 넘어감
- switch 문에서 하드코딩된 case (ex: switch(3) ... case 3: ... )

### Python
- 파이썬 슬라이싱, range는 앞 인덱스 포함, 뒤 인덱스 미포함
- 파이썬 자료형
  - [] : 배열, {} : 세트(중복X, 순서X) 혹은 딕셔너리(키-밸류), () : 튜플(값 변경X)
- 파이썬 `insert(i, elem)` => i 인덱스에 elem 집어넣음. 원래 i인덱스에 있던 애는 i+1인덱스로 밀려남.
- a가 배열일 때, print(a[1:4]) 인 경우, 배열 형식으로 출력됨. ex) ['B', 'C', 'D']
- 클래스 멤버 함수의 첫 번째 인자로 'self' 들어감 => 없으면 객체 생성 없이도 사용할 수 있는 함수로 선언됨.

### JAVA
- JAVA 상속 관련...
  - 상위 클래스에선 하위 클래스의 메서드나 값에 접근 X, 상위 클래스를 상속받는 하위 클래스는 상위 클래스의 메서드나 값에 접근 O
  - 클래스 생성시 객체의 타입과 생성되는 객체는 다를 수 있다.
  ```java
  class Parent {
      void show() { System.out.printIn("Parent"); }
  }
  class Child extends Parent {
      void show() { System.out.printIn("Child"); }
  }
  public void main() {
      Parent pa = new Child();
      pa.show()
      // pa 의 타입은 Parent지만, Child 객체를 생성했으므로 Child 객체의 show() 실행
  ```
  - super(10); => 상위 객체의 생성자에 10을 넣어서 활용. 상위 객체에 접근할 때 `super` 사용함!
- static String check(int n) {...} : 변수/메서드 앞에 `static`이 붙으면 객체 생성 없이도 사용할 수 있음!
- JAVA - interface - implements
- 클래스 관련 내용들 정리

# 클래스

## 개념

객체지향 프로그래밍에서 객체를 정의하는 틀로, 변수와 메서드를 가진다. 정보처리기사에서 나오는 프로그래밍 언어 중 JAVA, C++, Python에서 사용할 수 있다.

## 접근 제어자

- public : 모든 클래스에서 접근 가능
- private : 해당 클래스에서만 접근 가능
- protected : 해당 클래스와 상속받은 하위 클래스에서 접근 가능
- default : 같은 패키지 안에서 접근 가능

## 클래스 사용

### 정의

#### C++

```c++
class ClassName {
private:
    int a;
public:
    int func(void) {
        cout << "function" << endl;
        return 1;
    }
}
```

#### JAVA

```java
public class ClassName {
    private int a;
    public int func(void) {
        System.out.printIn("function");
        return 1;
    }
};
```

#### Python

```python
class ClassName:
    def function(self):
        print("function")
        return 1
```

### 선언

#### C++

```c++
void main() {
    ClassName a;
    ClassName *b = new ClassName();

    a.function();
    b->function();

    delete b;
}
```

#### JAVA

```java
public static void main(String[] args) {
    ClassName a = new ClassName();
    a.function();
}
```

#### Python

```python
a = ClassName()
a.function()
```

### 자기 자신 참조

- C++, JAVA : this
	- C++ : this -> 변수;
	- JAVA : this.변수;
- python : self
	- self.변수

## 생성자(Constructor)

### 개념

클래스 객체가 생성될 때 자동으로 호출되는 메서드로, 초기화할 때 사용함.

## 소멸자(Destructor)

### 개념

생성자의 반대로, 객체를 제거할 때 사용되는 메서드.

### 생성자, 소멸자 구현

#### C++

```c++
class A {
public:
    A(int a) {
        cout << "construct" << endl;
    };
    ~A() {
        cout << "destruct" << endl;
    };
};
```

#### JAVA

```java
public class A {
    public A(int a) {
        System.out.printIn("construct");
    };
    public void finalize() {
        System.out.printIn("destruct");
    };
}
```

#### Python

```python
class A:
    def __init__(self):
        print("construct")
    def __del__(self):
        print("destruct")
```

## 상속(Inheritance)

### 개념

한 객체의 속성(변수, 메서드 등)을 다른 객체가 물려받는 것.

#### C++

```c++
class A {
	...
};

// 상속
class B : public A {
	...
};
```

#### JAVA

```java
public class A {
	...
}

// 상속
public class B extends A {
	...
}
```

#### Python

```python
class A:
	...

# 상속
class B(A):
	...
```

### 오버로딩(Overloading)

- 같은 이름의 메서드를 여러 개 정의할 수 있는 기능. 매개변수의 개수가 다르거나, 같다면 타입이 다른 여러 메서드를 정의할 수 있다.
- 파이썬은 오버로딩 기능이 없다.

### 오버라이딩(Overriding)

- 상위 클래스의 메서드를 하위 클래스에서 재정의할 수 있는 기능.
- C++ : `virtual` 키워드 사용
- 메서드 이름, 매개변수의 개수, 타입, 반환형 등 메서드에 대한 모든 정보가 같아야 한다.

### 상위 클래스 접근

- C++ : 콜론 두 번(`::`)
- JAVA, Python : `super`

#### C++

```c++
class A { ... };
class B : public A {
    A::function();
};
```

#### JAVA

```java
public class A { ... }
public class B extends A {
    super.function();
}
```

#### Python

```python
class A : ...
class B(A) :
    super().function()
```

### 추상 클래스

- 구현되지 않은 메서드로, 하위 클래스에서 구현하도록 강제하는 기능이다.

#### C++

```c++
class ClassName {
public:
    virtual int function()=0;
};
```

#### JAVA

```java
abstract class ClassName {
    abstract int function();
}
```

#### Python

```python
class ClassName:
    def function(self):
        pass
```

### 인터페이스

- JAVA에서만 사용. 다형성 극대화, 유지보수성 높임.
- 일종의 추상클래스로, 추상 메서드와 상수를 멤버로 가짐.
- 구현된 것은 없는 일종의 설계도.

#### JAVA
```java
interface ClassName {
    void function();
}

class A extends ClassName {
    public void function() {
        System.out.printIn("Hello");
    }
}
```

#### 참고

수제비 2021 정보처리기사 실기
