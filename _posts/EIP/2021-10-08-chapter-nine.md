---
title: "정보처리기사 실기 공부 day9, 소프트웨어 개발 보안 구축"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - kunbon
    - 정보처리기사
tags:
    - 정보처리기사
    - study
last_modified_at: 2021-10-08
---

# 중요한 것 정리

소프트웨어 보안(기무가, 공격 기법, 서버 인증, 암호화 알고리즘, 데이터 암호화), 시큐어 코딩 가이드, 비즈니스 연속성 계획

# 소프트웨어 개발 보안

## 소프트웨어 개발 보안의 3요소

**기무가**

기밀성, 무결성, 가용성을 지키는 것이 소프트웨어 개발 보안의 핵심이다.

### 기밀성(Confidentiality)

인가되지 않은 대상을 접근하지 못하도록 차단하는 성질

### 무결성(Integrity)

데이터의 상태가 항상 보존되며, 정확성 및 완전성을 보장하는 성질

### 가용성(Availability)

권한을 가진 대상이 데이터를 사용할 수 있는 성질

## 공격기법

### DoS(Denial of Service) 공격

#### 개념

한대의 컴퓨터에서 공격 대상이 되는 컴퓨터의 자원을 부족하게 해 정상적으로 서버를 이용하지 못하게끔 하는 것.

공격자(1) == 많은 양의 데이터 전송 =⇒ 공격 대상

#### 종류

- 죽음의 핑(PoD, Ping of Death)
    - ICMP 패킷(Ping)을 잘게 쪼개 다수를 보내면, 수신측에서 패킷을 조합하는 과정에서 과부하나 오버플로우가 일어나 오류가 나는 것을 악용한 공격 기법.
    - ICMP 패킷이 쪼개져있는지 확인하고 이를 공격으로 의심해 차단하거나, 일정 수 이상 들어오는 ICMP 패킷을 무시하도록 설계하는 방식으로 대응할 수 있다.
- 스머프(Smurf)/스머핑(Smurfing)
    - 공격자 컴퓨터의 IP 주소를 공격 대상의 IP 주소로 바꾸어 관련된 네트워크 전체에 ICMP Echo 패킷을 직접 브로드캐스팅해 마비시키는 공격 기법.
    - 브로드캐스트 주소로 온 ICMP Echo 패킷을 받지 않도록 설정하거나, 직접 브로드캐스트 패킷 자체를 허용하지 않도록 라우터를 설정하는 방식으로 대응할 수 있다.
- SYN 플러딩(SYN Flooding)
    - TCP 프로토콜의 구조를 악용한 공격 기법으로, SYN 패킷만 줄곧 보내 서버가 감당할 수 있는 양을 모두 점유해 서버의 자원을 소모해버린다.
    - TCP 연결 타임아웃을 조정하거나, 큐를 늘려주거나, 침입 탐지 시스템 등을 설치하는 방식으로 대응할 수 있다.
- UDP 플러딩(UDP Flooding)
    - UDP 프로토콜의 구조를 악용한 공격 기법으로, UDP 패킷을 대량으로 만들어 임의의 포트 번호로 전송해 ICMP를 지속적으로 생성해 자원을 고갈시킨다.
    - 방화벽 설정을 통해 해당 공격에 대한 감지를 미리 함으로써 대응할 수 있다.
- 랜드 어택(Land Attack)
    - 출발지 IP와 목적지 IP의 주소를 동일하게 만들어 무한 루프에 빠지게 만드는 공격 기법.
    - 출발지와 목적지가 같은 패킷은 차단하는 방식으로 대응할 수 있다.
- 티어 드롭(Tear Drop)
    - 잘못된 IP 패킷 조각을 보내 패킷 조합 과정에서 문제를 일으키도록 만드는 공격 기법.
    - 과부하가 걸리는 패킷은 처리하지 않음으로써 대응할 수 있다.
- 봉크(Bonk)/보잉크(Boink)
    - 같은 시퀀스 번호를 보내거나, 일정한 간격으로 시퀀스 번호에 빈 부분을 만드는 등 프로토콜의 오류 제어를 악용해 패킷 조합에 오류를 일으키는 공격 기법.
    - 과부하가 걸리는 패킷은 처리하지 않음으로써 대응할 수 있다.
    

### DDoS(Distributed Dos)

#### 개념

한 대의 공격자가 있었던 DoS와 달리, DDoS는 여러 대의 분산된 공격자가 하나의 서버를 공격하는 기법이다.

#### 종류

