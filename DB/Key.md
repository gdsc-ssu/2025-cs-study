# Key에 대해 설명해 주세요

## Key란?

- 검색, 정렬시 Tuple 구분의 기준이 되는 Attribute
    - Tuple
        - 데이터베이스에서 하나의 row
        - 특정 데이터를 표현하는 단위 → 학생 정보 테이블이라면 하나의 학생 데이터가 튜플
    - Attribute
        - 데이터베이스에서 column
        - 각 튜플이 가진 데이터의 속성을 정의 → 학생 정보 테이블이라면 이름, 학번, 전공이 애트리뷰트

### 후보키

- 튜플을 유일하게 식별하기 위해 사용하는 속성들의 부분 집합
    - 기본키로 사용할 수 있는 속성
    - 2가지 조건 만족
        - 유일성: 키로 하나의 튜플을 유일하게 식별할 수 있음
        - 최소성: 꼭 필요한 속성으로만 구성

### 기본키

- 후보키 중 선택한 Main Key
    - Null 값을 가질 수 없음
    - 동일한 값이 중복될 수 없음

### 대체키

- 후보키 중 기본키를 제외한 나머지 키 = 보조키

### 슈퍼키

- 유일성은 만족하지만, 최소성은 만족하지 못하는 키

### 외래키

- 다른 릴레이션의 기본키를 그대로 참조하는 속성의 집합

## 기본키는 수정이 가능한가요?

- 기본키는 각 튜플을 고유하게 식별하기 위해 사용된다
- MySQL에서 기본키는 일반적으로 변경하지 않는 것이 좋지만,
  수정 자체는 가능하다 → UPDATE문으로

## 기본키 없이 테이블 생성이 가능한 이유

- 기본키를 지정하지 않아도 테이블이 생성된다
    - 기본키 대신 모둔 튜플을 유일하게 식별할 수 있는 내부 레코드를 자동으로 생성
    - 기본키와 별개로 내부적으로 rowid라는 개념을 사용해서 데이터를 저장하고 식별
        - 단일 행에 접근하는 가장 빠른 방법
        - 테이블 행의 고유 식별자
        - 테이블의 행이 어떻게 저장되는지 확인할 수 있음
    - [Oracle ROWID 공식 문서](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/ROWID-Pseudocolumn.html#GUID-F6E0FBD2-983C-495D-9856-5E113A17FAF1)
    - Clustered Index란? - InnoDB에서는 테이블이 기본적으로 클러스터드 인덱스 중심으로
        - 데이터와 인덱스가 하나로 통합
            - 기본키를 기준으로 데이터가 물리적으로 정렬된 상태로 저장
            - 즉, 인덱스 자체가 데이터
        - 테이블당 하나만 생성 가능
            - 하나의 테이블에는 하나의 클러스터드 인덱스만 존재 가능
            - 기본키를 지정하지 않으면 rowid를 기반으로 클러스터드 인덱스 생성
        - 기본키를 이용한 검색은 클러스터드 인덱스 덕분에 매우 빠르게 이루어짐

## 외래키 값은 NULL이 들어올 수 있나요?

- 외래키는 NULL을 허용할 수 있다 → 유연성을 위해
    - 외래키 제약 조건은 NULL이 아닌 값에만 적용되기 때문에, 외래키 칼럼에 NULL을 삽입해도 무결성 규칙을 위반하지는 않음
    - NULL 값이 특정 레코드와 맵핑되지 않음을 의미함
        1. 선택적 참조 관계
            1. 외래키가 NULL이면 해당 튜플이 특정 레코드를 참조하지 않는다는 의미
            2. 직원 테이블에서 manager_id가 NULL인 경우, 그 직원은 상사가 없는 상태를 나타냄
        2. 데이터의 독립성 보장
            1. 외래키를 반드시 채워야한다면, 종속적인 데이터를 입력할 때마다 부모 데이터를 입력해야한다
            2. NULL을 허용하면 부모 데이터가 없는 경우에도 독립적으로 데이터를 추가할 수 있다

## UNIQUE 키워드가 성능에 미치는 영향

- UNIQUE 제약 조건이 있는 칼럼은 인덱스가 자동 생성됨
    - 검색 속도 향상
    - 중복 데이터 검사 효율 향상
    - 하지만 인덱스 유지 관리 비용이 발생
- UNIQUE 제약 조건이 있는 칼럼에 NULL을 넣을 수 있나요?
    - 넣을 수 있다
    - UNIQUE 제약 조건은 NULL 값을 중복 값으로 간주하지 않는다
