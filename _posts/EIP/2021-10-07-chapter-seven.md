---
title: "정보처리기사 실기 공부 day7, SQL 응용"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - kunbon
    - 정보처리기사
tags:
    - 정보처리기사
    - study
last_modified_at: 2021-10-07
---

# 중요한 것 정리

데이터베이스 기본(트랜잭션, DDL, DML, DCL), 집계 함수, 절차형 SQL(프로시저, 사용자 정의 함수, 트리거)

# 데이터베이스 기본

## 트랜잭션

### 개념

데이터베이스의 상태를 변화시키는 작업의 단위.

### 특성

**ACID**

- Atomicity(원자성) - 최소 단위, 전체 성공 또는 전체 실패, 하나라도 실패하면 전체 취소, TCL
- Consistency(일관성) - 항상 일관된 상태 유지, 무결성 제약조건, 병행 제어
- Isolation(격리성) - 한 트랜잭션에 다른 트랜잭션 접근 불가, 고립화 수준
- Durability(영속성) - 완료된 트랜잭션 결과는 영원히 저장돼야 함, 회복 기법

### 상태

활성 상태(Active) → 부분 완료(Partially Committed) → 완료(Committed)

  → 실패(Failed) → 철회(Aborted)

### TCL(Transaction Control Language)

- COMMIT - 트랜잭션 완료. 영구적 저장
- ROLLBACK - 트랜잭션 취소. 무효화
- CHECKPOINT - 롤백 시점 저장.

### 병행 제어(Concurrency Control)

#### 개념

여러 트랜잭션 수행시 일관성을 유지하기 위해 제어하는 기법

데이터베이스의 활용도와 공유도를 극대화시키고, 일관성을 유지할 수 있도록 함.

#### 종류

- 로킹(Locking)
    - 순차적 진행 보장하는 직렬화 기법.
    - DB, 파일, 레코드 등은 로킹 단위가 될 수 있음.
    - 로킹 단위 작아지면 ⇒ 데이터베이스 공유도 증가, 로킹 오버헤드 증가
- 타임 스탬프 순서(Time Stamp Ordering)
    - 실행 시작 전 타임스탬프를 부여받고, 이 시간에 따라 작업 수행.
- 낙관적 검증
    - 일단 수행하고 종료시 검증 수행
- 다중버전 동시성 제어(MVCC, Multi Version Concurrency Control)
    - 트랜잭션의 타임스탬프와 접근하려는 데이터의 타임스탬프 비교해 적절한 버전 선택해 접근.

### 고립화 수준(Isolation Level)

#### 개념

다른 트랜잭션이 현재의 데이터의 무결성을 해치지 않도록 잠금을 설정하는 정도.

#### 종류

- Read Uncommitted
    - 갱신 중인(커밋되지 않은) 데이터에 대한 읽기 허용. 연산 불가.
- Read Committed
    - 갱신 완료된(커밋된) 데이터에 대한 읽기 허용. 갱신 중엔 읽기 불가.
- Repeatable Read
    - 트랜잭션 종료까지 갱신, 삭제 불가.
- Serializable Read
    - 순차적으로 읽을 때 데이터 전체애 대한 접근 불가.

### 회복 기법(Recovery)

#### 개념

트랜잭션 도중 오류 발생한 데이터베이스를 정상적인 상태로 복구시키는 작업.

#### 종류

- 로그 기반 회복 기법
    - 지연 갱신 회복 기법(Deferred Update) : 트랜잭션 완료 전까지 기록 X
    - 즉각 갱신 회복 기법(Immediate Update) : 트랜잭션 갱신 결과 바로 반영
- 체크 포인트 회복 기법
    - 체크 포인트를 기준으로 복원
- 그림자 페이징 회복 기법
    - 트랜잭션 수행 시 복제본 만들어 복구

## DDL(Data Definition Language)

### 개념

데이터를 정의하는 언어. 생성, 변경, 삭제 등을 수행하는 명령어들을 DDL이라 부른다.

