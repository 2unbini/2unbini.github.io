---
title: "정보처리기사 실기 공부 day2, 화면 설계"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - kunbon
    - 정보처리기사
tags:
    - 정보처리기사
    - study
last_modified_at: 2021-10-02
---

# 중요한 것 정리

UI 개념/유형/설계 원칙/지침, UI 품질 요구사항, UML

# UI(User Interface)

## 개념

사용자와 시스템 사이에 의사소통을 돕는 물리적 매개체.

cf) UX(User Experience) : UI를 포함한 사용자가 직/간접적으로 사용하며 느낀 총체적 경험.

## 유형

- CLI(Command Line Interface)
    커맨드 라인(텍스트)으로 조작하여 사용하는 인터페이스
    
- GUI(Graphical User Interface)
    그래픽 기반으로 한 인터페이스로, 마우스나 펜을 이용함
    
- NUI(Natural User Interface)
    신체 부위를 사용하는 인터페이스로, 터치와 음성 등을 이용함
    
- OUI(Organic User Interface)
    모든 것을 사용할 수 있는 인터페이스로, 존재하는 모든 사물을 이용함

## 설계 원칙

**직유학유**

- 직관성(Intuitiveness) : 사용자가 쉽게 이해하고 사용할 수 있어야 함
- 유연성(Flexibility) : 오류를 최소화하고 사용자의 상호작용에 최대한 반응할 수 있어야 함
- 학습성(Learnability) : 사용자가 쉽게 학습할 수 있어야 함
- 유효성(Efficiency) : 사용자의 목적을 정확하게 달성할 수 있어야 함

## 설계 지침

사용자 중심, 일관성, 단순성, 결과 예측 가능, 가시성, 표준화, 접근성, 명확성, 오류 발생 해결

# UI 품질 요구사항

**기신사 효유이**

기술성, 신뢰성, 사용성, 효율성, 유지보수성, 이식성

## 기술성(Functaionality)

> 실제 수행 결과와 요구사항을 비교 분석해 시스템의 동작을 관찰하는 품질 기준.

1. 적절성(Suitality) : 적절한 기능 제공?
2. 정밀성(Accuracy) : 정확도? 올바른 결과 산출?
3. 상호 운용성(Interoperability) : 상호작용해 운영 가능?
4. 보안성(Security) : 비인가된 접근 차단 가능? 고의적인 접근 인지?
5. 호환성(Compliance) : 유사한 환경에서 표준, 관례, 규정 준수?

## 신뢰성(Reliability)

> 일정한 시간 동안 의도된 기능이 수행됨을 보장하는지에 대한 품질 기준.

1. 성숙성(Maturity) : 고장 회피 능력?
2. 고장 허용성(Fault tolerance) : 오류시 어느정도 성능 유지?
3. 회복성(Recoverability) : 오류시 성능, 데이터 등 복구 가능?

## 사용성(Usability)

> 행위를 정확하고 쉽게 인지할 수 있는지에 대한 품질 기준.

1. 이해성(Understandability) : 논리적 개념, 적용 가능성 구분에 드는 사용자의 노력 정도
2. 학습성(Learnability) : 학습에 드는 사용자의 노력 정도
3. 운용성(Operability) : 운용/통제에 드는 사용자의 노력 정도

## 효율성(Efficiency)

> 할당된 시간, 한정된 자원 안에서 얼마나 빨리 운용할 수 있는지에 대한 품질 기준.

1. 시간 효율성(Time Behavior) : 반응 시간, 처리 시간, 처리율
2. 자원 효율성(Resource Behavior) : 자원의 양과 지속 시간

## 유지보수성(Maintainability)

> 요구사항 개선, 확장이 얼마나 용이한지에 대한 품질 기준.

1. 분석성(Analyzability) : 수정이 요구되는 부분 확인에 필요한 노력 정도
2. 변경성(Changeability) : 수정에 필요한 노력 정도
3. 안정성(Stability) : 수정으로 인한 영향이 끼치는 위험 요소
4. 시험성(Testability) : 수정 후 검증에 필요한 노력 정도

## 이식성(Portability)

> 다른 환경에서 얼마나 쉽게 적용할 수 있는지에 대한 품질 기준.