- 대역폭 소진 공격(3~4계층)
    - UDP/ICMP Traffic Flooding (UDP/ICMP Flooding, DNS Query Flooding)
    - TCP Traffic Flooding (SYN Flooding, SYN+ACK Flooding)
    - IP Flooding (LAND Attack, Teardrop)
- 애플리케이션(서비스) 마비 공격(7계층)
    - HTTP Traffic Flooding (GET Flooding, GET with Cache-Control)
    - HTTP Header/Option Spoofing (Slowris, Slowloris, Slow HTTP Read DoS)
    - Other L7 Service Flooding (Hash DoS, Hulk DoS, FTP/SMTP Attack)

#### 공격 구성 요소

- 핸들러(Handler) : 마스터 시스템
- 에이전트(Agent) : 직접 공격하는 컴퓨터
- 마스터(Master) : 에이전트 관리
- 공격자(Attacker) : 공격 본체
- 데몬 프로그램(Daemon) : 에이전트 시스템

#### 공격 도구

- Trinoo - UDP Flood
- Tribe Flood Network - UDP Flood, TCP SYN flood, ICMP Echo, Smurf
- Stacheldraht - ICMP Flood, SYN Flood, UDP Flood, Smurf

#### 대응 방안

차단 정책 업데이트, 좀비 PC IP 확보, 보안 솔루션 운영, 홈페이지 보안 관리, 시스템 패치

### DRDoS(Distributed Reflection DoS)

#### 개념

출발지 IP를 공격 대상 컴퓨터의 IP로 바꾸어 다수의 SYK 패킷을 전송하고, 이를 공격 대상 컴퓨터가 한꺼번에 SYK/ACK로 받아 과부하로 DoS가 되는 공격 방식

### 애플리케이션 공격

#### 개념

DDoS 7계층 공격에 해당하는 것으로, HTTP와 관련된 공격이 주를 이룬다.

#### 유형

- HTTP GET Flooding
    - 과도한 GET 메시지 사용해 과부하
- Sloworis(Slow HTTP Header DoS)
    - HTTP GET 메서드를 보낼 때 헤더의 끝을 알리는 개행 문자를전송하지 않아 연결상태를 지속시킴
- RUDY(Slow HTTP POST DoS)
    - 메시지 바디 부분을 아주 작게, 요청 헤더를 아주 크게 보내 연결상태 지속
- Slow HTTP Read DoS
    - TCP 윈도 크기와 데이터 처리율을 감소시킨 상태에서 HTTP 패킷을 지속적으로 보내 자원 소진
- Hulk DoS
    - URL을 계속 변경하면서 다량으로 GET 요청을 보내는 서비스 거부 공격
- Hash DoS
    - 다수의 파라미터를 POST 방식으로 전달해 해시 충돌을 발생시켜 자원 소진

### 네트워크 공격

#### 개념

네트워크 활동을 방해해 약화시키거나 제거하는 목적의 공격

#### 유형

- 스니핑(Sniffing)
    - 데이터만 몰래 들여다보는 수동적 공격 기법
- 트로이 목마(Trojan Horses)
    - 겉보기엔 정상적인 악성 프로그램을 통해 공격하는 기법
- 네트워크 스캐너(Scanner), 스니퍼(Sniffer)
    - 네트워크 하드웨어/소프트웨어의 취약점을 탐색하는 공격 도구
- IP 스푸핑(IP Spoofing)
    - 인증된 것처럼 IP 주소를 속여 정보를 빼내는 공격 기법
- ARP 스푸핑(ARP Spoofing)
    - MAC 주소를 위조해 특정 호스트로 나가는 패킷을 스니핑하는 공격 기법
- ICMP Redirect
    - ICMP 리다이렉트 메시지를 조작해 패킷의 목적지를 바꾸어 스니핑하는 공격 기법
- 패스워드 크래킹(Password Cracking)
    - 사전 크래킹(Dictionary Cracking) - 가능성 있는 단어를 파일로 만들어 그 단어를 대입해 크랙
    - 무차별 크래킹(Brute Force Cracking) - 무작위로 대입해 크랙
    - 패스워드 하이브리드 공격(Password Hybrid Attack) - 사전 + 무차별
    - 레인보우 테이블 공격(Rainbow Table Attack) - 해시 값을 테이블에 모아두고 검색해서 역으로 찾기

## 시스템 보안

### 버퍼 오버플로우(Buffer Overflow)

#### 개념

메모리에 할당된 버퍼 크기를 초과하는 데이터를 통해 프로세스의 흐름을 변경시켜 악성 코드를 실행시키는 기법

