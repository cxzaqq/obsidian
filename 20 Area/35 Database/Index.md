# 개요

![[index.png]]
정렬 후 꼬리표를 달아서 블럭 단위로 검색된다 보면 됨 마치 주소처럼
서울시 / 용산구 / ..
=> 조회 성능 상승
인덱스를 걸지 않고 검색 시 모든 컬럼을 다 검색함(full scan)

pk엔 고유 인덱스가 기본적으로 달려 있음
unique index: 중복된 값이 없는 컬럼에 달리는 인덱스(pk, uk)
non-unique index: 중복된 값이 있는 컬럼에 달리는 인덱스

## 인덱스 확인

```sql
CREATE TABLE phone (
	phone_code INT PRIMARY KEY,
	phone_name VARCHAR(100),
	phone_price DECIMAL(10, 2)
);

INSERT
  INTO phone
VALUES
(1, 'galaxys24', 1200000),
(2, 'iphone16pro', 1430000),
(3, 'galaxyfold7', 1730000);

-- pk는 인덱스가 기본적으로 생성됨
SHOW INDEX FROM phone;

-- truncate 해도 인덱스(구조)는 살아있음
TRUNCATE phone;

-- 인덱스가 있는 컬럼을 조건에 사용해 조회 시 인덱스를 통한 조회 가능
SELECT * FROM phone WHERE phone_code = 1;
EXPLAIN SELECT * FROM phone WHERE phone_code = 1;

-- 인덱스 생성
CREATE INDEX idx_name ON phone(phone_name);
-- 인덱스 생성 시 DB 엔진이 테이블의 전체 데이터를 스캔하여 기존 데이터들에 인덱스 적용
-- 이후 추가되는 데이터나 수정되는 데이터가 생기면 반영 및 갱신됨

SHOW INDEX FROM phone;
EXPLAIN SELECT * FROM phone WHERE phone_name = 'iphone16pro';

/* 쿼리 옵티마이저가 인덱스를 태울지 안 태울지 선택함 실행 계획에 그걸 기록해줌 explain 명령어 어떤 인덱스를 태웠나 볼 수 있음 */
```

```sql
/*

TRUNCATE는 데이터를 하나씩 지우는 게 아니라 데이터 페이지 자체를 DROP 후 재할당 하는 방식(빠름)

TRUNCATE 시 인덱스가 남아있는 이유?
인덱스는 테이블의 스키마 정의의 일부.
TRUNCATE는 데이터만 날리고 테이블 정의(=스키마)는 그대로 유지하기 때문에 인덱스의 정의도 그대로
남는다.

즉 구조는 살아있고 데이터는 날라가는 것임

*/
```

# 주의

인덱스는 꼭 필요한 곳에만 달아야 함
- 조건으로 활용할 컬
	WHERE, HAVING, JOIN 시 ON에 사용하는 컬럼
	동등 비교(=) 조건이 가장 효율이 좋음
	대소 비교(<, > 등), BETWEEN, LIKE 같은 범위 조건도 인덱스를 탈 수 있지만 효율이 떨어짐

- 데이터가 자주 수정되지 않는 컬럼에 달아야 함
	INSERT, DELETE, UPDATE 할 때마다 인덱스도 갱신해야 해서 쓰기 성능이 떨어짐. 하지만 읽기 성능이 훨씬 중요한 경우(로그 조회, 분석)에는 쓰기 성능을 희생하고서라도 인덱스를 다는 경우가 있음

- 전체 데이터의 10~15% 정도의 범위에 해당하는 조건이 가장 효율이 좋음(비고유 인덱스)
	여기서 효율은 투자 대비 효율
	DBMS 옵티마이저도 보통 카디널리티가 낮아서 전체 데이터의 일정 % 이상을 가져와야 한다면 인덱스를 쓰지 않고 풀스캔을 선택함(DB마다 다르지만 대략 15~20%임)

- 중복된 데이터가 많을수록 효율이 떨어짐 => 카디널리티가 높은(중복도가 낮은) 컬럼일수록 좋음


고유 인덱스는 특정 레코드 하나 찾을 때 최적
비고유 인덱스는 여러 행을 걸러내는 데 유용함

pk는 바뀌지 않는 컬럼. 즉 조회 시 pk에 조건을 거는 게 좋음

특정 컬럼에서 조회가 많이 일어난다? 위 요소들을 모두 고려해 인덱스를 고민하자.

# 단점

- 별도의 저장 공간 필요

- 쓰기 성능 저하

- 메모리 캐시 부담
	인덱스는 효율적으로 동작하려면 메모리에 올라와야 하는데 인덱스가 많으면 Buffer Pool(캐시 메모리)에서 자리를 많이 차지함 -> 자주 쓰는 인덱스의 캐시 효율이 떨어질 수도 있음

- 옵티마이저 선택 문제
	DB 옵티마이저가 잘못된 인덱스를 선택해서 오히려 성능이 떨어질 수 있음

- 인덱스의 Fragmentation이 많으면 주기적으로 Rebuild 해야 하는 경우가 있음

- 복잡한 쿼리에서 제약
	WHERE, JOIN, ORDER BY 등 특정 패턴에서만 잘 동작함
	LIKE '%abc', 함수 적용된 컬럼(DATE(col)), OR 조건이 많은 쿼리 등은 인덱스를 거의 못 씀씀

---

WHERE 절에 인덱스 컬럼 vs 일반 컬럼

