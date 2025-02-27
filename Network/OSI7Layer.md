# OSI 7계층 및 네트워크 통신 개요

## OSI 7계층

OSI(Open Systems Interconnection) 모델은 네트워크 통신 기능을 7개의 계층으로 나눈 것으로, 소프트웨어 및 하드웨어 구성 요소 전반에 걸쳐 모든 유형의 네트워크 통신을 캡슐화합니다.

### 1. Physical Layer (물리 계층)

- **역할:** 물리적 통신 매체와 이를 통해 데이터를 전송하는 기술을 다룸.
- **주요 기능:** 디지털 및 전자 신호 전송.
- **예시 기술:**
  - Bluetooth
  - NFC
  - Data 전송

### 2. Data Link Layer (데이터 링크 계층)

- **역할:** 물리 계층을 통해 네트워크에 연결된 두 시스템 간 데이터 전송을 담당.
- **주요 기능:**
  - 데이터 패킷을 캡슐화하여 데이터 프레임으로 변환.
  - 흐름 제어 및 오류 제어 수행.
- **예시 기술:**
  - Ethernet
  - Wi-Fi
- **하위 계층:**
  - Media Access Control (MAC)
  - Logical Link Control (LLC)

### 3. Network Layer (네트워크 계층)

- **역할:** 데이터 패킷의 라우팅과 전달, 논리적 주소 지정(IP 주소 관리)을 담당.
- **주요 프로토콜:**
  - IPv4
  - IPv6

### 4. Transport Layer (전송 계층)

- **역할:** 데이터 패킷이 손실이나 오류 없이 올바른 순서로 도착하도록 보장.
- **주요 프로토콜:**
  - **TCP (전송제어 프로토콜):** 연결 기반, 손실 없는 데이터 전송 (예: 파일 공유).
  - **UDP (사용자 데이터그램 프로토콜):** 무연결 기반, 손실이 허용되는 전송 (예: 비디오 스트리밍).

### 5. Session Layer (세션 계층)

- **역할:** 네트워크 상의 두 애플리케이션 간 세션 생성, 관리 및 종료.
- **주요 기능:**
  - 애플리케이션 연결 동기화.
  - 충돌 방지 및 세션 관리.
- **예시:**
  - Network File System (NFS)
  - Server Message Block (SMB)

### 6. Presentation Layer (표현 계층)

- **역할:** 데이터의 구문과 표현 방식 처리.
- **예시 포맷:**
  - HTML
  - JSON
  - CSV

### 7. Application Layer (응용 계층)

- **역할:** 사용자와 네트워크 간 상호작용.
- **주요 기능:** 애플리케이션 자체의 표준화된 통신 제공.
- **예시 프로토콜:**
  - HTTP, HTTPS
  - POP3, SMTP (Email Client)

## OSI 모델에서 통신 과정

OSI 모델은 애플리케이션이 네트워크를 통해 다른 장치의 애플리케이션과 통신할 수 있도록 설계되었습니다. 통신은 다음 과정을 따릅니다:

1. **발신자 측:**

   - 애플리케이션 계층에서 시작하여 데이터가 하위 계층으로 전달.
   - 각 계층은 데이터를 전달하기 전에 고유한 헤더와 주소를 추가.

2. **전송:**

   - 데이터가 물리적 매체를 통해 전송.

3. **수신자 측:**

   - 데이터가 각 계층에서 관련 헤더에 따라 처리됨.
   - 계층을 거칠수록 데이터는 점진적으로 압축 해제.

4. **결과:**
   - 데이터가 최종적으로 수신 측 애플리케이션에 도달.

## TCP/IP 모델

OSI 모델의 현대적 대안은 TCP/IP 모델이며, 다음과 같은 계층으로 구성됩니다:

1. Physical Layer (물리 계층)
2. Data Link Layer (데이터 링크 계층)
3. Network Layer (네트워크 계층)
4. Transport Layer (전송 계층)
5. Application Layer (응용 계층)

TCP/IP는 OSI 모델의 복잡성을 줄이고 실용성을 강조한 모델입니다.
