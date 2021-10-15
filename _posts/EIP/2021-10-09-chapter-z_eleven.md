---
title: "정보처리기사 실기 공부 day11, 응용SW 기초 기술 활용"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - kunbon
    - 정보처리기사
tags:
    - 정보처리기사
    - study
last_modified_at: 2021-10-09
---

# 중요한 것 정리

운영체제, 메모리 관리, 프로세스 관리, 가상화/클라우드, 네트워크, OSI 7계층, IP, 프로토콜

# 운영체제

## 운영체제

### 개념

컴퓨터와 사용자 간의 인터페이스를 담당해 사용자가 하드웨어를 사용할 수 있게 만들어주는 소프트웨어.

한정된 시스템 자원을 효율적으로 운용하는 주체.

사용자 편리성, 인터페이스 기능, 스케줄링, 자원 관리, 제어 등의 기능을 함.

운영체제 = 쉘(인터페이스) + 커널(실질적인 처리 담당)

### 종류

- Windows
    - GUI, 선점형 멀티태스킹, 자동 감지 기능, OLE
- Unix
    - 대화식, 다중 작업 기능, 다중 사용자 기능, 이식성, 계층적 트리 구조 파일 시스템
    - Linux, Mac, Android
    - chmod 777 test.c⇒ User, Group, Other에 read, write, execute 권한 모두 줌
    - chmod u=r, g-w ,o+x test.c ⇒ user에 read 부여, group에 write 제거, other에 execute 추가

### 기능

메모리 관리, 프로세스 관리

## 메모리 관리

**반배할교**

### 반입 기법

- 주기억장치에 적재할 다음 프로세스의 반입 **시기(When)** 결정.
- 요구 반입 기법, 예상 반입 기법

### 배치 기법

- 주기억장치의 **어디에(Where)** 프로세스를 저장할지 결정.
- 최초 적합(First-fit)
    - 가용공간 중 가장 첫 번째 위치에 할당
- 최적 적합(Best-fit)
    - 가용공간 중 가장 크기가 비슷한 위치에 할당
- 최악 적합(Worst-fit)
    - 가용공간 중 가장 크기가 큰 위치에 할당

### 할당 기법

- 주기억장치에 **어떻게(How)** 프로세스를 할당할 지 결정.
- 연속 할당 기법, 분산 할당 기법

### 교체 기법

- 주기억장치에 있는 프로세스 중 **무엇을(Who)** 제거할 것인지 결정.
- Swap In/Out, FIFO, Optimal, LRU, LFU, MFU, 시계 알고리즘

## 프로세스 관리

### 프로세스 상태

**생준실완대**

생성(Create) → 준비(Ready) ↔ 실행(Running) → 완료(Complete)

준비(Ready) ← 대기(Waiting) ← 실행(Running)

### 상태 전이

- 디스패치(Dispatch) : 준비 → 실행. 문맥 교환.
- 타이머 런 아웃(Timer run out) : 실행 → 준비. 타임 슬라이스 만료, 선점.
- 블록(Block) : 실행 → 대기. 입출력 발생
- 웨이크 업(Wake up) : 대기 → 준비. 입출력 종료

### 스케줄링

#### 개념

CPU를 사용하고자 하는 프로세스들 간의 우선순위 관리 작업.

#### 용어

서비스 시간, 응답시간(종료 시간 - 도착 시간), 평균 응답시간, 대기시간(종료 시간 - 서비스 시간), 평균 대기시간, 종료시간, 시간 할당량, 응답률

#### 유형

선점형 스케줄링, 비선점형 스케줄링.

### 선점형 스케줄링

#### 개념

우선순위가 높은 프로세스가 현재 CPU를 차지하고 있는 프로세스를 중단시키고 자신이 점유하는 방식.

#### 유형

- 라운드 로빈(Round Robin)
    - 같은 크기의 CPU 시간 할당. 이 시간 내에 처리 못하면 맨 뒤 큐로 보내진 뒤 다음으로 넘어감.
    - 시분할 시스템 이용
