---
title: "정보처리기사 실기 공부 day4, 통합 구현"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - kunbon
    - 정보처리기사
tags:
    - 정보처리기사
    - study
last_modified_at: 2021-10-04
---

# 중요한 것 정리

연계 요구사항 분석, 연계 모듈 구현(EAI, ESB, 웹 서비스)

# 연계 데이터/메커니즘

## 연계 요구사항 분석

### 개념

연계 시스템과 관련된 요구사항을 분석하는 과정

### 분석 기법

인터뷰, 체크리스트, 설문지, 델파이 기법, 브레인스토밍

### 분석 참고 문서

코드 정의서, 테이블 정의서, 응용 프로그램 구성도, 시스템 구성도

### 절차

시스템 현황 확인 → 정의서 확인 → 체크리스트 작성 → 인터뷰 및 면담 → 연계 요구사항 분석서 작성

## 연계 시스템 구성

송신 시스템 ↔ 중계 서버 ↔ 수신 시스템

## 연계 방식

직접 연계 방식, 간접 연계 방식

### 직접 연계 방식

- 단순, 비용 절감, 좋은 성능 ↔ 결합도 높음, 암/복호화 불가, 제한적

DB 링크(DB Link, 링크 객체 이용), DB 연결(DB Connection, 커넥션 풀 이용), API/Open API, JDBC(Java DataBase Connectivity), 하이퍼 링크(Hyper Link)

cf) Connection Pool : DB에 연결할 때 커넥션 계속 생성하면 자원 소모 높아지므로, 풀에 저장해두고 필요할 때 쓰는 방식.

cf) API(Application Programming Interface) : 응용프로그램에서 데이터를 주고받기 위해 규격을 정해 놓은 인터페이스

### 간접 연계 방식

- 상이한 것들 간 통합 가능, 보안 유연 적용 가능, 인터페이스 변경해도 오류 적음 ↔ 복잡, 기간 오래 소요

연계 솔루션(EAI, Enterprise Application Integration 기업 연계 통합), Web Service/ESB(WSDL, SOAP 프로토콜 이용), 소켓(Socket, 포트 할당)

cf) Socket : 같은 네트워크를 공유하는 프로세스 간 통신의 접속점. 클라이언트와 서버 간 정보 송수신 가능.

# 연계 모듈 구현

## EAI(Enterprise Application Integration)

### 개념

기업 응용 프로그램 통합으로, 기업에서 사용하는 각기 다른 플랫폼 또는 응용 프로그램 간 정보 교환 및 통합을 위한 솔루션. 미들웨어로 Hub 사용. 타이트한 통합 형태.

### 구성 요소

EAI 플랫폼, 어댑터, 브로커, 메시지 큐, 비즈니스 워크플로우

### 구축 유형

- 포인트 투 포인트(Point-to-point) : 1:1 통합, 개발자 간 커뮤니케이션으로도 통합 가능
- 허브 앤 스포크(Hub & Spoke) : 한 접점(허브)에 데이터를 전송하는 중앙 집중식, 허브 오류 시 전체 먹통
- 메시지 버스(Message Bus) : 애플리케이션 사이 미들웨어(버스)를 두는 미들웨어 통합 방식, 확장성, 대용량
- 하이브리드(Hybrid) : 그룹 내부에선 허브 앤 스포크를, 전체에선 메시지 버스를 사용하는 방식

## ESB(Enterprise Service Bus)

### 개념

Bus 미들웨어를 중심으로 서로 다른 플랫폼 또는 응용 프로그램을 느슨하게 결합하는 솔루션. EAI와 동일하게 통합 솔루션이지만, Bus(중앙집중X)형이며 느슨한 결합을 추구한다는 점이 차이.

### 특징

버스를 통한 느슨한 결합 ⇒ 확장성, 유연성 높음

## 웹 서비스(Web Service)

### 개념

네트워크 상의 서로 다른 컴퓨터끼리 상호작용할 수 있도록 표준화된 방식으로 정보를 공유하는 기술.

### 유형

SOAP, UDDI, WSDL

### SOAP(Simple Object Access Protocol)

네트워크 상태에서 XML 기반의 메시지를 교환하는 프로토콜. HTTP, HTTPS, SMTP 등 사용.

원격 프로시저 호출(RPC, Remote Procedure Call : 별도의 원격 호출 코드를 작성하지 않아도 원격으로 함수나 프로시저를 사용할 수 있도록 하는 프로세스 통신 기술)

네트워크 노드(클라이언트) ←메시지→ 다른 쪽 노드(서버)

### WSDL(Web Service Description Language)

SOAP이 객체 간 접속/연결을 위함이라면, WSDL은 실제 접근하고자 하는 데이터에 대한 상세 정보를 담고 있음.

XML 형식으로 구현.

### UDDI(Universal Description, Discovery and Integration)

WSDL을 등록하고 검색하기 위한 등록처로, 이 등록을 통해 웹 상에서 접근/검색이 가능하다.

### 웹 서비스 방식 구축 및 구현 절차

송수신 파일 경로 및 파일명 정의 → 송신 연계 응용 프로그램 구현 → 파일 전송(HTTP SOAP, 혹은 REST) → 수신 DB 반영 서비스 호출 처리(WSDL 작성 및 공유) → 수신 연계 응용 프로그램 구현
