### 프록시(Proxy)란?

- 보안상의 문제를 방지하기 위해 서버와 클라이언트가 직접 통신하지 않고 중개자를 사이에 두고 통신하는데, 이때 중개자가 프록시
    - 클라이언트: 프록시 서버를 서버라고 인식
    - 서버: 프록시 서버를 클라이언트라고 인식
- 프록시 서버 위치에 따라 Forward Proxy와 Reverse Proxy로 구분
- 프록시 서버 없이 웹 서버가 직접 서비스를 제공할 수도 있지만, 서버가 외부에 직접 노출되는 등 보안 위험이 높아지고, 캐싱 없이 매번 서버에 요청을 하는 등 성능 저하도 발생할 수 있음!

### Forward Proxy

- 클라이언트 앞에 위치, 프록시 서버가 **클라이언트 대신 서버에 통신**해주는 구성
- 프록시 서버가 클라이언트의 요청을 받아서 외부 서버와 통신하고 서버 대신 프록시 서버가 응답을 전달해줌
- 즉, 클라이언트는 프록시 서버만을 통해 정보를 얻음

<장점>

- 방문하려는 사이트에 직접 연결하지 않고 프록시에 연결하여 방화벽과 같은 제한을 우회할 수 있음
- 프록시 서버를 경유하므로 외부 웹사이트로의 엑세스를 필터링할 수 있음
- 프록시 서버는 캐시를 저장할 수 있음(캐싱)
- 서버는 클라이언트의 정보가 아닌 포워드 프록시의 정보를 받으므로 서버 측에 클라이언트 정보를 숨길 수 있음
- 저렴한 비용
- +어떤 사용자가 어떤 네트워크에 접근했는지 로그가 남음!

### Reverse Proxy

- 서버 앞에 위치, **서버 대신 클라이언트와의 통신**을 중개
- 서버쪽에 위치하여 서버의 데이터를 클라이언트에게 대신 전달
- 이를 통해 내부망(DB 정보)를 보호
- ex) nginx

<장점>

- 캐싱
	- cf) 리버스/포워드 프록시 모두 캐싱이 가능함. 프록시 자체의 장점!!
- DB와 연결되어 있는 서버와 클라이언트가 직접 통신하지 않고, 리버스 프록시 방식으로 프록시 서버가 외부 요청을 받고 실제 서버를 내부망에 두어 보호
- 부하 분산 : 프록시 서버를 여러 서버 앞에 두면 특정 서버가 과부화되는 것을 막을 수 있음, 로드 밸런서의 역할 수행
- 정적 콘텐츠를 제공하여 서버 부하 감소
    - 정적 콘텐츠와 동적 콘텐츠를 보는 곳을 나눠서 메모리 사용 효율을 높일 수 있음
    - cf) Web server vs Web Application Server
        - Web Server
            - 웹 브라우저 클라이언트로 부터 HTTP 요청을 받아서 HTML 페이지, 이미지, 비디오 및 파일과 같은 정적 컨텐츠를 제공
            - ex) nginx
        - Web Application Server(WAS)
            - 실시간 업데이트, 개인화된 정보 및 고객 지원과 같은 동적 컨텐츠를 제공하기 위해 만들어진 Application Server
            - WAS = Web Server + Web Container
            - ex) Tomcat
        - Web Server만 있다면 사용자의 요청에 따른 동적 컨텐츠를 모두 미리 만들어 둬야 함 ⇒ WAS를 통해 사용자 요청에 따라 데이터를 DB에서 가져와서 비즈니스 로직대로 그때 그때 결과를 만들어서 제공함으로써 자원을 효율적으로 사용!!!
        - WAS가 Web Server 기능도 할 수 있지만 기능을 분리하여 서버 부하 방지, 보안 강화 등 가능
- 실제 서버 IP를 클라이언트로부터 숨길 수 있음

### 로드 밸런싱(Load Balancing)

- 트래픽을 서비스 중단 없이 고르게 분산시켜 서버 부하를 줄여주는 기술
- 부하 분산: 요청을 여러 서버에 분산시킴
- 가용성: 한 서버에 문제 발생 시 다른 서버로 요청을 리디렉션하여 중단 없는 운영이 가능하도록 함
- 확장성: 추가 트래픽 수용이 필요할 때 새 서버를 추가하거나 기존 서버 제거 등 유연하게 시스템 확장 가능
- 보안: 해킹 공격 등으로부터 보호

### 리버스 프록시 vs 로드밸런싱

로드 밸런서는 리버스 프록시의 한 종류로 추가적으로 요청을 분산시키는 역할을 함

비슷한 개념이지만 목적에 있어 약간의 차이가 있음

- 리버스 프록시는 보안, 암호화, 캐싱 등을 목적으로 서버와 클라이언트 사이에서 작동, 주로 **보안 문제**에 초점!
- 로드밸런싱은 서버 사이의 트래픽을 고르게 분산하는 역할, **트래픽 관리**에 초점!