### 대상

- 도메인(Domain) : 원자값.
- 스키마(Schema) : 데이터베이스의 기본 구조.
    - 외부 스키마(External Schema) : 사용자, 개발자 관점의 논리적 구조. 사용자 뷰.
    - 개념 스키마(Conceptual Schema) : 데이터베이스의 전체적 논리적 구조. 전체 뷰. 개체 간 관계, 제약조건, 접근권한, 무결성, 보안 등에 대한 정의.
    - 내부 스키마(Internal Schema) : 물리적 저장장치 관점의 구조. 실제 저장될 형식, 표현 방법 등.
- 테이블(Table) : 데이터의 저장 공간
    - 튜플/행, 어트리뷰트/열, 식별자, 카디널리티(튜블의 개수), 차수(어트리뷰트의 개수), 도메인
- 뷰(View) : 가상 테이블
    - 한 개 이상의 물리 테이블을 조인 기능으로 만들 수 있는 가상 테이블.
    - 논리적 데이터 독립성 제공, 데이터 조작 연산 간소화, 보안 기능 제공, 뷰 변경 불가(CREATE, DROP으로 생성과 삭제만 가능)
- 인덱스(Index) : 검색을 위한 데이터 구조
    - 순서 인덱스(정렬된 순서대로), 해시 인덱스(해시함수, 키값으로 접근), 비트맵 인덱스, 함수 기반 인덱스, 단일 인덱스, 결합 인덱스(WHERE 조건 빈도 높을 때), 클러스터드 인덱스(기본키를 기준으로 묶어서 저장)
    - 인덱스 범위 스캔, 인덱스 전체 스캔, 인덱스 단일 스캔, 인덱스 생략 스캔

### 명령어(테이블)

- CREATE
    - `CREATE TABLE` 테이블 이름 (컬럼 이름 `데이터 타입` [제약 조건])
    - PRIMARY KEY, FOREIGN KEY, UNIQUE, NOT NULL, CHECK, DEFAULT
    
    ```sql
    CREATE TABLE 학생
    (
    	학번 VARCHAR(10) PRIMARY KEY,
    	이름 VARCHAR(10) UNIQUE,
    	생년월일 CHAR(8) NOT NULL,
    	
    	// 특정 제약조건 설정
    	성별 CHAR(1) CHECK (성별 = 'M' OR 성별 = 'F')
    )
    ```
    
- ALTER
    - `ALTER TABLE` 테이블 이름 `ADD` 칼럼 이름 `데이터 타입` [제약조건];
    - `ALTER TABLE` 테이블 이름 `MODIFY` 칼럼 이름 `데이터 타입` [제약조건];
    - `ALTER TABLE` 테이블 이름 `DROP` 칼럼 이름 `데이터 타입` [제약조건];
- DROP
    - 테이블 삭제
    - `DROP TABLE` 테이블 이름 [ `CASCADE | RESTRICT` ];
    - CASCADE : 참조 테이블도 모두 삭제, RESTRICT : 참조 중이면 제거하지 않음.
- TRUNCATE
    - 테이블 내용 삭제
    - `TRUNCATE TABLE` 테이블 이름;

### 명령어(뷰, 인덱스)

#### 뷰

- `CREATE VIEW` 뷰 이름 `AS` 쿼리;
    - UNION, ORDER BY 절 사용 불가.
- `CREATE OR REPLACE VIEW` 뷰 이름 `AS` 쿼리;
    - 뷰 교체.
- `DROP VIEW` 뷰 이름;

#### 인덱스

- `CREATE [UNIQUE] INDEX` 인덱스 이름 `ON` 테이블 이름(컬럼1, 컬럼2, ...);
- `ALTER [UNIQUE] INDEX` 인덱스 이름 `ON` 테이블 이름(컬럼1, 컬럼2, ...);
- `DROP INDEX` 인덱스 이름;

