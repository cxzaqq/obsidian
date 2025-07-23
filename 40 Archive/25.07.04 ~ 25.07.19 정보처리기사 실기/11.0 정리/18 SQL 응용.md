
# DDL (B)
Data Define Language

DB 구조, 데이터 형식, 접근 방식 등 DB를 구축하거나 수정할 목적으로 사용하는 언어

- CREATE
	SCHEMA, DOMAIN, TABLE, VIEW, INDEX를 정의
- ALTER
	TABLE의 정의 변경
- DROP
	SCHEMA, DOMAIN, TABLE, VIEW, INDEX를 삭제

# CREATE TABLE (B)

```sql
CREATE TABLE 테이블명
(속성명 데이터타입 [DEFAULT 기본값] [NOT NULL], ...
[, PRIMARY KEY(기본키_속성명, ...)]
[, UNIQUE(대체키_속성명), ...]
[, FOREIGN KEY(외래키_속성명), ...
REFERENCES 참조테이블(기본키_속성명, ..)]
[ON DELETE 옵션]
[ON UPDATE 옵션]
[, CONSTRAINT 제약조건명] [CHECK (조건식)]
);
```

### ON DELETE, UPDATE 옵션

- NO ACTION: 참조 테이블에 변화가 있어도 기본 테이블에는 아무런 조취를 취하지 않음
- CASCADE: 참조 테이블의 튜플이 삭제되면 기본 테이블의 관련 튜플도 모두 삭제되고 속성이 변경되면 관련 튜플의 속성 값도 모두 변경됨
- SET NULL: 참조 테이블에 변화가 있으면 기본 테이블의 관련 튜플의 속성 값을 NULL로 변경
- SET DEFAULT: 참조 테이블에 변화가 있으면 기본 테이블의 관련 튜플의 속성 값을 기본값으로 변경

### CONSTRAINT, CHECK
- CONSTRAINT: 제약 조건의 이름을 지정함
- CHECK: 속성 값에 대한 제약 조건을 정의함

# CREATE VIEW (C)

view를 정의하는 명령문

```SQL
CREATE VIEW 뷰명[(속성명[, 속성명, ...])]
AS SELECT문;
```

# CREATE INDEX (A)

인덱스를 정의하는 명령문
```SQL
CREATE [UNIQUE] INDEX 인덱스명
ON 테이블명(속성명 [ASC | DESC] [, 속성명 [ASC | DESC]])
[CLUSTER];
```

- UNIQUE
	- 사용된 경우: 중복 값이 없는 속성으로 인덱스를 생성
	- 생략된 경우: 중복 값을 허용하는 속성으로 인덱스를 생성
- 정렬 여부 지정
	- ASC: 오름차순
	- DESC: 내림차순
	- 생략: 오름차순
- CLUSTER: 사용하면 인덱스가 클러스터드 인덱스로 설정됨

클러스터드 인덱스?
인덱스가 가리키는 것이 실제 데이터

비클러스터드 인덱스?
인덱스는 실제 데이터의 위치를 가리킴

# ALER TABLE (A)

테이블에 대한 정의를 변경하는 명령문

```SQL
ALTER TABLE 테이블명 ADD 속성명 데이터_타입 [DEFAULT '기본값'];

ALTER TABLE 테이블명 ALTER 속성명 [SET DEFAULT '기본값'];

ALTER TABLE 테이블명 DROP COLUMN 속성명 [CASCADE];
```

ADD: 새로운 속성을 추가
ALTER: 특정 속성의 이름, 데이터 타입, 기본값을 변경
DROP COLUMN: 특정 속성을 삭제

# DROP (A)

스키마, 도메인, 기본 테이블, 뷰 테이블, 인덱스, 제약 조건 등을 제거하는 명령문

```SQL
DROP SCHEMA 스키마명 [CASCADE | RESTRICT];
DROP DOMAIN 도메인명 [CASCADE | RESTRICT];
DROP TABLE 테이블명 [CASCADE | RESTRICT];
DROP VIEW 뷰명 [CASCADE | RESTRICT];
DROP INDEX 인덱스명 [CASCADE | RESTRICT];
DROP CONSTRAINT 제약조건명;
```

CASCADE: 제거할 요소를 참조하는 다른 모든 개체를 함께 제거
RESTRICT: 다른 개체가 제거할 요소를 참조 중일 때는 제거를 취소

# DCL (B)

Data Control Language
데이터의 보안, 무결성, 회복, 병행 제어 등을 정의하는 데 사용하는 언어

- COMMIT
	명령에 의해 수행된 결과를 실제 물리적 디스크로 저장하고 DB 조작 작업이 정상적으로 완료되었음을 관리자에게 알려줌
- ROLLBACK
	DB 조작 작업이 비정상적으로 종료되었을 때 원래의 상태로 복구
- GRANT
	DB 사용자에게 사용 권한을 부여
- REVOKE
	DB 사용자의 사용 권한을 취소

# GRANT/REVOKE (A)

