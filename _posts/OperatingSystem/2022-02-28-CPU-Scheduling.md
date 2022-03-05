---
title: "[KOCW] 운영체제 - 반효경, CPU 스케줄링"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - kunbon
    - 운영체제
tags:
    - 운영체제
    - study
last_modified_at: 2022-02-28
---

# CPU Scheduling

- CPU Burst와 I/O Burst를 반복하는 것이 프로그램 실행이다.
	- CPU Burst: CPU의 실행, I/O Burst: I/O의 실행
- CPU가 많이 실행되는 것(계산 위주) -> CPU Bound Job
- I/O가 많이 실행되는 것(사용자 인터랙션 잦음) -> I/O Bound Job
	- I/O Bound Job(=> Interactive Job)에 적절한 자원을 제공하는 것 등 **시스템 자원을 효율적으로 사용하기 위해** CPU 스케줄링이 필요하다.
- 어떤 프로그램에 CPU를 줄 것인가, 그리고 그 프로그램에서 계속 CPU를 점유할 것인가

## CPU Scheduler & Dispatcher

### CPU Scheduler(CPU 스케줄러)
- Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고르는 것.

### Dispatcher
- CPU 제어권을 프로세스에 넘겨주는 것.
- 이 과정을 문맥 교환(context switch)이라고 한다.

### CPU 스케줄링이 필요한 경우
- 프로세스에 상태 변화가 있는 경우.
	- Running -> Blocked(I/O 요청 등)
	- Running -> Ready(timer interrupt)
	- Blocked -> Ready(우선순위가 높은 프로세스의 I/O 완료)
	- Terminate
		- 1, 4는 자진 반납(nonpreemptive), 2, 3은 강제 반납(preemptive).

현대의 스케줄링은 선점형(preemptive) 알고리즘으로 진행된다

- 선점형(preemptive): CPU가 다른 프로세스에 의해 선점당할 수 있음
- 비선점형(nonpreemptive): CPU 사용이 끝날 때까지 다른 프로세스의 영향을 받지 않음

### Scheduling Criteria, 성능 척도

매 CPU Burst마다 처리량/시간 등은 계속 계산된다.

#### 시스템 입장
- CPU 하나로 최대한 많은 것을 할 때
	- CPU Utilization(이용률): 전체 시간에서 CPU가 놀지 않고 일한 시간
		keep the CPU as busy as possible
	- CPU Throughput(처리량): 주어진 시간 동안 몇 개의 처리를 했는지에 대한 양
		no. of processes that complete their execution per time limit

#### 프로그램 입장
- 더 빨리 처리되는 것(시간적인 문제)
	- Turnaround Time(소요 시간/반환 시간): CPU를 기다리고 실행하고 끝나는 모든 시간
		amount of time to execute a particular process
	- Waiting Time(대기 시간): CPU를 기다리고 있는 시간, 한 번의 CPU Burst 동안에 CPU를 기다린 모든 시간
		amount of time a process has been waiting in the ready queue
	- Response Time(응답 시간): CPU를 처음으로 얻기까지 걸린 시간
		amount of time it takes from when a request was submitted until the first response is produced

## CPU Scheduling Algorithm

### FCFS(First Come First-Served)

- 비선점형 스케줄링
- 처음 도착한 프로세스부터 실행한다
- Convoy effect: 실행 시간이 긴 프로세스가 먼저 점유해버리면 그만큼 짧은 실행 시간이 걸리는 프로세스가 기다려야 하는 것. -> 비효율적이다.
- 앞에 어떤 프로세스가 있느냐에 따라 전체의 프로세스 대기 시간에 큰 영향을 끼친다.

### SJF(Shortest Job First)

- CPU Burst가 가장 짧은 프로세스에게 먼저 스케줄링하는 것.
- CPU Burst time을 활용해 스케줄링함
- Two Schemes
	- nonpreemptive
		- 일단 CPU를 점유하면 CPU Burst가 끝날 때까지 다른 프로세스에게 선점당하지 않음.
	- preemptive
		- 현재 점유하고 있는 프로세스보다 CPU Burst가 더 짧은 프로세스에게 선점당함.
		- Shortest-Remaining-Time-First(SRTF)라고도 부른다.
- 평균 대기 시간의 최솟값을 보장한다 => preemptive인 경우

#### 문제점
- Starvation(기아현상). CPU 사용량이 긴 프로세스는 영원히 CPU를 점유할 수 없게 될 수 있다.
- CPU Burst time을 미리 알 수 없다. 과거에 얼마나 썼는지에 따라 예측할 순 있음.

### Priority Scheduling

- 우선순위 스케줄링: 우선순위가 가장 높은 프로세스에게 스케줄링
- Two Schemes(SJF와 동일. 현재 점유하고 있는 프로세스와 새로 들어온 프로세스의 우선순위에 따라 선점 가능/불가능)
	- nonpreemptive
	- preemptive