## DML(Data Manipulation Language)

### 개념

데이터로 저장된 것들을 입력, 조회, 수정, 삭제(CRUD) 하는 언어.

### 명령어

- SELECT
    - 데이터 내용 조회
    - `SELECT` 속성명 `FROM` 테이블명
    - `SELECT DISTINCT` 속성명 `FROM` 테이블명 : 중복 제거
    - `SELECT` 속성명 `FROM` 테이블명 `WHERE` 조건 : 조건에 따라 검색
        - `WHERE` 칼럼 `BETWEEN` A `AND` B; ⇒ 범위
        - `WHERE` 칼럼 `IN` (A, B, ...); ⇒ 특정 조건/집합
        - `WHERE` 칼럼 `LIKE` 패턴; ⇒ 문자열 패턴(%: 0개 이상 일치, []: 1개 일치, [^]: 1개 불일치, _ : 특정 위치 1개 일치)
        - `WHERE` 칼럼 `IS NULL` ; ⇒ NULL인 것 조회 ↔ `IS NOT NULL`
        - 복합조건 : `AND`, `OR`, `NOT`
    - `SELECT` 속성명 `FROM` 테이블명 `GROUP BY` 속성명 : 속성값을 그룹으로 분류
    - `SELECT` 속성명 `FROM` 테이블명 `GROUP BY` 속성명 `HAVING` 그룹조건 : 분류된 그룹에 대해 조건 적용
    - `SELECT` 속성명 `FROM` 테이블명 `ORDER BY` 속성 [ `ASC | gggDESC` ] : 속성 정렬
    - 조인(Join)
        - 내부 조인 : `FROM` A `[INNER] JOIN` B `ON` 조건 : 공통된 값을 기준으로 결합
        - 외부 조인 : `FROM` A `LEFT/RIGHT/FULL [OUTER] JOIN` B `ON` : 왼쪽 기준, 오른쪽 기준, 양쪽 테이블 데이터 추출
        - 교차 조인 : `FROM` A `CROSS JOIN` B : 무지성 조인, 상황에 따라 중복 많을 수 있음
        - 셀프 조인 : `FROM` A `[INNER] JOIN` B `ON` 조건 : 자기 자신에 별칭 지정한 후 조인
    - 서브쿼리(Sub-Query)
        - SELECT, FROM, WHERE 절에서 사용할 수 있는 일종의 중첩 쿼리문.
    - 집합 연산자(Set Operator)
        - UNION : 합집합 (= 중복 제거)
        - UNION ALL : 합집합 + 중복 포함
        - INTERSECT : 교집합 (= 중복만)
        - MINUS : 차집합 (앞에만 있는 것)
- INSERT
    - 데이터 내용 삽입
    - `INSERT` `INTO` 테이블이름(속성 이름) `VALUES` (데이터);
- UPDATE
    - 데이터 내용 변경
    - `UPDATE` 테이블이름 `SET` 속성 이름 = 데이터 `WHERE` 조건;
- DELETE
    - 데이터 삭제
    - `DELETE` `FROM` 테이블이름 `WHERE` 조건;

## DCL(Data Control Language)

### 개념

관리자가 사용하는 제어용 언어로, 보안, 무결성 유지, 병행 제어 및 회복을 위한 기능이다.

### 유형

- GRANT
    - 권한 부여
    - `GRANT` 권한 `ON` 테이블 `TO` 사용자;
- REVOKE
    - 권한 제거
    - `REVOKE` 권한 `ON` 테이블 `FROM` 사용자;

# SQL 응용

## 집계성 SQL 작성

### 데이터 분석 함수

총합, 평균 등의 데이터 분석을 위해 두 행 이상의 데이터를 모아 처리하는 것을 목적으로 하며, `GROUP BY` 구문으로 그룹핑하고, `SELECT`, `HAVING`, `ORDER BY` 등에 사용된다.

### 데이터 분석 함수 종류