```SQL
GRANT 권한_리스트 ON 개체 TO 사용자 [WITH GRANT OPTION];

REVOKE [GRANT OPTION FOR] 권한_리스트 ON 개체 FROM 사용자 [CASCADE];
```

### 권한 종류
ALL, SELECT, INSERT, DELETE, UPDATE 등

### WITH GRANT OPTION
부여 받은 권한을 다른 사용자에게 다시 부여할 수 있는 권한을 부여함

### GRANT OPTION FOR
다른 사용자에게 권한을 부여할 수 있는 권한을 취소함

### CASCADE
권한 취소 시 권한을 부여 받았떤 사용자가 다른 사용자에게 부여한 권한도 연쇄적으로 취소함
# ROLLBACK (A)

변경되었으나 아직 COMMIT 되지 않은 모든 내용들을 취소하고 DB를 이전 상태로 돌리는 명령어

# DML (B)

Data Manipulation Language

저장된 데이터를 실질적으로 관리하는 데 사용되는 언어

SELECT, INSERT, DELETE, UPDATE

# 삽입문 (A)

```SQL
INSERT INTO 테이블명([속성명1, 속성명2, ...])
VALUES (데이터1, 데이터2, ...);
```

- SELECT 문을 사용하여 다른 테이블의 검색 결과를 삽입할 수도 있다.
# 삭제문 (A)

```SQL
DELETE FROM 테이블명 [WHERE 조건]
```

- 테이블 구조는 남기에 DROP과는 다름
# 갱신문 (A)

```SQL
UPDATE 테이블명
SET 속성명 = 데이터[, 속성명 = 데이터, ...]
[WHERE 조건];
```

# SELECT (A)

```SQL
SELECT [PREDICATE] [테이블명.]속성명 [AS 별칭][, [테이블명.]속성명, ...]
FROM 테이블명[, 테이블명, ...]
[WHERE 조건]
[GROUP BY 속성명, 속성명, ...]
[HAVING 조건]
[ORDER BY 속성명 [ASC | DESC]];
```

- PREDICATE
	검색할 튜플 수를 제한하는 명령어를 기술
	DISTINCT: 중복된 튜플이 있으면 그 중 첫 번째 한 개만 표시
# 기본 검색 (A)

```SQL
SELECT DISTINCT 주소 FROM 사원;
```
=> 중복된 주소는 하나만 나오게
# 조건 지정 검색 (A)
-
# 정렬 검색 (A)
-
# 하위 질의 (A)

```SQL
SELECT 이름, 기본급, 주소
FROM 사원
WHERE 기본급 < ALL (SELECT 기본급 FROM 사원 WHERE 주소 = '망원동');
```

ALL ( ) => 하위 질의로 검색된 범위를 기본 질의의 조건으로 사용
즉 망원동에 거주하는 사원들의 기본급보다 적은 기본급을 받는 사원 조회(가장 작읁 기본급 적용)
# 그룹 함수 (A)

- COUNT(속성명)
- SUM(속성명)
- AVG(속성명)
- MAX(속성명)
- MIN(속성명)
- STDDEV(속성명): 그룹별 표준 편차를 구하는 함수
- VARIANCE(속성명): 그룹별 분산을 구하는 함수

# 그룹 지정 검색 (A)

```SQL
SELECT 부서, COUNT(*) AS 사원수
FROM 상여금
WHERE 상여금 >= 100
GROUP BY 부서
HAVING COUNT(*) >= 2;
```
=> <상여금> 테이블에서 '상여금'이 100 이상인 사원이 2명 이상인 '부서'의 튜플 수
# 집합 연산자를 이용한 통합 질의 (A)

- UNION: 합집합
- UNION ALL: 합집합(중복도 모두 출력)
- INTERSECT: 교집합
- EXCEPT: 차집합

# JOIN (C)

2개 이상의 릴레이션에서 연관된 튜플들을 결합하여 하나의 새로운 릴레이션을 반환

- INNER JOIN
	THETA, EQUI, NATURAL, NON-EQUI
- OUTER JOIN
	LEFT OUTER, RIGHT OUTER, FULL OUTER

# INNER JOIN (A)

- THETA
```SQL
SELECT *
FROM A
JOIN B ON A.age >= B.min_age
```
`=`뿐만 아니라 `>`, `<`, `!=`, `>=`, `<=` 등 모든 비교 연산자(θ)를 사용하는 JOIN

- EQUI
```SQL
SELECT *
FROM employee
JOIN department ON employee.dept_id = department.id;
```
THETA 조인의 특수한 경우 즉 =만 사용 시

- NATURAL
```SQL
SELECT *
FROM A
NATRUAL JOIN B
```
두 릴레이션에서 이름이 같은 컬럼끼리 자동으로 JOIN

- NON-EQUI
```SQL
SELECT *
FROM salary_grade
JOIN employee ON employee.salary BETWEEN salary_grade.low AND salary_grade.high;
```
THETA JOIN 중 `=`이 아닌 것만 모은 것
`BETWEEN`, `<`, `>` 등 동등하지 않은 조건

USING?
```SQL
SELECT *
FROM employee
JOIN department USING (dept_id);
```
=> dept_id가 같은 것끼리 조인