- SRT(Shortest Remaining Time First)
    - 가장 짧은 시간이 걸리는 프로세스 먼저 처리. 남은 시간이 가장 짧은 애가 큐에 있으면 바로 선점.
- 다단계 큐(Multi Level Queue)
    - 여러 개의 큐를 이용. 각 큐는 독자적인 방식 사용.
- 다단계 피드백 큐(Multi Level Feedback Queue)
    - 큐마다 다른 시간 할당량 부여. FIFO + RR로, 마지막 단계에서 라운드 로빈 방식 사용.

### 비선점형 스케줄링

#### 개념

작업이 끝날 때까지 한 프로세스가 CPU를 점유하고 다른 프로세스가 중간에 점유할 수 없는 방식.

#### 유형

- 우선순위(Priority)
    - 프로세스 별 우선순위에 따라 할당.
    - 동일순위인 경우, FIFO(FCFS, First Come First Service)
- 기한부(Deadline)
    - 작업이 명시된 기한이나 시간 내에 완료.
- FIFO(First In First Out)
    - 큐에 도착한 순서대로 처리.
- SJF(Shortest Job First)
    - 도착 시점을 기준으로 가장 서비스 시간이 작은 프로세스를 먼저 수행. 요구시간이 긴 프로세스는 기아현상 발생 가능.
- HRN(Highest Response Ratio Next)
    - 현재 응답률이 가장 높은 프로세스를 먼저 수행. 기아현상 보완.
    - 우선순위 = (대기 시간 + 서비스 시간) / 서비스 시간

### 교착 상태(Deadlock)

다중 프로세싱 환경에서 두 개 이상의 프로세스가 특정 자원할당을 무한정 대기하는 상태.

#### 발생 조건
**환비상점**

- 환형 대기(Circular Wait) : 두 개 이상의 프로세스 간 점유와 대기가 원형인 상태
- 비선점(Non Preemption) : 한 프로세스가 점유한 이상 다른 프로세스가 선점할 수 없음
- 상호배제(Mutual Exclusive) : 프로세스가 자원을 배타적으로 점유해 다른 프로세스가 사용할 수 없음
- 점유와 대기(Block & Wait) : 한 프로세스가 점유하고 있을 때 다른 프로세스가 대기하는 상태

#### 해결 방안
- 예방(Prevention) : 상호배제 제외 다른 교착상태 발생 조건을 부정하는 방안.
- 회피(Avoidance) : 안전한 상태 유지할 수 있는 요구만 수락.
- 발견(Detection) : 감시 알고리즘으로 교착상태 검사.
- 복구(Recovery) : 교착상태가 사라질 때까지 프로세스 순차적으로 Kill.

## 가상화, 클라우드

### 가상화

컴퓨터 리소스의 추상화

컴퓨팅 가상화, 스토리지 가상화, I/O 가상화, 컨테이너, 분산처리 기술, 네트워크 가상화 기술

### 클라우드 컴퓨팅

인터넷을 통해 가상화된 IT 리소스를 제공하고, 정보를 인터넷에 연결된 다른 컴퓨터로 저장하는 기술

사설 클라우드, 공용 클라우드, 하이브리드 클라우드

인프라형 서비스(IaaS, Infrastructure as a Service), 플랫폼형 서비스(PaaS, Platform as a Service), 소프트웨어형 서비스(SaaS, Software as a Service)

# 네트워크

## 네트워크

원하는 정보를 수신자 또는 기기에 정확하게 전송하기 위한 기반 인프라

WAN(광대역 네트워크), LAN(근거리 네트워크)

#### 네트워크 관련

WPAN(Wireless Personal Area Network, 10m 이내의 무선 네트워크)

NAT(Network Access Translation, 외부에 알려진 것과 다른 IP 쓰는 내부 네트워크 IP변환)

DHCP(Dynamic Host Configuration Protocol, 임대 개념의 IP 할당)

CDN(Contents Delivery Network, 용량 크고 사용자가 자주 쓰는 콘텐츠 서버에 미리 저장)

