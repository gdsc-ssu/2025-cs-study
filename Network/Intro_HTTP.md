> 참고자료 : HTTP 완벽 가이드 - 웹은 어떻게 동작하는가

# 1장 HTTP 개관

## 1. HTTP

- HTTP : Hypertext Transfer Protocol
- World Wide Web 의 기본 요소 : HTTP 클라이언트 / HTTP 서버
  - HTTP 프로토콜을 통해 통신

## 2. 리소스

- 웹 서버 : 리소스 관리 및 제공

1. 미디어 타입 MIME : Multipurpose Internet Mail Extensions
   - 멀티미디어 콘텐츠를 기술 & 개발 가능
   - 웹 서버 : HTTP obj 에 MIME 타입 붙임 -> 다룰 수 있는 객체인지 확인
   - e.g. `text/html` HTML 문서, `image/jpeg` JPEG 이미지 등
2. URI : Uniform Resource Identifier
   - 각 웹 서버 리소스 이름
   - 클라이언트가 URI 를 통해 특정 리소스에 접근 가능
     `http://www.joes-hw.com/specials/saw-blade.gif`
     - scheme `http://` : HTTP protocol
     - internet addr of server `www.joes-hw.com` : www.joes-hw.com 으로 이동
     - resource in server `/specials/saw-blade.gif` : /specials/saw-blade.gif 라고 불리는 리소스 지칭
3. URL : Uniform Resource Locator
   - 리소스 식별자의 흔한 형태
   - 특정 서버의 한 리소스에 대한 구체적 위치
   - 오늘날, URI == URL

## 3. 트랜잭션

- HTTP 트랜잭션 = HTTP 요청 (c -> s) + HTTP 응답 (s -> c)

1. HTTP 메서드
   - 1 method in 1 HTTP request msg
     - GET : c 의 request == s -> c 로 지정 리소스 보내줘
     - PUT : c -> s 인 데이터를 지정한 이름의 리소스로 저장해조
     - DELETE : 지정한 리소스를 s 에서 삭제해조
     - POST : c data 를 s gateway app 으로 보내조
     - HEAD : 지정한 리소스에 대한 응답에서 HTTP header 만 조
2. 상태 코드
   - 상태 코드 in 모든 HTTP response
   - c 의 요청에 대한 반응
   - 상태 코드 & response phrase
     - 200 OK
     - 200 Document attached
3. 대량의 트랜잭션 수행
   - 웹 페이지 == 리소스의 모음집

## 4. 메시지

![HTTP MSG](ny_http_msg.png)

## 5. TCP Connection

- TCP (Transmission Control Protocol)

1. TCP / IP
   - TCP 와 IP 층의 패킷 교환 네트워크 프로토콜의 집합
   - 어떤 종류의 pc 나 network 든 서로 Reliablility 보장
   - Error-free Transmission
     - Error Detection / Error Correction
   - In-order Delivery
     - Sequence Num / Buffering
   - Unfragmented Data Stream
     - Segmentaion & Reassembly
     - Flow Control
2. 접속, IP 주소, 포트 번호
   - Connection : HTTP message 전송을 위한 c 와 s 사이 연결
     - IP (Internet Protocol) 주소 + Port num 필요
       e.g. `http://207.200.83.29:80/index.html` : IP + Port num
       `http://www.netscape.com:80/index.html` : Domain Name (or Host Name) + Port num
       `http://www.netscape.com/index.html` : Domain Name (or Host Name), default port num 80
   - 웹 브라우저가 HTTP 를 이용해 서버의 HTML 리소스를 가져오는 방법
     a) 웹 브라우저 : 서버의 URL 에서 Host Name 추출  
      b) 웹 브라우저 : 서버의 Host Name -> IP 변환  
      c) 웹 브라우저 : URL 에서 Port num 추출 (있을 시)  
      d) 웹 브라우저 : 웹 서버와 TCP Connection  
      e) 웹 브라우저 : 서버에 HTTP request 전송  
      f) 서버 : 웹 브라우저에 HTTP response 전송  
      g) 웹 브라우저 : Connection 닫히면, HTML 리소스를 사용자에게 보여줌

## 6. Protocol Version

- HTTP protocol 의 여러 버전을 잘 다루기 위해 HTTP 애플리케이션이 열심히 일해야 함!

1. HTTP/0.9
2. HTTP/1.0
3. HTTP/1.0+
4. HTTP/1.1
5. HTTP/2.0

## 7. 웹의 구성요소

1. Proxy : c 와 s 사이의 HTTP 중개자
   - c 의 모든 HTTP request 를 받아 s 로 전달
   - 사용자를 위한, 사용자를 대신한
   - 용도 : 보안, 애플리케이션 통합, 성능 최적화 등
   - e.g. 바이러스 검출, 성인 콘텐츠 차단 등
2. cache : 자추 찾는 웹페이지 사본을 c 근처에 저장하는 HTTP 창고
   - 특별한 종류의 HTTP Proxy s
   - 용도 : 빨리, 효율적 동작 & 프라이버시 보호
3. gateway : 다른 서버들의 중개자
   - 주로 HTTP 트래픽을 다른 protocol 로 변환
   - e.g. HTTP/FTP gateway
4. tunnel : 두 connection 사이에 raw 데이터 그대로 전달
   - 주로 비 HTTP 데이터를 1개 이상 HTTP 연결을 통해 그대로 전송
   - e.g. HTTP/SSL tunnel
5. Agent : 사용자를 위해 HTTP 요청을 만들어주는 c program
   - e.g. 웹 브라우저, 스파이더 ,웹 로봇 등
