> 참고자료 : HTTP 완벽 가이드 - 웹은 어떻게 동작하는가

# 2장 URL 과 리소스

## 1. URL 기본

- 리소스(r)의 위치

1. URL 과 URI
   - HTTP 명세 : URI 가 더 일반적인 개념
   - HTTP app : URL ⊂ URI
2. URL 의 구조
   - `스킴://서버위치/경로`
   - 스킴 : 웹 c 가 r 에 **어떻게** 접근하는지 ==
   - 서버위치 : r 가 **어디에** 호스팅 되어 있는지
   - 경로 : s 의 local r 중 요청받은 r 이 **무엇**인지

## 2. URL 문법

- depends on scheme but similar
- `<scheme>://<user name>:<password>@<host>:<port>/<path>;<parameter>?<query>#<fragment>`

1. scheme 스킴
   - r 에 어떻게 접근? == URL 을 해석하는 app 이 **어떤 protocol** 사용?
     - e.g. : `http`, `ftp`, etc.
   - 알파벳으로 시작, 대소구분 X
     - `http://www.nav...` === `HTTP://www.nav..`
   - URL 의 나머지 부분과 `:` 로 구분
2. user name 사용자 이름 & password 비밀번호
   - shceme 에 따라, **사용자 인증**이 필요한 경우
   - 사용자 인증이 필요한 scheme 에서 user name 과 pw 를 생략 => default 값 이용
     - FTP 서버 (e.g. 회사 FTP 서버의 자료 업로드 시)
       - default user name : `anonymous`
       - defalut pw : 브라우저마다 다름 (크롬 : `chrome@example.com`)
       - `ftp://anonymous:my_pw@ftp.prep.ai.mid.edu/pub/gnu`
3. host 호스트 & port 포트
   - host : 접근하려는 r 을 가진 **인터넷 상 host**
     - `host name` or `IP addr`
     - e.g. `www.joes-hw.com:80`, `161.58.228.45:80`
   - port : s 가 열어놓은 **network port**
     - TCP 기반 HTTP default port : `80`
4. path 경로
   - r 가 **s 의 어디에** 있는지
   - 계층적 파일 시스템 경로와 유사
   - 경로 조각 : `/`를 기준으로 나뉨
     - 자체만의 `parameter` 가짐
5. parameter 파라미터
   - to s 에 **정확한** 요청
     - `http://www.j-hw.com/hammers;sale=false/index.html;graphics=true`
   - `name=value` 식으로 표현
   - `;` : URL 의 나머지 부분과 구분
6. query 질의 문자열
   - to 요청 받을 **r 형식의 범위 축소**
     - `http://www.j-hw.com/inventory-check.cgi?item=12731&color=blue`
     - e.g. DB
   - `?` 뒤
   - `&` : 다른 쌍(`이름=값`)과 구분
   - gateway 를 가리키는 URL 의 경로와 함께 전달
     - e.g. `inventory-check.cgi`
7. fragment 프래그먼트
   - for r 의 **특정 부분**, r 내의 **조각**
     - `http://www.j-hs.com/tools.html#drills`
   - `#` 뒤
   - generally, HTTP s 는 객체 **전체** 다룸
     - 브라우저는 URL 의 fragment 를 s 에게 전달 X
     - 즉, agent(e.g. 브라우저) : 전체 r 을 내려 받은 후 (from s), fragment 사용하여 **일부**만 u 에게 view (scroll down etc.)
       > Q. fragment 를 사용하기 위해 HTML에 추가해야 하는 게 있나? <br> fragment 를 제대로 사용하기 위해, 해당 요소 id 를 지정해야 한다.<br>e.g. `<h2 id="drills">Drills</h2>`

## 3. 단축 URL

