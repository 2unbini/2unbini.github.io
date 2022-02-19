---
title: "[KOCW] 운영체제 - 반효경, 시스템 구조와 프로그램 실행"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - kunbon
    - 운영체제
tags:
    - 운영체제
    - study
last_modified_at: 2022-02-16
---

# 시스템 구조와 프로그램

컴퓨터 시스템에서 하드웨어가 어떻게 동작하는지, 하드웨어 위에서 프로그램이 어떻게 동작하는지에 대한 내용.

## 컴퓨터 시스템 구조

``` markdown
<--Host Computer-->
+-----+   +--------+
| CPU | - | Memory | - I/O Device (keyboard, monitor, disk)
+-----+   +--------+
```
- CPU: 메모리에서 인스트럭션 하나씩 읽어들여 실행.
	- **interrupt line**: cpu는 메모리에 있는 인스트럭션만 실행함. I/O 디바이스에서 들어오는 인터럽트를 알아채기 위함.
	- **mode bit**: 어떤 모드인지 알려주는 비트.
	- **device controller**: 각 I/O 디바이스를 전담하는 CPU.
	- CPU가 인터럽트를 너무 많이 받는다 -> 비효율적 -> DMA Controller
		- **DMA Controller**: CPU, DMA Controller 모두 메모리 영역에 접근할 수 있음. 중간중간 local buffer에 있는 값을 메모리에 가져오고, 한번만 CPU에게 인터럽트를 걸어 CPU의 효율적인 사용을 도움.
- Memory: CPU의 작업 공간.
	- **local buffer**: 각 I/O 디바이스의 작업 공간.
	- **memory controller**: 메모리 접근을 제어하는 기능.
- timer: 특정 프로그램이 CPU를 독점하는 것을 막는 것.
	- 운영체제가 사용자 프로그램에 cpu를 주는 것은 가능하지만, 줬던 것을 뺏을 순 없다. 그래서 추가적인 하드웨어(timer)를 통해 cpu를 제어할 수 있게 하는 것.

#### 동작

1. OS가 어떠한 프로그램을 실행할 때, timer로 시간 제약을 걸어 놓고, 메모리에 올라간 해당 프로그램을 CPU에게 할당한다.
2. CPU는 그 프로그램 인스트럭션을 메모리로부터 읽어들여 수행한다.
3. 그 과정에서 I/O 디바이스나 timer에 의한 인터럽트를 감지하고, 그 인터럽트에 따라 device controller에게 해당 I/O 디바이스에 입/출력을 넘겨주거나 CPU에게 다른 프로그램 인스트럭션을 읽어들여 실행하게 한다.
4. CPU가 프로그램을 실행하고 있는 때에, 프로그램이 I/O를 해야 하면 OS 시스템 콜로 트랩을 건다.
5. OS는 이를 올바른 요청인지 확인하고, 다시 명령을 실행한다.

즉, 현대의 운영체제는 **인터럽트**에 의해 구동된다.

### Mode bit

- 사용자 프로그램의 잘못된 수행으로 다른 프로그램 및 운영체제에 피해가 가지 않도록 하기 위한 보호 장치.

- 1: 사용자 모드(사용자 프로그램 수행)
	- 보안상의 목적으로 제한된 일만 수행.
- 0: OS모드(커널 모드)
	- 운영체제가 CPU를 가지고 있을 땐 무슨 일(Memory, I/O device 접근)이든 가능.
	- interrupt나 exception발생시 0으로 바뀜.

### Timer

- 특정 프로그램이 CPU를 독점하는 것을 막기 위함.
	- 정해진 시간이 흐른 뒤 운영체제에게 제어권이 넘어가도록 함.

### Device Controller

- I/O Device Controller
	- 해당 I/O 장치를 관리하는 일종의 작은 CPU
	- 제어정보를 위한 register: cpu -> device controller -> I/O device
	- 데이터를 담는 local buffer: I/O data -> local buffer -> memory -> CPU
	- I/O는 실제 device와 Local buffer 사이에서 일어남.

* device driver: 소프트웨어. 하드웨어에 접근하기 위한 코드.

### I/O의 수행
- OS에 의해 수행됨.
- 사용자 프로그램은 어떻게 I/O를 하는지?
	- **시스템콜**: OS 함수 호출 <- 프로그램이 직접 interrupt를 걸 수 있도록 함.
		=> Trap을 이용해 인터럽트 거는 것. 왜? I/O 를 시작하기 위해.
	- 올바른 요청인지 OS가 확인한 뒤 명령 수행.
- I/O 요청 -> 소프트웨어 인터럽트, I/O 끝남을 알림 -> 하드웨어 인터럽트

### Interrupt

- 인터럽트 당한 시점의 레지스터와 program counter를 저장한 뒤 CPU의 제어를 인터럽트 처리 루틴에 넘김.

#### 하드웨어 인터럽트(Interrupt): 하드웨어가 발생시킨 인터럽트
- ex) I/O, Timer 등