- 집계 함수 : 여러 행 또는 테이블 전체에서 하나의 결괏값 반환
- 그룹 함수 : 그룹의 중간 합계 분석
- 윈도 함수 : 온라인 분석 처리 용도

## 집계 함수(Aggregate Function)

### 개념

여러 행 또는 테이블 전체에서 하나의 결괏값을 반환하는 함수.

### 구문

```sql
SELECT 컬럼1, 컬럼2, ..., 집계함수
	FROM 테이블명
[WHERE 조건]
GROUP BY 컬럼1, 컬럼2, ...
[HAVING 조건식]
```

### 함수 종류

- COUNT - 행의 줄 수
- SUM - 행의 칼럼 합계
- AVG - 행의 칼럼 평균
- MAX - 행의 칼럼 최댓값
- MIN - 행의 칼럼 최솟값
- STDDEV - 행의 칼럼 간 표준편차
- VARIAN - 행의 칼럼 간 분산

### 예시

```sql
// 입학년도가 2015 이상인 학생들의 수
SELECT COUNT(*)
	FROM STUDENT
  WHERE 입학년도 >= 2015
```

## 그룹 함수(Group Function)

### 개념

열의 값에 따라 그룹화하여 그룹별로 결과를 반환하는 함수.

### 구문

```sql
SELECT 칼럼1, 칼럼1, ..., 집계 함수
	FROM 테이블명
[WHERE 조건]
GROUP BY [칼럼...] 그룹 함수 (칼럼)
[HAVING 조건절]
[ORDER BY 선택]
```

### 함수 종류

- ROLLUP
    - 중간 집계 값 산출
    - SELECT 뒤에 오는 칼럼을 GROUP BY나 ROLLUP 뒤에도 기재해야 함.
    - 순서에 따라 값 달라질 수 있음
    - 예시
        
        ```sql
        SELECT DEPT, JOB, SUM(SALARY)
        	FROM DEPT_SALARY
         GROUP BY ROLLUP(DEPT, JOB);
        
         DEPT  |   JOB   |  SALARY
        -------|---------|----------
         마케팅  |   부장   |   5,000
        -------|---------|----------
         마케팅  |   차장   |   2,000
        -------|---------|----------
         마케팅  |         |   7,000
        -------|---------|----------
          기획  |   부장   |   4,000
        -------|---------|----------
          기획  |   사원   |   2,000
        -------|---------|----------
          기획  |         |   6,000
        -------|---------|----------
               |         |  13,000
        -------|---------|----------
        
        ```
        
- CUBE
    - 결합 가능한 모든 값에 대한 집계 생성
    - 순서에 따라 값 달라질 수 있음
    - 예시
        
        ```sql
        SELECT DEPT, JOB, SUM(SALARY)
        	FROM DEPT_SALARY
         GROUP BY CUBE(DEPT, JOB);
        
         DEPT  |   JOB   |  SALARY
        -------|---------|----------
               |         |  14,000
        -------|---------|----------
               |   부장   |   9,000
        -------|---------|----------
               |   차장   |   3,000
        -------|---------|----------
               |   사원   |   2,000
        -------|---------|----------
         마케팅  |         |   8,000
        -------|---------|----------
         마케팅  |   부장   |   5,000
        -------|---------|----------
         마케팅  |   차장   |   3,000
        -------|---------|----------
          기획  |         |   6,000
        -------|---------|----------
          기획  |   부장   |   4,000
        -------|---------|----------
          기획  |   사원   |   2,000
        -------|---------|----------
        ```
        
- GROUPING SETS
    - 칼럼 간 순서와 무관한 결과를 얻을 수 있으며, 개별 집계를 구할 수 있음.
    - 예시
        
        ```sql
        SELECT DEPT, JOB, SUM(SALARY)
        	FROM DEPT_SALARY
         GROUP BY GROUPING SETS(DEPT, JOB);
        
         DEPT  |   JOB   |  SALARY
        -------|---------|----------
               |         |  14,000
        -------|---------|----------
               |   부장   |   9,000
        -------|---------|----------
               |   차장   |   3,000
        -------|---------|----------
               |   사원   |   2,000
        -------|---------|----------
         마케팅  |         |   8,000
        -------|---------|----------
          기획  |         |   6,000
        -------|---------|----------
        ```
        
    

