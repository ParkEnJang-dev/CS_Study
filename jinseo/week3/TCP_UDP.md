# TCP/IP 정리

## TCP/IP란?

TCP/IP는 인터넷의 기반이 되는 통신 프로토콜 집합입니다. TCP(Transmission Control Protocol)와 IP(Internet Protocol)를 중심으로 여러 프로토콜들이 계층적으로 구성되어 있습니다.

## TCP/IP 4계층 모델

### 1. 응용 계층 (Application Layer)
- HTTP, HTTPS, FTP, SMTP, DNS 등의 프로토콜
- 사용자에게 네트워크 서비스를 제공
- 백엔드 개발자로서 가장 많이 다루는 계층 (REST API, WebSocket 등)

### 2. 전송 계층 (Transport Layer)
- TCP와 UDP가 대표적
- 포트 번호를 통해 애플리케이션 식별
- 데이터의 신뢰성과 흐름 제어 담당

### 3. 인터넷 계층 (Internet Layer)
- IP, ICMP, ARP 등
- 패킷의 경로 설정(라우팅)
- IP 주소를 통한 호스트 식별

### 4. 네트워크 인터페이스 계층 (Network Interface Layer)
- 물리적 네트워크 하드웨어와의 인터페이스
- 이더넷, Wi-Fi 등

## TCP의 주요 특징

### 신뢰성 보장
- 3-way handshake로 연결 수립
- 4-way handshake로 연결 종료
- 데이터 전송 순서 보장
- 오류 검출 및 재전송

### 흐름 제어
- 수신자의 버퍼 오버플로우 방지
- Sliding Window 기법 사용

### 혼잡 제어
- 네트워크 혼잡도에 따라 전송 속도 조절
- Slow Start, Congestion Avoidance 알고리즘

## TCP vs UDP

**TCP**: 연결 지향, 신뢰성, 순서 보장 → HTTP, 이메일, 파일 전송  
**UDP**: 비연결성, 빠른 속도, 순서 보장 안 함 → 동영상 스트리밍, DNS, 게임

## 백엔드 개발 관련 포인트

**HTTP Keep-Alive**: TCP 연결을 재사용하여 성능 향상

**타임아웃 설정**: Connection timeout, Socket timeout 이해 필요

**포트 번호**:
- Well-known ports (0-1023): HTTP(80), HTTPS(443)
- Registered ports (1024-49151): 애플리케이션 서버
- Dynamic ports (49152-65535): 임시 포트

**3-way Handshake 과정**:
1. Client → Server: SYN
2. Server → Client: SYN-ACK
3. Client → Server: ACK

---

# TCP와 UDP 상세 비교

## TCP (Transmission Control Protocol)

### 연결 지향 프로토콜
- 3-way handshake로 연결 수립 후 데이터 전송
- 연결 상태를 유지하며 통신
- 양방향 통신 (Full-Duplex)

### 신뢰성 보장
- 데이터 전송 순서 보장 (시퀀스 번호 사용)
- 데이터 손실 시 재전송
- 오류 검출 및 수정
- ACK(응답) 메커니즘으로 수신 확인

### 흐름 제어 (Flow Control)
- 수신자의 처리 능력에 맞춰 전송 속도 조절
- Sliding Window 기법 사용
- 수신 버퍼 오버플로우 방지

### 혼잡 제어 (Congestion Control)
- 네트워크 혼잡도에 따라 전송량 조절
- Slow Start, Congestion Avoidance, Fast Retransmit, Fast Recovery

### 헤더 크기
20~60 bytes (기본 20 bytes)

### 사용 예시
- HTTP/HTTPS (웹 통신)
- 이메일 (SMTP, POP3, IMAP)
- 파일 전송 (FTP)
- SSH, Telnet
- 데이터베이스 연결

## UDP (User Datagram Protocol)

### 비연결 지향 프로토콜
- 연결 수립 과정 없음
- 데이터그램 단위로 독립적 전송
- 상태를 유지하지 않음 (Stateless)

### 신뢰성 보장 안 함
- 데이터 순서 보장 안 함
- 손실된 데이터 재전송 안 함
- 오류 검출만 하고 수정은 안 함
- ACK 없음

### 빠른 전송 속도
- 연결 수립/해제 오버헤드 없음
- 흐름 제어, 혼잡 제어 없음
- 단순한 구조로 처리 속도 빠름

### 헤더 크기
8 bytes (고정)

### 사용 예시
- DNS (도메인 조회)
- 실시간 스트리밍 (동영상, 음성)
- 온라인 게임
- VoIP (인터넷 전화)
- DHCP (IP 주소 할당)
- SNMP (네트워크 관리)

## TCP vs UDP 비교표

| 특징 | TCP | UDP |
|------|-----|-----|
| 연결 방식 | 연결 지향 | 비연결 지향 |
| 신뢰성 | 보장 | 보장 안 함 |
| 순서 보장 | O | X |
| 속도 | 느림 | 빠름 |
| 헤더 크기 | 20~60 bytes | 8 bytes |
| 재전송 | O | X |
| 흐름/혼잡 제어 | O | X |
| 방식 | 1:1 통신 | 1:1, 1:N, N:N 가능 |

## 백엔드 개발 관점에서의 선택 기준

### TCP를 사용해야 할 때
- 데이터 정확성이 중요한 경우
- 파일 전송, 금융 거래, 로그인 등
- Spring Boot의 REST API는 기본적으로 TCP 기반 HTTP 사용

### UDP를 사용해야 할 때
- 실시간성이 중요하고 약간의 데이터 손실은 허용 가능
- 라이브 방송, 화상 회의
- 대량의 클라이언트에게 빠르게 전송해야 할 때

## 실무 예시

### Spring Boot에서 TCP
```java
// HTTP 통신은 기본적으로 TCP
@GetMapping("/api/users")
public List getUsers() {
    return userService.findAll();
}
```

### UDP 사용 케이스
- WebRTC를 이용한 실시간 화상 통화
- 게임 서버에서 플레이어 위치 업데이트
- 모니터링 시스템의 메트릭 수집

## 면접 단골 질문

1. **TCP와 UDP의 차이점은?**
2. **왜 HTTP는 TCP를 사용하나요?** → 웹 페이지 데이터는 정확히 전달되어야 하므로
3. **실시간 스트리밍이 UDP를 사용하는 이유는?** → 약간의 손실보다 지연 없는 전송이 중요
4. **TCP의 3-way handshake 과정 설명**
5. **HTTP/3는 UDP 기반인데 왜 그런가요?** → QUIC 프로토콜이 UDP 위에서 신뢰성 구현