# OUTER JOIN (A)

### LEFT OUTER JOIN
값이 없는 우측 항 릴레이션의 값을 NULL로 채워서 반환

### RIGHT OUTER JOIN
값이 없는 좌측 항 릴레이션의 값을 NULL로 채워서 반환

### FULL OUTER JOIN
값이 없는 양측 항 릴레이션들의 값을 NULL로 채워서 반환

# 트리거 (B)

이벤트가 발생할 때 관련 작업이 자동으로 수행되게 하는 절차형 SQL

  

# 추가
# SQL 분석 함수 & 집계 확장 정리
## 🧩 1. RANK 계열 함수
### 1.1 RANK()
- 동일한 값에 같은 순위를 부여하고, **순위 건너뜀**
- 예:

| 점수  | RANK |
| --- | ---- |
| 100 | 1    |
| 100 | 1    |
| 90  | 3    |

```sql

SELECT 이름, 점수,

       RANK() OVER (ORDER BY 점수 DESC) AS 순위

FROM 학생;

```
---

### 1.2 DENSE_RANK()
- 동일한 값에 같은 순위, **순위 건너뛰지 않음**
- 예:

| 점수  | DENSE_RANK |
| --- | ---------- |
| 100 | 1          |
| 100 | 1          |
| 90  | 2          |

```sql

SELECT 이름, 점수,

       DENSE_RANK() OVER (ORDER BY 점수 DESC) AS 순위

FROM 학생;

```
---

### 1.3 ROW_NUMBER()
- **동일한 값도 고유 번호 부여** (순서 강제 지정)
- 예:

| 점수 | ROW_NUMBER |
|-|-|
| 100  | 1          |
| 100  | 2          |
| 90   | 3          |

```sql

SELECT 이름, 점수,

       ROW_NUMBER() OVER (ORDER BY 점수 DESC) AS 번호

FROM 학생;

```
---

## 📊 2. 집계 확장 기능: ROLLUP / CUBE / GROUPING SETS

### 2.1 ROLLUP
- 계층적 집계를 수행 (누적 집계)
- 예: (연도, 분기)로 그룹할 경우 → 연도별 소계 포함
```sql

SELECT 연도, 분기, SUM(매출)

FROM 판매

GROUP BY ROLLUP(연도, 분기);

```

출력 예:

| 연도   | 분기   | 매출합계     |         |
| ---- | ---- | -------- | ------- |
| 2024 | Q1   | 1000     |         |
| 2024 | Q2   | 1200     |         |
| 2024 | NULL | 2200     | ← 연도 소계 |
| NULL | NULL | 전체합계     |         |

---
### 2.2 CUBE
- 가능한 모든 조합으로 집계 (다차원 분석용)
```sql

SELECT 지역, 제품, SUM(매출)

FROM 판매

GROUP BY CUBE(지역, 제품);

```

출력 예:

| 지역   | 제품    | 매출합계     |         |
| ---- | ----- | -------- | ------- |
| 서울   | A     | 500      |         |
| 서울   | NULL  | 900      | ← 서울 전체 |
| NULL | A     | 800      | ← A 전체  |
| NULL | NULL  | 1500     | ← 전체합계  |

---
### 2.3 GROUPING SETS
- 원하는 조합만 집계 가능
```sql

SELECT 지역, 제품, SUM(매출)

FROM 판매

GROUP BY GROUPING SETS (

  (지역, 제품),

  (지역),

  ()

);

```
- 위 쿼리는 (지역, 제품), (지역), 전체 집계를 **직접 지정**함
---
## 📌 3. 기타 윈도우 함수 예시
### 3.1 NTILE(n)

- 데이터를 n등분하여 그룹 나누기

```sql

SELECT 이름, 점수,

       NTILE(4) OVER (ORDER BY 점수 DESC) AS 분위

FROM 학생;

```
---
### 3.2 LAG / LEAD

- 이전/다음 행의 값을 참조

```sql

SELECT 이름, 점수,

       LAG(점수) OVER (ORDER BY 점수 DESC) AS 이전점수,

       LEAD(점수) OVER (ORDER BY 점수 DESC) AS 다음점수

FROM 학생;

```
---
## 📚 요약

| 함수 / 기능       | 설명                      | 주요 용도              |
| ------------- | ----------------------- | ------------------ |
| RANK()        | 공동 순위, 건너뜀              | 순위 매기기             |
| DENSE_RANK()  | 공동 순위, 건너뛰지 않음          | 순위 매기기             |
| ROW_NUMBER()  | 행마다 고유 순번               | 정렬된 번호 지정          |
| ROLLUP        | 누적 소계                   | 연도별, 월별 누계 등       |
| CUBE          | 전체 조합 집계                | 다차원 분석             |
| GROUPING SETS | 집계 조합을 직접 지정            | 유연한 다차원 분석         |
| LAG / LEAD    | 이전/다음 행 참조              | 변화 추적, 시계열 비교      |
| NTILE(n)      | n분위로 나누기                | 성적 분위, 상/중/하 나누기 등 |