#### 유형

- 스택 버퍼 오버플로우
    - 스택 영역에서 발생(local vlaue, return)
- 힙 버퍼 오버플로우
    - 힙 영역에서 발생(동적으로 할당됨)
    

#### 대응 방안

스택가드(Stackguard), 스택실드(Stack Shield), ASLR(Address Space Layout Randomization), 안전한 함수 활용, 실행 제한

### 백도어(Backdoor)

#### 개념

정상적인 인증/보안 절차를 우회하고 암호화된 데이터에 접근할 수 있도록 일부러 만들어둔 것

#### 탐지 기법

프로세스 및 열린 포트 확인, Setuid 파일 검사, 백신 및 백도어 탐지 툴 활용, 무결성 검사, 로그 분석

### 주요 공격 기법

- 키로거 공격(Key Logger Attack) - 키보드 움직임 탐지해서 정보 빼돌리는 공격
- 포맷 스트링 공격(Format String Attack) - 포맷 스트링을 활용하는 함수의 취약점을 악용한 공격
- 레이스 컨디션 공격(Race Condition Attack) - 프로세스나 스레드의 접근 순서를 악용한 공격
- 루트킷(Rootkit) - 침입 사실을 숨기고 이후 침입을 위한 공격 수단을 설치하는 프로그램의 모음

## 보안 관련 용어

스피어피싱(Spear Phishing, 메일 발송), 스미싱(Smishing), 큐싱(Qshing), 봇넷(Botnet), APT 공격(Advanced Persistent Threat, 특정 타깃 공격), 공급망 공격(Supply Chain Attack), 제로데이 공격(Zero Day Attack, 취약점 발표 전에 공격), 웜(Worm, 스스로 복제), 악성 봇(Malicious Bot, 원격 제어 가능한 좀비 PC를 만드는 프로그램), 사이버 킬체인(Cyber Kill Chain, APT 공격 방어 모델), 랜섬웨어(Ransomware, 암호화된 파일 인질), 이블 트윈(Evil Twin Attack, wifi처럼 행세), 난독화(Obfuscation), Tripwire(침입 등을 탐지하는 도구), Ping(원격 호스트가 정상적인지 확인하는 명령어), Tcpdump(패킷 분석 도구)

## 서버 인증과 접근 통제

### 개념

서버 접속시 인증하는 과정.

스니핑, 피싱 방지, 데이터 변조 방지를 위해서 실행하며, 이를 통해 기업 신뢰도 향상을 할 수 있다.

### 유형

지식기반 인증(ID, password), 소지기반 인증(공인인증서, OTP), 생체기반 인증(얼굴, 지문, 홍채 등), 특징기반 인증(서명, 몸짓 등)

### 서버 접근 통제

#### 개념

서버 접근을 통제하는 기능.

기밀성, 무결성, 가용성을 확보하기 위함.

#### 기법

- 식별(Identification) - 주체(데이터 접근 주체)가 객체(데이터)에게 자신이 누구인지 밝힘
- 인증(Authentication) - 주체의 신원 검증
- 인가(Authorization) - 주체에 접근(CRUD 등의 행동) 허용
- 책임추적성(Accountability) - 주체의 접근 추적, 기록

#### 유형

- 임의적 접근 통제(DAC, Discretionary Access Control)
    - 신분 기반으로 접근을 통제하는 방식
    - 데이터 소유자가 권한을 부여하며, 정책 변경이 유연하다.
- 강제적 접근 통제(MAC, Mandatory Access Control)
    - 보안 등급을 기반으로 접근을 통제하는 방식
    - 시스템에서 부여하며, 안정적이다.
- 역할기반 접근 통제(RBAC, Role Based Access Control)
    - 역할을 기반으로 접근을 통제하는 방식
    - 중앙관리자가 통제하며, 관리하기 좋다.

#### 접근 통제 보호 모델

- 벨-라파듈라 모델(BLP, Bell-LaPadula Policy)
    - 기밀성 강조한 MAC(강제적 접근 통제)모델. 미 국방부에서 사용.
    - No Read Up(낮은 주체가 높은 객체 읽을 수 없음), No Write Down(높은 주체가 낮은 객체 쓸 수 없음)
- 비바 모델
    - 무결성 보장하는 최초의 모델. 벨-라파듈라 모델을 보완.
    - No Read Down(높은 주체가 낮은 객체 읽을 수 없음), No Write Up(낮은 주체가 높은 객체 쓸 수 없음)

## 암호화 알고리즘

### 개념

데이터의 기밀성과 무결성 확보를 위해 정보를 암호화하는 기법.

