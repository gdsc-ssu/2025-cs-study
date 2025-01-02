# View

- DB에서 뷰는 하나 또는 그 이상의 테이블을 조인하여 만든 새로운 가상 테이블을 의미한다.
- 실제로 테이블을 생성한 건 아니지만, 사용자에게는 실존 테이블과 동일하게 사용된다.
- 뷰를 가지고 새로운 뷰를 만들 수도 있다.
- 이는 주로 특정 정보만 제공하고 싶은 경우나 복잡한 쿼리를 가상 테이블로 만들어 간편하게 활용할 수 있어 업무에서도 자주 이용하는 기능이다.

예를 들면,

두 개의 테이블이 있다고 가정하자.
`user`, `payment`.
개발자가 회원별 결제 이력을 조회하고 싶은데, 회사 규정상 민감한 개인정보는 제공하고 싶지 않은 경우 뷰를 만들어서 제공하면 된다.
user 테이블에 있는 민감한 개인정보가 담긴 컬럼을 제외하고 payment 테이블에도 카드 번호를 제외한 컬럼만 조인해서 뷰를 만들어 개발자가 해당 뷰를 접근하도록 하면 된다.

# View 사용법
예) <p>
`user` : id, name, email, phone_number <p>
`payment` : id, user_id, payment_date, amount, card_number

<br>

```angular2html
# 뷰 네이밍 규칙 : 접두사 "v"를 붙인다.
CREATE VIEW v_user_payment AS
SELECT
	u.id AS user_id,
	u.name,
	p.payment_date,
	p.amount
FROM
	user u
JOIN
	payment p
ON
	u.id = p.user_id;
```
위와 같이, 민감한 정보(phone_number, card_number 등)를 제외하고,
`user`의 이름과 `payment`의 결제 내역만 포함한 뷰를 생성할 수 있다.

<br>

```angular2html
DROP VIEW v_user_payment
```
뷰를 삭제할 수 있고,

<br>


```angular2html
SELECT name, payment_date, amount
FROM v_user_payment
WHERE user_id = 1234;
```
일반적으로 테이블을 `SELECT`하는 것과 동일하게 뷰를 조회할 수 있다.

<br>

뷰를 읽기 전용으로 만들고 싶은 경우 쿼리 마지막에 `with read only`를 추가하면 된다.
그러면 `DML(INSERT, UPDATE, DELETE)`은 사용할 수 없기에 외부에 제공할 때 사용하기 좋은 옵션이다.
```angular2html
ALTER VIEW v_user_payment READ ONLY;

or

CREATE VIEW v_user_payment AS
/* query */
WITH READ ONLY;
```

# View 장단점
뷰의 장점

- 데이터 조회가 용이하다. (복잡한 쿼리를 단순화)
- 사용자별 필요한 정보만 제공할 수 있다. (보안 이점)
- 물리적인 공간을 차지하지 않는다.

뷰의 단점

- 뷰에 인덱스를 구성할 수 없다.
- 뷰를 포함하여 뷰를 만든 경우 연관 뷰를 삭제하면 생성된 뷰도 삭제된다.
- 한번 정의된 뷰는 수정이 불가하다.