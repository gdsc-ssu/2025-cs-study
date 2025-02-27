## 1️⃣ **프로토콜이란?**

통신 프로토콜 또는 통신 규약은 컴퓨터나 원거리 통신 장비 사이에서 메시지를 주고받는 양식과 규칙의 체계이다. 즉 **통신 규약 및 약속**이다.

---

## 2️⃣ **프로토콜의 기본 요소**

- **구문(Syntax)**  
  전송하고자 하는 **데이터의 형식(Format), 부호화(Coding), 신호 레벨(Signal Level)** 등을 규정.
- **의미(Semantics)**  
  두 기기 간의 효율적이고 정확한 정보 전송을 위한 협조 사항과 오류 관리를 위한 제어 정보를 규정.
- **시간(Timing)**  
  두 기기 간의 통신 속도, 메시지의 순서 제어 등을 규정.

---

## 3️⃣ **프로토콜의 종류**

| **계층**               | **프로토콜**              |
| ---------------------- | ------------------------- |
| 응용(Application)      | HTTP, SMTP, FTP           |
| 표현(Presentation)     | ASCII, MPEG, JPEG, MIDI   |
| 세션(Session)          | NetBIOS, SAP, SDP, NWLink |
| 전송(Transport)        | TCP, UDP                  |
| 네트워크(Network)      | IP, ICMP                  |
| 데이터 링크(Data Link) | Ethernet, PPP             |
| 물리(Physical)         | IEEE 802.3, Bluetooth     |

각 계층의 표준화된 프로토콜 사용으로 다른 시스템이나 네트워크 간의 호환성이 보장될 수 있음.

---

## 4️⃣ **프로토콜의 기능**

- **주소 설정 (Addressing)**  
  통신 상대방을 정확히 식별하기 위해 사용.  
  IP 주소, MAC 주소, 포트 번호 등 다양한 주소 체계 활용.

- **단편화(Fragmentation)와 재합성(Assembly)**

  - 단편화: 송신 측에서 긴 데이터 블록을 크기가 일정한 작은 블록으로 나누어 전송.
  - 재합성: 수신 측에서 쪼개진 작은 데이터 블록을 재합성하여 원래 메시지로 복원.

- **캡슐화 (Encapsulation)**  
  상위 계층의 데이터를 하위 계층에서 처리할 수 있도록 헤더에 송수신 주소, 오류 제어 정보 등을 추가하는 과정.  
  플래그, 주소, 제어 정보 등을 부착하며, OSI 모델의 각 계층에서 이루어짐.

- **흐름 제어 (Flow Control)**  
  송신 측이 수신 측의 처리 속도를 초과하지 않도록 데이터 전송 조절.  
  예: TCP의 윈도우 크기 설정.

- **에러 제어 (Error Control)**  
  데이터 전송 중 발생할 수 있는 오류를 탐지하고 수정.

- **동기화 (Synchronization)**  
  송신자와 수신자가 데이터를 주고받을 준비가 되었는지 확인.  
  예: TCP의 3-way handshake.

- **보안 (Security)**  
  데이터 암호화 및 인증을 통해 전송 중 데이터를 보호.  
  예: HTTPS, TLS/SSL.

---

## 5️⃣ **프로토콜의 중요성**

1. **통신의 표준화**  
   서로 다른 기기 및 시스템 간 효율적인 통신을 가능하게 하며, 디지털 세계의 상호운용성과 호환성을 보장.

2. **데이터의 안정성 및 신뢰성**  
   데이터 전송 중 오류를 감지하고 수정하는 메커니즘을 포함.  
   이를 통해 데이터의 정확성과 신뢰성이 보장됨.

3. **보안**  
   데이터 전송 중 암호화, 인증, 보안 연결 등을 통해 데이터를 보호.

4. **효율적인 데이터 관리**  
   프로토콜은 데이터 전송을 최적화하고 네트워크 혼잡을 관리하는 방법을 제공.  
   네트워크의 효율성과 성능을 향상시킴.

---

참고자료

1. https://usefultoknow.tistory.com/entry/%ED%94%84%EB%A1%9C%ED%86%A0%EC%BD%9CProtocol%EC%9D%B4%EB%9E%80-or-%ED%86%B5%EC%8B%A0%EA%B7%9C%EC%95%BD
2. https://computer-science-student.tistory.com/377