1. 적용성(Adaptability) : 특별한 조치 없이 전환되는 능력
2. 설치성(Installability) : 설치하는 데 필요한 노력의 정도
3. 대체성(Replaceability) : 다른 소프트웨어 대신 사용할 수 있는 능력

# UML(Unified Modeling Language)

## 개념

객체지향 소프트웨어 개발에서 나온 산출물을 명세화, 시각화, 문서화할 때 사용하는 표준화된 범용 모델링 언어.

## 특징

- 가시화 언어 : 개념 모델 작성 시 오류가 적고 의사소통에 용이
- 구축 언어 : 다양한 프로그래밍 언어로 예측 가능, UML을 소스코드로 변환하여 구축 가능, 역공학 가능
- 명세화 언어 :  정확한 모델 제시, 완전한 모델 작성 가능
- 문서화 언어 : 시스템에 대한 평가 및 의사소통의 문서

## 구성 요소

**사관다**

- 사물 : 주제를 나타내는 요소. 추상적 개념. 단어에 있어 명사 또는 동사.
- 관계 : 사물의 의미를 명확히 해 주는 요소. 관계 표현. 단어에 있어 형용사 또는 부사.
- 다이어그램 : 사물과 관계를 그림으로 표현.

## 다이어그램

구조적 다이어그램(Structural Diagram)과 행위적 다이어그램(Behavioral Diagram)으로 나뉜다.

각각은 정적(Static) 다이어그램, 동적(Dynamic) 다이어그램으로도 생각할 수 있다.

### 구조적 다이어그램/정적 다이어그램

**클객컴배복패**

- 클래스(Class)
	클래스의 속성, 연산, 그 간의 정적 관계 표현
- 객체(Object)
	연관된 인스턴스를 객체와 객체 사이의 관계로 표현
- 컴포넌트(Component)
	물리적인 컴포넌트와 그 사이의 의존 관계 표현
- 배치(Deployment)
	컴포넌트 간 종속성 표현, 물리적 요소들 위치 표현
- 복합체 구조(Composite Structure)
	클래스 또는 컴포넌트가 복합 구조를 가질 때 내부 구조 표현
- 패키지(Package)
	유스케이스 또는 클래스 등을 그룹화한 패키지의 관계 표현

### 행위적 다이어그램/동적 다이어그램

**유시커상활타**

- 유스케이스(Usecase)
	기능 및 외부 요소를 사용자의 관점에서 표현
- 시퀀스(Sequence)
	객체 간 동적 상호작용(메시지)를 시간적 흐름으로 표현
- 커뮤니케이션(Communication)
	객체 간 메시지와 연관관계 표현
- 상태(State)
	속한 클래스 혹은 상호작용하는 객체에 따라 변화하는 상태 표현
- 활동(Activity)
	처리 로직이나 조건에 따른 처리 흐름 순서대로 표현
- 타이밍(Timing)
	상태 변화, 시간 제약 등을 명시적으로 표현
- 상호작용개요(Interaction Overview)
	상호작용 다이어그램 간의 제어 흐름 표현

## 스테레오 타입

### 개념

기본적인 UML 요소에 더해 사용할 수 있도록 하는 확장 매커니즘.

`<<>>` 길러멧(Guillemet) 기호를 사용해 표시하며, 포함, 확장 관계 및 인터페이스 등을 표현하는 데 사용한다.

### 유형

- << include >> : 하나의 유스케이스가 다른 유스케이스를 반드시 실행하는 포함 관계
- << extend >> : 하나의 유스케이스가 다른 유스케이스를 실행할수도 안할수도 있는 확장 관계
- << interface >> : 추상 메서드와 상수만으로 구성된 클래스, 즉 인터페이스 정의
- << entity >> : 기억 장치에 저장되어야 할 정보를 표현하는 클래스
- << boundary >> : 시스템과 외부 액터와의 상호작용을 담당하는 클래스
- << control >> : 시스템이 제공하는 기능의 로직 및 제어 담당하는 클래스

## UML 유형

### 클래스 다이어그램

#### 개념
    
객체지향 모델링 시 클래스의 속성 및 연산과 클래스 간 정적인 관계를 표현한 다이어그램이다.
    
#### 구성 요소
    