#### 소프트웨어 인터럽트(Trap)
- Exception: 프로그램이 오류를 범한 경우
- System Call: 프로그램이 커널 함수를 호출하는 경우

#### 인터럽트 벡터
- 인터럽트 처리 루틴 주소를 가짐. 인터럽트 종류마다 어디에 있는 함수를 실행해야 하는지 정의해 놓은 일종의 테이블.

#### 인터럽트 처리 루틴
- 인터럽트 처리 커널 함수. 실제로 인터럽트 처리하는 부분/함수.

### 동기식 입출력과 비동기식 입출력

#### 동기식 입출력(synchronous I/O)
- I/O 요청 후 입출력 작업이 완료된 후에야 제어가 사용자 프로그램에 넘어감
- 구현 방법 1
	- I/O가 끝날 때까지 CPU를 가지고 있음 -> CPU 낭비
	- 매 시점 I/O가 한번만 일어날 수 있음
- 구현 방법 2
	- I/O가 완료될 때까지 해당 프로그램에게서 CPU 빼앗음
	- I/O 처리를 기다리는 줄에 해당 프로그램을 줄 세움
	- 다른 프로그램에게 CPU 넘겨줌

#### 비동기식 입출력(asynchronous I/O)
- I/O가 시작된 후 입출력 작업이 끝나길 기다리지 않고 제어가 사용자 프로그램에 바로 넘어감

두 방법 모두 I/O의 완료를 **interrupt**를 통해 알려준다.

### DMA(Direct Memory Access) Controller

- I/O 컨트롤러의 자잘한 인터럽트 => CPU의 오버헤드
- 효율적 이용을 위해 I/O 버퍼에 어느정도 블럭에 해당하는 데이터가 입력되면 그 때 CPU에게 알려줌
- CPU의 중재 없이 device controller가 buffer storage의 내용을 메모리에 block단위로 직접 전송할 수 있게 함.

### 서로 다른 입출력 명령어

CPU에서 메모리에 접근하는 인스트럭션과 I/O 디바이스에 접근하는 인스트럭션이 분리된 방식이 있고, 메모리의 연장선으로 I/O 디바이스에 접근하는 인스트럭션이 있다.

후자를 Memory Mapped I/O라고 함. 전자는 일반적인 I/O.

### 저장장치 계층 구조

- CPU가 직접 접근: primary(executable)
- CPU가 직접 접근X: secondary
	- speed: primary > secondary
	- cost: primary > secondary
	- volatility(휘발성): primary > secondary
	- caching: 재사용을 목적으로 하는 것. speed와 cost를 조금 더 효율적으로 사용하기 위함.

## 프로그램의 실행(메모리 load)

- 프로그램은 파일시스템에 파일 형태로 저장.
- 이를 실행시키면 그 프로그램의 메모리 주소 공간(Virtual memory)이 만들어짐. 이 때 프로그램이 프로세스가 됨.
	- Virtual Memory: stack-data-code
		- 각 프로그램마다 독자적으로 가지고 있는 메모리 주소 공간을 뜻함.
- 이를 물리적인 메모리에 올려서 실행. 당장 필요한 부분만 메모리에 올리고, 사용하지 않는 것은 메모리에서 쫓아냄.
	- virtual -> physical 되면서 주소 변환이 일어남.
- swap area에 있는 내용은 전원이 꺼지면 휘발되는 메모리의 연장 공간.
- file system은 프로그램 등 파일을 저장하는 비휘발성 공간.

### 커널 주소 공간의 내용

- stack
	- 커널 스택(프로그램마다 따로 커널 스택을 별도로 가짐)
- data
	- 운영체제가 이용하는 자료구조
	- CPU, Memory, Disk, PCB(Process Control Block)
- code
	- 커널 코드
	- 시스템콜, 인터럽트 처리 코드
	- 편리한 서비스 제공을 위한 코드

### 사용자 프로그램이 사용하는 함수

- 함수(Function)
	- 사용자 정의 함수: 프로그램에서 정의한 함수.
	- 라이브러리 함수: 갖다 쓰는 함수. 프로그램 실행 파일에 포함되어 있음.
	- 커널 함수: 운영체제 프로그램의 함수.
		- 커널 함수 호출 == 시스템 콜 (커널 주소 공간의 code 영역에 있음) 

사용자 정의 함수, 라이브러리 함수: (프로세스 주소 공간의 code 영역에 있음)

### 프로그램의 실행

- 프로그램은 user mode와 kernel mode를 쭉 반복하다가 종료된다.

```
A의 주소공간     Kernel의 주소공간      A의 주소공간     Kernel의 주소공간
user mode       kernel mode       user mode       kernel mode
-----------> ------------------> ------------> ------------------>
|           |                   |             |                  |
program    system          return from      system            program
begins      call             kernel          call               ends
```

## 출처

해당 내용은 KOCW 강의 중 [이화여자대학교의 운영체제-반효경 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323)의 내용을 정리한 것입니다.