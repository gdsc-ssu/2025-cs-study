# URI vs URL

## URI (Uniform Resource Identifier)

- **정의:** 인터넷상의 리소스 "자원 자체"를 식별하는 고유한 문자열 시퀀스.
- **특징:**
  - 조금 더 포괄적인 개념.
  - 리소스를 식별하기 위한 고유 문자열.
- **예시:** `soongsil.ac.kr`

## URL (Uniform Resource Locator)

- **정의:** 네트워크 상에서 리소스의 위치를 나타내는 규약.
- **특징:**
  - 자원 식별자와 위치를 동시에 제공.
  - 리소스에 어떻게 도달할 수 있는지를 포함.
- **예시:** `https://soongsil.ac.kr`

---

# REST (Representational State Transfer)

## REST 구성 요소

1. **Resource:** 리소스를 URL로 표현.
2. **Verb:** HTTP Method (GET, POST, PUT, DELETE).
3. **Representations:** 리소스의 표현 방식.

## REST 특징

1. **Uniform Interface:**
   - URL로 지정한 리소스를 조작하는 통일된 인터페이스 제공.
2. **Stateless:**
   - 무상태성: 요청마다 상태 정보를 저장하지 않고 독립적으로 처리.
3. **Cacheable:**
   - 응답에 캐싱 기능을 적용 가능.
4. **Self-Descriptiveness:**
   - REST API 메시지만 보고도 쉽게 이해할 수 있음.
5. **Server-Client 구조:**
   - 서버와 클라이언트가 명확히 분리되어 개발과 유지보수가 독립적으로 이루어짐.
6. **계층형 구조:**
   - 보안, 로드 밸런싱, 암호화 계층 등을 추가하여 구조상의 유연성을 제공.
   - Proxy, Gateway 같은 네트워크 기반 중간 매체를 사용할 수 있음.

## REST 디자인 가이드

1. **URI는 리소스를 표현해야 함.**
   - 리소스명은 **명사**로 작성 (동사 사용 지양).
2. **행위는 HTTP Method로 표현.**
   - **CRUD 매핑:**
     - **GET:** Read
     - **POST:** Create
     - **PUT:** Update
     - **DELETE:** Delete
