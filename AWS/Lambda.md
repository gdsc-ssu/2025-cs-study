## Labmbda란?

AWS Lambda는 서버를 프로비저닝 또는 관리하지 않고도 실제로 모든 유형의 애플리케이션 또는 백엔드 서비스에 대한 코드를 실행할 수 있는 이벤트 중심의 서버리스 컴퓨팅 서비스이다.

- Function 단위로 어플리케이션을 올림
- 이벤트가 발생되면 실행
- 서버리스의 대표적인 서비스

- 로드 밸런싱 지원
- 오토 스케일링 지원
- 에러시 자동 에러 핸들링

![](https://i.imgur.com/xHB2WzT.png)

## 서버리스란??
> 서버가 없다??? 

서버가 없다는 뜻이 아닌 서버의 존재를 신경 쓸 필요가 없다는 뜻이다.  
사용자는 코드에만 집중하면 된다

#### 장점
- 개발자가 서버를 직접 관리할 필요가 없는 아키텍쳐
- 사용량에 따라 자원 할당. Auto Scaling
- 사용한 만큼 지불
- 서버에 대해 일정 관리할 필요가 없어 비즈니스 로직에 집중할 수 있음

코드를 Function 단위로 쪼개 대기 상태에 둠
-> 요청이 들어오면 서버가 대기상태의 Function을 실행시켜 처리
-> 끝나면 다시 대기 상태로 만듬
### 언제 쓰는게 좋은가?

코드를 계속 실행시키는 것보단 특정 조건때 실행하는 코드를 사용할때 유용한다.
- 서버 없이 간단한 코드 실행
- 특정 기간, 특정 주기로 코드 실행
- 트리거 실행될 때 코드 실행
이럴때 사용 하면 편하다.

단점도 존재한다
- 코드 용량이 250MB
- 함수 실행 시간 최대 15분
- 처음 함수가 cold start를 하게 되여 초기 지연시간이 발생
- 비쌈

## Lambda 함수 구성
#### Handler() 함수
람다 호출 시 실행되는 함수
하나의 람다당 하나의 `Handler()`
#### event 객체
람다 호출 시 전달되는 데이터
#### context 객체
람다에 대한 런타임 관련 정보가 담겨있음

```python
import json
def lambda_handler(event, context):
	print(event)
	print(context.get_remaining_time_in_millis())
	print(context.memory_limit_in_mb)
	
	return {
		'statusCode': 200,
		'body': json.dumps(event['Records']['s3']['bucket']['name'])
	}
```

## Lambda 함수 실행

#### Cold Start
- 함수를 실행 시킬 실행환경이 없으면, 새로운 실행환경을 만듦
#### Warm Start
- 함수를 실행 시킬 실행환경이 있으면, 해당 실행 환경 에서 함수를 실행 시킴

### 실행 환경

![](https://d2908q01vomqb2.cloudfront.net/fc074d501302eb2b93e2554793fcaf50b3bf7291/2019/06/27/Screen-Shot-2019-06-27-at-2.23.51-PM-1024x510.png)

#### 동기 호출
- API Gateway
#### 비동기 호출
- Amawon SNS, S3
	- SNS에 메세지가 도달했을때
	- S3 객체 생성시 
	- 비동기로 전달
#### Stream 기반 호출
- DynamoDB Stream, Kinesis를 플링하여 전달

## 작동원리

![](https://i.imgur.com/JHX8Z3q.png)

### 동기
- Worker
	- 실제 요청된 함수 코드를 실행한다.
- Count service
	- Concurrency 제한 등 요청 관련 트래킹 및 관리를 한다.
- Placement service
	- Worker을 생성,, 수명 주기를 관리한다.
- Worker Manager
	- Worker들의 상태를 확인하고 Request를 할당 혹은 Placement service에 worker 생성을 요청한다.
- Frontend Worker
	- User들이 보낸 Requestfmf 받고 검증 후 Worker Manager에게 전달한다.
- Load Balancer
	- Application Load Balancer로 user들이 Invoke 하는 request들을 Frontend로 Route하는 기능을 한다.
로 작동이 된다

간략하게 흐름은
요청 -> 로드밸런서 -> 프론트엔드 -> 작업 관리자 -> 워커 -> 실행
의 순서이다.

#### 비동기
비동기 호출의 경우에는
- Event Invoke Frontend Service
	- 비동기 요청을 받고 내부 SQS 대기열에 배치한다.
- Polling Instances
	- Poller 들이 SQS에서 메세지를 읽고 기존 Frontend Service로 전송한다.
- Event Destinations
	- 동기 방식대로 처리 후 결과에 따른 알림을 제공한다.

결국 앞단에서 SQS를 이용해 대기열을 이용하는 것 외에는 기존 방식과 다르지 않다.

### 구조

![](https://i.imgur.com/I0gI018.png)

함수별로 자체 컨테이너에서 실행된다고 생각해도 괜찮을것 같다.
그리고 사실상 모두 하나의 EC2위에서 관리되고 있다.

좀 더 내부로 들어가보자면

![](https://i.imgur.com/kLm9dKw.png)

위 그림과 같은 구조로 실행되게 된다.
같은 함수를 동시에 여러번 호출하게 되면 동일한 샌드박스 위에서 실행된다.

![](https://i.imgur.com/TvRhKaO.png)

그러다 FireCracker 라는 가상화 기술이 나오면서 기존의 VM보다 빠르고 안전한 microVM을 사용하게 되었다.
따라서 성능을 유지하면서 실행 시간 감소, 메모리 사용량 감소, 확장성 강화했다.

참고로 FireCracker는 오픈소스이고 Rust로 작성되어있다.
- https://github.com/firecracker-microvm/firecracker


좀 더 자세한 설명 보고 싶으신 분은
- https://www.youtube.com/watch?v=QdzV04T_kec