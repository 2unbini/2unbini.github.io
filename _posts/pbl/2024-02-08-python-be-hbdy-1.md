---
title: "모바일 앱 개발자의 Python BE 개발일지, 개발 스택 선정(Flask, MySQL)"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - python
    - pbl
tags:
    - python
    - backend
    - 개발일지
    - pbl
last_modified_at: 2024-02-08
---

# 모바일 앱 개발자의 Python BE 개발일지 시작.

Swift로 iOS 앱 개발하는 사람이지만 호기심이 많아 이번 프로젝트는 Python Backend 개발을 하기로 했다.

퇴사 직전 백엔드 팀장님께 아~~~~주 간략하게 Python BE 개발에 대해 이야기를 나누었고, Google ML Bootcamp 하면서 머리채 잡고 파이썬 코딩에 익숙(?)해져서 이번 프로젝트 백엔드 언어로 파이썬을 하기로 결정했다.

한국의 취업 시장에서 백엔드 언어로 자바를 선호하는 것을 알지만 내가 백엔드 개발자로 취업하려고 하는 것도 아니고,

이왕이면 AI/ML 개발까지 발을 넓힐 수 있는 파이썬으로 프로젝트를 해보는 것이 좋겠다는 생각이 들었다.

## 백엔드 개발

모바일 앱 개발할 때 백엔드 개발자와 협업할 일이 많아서 관련된 지식이 완전 무의 상태는 아니었다.

하지만 백엔드 개발을 만만히 봤던 것은 사실인듯(ㅠㅠ)

### 요구사항 정리

프론트엔드 개발자와 함께 프로젝트 기획과 와이어프레임을 구성했다.

처음엔 너무 쉬워서 뚝딱 해내면 어떡하지? 라는 겸손하지 못한 생각을 했는데 생각보다 API도 많고, SNS 로그인이 들어가면서 로그인 관련 대응을 해야 하는 챌린지들이 생겼다.

시간을 두고 각자 SNS oauth 과정을 공부하고 만나기로 했다. 그 사이에 나는 DB 설계를 해놓고 API 명세를 함께 하기로 했다.

## 서버 프레임워크

절차를 모르니 일단 파이썬 서버 프레임워크를 구글에 냅다 검색했다.

> 2024 python server framework

엄청 익숙한 Django가 나왔고, 줄줄이 소세지 중에 `Flask`를 발견했다.

### 서버 프레임워크 - Flask

Java - Spring, Python - Django 이게 국룰이라 당연히 장고를 써야하는 줄 알았다.

이전에 한번 BE 개발에 관심이 생겨 Java/Spring Boot 강의를 들었던 적이 있다. 이 때 느낀 점: 과하다...

어떤 아키텍처에 따라 잘 관리될 수 있는 프레임워크일진 몰라도 작은 프로젝트에 적용하기엔 과스펙인 느낌이 들었다. Django vs Flask 비교 글을 보면 장고는 과한 대신 대규모 서버 관리에 좋고, 플라스크는 적은 기능으로 비교적 작은 서비스에 적합하다는 말들이 많았다.

루비 온 레일즈, 자바 스프링부트를 만져봤던 기억을 더듬어봤을 때 잡다한 기능들이 많은 프레임워크는 내가 하는 프로젝트 사이즈보다 과한 선택이었고, 잘 유지하지 못하는 결말을 맞았기 때문에 비교적 라이트한 프레임워크로 정했다.

## 데이터베이스

플라스크로 로컬에서 Hello, Flask 찍어보고 라우팅도 해보고나니 데이터베이스의 필요성이 느껴졌다.

뭐가 저장돼야 그걸 끄집어내서 보내주던가 하지... 라는 생각이 들면서 아 다음이 DB구나 싶었다.

### PostgreSQL vs MySQL

전 회사에서 PostgreSQL이라는 RDBMS를 알게 되었다. MySQL이 근본이라면 PostgreSQL은 요즘 대세인 관계형 데이터베이스였다. (cf- [RDBMS에 대한 깔끔한 정리 글](https://www.whatap.io/ko/blog/173/)이 있어서 첨부)

요즘 대세 못 참지;; 하고 PostgreSQL을 써보려 했다.

하지만...

### MySQL 선택ㅎ

대세라는 이유로 PostgreSQL을 쓰기엔 초급용 리소스를 찾기 쉽지 않았고, Flask를 선택한 이유와 비슷하게 PostgreSQL은 MySQL에 비해 서비스 범위가 넓을 때에 사용된다고 한다. ([AWS 비교글](https://aws.amazon.com/ko/compare/the-difference-between-mysql-vs-postgresql/))

겸사겸사(?) 자바 스프링부트 강의를 들을 때 MySQL과 Workbench를 설치해놔서 그 과정을 생략하고 바로 시작할 수 있다는 장점도 있었기 때문에 선정했다.

요구사항 정리하면서 스키마를 어떻게 구성할지 생각했다. 이것저것 리소스를 찾아보면서 네모들이 연결된 다이어그램을 볼 수 있었는데 나도 그걸 만들고 싶었다. Workbench는 스키마 디자인을 바로바로 할 수 있는 기능이 있었기에 뉴비인 내가 쓰기 아주 좋겠다는 생각이 들었다.

## To be continued...

한번에 모든 것들을 다 정해놓고 가고 있지 않아서 개발 일지도 끊어서 갈 것 같다.

DB 생성 및 설정에 관한 정보들을 잘 알지 못해서 관련해서 차근차근 깨나가고 다음 단계로 넘어가려 한다.

물론 내가 기본적으로 생각하고 있는 BE 개발의 플로우와 실제 플로우가 같은지 빨리 검증헤보고 싶은 마음도 있지만 ㅎ 초반 러쉬 부리지 말고 이번엔 용두용미로 만들 수 있도록 단계를 밟아야쥥!