## 윈도 함수

### 개념

온라인 분석 처리 용도로 사용하는 함수며, 표준 SQL에 추가됐다.

OLAP(OnLine Analytical Processing) 함수라고 부른다.

### 구문

```sql
SELECT 함수명(파라미터)
	OVER
([PARTITION BY 칼럼1, ...]
[ORDER BY 칼럼a, ...])
	FROM 테이블명
```

### 유형

- 순위 함수
    - 레코드의 순위 계산
    - RANK, DENSE_RANK, ROW_NUMBER
- 행 순서 함수
    - 가장 먼저 혹은 나중에 나오는 값 이전, 이후의 값 출력
    - FIRST_VALUE, LAST_VALUE, LAG, LEAD
- 그룹 내 비율 함수
    - 백분율 등 비율과 관련된 통계
    - RATIO_TO_REPORT, PERCENT_RANK

### 예시

```sql
SELECT NAME, SALARY
	RANK() OVER (ORDER BY SALARY ASC) A,
	DENSE_RANK() OVER (ORDER BY SALARY ASC) B,
	ROW_NUMBER() OVER (ORDER BY SALARY ASC) C
FROM EMPLOYEE;

// 각 A, B, C열에 순위 매겨짐
```

# 절차형 SQL(Procedural Language)

## 개념

일반적인 프로그래밍 언어처럼 절차 지향적으로 SQL문을 짤 수 있는 언어.

보통의 프로그래밍 언어처럼 조건문(IF THEN, ELSIF THEN), 케이스 문(CASE WHEN THEN SET), 반복문(LOOP, WHILE LOOP EXIT WHEN, FOR IN LOOP), 예외문(EXCEPTION WHEN THEN SET)이 있다.

## 종류

### 프로시저(Procedure)

#### 개념

쿼리를 함수처럼 실행하기 위한 쿼리의 집합

#### 구성

- 선언부(DECLARE)
- 시작/종료부(BEGIN/END)
- 제어부(CONTROL)
- SQL
- 예외부(EXCEPTION)
- 실행부(TRANSACTION)

#### 문법

```sql
CREATE [OR REPLACE] PROCEDURE 프로시저 이름
(파라미터 이름 [IN | OUT | INOUT] 데이터 타입, ...)
IS
	변수 선언
BEGIN
	명령어;
[COMMIT | ROLLBACK]
END;

// 호출문
EXECUTE 프로시저 이름 (파라미터,...);
```

### 사용자 정의 함수(User-Defined Function)

#### 개념

수행 결과를 값으로 반환할 수 있는 함수.

#### 구성

트랜잭션과 동일한데, 여기에 실행부(TRANSACTION)을 빼고 반환부(RETURN)이 들어감.

#### 문법

```sql
CREATE [OR REPLACE] FUNCTION 함수이름
(파라미터 이름 IN 데이터 타입)
RETURN 데이터 타입
IS
	변수 선언
BEGIN
	명령어;
	RETURN 변수;
END;
```

### 트리거(Trigger)

#### 개념

이벤트(삽입, 갱신, 삭제 등)가 발생할 때마다 실행되는 함수.

#### 구성

프로시저와 사용자 정의 함수와 유사하지만, 프로시저처럼 반환값이 없고 사용자 정의 함수처럼 IN 만 가능하다.

여기에 이벤트부(EVENT)가 추가된다.

#### 문법

```sql
CREATE [OR REPLACE] TRIGGER 트리거 이름
[BEFORE | AFTER] 유형 ON 테이블 이름
[FOR EACH ROW]
BEGIN
END;
```