---
title: "[KOCW] 운영체제 - 반효경, Process 동기화"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - kunbon
    - 운영체제
tags:
    - 운영체제
    - study
last_modified_at: 2022-03-05
---

# Process Synchronization

## Race Condition

```
          ------>        <------
Execution          Data          Execution
          <------        ------>
```
예) 하나의 연산은 count--, 다른 하나의 연산은 count++일 때 데이터를 받아와 각자 연산을 하고 데이터에 결과를 적용하면 맨 마지막에 연산된 결과만 반영된다.

- 하나의 데이터를 여러 주체가 동시에 접근하려고 하는 것을 Race Condition(경쟁 상태)이라 함.
- 공유 메모리를 사용하는 프로세스들, 커널 내부 데이터에 접근하는 루틴들 간에 이러한 간섭이 생길 수 있음.

### 발생하는 상황과 해결방법

1. 커널 수행 중 인터럽트 발생시
	- 해결방법: 커널 수행 중엔 인터럽트 처리를 하지 않게 한다. 커널 수행이 끝나면 인터럽트 처리.
2. 프로세스가 시스템 콜을 해 커널 모드로 수행중인 도중에 context switch가 일어날 때
	- 해결방법: 커널 모드에서 수행중일 땐 CPU를 선점하지 않게 한다. 커널 모드에서 사용자 모드로 다시 돌아갈 때 선점.
3. 멀티프로세서에서 공유 메모리 내의 커널 데이터
	- 하나의 프로세서가 데이터에 접근할 때 데이터에 대한 lock/unlock을 걸어야 한다.
	- 한 번에 커널에 하나의 프로세서만 들어갈 수 있게 한다. (비효율적인 방식)

## Process Synchronization 문제

- 공유 데이터의 동시 접근은 데이터의 불일치 문제를 발생시킬 수 있다.
- 일관성 유지를 위해 협력 프로세스 간의 실행 순서를 정해주는 매커니즘 필요하다.

* Race condition
- 여러 프로세스들이 동시에 공유 데이터에 접근하는 상황
- 데이터의 최종 연산 결과는 마지막에 그 데이터를 다룬 프로세스에 따라 달라진다

=> Race condition을 막기 위해 동시 접근은 동기화가 잘 되어야 한다.

## Critical-Section Problem

- 하나의 프로세스가 critical-section에 들어갔을 때 다른 모든 프로세스는 해당 섹션에 들어갈 수 없어야 한다.
- critical-section에 복수의 프로세스가 들어가 서로 다른 연산을 하게 되면, race condition 발생하기 때문.

### Critical-section
- 공유데이터에 접근하는 코드.
- 앞의 예시에서, a = a + 1, a = a - 1과 같은 공유데이터에 접근하는 코드.

## 프로그램적 해결법의 충족조건

#### Mutual Exclusion(상호 배제)
- 프로세스 하나가 critical section 부분을 수행중일 때 다른 모든 프로세스가 들어가면 안 됨.

#### Progress
- 아무도 critical section에 있지 않은 상태에서 그 섹션에 들어가고자 하는 프로세스가 있으면 들어가게 해 주어야 함.

#### Bounded Waiting
- 프로세스가 critical section에 들어가려고 요청한 후부터 그 요청이 허용될 때까지 다른 프로세스들이 critical section에 들어가는 횟수에 한계가 있어야 함.
- 즉, 기아현상을 막아야 함.

### 해결 방법 - Algorithm 1

```c
do {
	while(turn != 0); // P1이 기다림
	critical section  // P0이 실행됨
	turn = 1          // P1이 실행될 수 있도록 바꾸어줌
	remainder section
} while (1);
```

- mutual exclusion을 만족함
- 하지만, 아무도 critical section에 없을 때 쭉 critical section이 실행이 안 됨.
- 즉, progress를 만족하지 않음.

### 해결 방법 - Algoritm 2

```c
do {
	flag[i] = true;
	while (flag[j]);
	critical section
	flag[i] = false;
	remainder section
} while (1);
```

- mutual exclusion은 만족하지만, progress를 만족하지 않음.
- flag를 올려주지 않는 한 영원히 critical section 실행 안 됨.

### 해결 방법 - Algorithm 3(Peterson's Algorithm)

```c
do {
	flag[i] = true;
	turn = j;
	while (flag[j] && turn == j);
	critical section
	flag[i] = false;
	remainder section
} while(1);
```

- 모든 충족 조건을 만족한다.
- 하지만 Busy waiting(Spin lock) 문제점이 있음.
	- while문으로 계속 돌면서 wait함. 즉, 자신의 할당 시간 만큼 CPU와 메모리를 계속 사용하면서 wait하는 것.
