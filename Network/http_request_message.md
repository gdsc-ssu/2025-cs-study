# 1. HTTP Request Mesasge 작성

## 1.1. 브라우저에 URL 입력

### 각종 URL 형식
- `http://user:password@www.cyber.co.kr:80/dir/file1.html`
  - HTTP 프로토콜로 웹 서버에 액세스하는 경우
  - `http:`: 프로토콜 (데이터 출처에 액세스하는 방법, 통신 동작의 규칙)
  - `//`: 나중에 이어지는 문자열이 서버의 이름임을 나타냄
  - `user`: 사용자명(생략 가능)
  - `password`: 패스워드(생략 가능)
  - `www.cyber.co.kr`: 웹 서버의 도메인명
  - `80`: 포트 번호(생략 가능)
  - `/dir/file1.html`: 파일의 경로명
- `ftp://user:password@ftp.cyber.co.kr:21/dir/file1.html`
  - FTP 프로토콜로 파일을 다운로드하거나 업로드하는 경우
  - `ftp:`: 프로토콜 (데이터 출처에 액세스하는 방법, 통신 동작의 규칙)
  - `//`: 나중에 이어지는 문자열이 서버의 이름임을 나타냄
  - `user`: 사용자명(생략 가능)
  - `password`: 패스워드(생략 가능)
  - `ftp.cyber.co.kr`: FTP 서버의 도메인명
  - `21`: 포트 번호(생략 가능)
  - `/dir/file1.html`: 파일의 경로명
- `file://localhost/c:/path/file1.zip`
  - 클라이언트 PC 자체의 파일에서 데이터를 읽어오는 경우
  - `file:`: 프로토콜 (데이터 출처에 액세스하는 방법, 통신 동작의 규칙)
  - `//`: 나중에 이어지는 문자열이 서버의 이름임을 나타냄
  - `localhost`: 컴퓨터명(생략 가능)
  - `c:/path/file1.zip`: 파일의 경로명
- `mailto:tone@cyber.co.kr`
  - 메일을 송신하는 경우
  - `mailto:`: 프로토콜 (데이터 출처에 액세스하는 방법, 통신 동작의 규칙)
  - `tone@cyber.co.kr`: 메일 주소
- `news:comp.protocols.tcp-ip`
  - 뉴스그룹의 기사를 읽는 경우
  - `news:`: 프로토콜 (데이터 출처에 액세스하는 방법, 통신 동작의 규칙)
  - `comp.protocols.tcp-ip`: 뉴스그룹명

## 1.2. 브라우저가 URL 해독

웹 서버에 액세스하는 경우를 예로 설명한다.

- `http://www.lab.cyber.co.kr/dir1/file1.html`
  - 프로토콜: `http`
  - 웹서버명: `www.lab.cyber.co.kr`
  - 데이터 출처(파일)의 경로명: `dir1/file1.html`
  - 결론: `www.lab.cyber.co.kr`이라는 웹 서버에 있는 `/dir1/file1.html`이라는 경로의 파일, 즉 `/dir1/`이라는 디렉토리의 아래에 있는 `file1.html` 파일에 액세스한다는 의미

## 1.3. 파일명을 생략한 경우

- `http://www.lab.cyber.co.kr/dir/`
  - `/dir/`의 다음에 써야 할 파일명을 쓰지 않고 생략한 경우 어느 파일에 액세스해야 할지 모름
  - 따라서 파일명을 생략할 때를 대비하여 파일명을 미리 서버측에 설정해 둠
  - 이 설정은 서버에 따라 다르지만 보통 `index.html` 또는 `default.html`이라는 파일명을 설정해 둠
  - 따라서 파일명을 생략한 경우 `dir/index.html` 또는 `dir/default.html`이라는 파일에 액세스

- `http://www.lab.cyber.co.kr/`
  - 이와 같이 웹 서버의 도메인명만 쓴 URL의 경우도 파일명을 생략한 경우임
  - 끝에 `/`가 있으므로 `/`라는 디렉토리(루트 디렉토리)가 지정되고 파일명은 생략된 것
  - 따라서 `/index.html` 또는 `/default.html`이라는 파일에 액세스

- `http://www.lab.cyber.co.kr`
  - 이렇게 끝의 `/`까지 생략된 경우도 인정해줌
  - 이 경우에도 `/index.html` 또는 `/default.html`이라는 파일에 액세스

- `http://www.lab.cyber.co.kr/whatisthis`
  - 이 경우 `whatisthis`를 파일명으로 보는 것이 맞을까? 아니면 디렉토리명이고 파일명은 생략된 형태로 보는 것이 맞을까?
  - 이 경우 웹 서버에 `whatisthis`라는 파일이 있으면 파일명으로 보고, `whatisthis`라는 디렉토리가 있으면 디렉토리명으로 보는 것이 통례
    - 같은 이름을 가진 파일과 디렉토리를 둘 다 만들 수 없으므로 `whatisthis`라는 파일과 `whatisthis`라는 디렉토리가 둘 다 존재하는 경우는 없음

## 1.4. HTTP의 기본 개념

## 1.5. HTTP 리퀘스트 메시지 작성

## 1.6. 응답 수신