1. 상대 URL : URL 을 **짧게** 표시하는 방식
   - e.g. `<A HREF="./hammers.html">hammers</A>.`
     - fragment or part of URL
   - **문서의 URL** (e.g. j-hw 웹 서버의 /tools.html) 을 기준으로 해석 O
   - HTML 작성자가 URL 에 scheme, host, etc. 입력하지 않아도 됨
   - **base 기저** : 상대 URL 로 r 에 접근하기 위해 필요한 URL
     - 상대 URL to 절대 URL 변환 시, 필요
   - app (e.g. 브라우저) : 상대 URL ↔ 절대 URL
   - 상대 URL to 절대 URL
     ![RFC 1808](ny_converting_relative_to_abs_URL.png)
   - vs 절대 URL : r 에 접근하기 위한 모든 info
     - `http://www.j-hw.com/tools.html/hammers.html`
2. URL 확장 : 브라우저에 URL 입력하는 동안에 자동으로 URL 확장
   - 호스트 명 확장 : 단순 규칙 사용
     - e.g. 주소 입력란 : `yahoo` → 브라우저가 `www.`, `.com` 붙여서 `www.yahoo.com` 으로 만들고 해당 페이지 view
       > comment : 시도해보았으나, 크롬에서는 주소 입력란에 yahoo, google 등을 검색했을 때, 호스트 명 확장을 진행하지 않고 구글 검색에서 yahoo, google 등을 검색. <br> ∵ 크롬에서는 기본 검색 엔진으로 설정. <br> ∴ `yahoo.com` or `google.com` 등 유효한 도메인 명을 미완성인 채로 치면 호스트명 확장이 발생.
   - 히스토리 확장 : 과거에 사용자가 방문했던 URL 의 기록을 저장
     - e.g. 주소 입력란 : `notion` → 브라우저가 최신에 방문했던 `notion` 주소들을 보여줌

## 4. 안전하지 않은 문자

- URL : 각 r 의 유일한 이름 부여
- 안전한 전송 : 어떤 인터넷 protocol 을 통해서든, 정보가 유실될 위험 X URL 전송

1. URL 문자 집합
   - alphabet + 특정한 US-ASCII + escape sequences
   - escape sequences : 안전하지 않은 문자 (US-ASCII 에서 사용 금지된 문자들로, 특정 문자나 데이터)를 안전한 문자로인코딩 O
2. 인코딩 체계
   - escape 문자 : `%` 로 시작해 ASCII 코드로 표현되는 2개의 16진수 숫자
     - e.g. ~ → `%7E` / % → `%25`
3. 문자 제한
   - 예약어 존재
     - e.g. `%`, `/`, `,`, `#`, `?` 등 URL 에 쓰이거나
     - `@`, `&`, `=` 등 특정 scheme 에서 선점
4. 입력 받은 URL 의 인코딩
   - 최초로 URL 을 입력 받는 app (e.g. 브라우저) 에서 결정

## 5. scheme 에 따른 URL 구조 (대표 3개)

1. http
   - `http://<host>:<port>/<path>?<query>#<fragment>`
   - default port : 80
   - `http://www.j-hw.com:80/index.html`
2. https
   - `https://<host>:<port>/<path>?<query>#<fragment>`
   - default port : 443
   - `https://www.j-hw.com/secure.html`
3. ftp
   - `ftp://<user name>:<password>@<host>:<port>/<path>;<parameter>`
   - `ftp://anonymous:joe%40joes-hw.com@prep.ai.mit.edu:21/pub/gnu`

## 6. URN

- URL : r 의 **주소**, 특정 시점에 r 가 위치한 곳

1. URN : Uniform Resource Names
   - 객체의 위치가 이동해도 **항상 객체를 가리키는 이름** 제공
   - PURL (Persistent Uniform Resource Locators) : URL → URN 기능 제공
     - r 의 실제 URL 목록 관리
     - 추적하는 r 위치 중개 서비스
     - r 을 우회적으로 제공 → 영구적인 URL 사용 가능
2. 애로 사항
   - 표준 제정
   - 여러 HTTP app 수정 필요

---

### 후기

- URL, 간단하지만 세세한 아이!
- 브라우저가 하는 게 정말 많다는 생각이 들었다. 다음 장들도 얼른 공부해서 브라우저와도 친해져야 겠다.