- SJF도 우선순위 스케줄링의 일종이다.
	- priority = predicted next CPU Burst Time

#### 문제점
- Starvation(기아현상). 우선순위가 낮은 프로세스는 영원히 CPU를 점유할 수 없게 될 수 있다.
#### 해결 방법
- Aging(노화). 시간이 지남에 따라 우선순위를 조금씩 늘려준다.

### Round Robin(RR)

- 현대의 CPU 스케줄링은 Round Robin에 기반하고 있다.
- 각 프로세스는 동일한 크기의 할당 시간(time quantum, 보통 10-100ms)을 가지고,
- 이 시간이 지나면 프로세스는 선점(preemptive)당하고 ready queue의 마지막에 가서 다시 기다린다.
- 이 알고리즘을 사용하면 프로세스의 **응답 시간이 가장 줄어들며**, Burst Time을 예측할 필요도 없어진다.
- n개의 프로세스가 ready queue에 있고 할당 시간이 q time unit인 경우, 각 프로세스는 CPU 시간의 1/n을 얻는다.
	- 대기시간이 프로세스의 실행 시간과 비례한다 => (n-1)q time unit
- SJF보다 평균 소요시간은 더 걸리지만 응답 시간은 덜 걸린다.

#### 성능
- q large -> FCFS
- q small -> Context Switch 오버헤드가 커진다.

### Multilevel Queue
- 프로세스의 우선순위에 따라 다중 큐를 구성
- Ready queue를 여러 개로 분할
	- foreground(interactive)
	- background(batch - no human interaction)
- 각 큐는 독립적인 스케줄링 알고리즘을 가짐
	- foreground -> RR(응답시간이 짧으므로)
	- background -> FCFS(Context Switch 오버헤드가 적으므로)
- 큐에 대한 스케줄링이 필요
	- Fixed priority scheduling -> Starvation 발생 가능
	- Time slice
		- 각 큐에 CPU time을 적당한 비율로 할당
		- e.g. foreground -> 80%, background -> 20%...


### Multilevel Feedback Queue

- 프로세스가 다른 큐로 이동할 수 있음
- 처음 들어오는 프로세스는 우선순위 가장 높은 큐로, 우선순위가 높은 큐일수록 RR time quantum이 짧다.
	- 할당 시간을 모두 끝낸 뒤 ready queue의 마지막으로 갈때마다 우선순위가 낮은 큐로 이동한다.
	- 사용시간이 짧은 프로세스일수록 우선순위가 높음.

### Multi-Processor Scheduling

- CPU가 여러 개인 경우, 스케줄링이 더 복잡해짐.
	- Homogeneous processor
		- queue에 한줄로 세워서 각 프로세서가 알아서 꺼내가게 함
		- 특정 프로세서에서 수행되어야 하는 프로세스가 있는 경우에 문제가 더 복잡해짐
	- Load sharing
		- 일부 프로세서에 job이 몰리지 않도록 부하를 적절히 공유하는 매커니즘 필요
		- 별개의 큐를 두는 방법 vs 공동 큐를 사용하는 방법
	- Symmetric Multiprocessing (SMP)
		- 각 프로세서가 각자 알아서 스케줄링 결정
	- Asymmetric Multiprocessing
		- 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임지고 나머지 프로세서는 그를 따름

### Real Time Scheduling

- 정해진 시간 안에 반드시 실행되어야 하는 것 -> real time job
- Hard real-time systems
	- Hard real-time task는 정해진 시간 안에 반드시 끝내도록 스케줄링
- Soft real-time computing
	- Soft real-time task는 일반 프로세스에 비해 높은 우선순위를 갖도록 스케줄링

### Thread Scheduling

- Local Scheduling
	- User level thread의 경우 사용자 수준에서 어떤 thread를 스케줄링할지 결정
	- 운영체제는 모름. 그냥 프로세스에 CPU를 주고, 어떤 스레드에 CPU를 줄지는 내부에서 결정
- Global Scheduling
	- Kernel level thread의 경우 일반 프로세스와 마찬가지로 어떤 thread를 스케줄링할지 OS가 결정

## Algorithm Evaluation

### 알고리즘 수행 성능 척도
- Queueing models
	- 확률 분포로 주어지는 arrival rate와 service rate를 통해 performance index값을 계산
- Implementation & Measurement (구현 & 성능측정)
	- 실제 시스템에 알고리즘을 구현해 실제 작업에 대해 성능을 측정한 후 비교
- Simulation (모의 실험)
	- 모의 프로그램으로 작성한 알고리즘을 trace 입력으로 결과 비교
	- 구현/성능측정이 어려울 때 비교적 쉽게 진행


## 출처

해당 내용은 KOCW 강의 중 [이화여자대학교의 운영체제-반효경 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323)의 내용을 정리한 것입니다.