### 양방향 방식

#### 대칭 키 암호 방식

- 평문—암호화→암호문—복호화→평문 과정에서 암호화, 복호화에 같은 암호 키 사용.
- 블록 암호 방식 : 고정 길이의 블록 암호를 반복
    - DES(Data Encryption Standard, 미국 연방 표준국 NIST), SEED(한국 인터넷 진흥원 KISA), AES(Advanced Encryption Standard, NIST), ARIA(Academy, Research Institute, Agency, 국가정보원과 산학연구협회), IDEA(International Data Encryption Algorithm, 스위스)
- 스트림 암호 방식 : 평문 + 난수열
    - RC4, LFSR(Linear Feedback Shift Register)

#### 비대칭 키 암호 방식

- 평문—암호화→암호문—복호화→평문 과정에서 암호화, 복호화에 다른 암호 키 사용.
- 공개 키와 비밀 키로 이루어져 있으며, 공개 키를 비밀 키로 복호화한다.
- 디피-헬만(Diffie-Hellman, 최초), RSA(Rivest- Shamir- Adleman, 소인수분해), ECC(Elliptic Curve Cryptography, RSA 보완), ElGamal(이산대수 원리)

### 일방향 방식

#### 해시 암호 방식

- 암호화만 존재. 평문을 해시 값으로 출력하는 암호화 방식.
- MAC(Message Authentication Code) : 메시지 인증 코드로 무결성과 인증 보장
    - HMAC, NMAC
- MDC(Modification Detection Code) : 변경 감지 코드로 무결성 보장
    - MD5(Message-Digest algorithm 5), SHA(Secure Hash Algorithm, SHA-1, SHA-256/384/512), HAS-160(MD5 + SHA-1)

## 데이터 암호화 전송

### IPSec(Internet Protocol Security)

- IP계층(3계층)에서의 무결성, 인증 보장.
- 인증(AH, Authentication Header) 프로토콜, 암호화(ESP, Encapsulation Security Payload) 프로토콜, 키 관리(IKE, Internet Key Exchange) 프로토콜
- 전송 모드(Transport Mode)와 터널 모드(Tunnel Mode) 존재.

### SSL(Secure Socket Layer)/TLS(Transport Layer Security)

- 전송계층(4계층), 응용계층(7계층)에서의 기밀성, 무결성 보장.
- https://

### S-HTTP(Secure HyperText Transfer Protocol)

- 웹상에서 암호화하는 방법
- HTTP 사용한 애플리케이션만 가능

# 소프트웨어 개발 보안 구현

## 시큐어 코딩 가이드

### 개념

보안 취약점을 찾아 예방하고, 안전한 소프트웨어를 개발하는 코딩 가이드.

- 입력데이터 검증 및 표현
    
    입력 데이터에 대한 검증
    
    - XSS(Cross Site Script) : 웹페이지에 부적절한 스크립트 실행
        - 공격자 → 웹(XSS) ↔ 사용자(감염) → 공격자(정보)
        - Stored XSS(페이지를 통해), Reflected XSS(이메일을 통해), DOM XSS(이메일을 통해)
    - 사이트 간 요청 위조(CSRF, Cross Site Request Forgery) : 공격자의 의도에 따라 웹사이트가 동작
    - SQL 삽입(Injection) : 악의적으로 SQL 구문 삽입해 DB 조작
        - Form, Union, Stored Procedure, Mass, Error-Based, Blind SQL Injection
- 보안 기능
    
    인증, 접근 제어, 기밀성, 암호화, 권한 등
    
- 시간 및 상태
    
    자원 접근 직렬화, 프로세스 병렬 실행, 세션 통제(Session Control)
    
- 에러 처리
- 코드 오류
- 캡슐화
- API 오용

## 비즈니스 연속성 계획(BCP, Business Continuity Plan)

### 개념

재해, 재난, 장애 등으로부터 재해 복구, 업무 복구 및 재개 등을 통한 비즈니스 연속성을 보장하는 체계

### 주요 용어

- BIA(Business Impact Analysis) : 시간흐름에 따른 손실평가를 조사하는 BCP를 구축하기 위한 영향분석
- RTO(Recovery Time Objective) : 중단부터 재가동될때까지의 시간
- RPO(Recovery Point Objective) : 재가동될 때 데이터의 손실 허용 시점
- DRP(Disaster Recovery Plan) : 재난 복구 계획
- DRS(Disaster Recovery System) : 재해복구센터

#### 참고

수제비 2021 정보처리기사 실기