- 소프트웨어적으로 해결하려고 하다 보니 이렇게 힘든 것..! 하드웨어적인 방식을 곁들이면 된다!

### Synchronization Hardware

```c
// synchronization variable
boolean lock = false;

// process
do {
	while(Test_and_Set(lock));
	critical section
	lock = false;
	remainder section
} while (1)
```

- 하드웨어적으로 Test and Set을 atomic하게 수행할 수 있도록 지원.
	- 값을 읽는 작업과, 값을 저장하는 작업을 동시에 하는 작업을 다른 프로세스에 영향을 받지 않고 수행할 수 있도록 함.
- 앞의 소프트웨어적인 방식으로 복잡하게 해결할 필요가 없다.

## Semaphores

- 앞의 방식들을 추상화시킴
- Semaphore S
	- 두 가지 atomic 연산에 의해서만 접근 가능한 추상 자료형
	- 정수 변수라고 할 때,
		- 양수이면 값을 하나 깎고 critical section에 진입, 그렇지 않으면 wait하는 P 함수와,
		- critical section에서 빠져나와 S의 값을 증가시키는 V 함수
	- 해당 함수 두 가지를 프로그래머가 소프트웨어적으로 가져다 쓰면 됨.
	- 프로그래머는 추상 자료형으로 제공되는 Semaphore의 구현부를 생각하지 않아도 됨. -> 훨씬 간단하게 프로그램을 작성할 수 있다는 것.

```c
semaphore mutex; // initially 1

do {
	P(mutex);
	critical section
	V(mutex);
	remainder section
} while(1);
```

- 해당 방식도 Busy-wait(spin lock).
- 하지만 Block & WakeUp(sleep lock)으로 구현할 수도 있다.

### Block & WakeUp Implementation

```c
// Semaphore를 다음과 같이 정의

typedef struct
{
	int value;
	struct process *L;
} semaphore;

semaphore S;

     S
 +-------+
 | value |
 |   L ------> PCB ----> PCB ----> PCB
 +-------+

```

- block
	- 커널이 block 함수를 호출한 프로세스를 suspend 시킴.
	- 해당 프로세스의 PCB를 semaphore의 wait queue에 넣음.
- wakeup(P)
	- block된 프로세스 P를 wakeup 시킴.
	- 해당 프로세스의 PCB를 ready queue에 넣음.


```c
P(S):

S.value--;
if(S.value < 0)
{
	// add process to S.L
	block();
}

V(S):

S.value++;
if (S.value <= 0)
{
	// remove process P from S.L
	wakeup(P);
}
```

- 여기서 S.value는 상황을 나타내는 것.

### Busy-wait VS Block & Wakeup

- Semaphore를 구현하는 방식에 있어서 Busy-wait은 계속 CPU 할당 시간을 태우면서 기다려야하지만, Block & Wakeup은 CPU를 반납하고 blocked 상태로 들어가서 기다리므로 일반적으로 후자가 효율적.
- 하지만, Block & Wakeup도 오버헤드가 든다.
	- critical section의 길이가 짧으면 Busy-wait도 괜찮다.

### Types of Semaphore

#### Counting Semaphore
- 도메인이 0 이상인 임의의 정수값.
- 주로 자원의 개수를 세는 데 사용.

#### Binary Semaphore
- 0 또는 1
- 주로 mutual exclusion(lock/unlock)에 사용.

### Semaphore 사용시의 주의점

#### Deadlock
- 둘 이상의 프로세스가 서로 상대방에 의해 충족될 수 있는 이벤트를 무한히 기다리는 현상
- Semaphore S, Q가 각 1로 초기화된 상태에서, 프로세스 0과 1이 다음과 같은 상태일 때
```
process0     process1

  P(S);        P(Q);
  P(Q);        P(S);
  ...          ...
  V(S);        V(Q);
  V(Q);        V(S);
```
- 프로세스 0이 P(S);로 S를 획득하고 CPU를 뺏겼을 때, 프로세스 1이 P(Q);로 Q를 획득하고 나면
- 각 프로세스 0과 1은 영원히 Q와 S를 획득하지 못한다. 이것이 교착상태.
- 이를 해결하기 위해, 프로세스 0과 1의 코드를 P(S); P(Q);로 통일시키면 된다.

#### Starvation
- indefinite blocking

## 출처

해당 내용은 KOCW 강의 중 [이화여자대학교의 운영체제-반효경 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323)의 내용을 정리한 것입니다.