## OSI(Open System Interconnection) 7계층

- 7 - 응용 계층(Application Layer)
    - 사용자 ↔ 네트워크
    - HTTP, FTP
- 6 - 표현 계층(Presentation Layer)
    - 데이터 형식 설정, 암/복호화
    - JPEG, MPEG..
- 5 - 세션 계층(Session Layer)
    - 연결 접속
    - RPC, NetBIOS
- 4 - 전송 계층(Transport Layer)
    - 프로세스 연결, 신뢰성 있는 통신, 흐름제어/오류제어/혼잡제어
    - TCP, UDP, SCTP(TCP + UDP)
    - L4 스위치(TCP, UDP, 로드밸런싱)
- 3 - 네트워크 계층(Network Layer)
    - 단말 간 데이터전송
    - IP, CMP
    - 라우터(LAN-LAN, LAN-WAN), 게이트웨이(프로토콜 서로다른 통신망에 접속), L3스위치, 인터넷 공유기, 망 스위칭 허브(대단위 지역 커버)
- 2 - 데이터링크 계층(Data Link Layer)
    - 시스템 간 데이터 전송, 동기화, 오류제어/흐름제어/회선제어
    - HDLC, PPP
    - 브리지(LAN 연결), L2 스위치(MAC 주소 기반), NIC(외부 네트워크와 데이터), 스위칭 허브(스위치 기능을 가진 허브)
- 1 - 물리 계층(Physical Layer)
    - 0과1의 비트정보, 전기적 신호 변환
    - RS-232C
    - 허브(여러대 컴퓨터 연결해 네트워크로 보냄), 리피터(디지털 신호 증폭기)

## 프로토콜

### 개념

서로 다른 기기 간 데이터 교환을 위해 약속된 통신 규약.

### 기본 요소

구문(Syntax - 형식), 의미(Semantic - 제어), 타이밍(Timing - 속도와 순서)

### 네트워크 프로토콜

- 컴퓨터나 원거리 통신 장비 사이에서 메시지 주고받는 양식과 규칙의 체계
- 단편화, 재조립, 캡슐화, 연결 제어, 오류 제어, 동기화, 다중화, 주소 지정

### 기능

단편화(Fragmentation), 재조립/재합성(Assembly), 흐름 제어(Flow Control), 오류 제어(Error Control), 순서 결정(Sequencing), 주소 설정(Addressing), 동기화(Synchronization)

## 계층별 프로토콜

### 2 - 데이터 링크 계층(Data Link Layer)

- 회선 제어, 흐름 제어, 오류 제어
- HDLC(High-level Data Link Control) : 동기식 비트 중심
- PPP(Point-to-Point Protocol) : 두 노드 간 직접 연결
- 프레임 릴레이(Frame Relay) : 프로토콜 처리 간략화, 프레임 중계와 다중화 기능. 처리속도 향상
- ATM(Asynchronous Transport Mode) : 비동기식 시분할 다중화 방식

### 3 - 네트워크 계층(Network Layer)

- 네트워크를 통해 패킷 전송
- IP(Internet Protocol) : 네트워크상에서 패킷 단위로 데이터 교환
- ARP(Address Resolution Protocol) : IP 주소를 MAC 주소로 변환
- RARP(Reverse Address Resolution Protocol) : MAC 주소는 알지만 IP를 모를 때
- ICMP(Internet Control Management Protocol) : IP 패킷 처리시 오류 알려주는 프로토콜
- IGMP(Internet Group Management Protocol) : 그룹 멤버십 구성
- 라우팅 프로토콜(Routing Protocol) : 데이터 전송을 위해 가는 최적의 경로 설정
    - RIP(Routing Information Protocol) : 거리벡터 알고리즘에 기초.
        - 벨만-포드 알고리즘 사용. 15홉 제한. UDP 사용. 30초마다 브로드캐스팅
    - OSPF(Open Shortest Path First) : 링크 상태(Link-State) 알고리즘 적용.
        - 다익스트라 알고리즘 사용. 홉 무제한. 라우팅 메트릭 지정. AS 분할사용
    - BGP(Border Gateway Protocol) : 자율 시스템 간 경로 정보 교환
        - 경로 벡터 알고리즘 + TCP. ISP 사업자들 간에 주로 사용.