클래스 이름, 속성, 연산, 접근 제어자(접근 제한자), 관계

- 클래스(Class) : 공통의 속성, 연산, 관계, 의미 등을 공유하는 객체의 집합
- 속성(Attribute) : 인스턴스가 보유할 수 있는 값의 범위를 기술
- 연산(Operation), 메서드 : 클래스의 행위적 특성. 객체에 요청해 행동에 영향을 주는 것
- 접근 제어자(Access Modifier) : 클래스에 접근할 수 있는 정도를 표현 ( - private, + public, # protected, ~ default)

#### 클래스 간 관계(Relation)
    
연관 관계, 집합 관계, 복합 관계(포함 관계), 일반화 관계, 의존 관계, 실체화 관계
    
1. 연관 관계(Association)
    - 클래스가 서로 개념적으로 연결됐을 때, **실선**으로 표현.
    - 방향성은 화살표로 표현하되, 양방향일 땐 화살표 생략.
     - 다중성 표현은 숫자로 나타냄 ex) 1, 0..1, 0..*, *, 1..*, 등...

2. 집합 관계(Aggregation), 복합 관계(Composition)
    - 집합 관계와 복합 관계는 연관 관계에 포함된다.
    - 집합 관계 : 하나의 객체에 여러 개의 독립적인 객체들이 구성. 하나의 사물이 다른 사물에 포함.
        - 포함하는 쪽에 빈 마름모
    - 복합 관계 : 집합 관계의 특수한 형태로, 포함하는 사물의 변화가 포함되는 사물에 영향을 미침. 포함 관계라고도 함. 영구적이고, 집합 관계보다 더 강한 관계.
        - 포함하는 쪽에 속이 채워진 마름모

3. 일반화 관계(Generalization), 의존 관계(Dependency)
    - 일반화 관계 : 일반적인 개념을 부모(상위), 구체적인 개념을 자식(하위). 상속 관계라고도 함.
        - 일반적인 쪽에 빈 삼각형
    - 의존 관계 : 하나의 클래스가 다른 클래스를 사용. 영향 주는 시간 동안만 연관 유지.
        - 영향을 받는 쪽에 점선 화살표

4. 추상 클래스(Abstract Class), 인터페이스(Interface), 실체화 관계(Realization)
    - 추상 클래스 : 동일한 부모를 가지는 클래스를 묶어 상속으로 기능을 확장시키는 것이 목적.
        - extends, <<abstract>>, 부모 클래스 쪽에 빈 삼각형
    - 인터페이스 : 구현하는 모들 클래스에 대해 특정한 메서드가 반드시 존재하도록 강제하는 역할.
        - implements, <<interface>>, 인터페이스 쪽에 점선 빈 삼각형
    - 실체화 관계 : 추상 클래스나 인터페이스를 상속받아 자식 클래스가 추상 메서드를 구현할 때 사용.
        - 추상 클래스, 인터페이스의 형태와 동일

#### 참고 그림

