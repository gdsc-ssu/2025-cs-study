## 개요

최근에 lock 관련해서 모르는 것들이 많아 DB 공부를 해야겠다고 생각했습니다. 데이터 무결성이 가장 중요한 경우에는 어떻게 대응을 해야할까? 좋아요같은 수시로 변하는 기능에는 어떻게 락을 잡아야 하지? 이런 고민을 하다가 DB와 lock, 동시성 제어 등을 조금씩 공부해보려고 합니다. 이번주는 간단하게 MySQL로 동시성 제어를 알아볼까 합니다.

## 목표

1. 간단하게 MySQL 아키텍처 알아보기
2. ANSI SQL 표준과 MySQL에서 구현하는 방식 알아보기
3. MVCC와 MySQL의 InnoDB 엔진 알아보기

## 1. MySQL 아키텍처

목적 : MySQL의 아키텍처를 빠르게 훌어보자

### MySQL의 논리적 아키텍처

MySQL은 Client -> DB서버(연결 핸들링, 파서, 옵티마이저) -> 스토리지 엔진으로 구성

각 클라이언트의 연결은 단일 스레드를 할당하여 진행. 서버는 미리 스레드 캐시(스레드 풀과 비슷하지만 다름)를 유지 관리함

MySQL은 쿼리를 구문 분석하여 트리를 생성하고 이를 이용해 최적화를 진행. 쿼리 재작성, 테이블 읽는 순서, 사용할 인덱스 선택 등이 이루어짐.

### 동시성 제어

S-Lock : 읽기 잠금, X-Lock : 쓰기 잠금

lock을 사용할수록 데이터 무결성은 올라가지만 잠금 설정, 잠금 해제 여부 확인, 잠금 해제 등의 작업으로 오버헤드가 존재하고 성능 저하의 원인이 되어 `트레이드 오프`가 있음

각각의 트랜잭션들을 순차적으로(serial) 실행하면 동시성이 떨어질 것임. 이를 개선하기 위해 순차적인 것처럼(serializable) 스케줄링을 진행

table lock : 기본적인 잠금. `동시성이 낮지만 가장 낮은 오버헤드`를 가진 전략. lock의 범위가 테이블. 성능 향상을 위해 일부 동시 쓰기 작업을 허용하기도 함

row lock : 스토리지 엔진에서 구현. `동시성이 높지만 가장 높은 오버헤드`를 가진 전략. lock의 범위가 행.

### 트랜잭션

ACID를 만족하는 sql로 구성된 작업 단위.

ANSI SQL 표준에서는 다음의 트랜잭션 격리 수준을 제시함. 격리 수준이 높을수록 동시성은 작아지고, 오버헤드가 많아짐

1. READ UNCOMMITED : dirty read(커밋되지 않은 내용)을 읽을 수 있음. non-repeatable read(트랜잭션 초반에 읽은 값과 그 후에 다시 읽은 값이 일치하지 않음. 도중에 다른 트랜잭션에 의해 변경이 되었기 때문), phantom read(트랜잭션 초반에 읽은 값과 그 후에 다시 읽은 값에 행이 삭제되거나 추가됨. 도중에 다른 트랜잭션에 의해 행이 추가되거나 삭제되었기 때문)도 가능하여 데이터 일관성이 매우 낮음. 따라서 잘 사용 안함
2. READ COMMITED : dirty read방지. non-repeatable read, phantom read는 허용. MySQL외 대부분 DBMS는 이 방법을 기본으로 채택
3. REPEATABLE READ : Non-reapeatable read 방지. MySQL에서는 스토리지 엔진에서 구현됨(MVCC). `트랜잭션이 시작된 스냅샷 데이터`를 기준으로 조회를 수행. phantom read는 막지 못함. MySQL의 방식
4. SERIALIZABLE : 읽는 행과 함께 관련된 행까지 락을 걸어 phantom read까지 막음. 오버헤드가 많아 데이터 무결성이 엄격하게 지켜져야 하는 곳에서 사용

데드락이 발생하면 대부분의 DBMS는 타임아웃이나 교착 상태 탐지를 이용해 데드락에 대응함. 스토리지 엔진마다 데드락에 대응하는 방법이 다르며 MySQL에서 많이 사용하는 InnoDB의 경우 순환 종속성 탐지를 통해 데드락을 방지하고, 발생하면 롤백을 시키는 방법을 사용

MySQL은 지금까지 많은 스토리지 엔진을 제공했지만, 요즘은 InnoDB가 최고의 표준이고 권장되는 엔진임. 다음의 트랜잭션 기본 요소는 InnoDB의 구현을 기반함

- AUTOCOMMIT : 트랜잭션이 아닌 단일 쿼리는 트랜잭션으로 처리되어 즉시 커밋
- explicit locking과 implicit locking : InnoDB는 기본적으로 2-phase locking을 사용하여 lock의 획득과 해제를 관리. 이 때 lock 설정을 우리가 쿼리를 통해 작성해줄 필요가 없음(implicit locking). 또한 InnoDB의 경우 "LOCK TABLE"과 같은 명령을 지원하여 명시를 할 수 있음(Explicit locking)

### MVCC(Multiversion Concurrency Control)

서로 다른 트랜잭션에서 행의 다중버전(non-repeatable read 문제의 행)을 처리하기 위해 사용. MySQL뿐만아니라 오라클, PostgreSQL 등에서 사용(표준이 없어 차이는 있음)

트랜잭션마다 id를 할당하고, 시작된 시점의 데이터를 스냅샷으로 저장하여 읽기 작업을 수행. undo로그와 redo로그도 작성하여 롤백이나 스냅샷 생성에 도움을 줌

MVCC는 row lock의 변형이라고 생각하면 됨. 대부분의 경우 lock을 걸 필요가 없어 오버헤드가 낮고, repeatable read와 read commited 격리 수준에서만 작동

### InnoDB 엔진

MySQL의 기본 스토리지 엔진이며 가장 중요하다고 볼 수 있음. '테이블 스페이스'라는 블랙박스에 데이터를 저장

InnoDB는 MVCC를 사용하여 높은 동시성을 구현하고 SQL 표준 격리를 모두 구현

InnoDB는 다른 엔진과 다른 독특한 인덱싱을 구현. 따라서 키 검색을 빠르게 할 수 있음. 하지만 보조 인덱스에 기본 키 열을 포함하고 있어, `키의 값이 크면 인덱스도 커짐`. 따라서 키값은 작게 관리가 필요 -> PK는 increament로 구현하고 필요하다면 `surrogate key를 만들어 6자리 영어 숫자 조합 키를 만드는게 좋다고 생각`

cf) InnoDB의 다른 엔진과 다른 점은 Clustered Index를 사용한다는 점이다. 다른 엔진과 마찬가지로 빠른 검색을 위해 B Tree대신 B+ Tree를 사용하는 것은 같다고 한다.
Clustered Index는 쉽게 말하면, `인덱스에 따라 로우를 정렬`하는 방식이다. 반면에 Non-Clustered Index는 데이터 테이블과 별개로 키값을 저장한다.
이를 통해 전자 방식은 인덱스 조회만으로 데이터를 검색할 수 있는 반면, 후자는 인덱스 조회 후 얻은 키 값으로 다시 한 번 조회를 해야한다.

\*참고 : MySQL 성능 최적화, [클러스터형 및 비클러스터형 인덱스](https://learn.microsoft.com/ko-kr/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?view=sql-server-ver16)