### 4 - 전송 계층(Transport Layer)

- 종단 간 신뢰성 있는 데이터 전달. 오류 제어, 혼잡 제어, 흐름 제어.
- TCP(Transmission Control Protocol)
    - 신뢰성 보장, 연결 지향적, 흐름제어, 혼잡제어
    - 오류 발생시 ICMP 프로토콜을 사용해 오류 메시지 보냄.
- UDP(User Datagram Protocol)
    - 비신뢰성, 순서 보장하지 않음, 실시간 스트리밍, 멀티캐스팅, 단순함

### 5 - 세션 계층(Session Layer)

- 연결이 끊어지지 않도록 유지하는 구조 제공.
- RPC(Remote Procedure Call) : 원격 프로시저 호출. 원격으로 프로시저 실행 가능.
- NetBIOS(Network Basic Input/Output System) : 응용계층에 API 제공

### 6 - 표현 계층(Presentation Layer)

- 정보를 알맞은 형태로 변환하여 사용자/애플리케이션에 제공.

### 7 - 응용 계층(Application Layer)

- 응용 프로그램과 직접 연계하여 서비스 제공.
- HTTP(HyperText Transfer Protocol) : 인터넷에서 데이터 주고받는 프로토콜
- FTP(File Transfer Protocol) : 파일 전송 프로토콜
- SMTP(Simple Mail Transfer Protocol) : 메일 전송 프로토콜
- POP3(Post Office Protocol Version 3) : 원격으로 이메일 가져올 때 사용하는 프로토콜
- IMAP(Internet Messaging Access Protocol) : 원격으로 인터넷 가져오는 프로토콜
- Telnet : 네트워크 연결에 사용하는 프로토콜

## IP

### IPv4

인터넷에서 데이터를 교환하기 위한 32비트 주소체계의 프로토콜

멀티캐스트, 유니캐스트, 브로드캐스트 가능

- A클래스 : 1~126
- B클래스 : 128~191
- C클래스 : 192~223
- D클래스 : 멀티캐스트
- E클래스 : 연구용

### IPv6

IPv4의 보완책으로, 인터넷에서 데이터를 교환하기 위한 128비트 주소체계의 프로토콜

멀티캐스트, 유니캐스트, 애니캐스트 가능

cf) 멀티캐스트(1→특정 여러명), 유니캐스트(1→1), 브로드캐스트(1→전체), 애니캐스트(1→인접노드)

### 전환 방법

- 듀얼 스택(Dual Stack)
    - 두 프로토콜 모두 가지고 있으면서 통신하는 상대에 따라 IP 스택 선택하는 방식.
- 터널링(Tunneling)
    - IPv6 -IPv4→ IPv6로 갈 때, 터널 만드는 것.
- 주소 변환(Address Translation)
    - 두 IP 망 사이에 주소변환기(게이트웨이)를 사용.

## 패킷 교환 방식

패킷 단위를 사용해 데이터 송/수신.

패킷이란 정보를 일정한 크기로 분할한 뒤 각 패킷에 송수신 주소 및 부가 정보를 입력한 것으로, 컴퓨터 네트워크에서 주로 사용하는 방식.

↔ 회선 교환 방식(비 저장 방식) : 물리적 전용선을 활용해 데이터 전달 경로가 정해진 후 해당 경로로만 전달.

⇒ 대역폭 고정, 안정적인 전송률 확보 ex) 전화망

- 가상 회선 방식 : 목적지 호스트와 미리 연결한 후, 통신하는 연결형 교환 방식
- 데이터그램 방식 : 헤더에 붙어서 개별적으로 전달하는 비연결형 교환 방식

#### 참고

수제비 2021 정보처리기사 실기