![](https://upload.wikimedia.org/wikipedia/commons/thumb/e/ed/UML_diagrams_overview.svg/840px-UML_diagrams_overview.svg.png)

출처 : 위키피디아
    
### 유스케이스 다이어그램
    
#### 개념

시스템이 제공하고 있는 기능 및 그와 관련된 외부 요소를 사용자의 관점에서 표현하는 다이어그램.
        
#### 구성 요소
        
유스케이스, 액터, 시스템, 시나리오, 이벤트의 흐름
        
- 유스케이스(Usecase) : 시스템이 제공하는 서비스/기능. 액터가 시스템을 통해 수행하는 행위.
- 액터(Actor) : 시스템과 상호작용해 이벤트를 시작하게 하는 객체.
- 시스템(System) : 전체 시스템.
- 시나리오 : 발생되는 이벤트의 흐름
- 이벤트의 흐름 : 사람, 시스템, 하드웨어, 시간의 흐름에 의해 시작

#### 유스케이스 다이어그램의 관계
        
액터-유스케이스, 유스케이스-유스케이스 간 관계 있을 수 있음.
        
포함 관계, 확장 관계, 일반화 관계
        
1. 포함 관계(include)
    - 유스케이스를 실행할 때 다른 유스케이스가 반드시 실행되는 관계. 2개 이상의 유스케이스 이벤트 흐름에서 중복적인 부분이 발생하면 포함 관계를 설정.
    - <<include>> 로 표현
2. 확장 관계(extend)
    - 특정 조건에서 한 유스케이스로만 확장되는 관계.
    - <<extend>> 로 표현
3. 일반화 관계(generalization)
    - 추상적인 액터를 좀 더 구체화된 액터로 표현해주는 관계.
    - 추상적인 액터 쪽에 빈 삼각형

#### 참고 그림

![유스케이스](https://upload.wikimedia.org/wikipedia/commons/thumb/1/1d/Use_case_restaurant_model.svg/1920px-Use_case_restaurant_model.svg.png)

출처 : 위키피디아
    
### 시퀀스 다이어그램
    
#### 개념
        
객체 간 상호작용을 메시지 흐름으로 표현한 다이어그램. 시간적 개념을 중심으로 모델링하는 과정. 

객체의 오퍼레이션과 속성을 상세히 정의해야 함. 시퀀스 다이어그램은 유스케이스를 실현(Realization)한다.
        
#### 구성요소
        
객체, 생명선, 실행, 메시지
        
- 객체(Object) : 위쪽에 표시. 아래에 생명선. 사각형 안의 밑줄 친 형태로 명시
- 생명선(Lifeline) : 객체로부터 뻗어 나가는 점선. 실제 시간이 흐름에 따라 생명주기 안에서 객체에 발생하는 이벤트 명시.
- 실행(Activation) : 직사각형으로 오퍼레이션이 실행되는 시간을 표현. 길어질수록 수행 시간이 긺.
- 메시지(Message) : 객체 간 상호작용은 메시지 교환으로 이루어짐. 객체에서 객체로 메시지 전달하여 오퍼레이션 수행. 화살표로 표시.

#### 참고 그림

![시퀀스](https://upload.wikimedia.org/wikipedia/commons/thumb/9/9b/CheckEmail.svg/1200px-CheckEmail.svg.png)

출처 : 위키피디아
    
### 패키지 다이어그램
    
#### 개념
        
서로 다른 패키지들 사이의 의존 관계를 표현하기 위한 다이어그램.
        
#### 구성 요소
        
패키지, 의존 관계
        
- 패키지 : 폴더 모양으로 표기. 요소들을 그룹으로 조직.
- 의존 관계 : 점선 화살표로 표기. 의존성을 나타내기 위해 스테레오 타입 <<import>>, <<access>> 추가할 수 있음.

#### 참고 그림

![](https://upload.wikimedia.org/wikipedia/commons/b/bc/Deployment_Model_Structure.PNG)

출처 : 위키피디아
    
### 활동 다이어그램
    
#### 개념
        
어떤 기능을 수행하는지 객체의 처리 로직이나 조건에 따른 처리의 흐름을 순서대로 표현.
        
하나의 유스케이스, 혹은 유스케이스들 사이에서 발생하는 복잡한 처리의 흐름 명확하게 표현할 수 있음.
        
처리 과정 동안 일어나는 일들을 단계적으로 표현. → 처리되면 다음으로 넘어감. 시작과 종료 항상 존재.
        
#### 구성요소
        
시작점, 전이, 액션/액티비티, 조건(판단)노드, 병합 노드, 포크 노드, 조인 노드, 구획면
        
- 시작점(Initial Point)
    - 활동의 시작. 검은색 동그라미로 표시. 유일무이.
- 전이(Transition)
    - 실행의 흐름. 화살표로 표시.
- 액션(Action), 액티비티(Activity)
    - 일의 처리와 실행. 액션은 최소 작업, 액티비티는 액션들로 분리될 수 있는 작업. 모서리가 둥근 사각형으로 안에 명칭 써 넣음.
- 종료점(Final Node)
    - 처리의 종료. 여러 개 존재할 수 있음. 눈알 모양(검은색 동그라미가 있는 빈 동그라미)
- 조건(판단) 노드 (Decision Node)
    - 조건에 따른 제어 흐름 분리. 마름모로 표시. 들어오는 흐름은 하나, 나가는건 여러개.
- 병합 노드(Merge Node)
    - 여러 흐름이 하나로 합쳐짐. 마름모로 표시. 들어오는 흐름 여러개, 나가는 건 한개.
- 포크 노드(Fork Node)
    - 평행적으로 수행되는 흐름 분리. 가로선과 분리되는 흐름은 세로 화살표로 표시.
- 조인 노드(Join Node)
    - 포크 노드로 나눠진 흐름을 하나로 모음. 가로선과, 분리 및 병합된 흐름은 세로 화살표로 표시.
- 구획면(Swim Lane)
    - 액티비티 수행의 주체를 구분하는 면. 실선으로 면을 만들어 구분.

#### 참고 그림

![](https://upload.wikimedia.org/wikipedia/commons/thumb/e/e7/Activity_conducting.svg/1200px-Activity_conducting.svg.png)

출처 : 위키피디아
    
### 상태 다이어그램
    
#### 개념
        
하나의 객체가 속한 클래스의 상태 변화 혹은 다른 객체와의 상호작용에서 상태가 변화하는 양상을 표현.
        
#### 구성요소
        
상태, 시작 상태, 종료 상태, 전이, 이벤트, 전이 조건
        
- 상태(State)
    - 객체가 존재할 수 있는 조건. 둥근 사각형 안에 상태 명시.
- 시작 상태
    - 객체의 시작 상태. 검은 동그라미로 표시.
- 종료 상태
    - 객체의 종료 상태. 눈알 모양(검은색 동그라미가 있는 빈 동그라미)으로 표시.
- 전이
    - 상태가 다른 상태로 변화. 상태의 변화. 화살표로 표시.
- 이벤트
    - 상태의 전이를 만들게 하는 요인. 전이(화살표) 위에 이벤트 이름 명시.
- 전이 조건
    - 특정 조건을 만족할 때 전이가 일어나길 바랄 때 사용. 전이(화살표) 위에 [전이 조건] 명시.

#### 참고 그림

![](https://upload.wikimedia.org/wikipedia/commons/a/ae/Statechart_vs_flowchart.png)

출처 : 위키피디아
    
### 커뮤니케이션 다이어그램
    
#### 개념
        
시퀀스 다이어그램처럼 객체들 간의 메시지를 표현. 더해서 객체 간 관계도 표현하는 다이어그램.
        
#### 구성요소
        
액터, 객체, 링크, 메시지
        
- 액터(Actor)
    - 서비스를 요청하는 요소(사람, 시스템 등)
- 객체(Object)
    - 메시지를 주고받는 주체. 사각형 안에 콜론(:)을 기준으로 앞은 객체 이름, 뒤는 클래스 이름 명시.
- 링크(Link)
    - 객체들 간의 관계. 실제 객체 간의 관계. 실선으로 표시.
- 메시지(Message)
    - 객체들 간에 주고받는 내용. 메시지를 받는 쪽으로 화살표 표시. 링크에 메시지 명시.

#### 참고 그림

![](https://upload.wikimedia.org/wikipedia/commons/thumb/b/b4/UML_Communication_diagram.svg/1200px-UML_Communication_diagram.svg.png)

출처 : 위키피디아
    
### 컴포넌트 다이어그램
    
#### 개념
        
컴포넌트와 그 사이의 의존 관계를 나타내는 다이어그램. 구현 단계에서 사용되는 다이어그램이니만큼, 코드 기반의 물리적 구조로 표현됨.
        
#### 구성요소
        
컴포넌트, 인터페이스, 의존 관계
        
- 컴포넌트
    - 탭이 달린 직사각형으로, 안에 이름 명시. 컴포넌트 이름 앞에 패키지 이름을 붙이거나 내부 오퍼레이션을 보여줄 수도 있음.
- 인터페이스
    - 컴포넌트에 인터페이스 적용. 인터페이스 쪽으로 점선 화살표로 표시.
- 의존 관계
    - 컴포넌트 간 의존 관계. 의존하는 쪽으로 점선 화살표로 표시.

#### 참고 그림

![](https://upload.wikimedia.org/wikipedia/commons/thumb/b/b8/Policy_Admin_Component_Diagram.PNG/1200px-Policy_Admin_Component_Diagram.PNG)

출처 : 위키피디아