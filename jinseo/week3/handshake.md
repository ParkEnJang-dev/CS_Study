# 3-Way Handshake와 4-Way Handshake

## 3-Way Handshake (연결 수립)

TCP 연결을 수립하는 과정입니다.

### 과정

#### 1단계: SYN (Client → Server)
- 클라이언트가 서버에 연결 요청
- SYN 플래그를 1로 설정
- 클라이언트의 초기 시퀀스 번호(ISN) 전송
- 상태: Client (CLOSED → SYN_SENT)

#### 2단계: SYN-ACK (Server → Client)
- 서버가 연결 요청을 수락
- SYN, ACK 플래그를 1로 설정
- 서버의 초기 시퀀스 번호(ISN) 전송
- 클라이언트의 시퀀스 번호 + 1을 ACK 번호로 전송
- 상태: Server (LISTEN → SYN_RECEIVED)

#### 3단계: ACK (Client → Server)
- 클라이언트가 서버의 응답 확인
- ACK 플래그를 1로 설정
- 서버의 시퀀스 번호 + 1을 ACK 번호로 전송
- 이 단계부터 데이터 전송 가능
- 상태: Client (SYN_SENT → ESTABLISHED), Server (SYN_RECEIVED → ESTABLISHED)

### 시각적 표현
```
Client                           Server
  |                                 |
  |  -------- SYN (seq=100) ------> |  [1단계]
  |                                 |
  |  <-- SYN-ACK (seq=200, ack=101) |  [2단계]
  |                                 |
  |  -------- ACK (ack=201) ------> |  [3단계]
  |                                 |
  |  ===== 연결 수립 완료 =====      |
  |  <------ 데이터 전송 ------>     |
```

### 왜 3-way인가?
- 양방향 통신을 위해 양쪽의 초기 시퀀스 번호를 교환해야 함
- 2-way만으로는 서버의 준비 상태를 클라이언트가 확인할 수 없음
- 네트워크 지연으로 인한 중복 연결 방지

### 실제 예시
```
브라우저에서 https://example.com 접속 시:
1. 브라우저 → 서버: "연결하고 싶어요" (SYN)
2. 서버 → 브라우저: "알겠어요, 나도 준비됐어요" (SYN-ACK)
3. 브라우저 → 서버: "확인했어요, 이제 데이터 주세요" (ACK)
```

## 4-Way Handshake (연결 종료)

TCP 연결을 종료하는 과정입니다.

### 과정

#### 1단계: FIN (Client → Server)
- 클라이언트가 연결 종료 요청
- FIN 플래그를 1로 설정
- "더 이상 보낼 데이터가 없습니다"
- 상태: Client (ESTABLISHED → FIN_WAIT_1)

#### 2단계: ACK (Server → Client)
- 서버가 종료 요청 수신 확인
- ACK 플래그를 1로 설정
- "종료 요청 받았습니다, 잠시만 기다려주세요"
- 서버는 아직 보낼 데이터가 있을 수 있음
- 상태: Server (ESTABLISHED → CLOSE_WAIT), Client (FIN_WAIT_1 → FIN_WAIT_2)

#### 3단계: FIN (Server → Client)
- 서버가 남은 데이터 전송 완료 후 종료 준비
- FIN 플래그를 1로 설정
- "이제 저도 종료할게요"
- 상태: Server (CLOSE_WAIT → LAST_ACK)

#### 4단계: ACK (Client → Server)
- 클라이언트가 서버의 종료 확인
- ACK 플래그를 1로 설정
- 상태: Client (FIN_WAIT_2 → TIME_WAIT → CLOSED), Server (LAST_ACK → CLOSED)
- 클라이언트는 TIME_WAIT 상태에서 일정 시간(2MSL) 대기 후 완전 종료

### 시각적 표현
```
Client                           Server
  |                                 |
  |  -------- FIN (seq=300) ------> |  [1단계]
  |                                 |
  |  <------- ACK (ack=301) ------- |  [2단계]
  |                                 |
  |  (서버가 남은 데이터 전송...)      |
  |                                 |
  |  <------ FIN (seq=400) -------- |  [3단계]
  |                                 |
  |  -------- ACK (ack=401) ------> |  [4단계]
  |                                 |
  | [TIME_WAIT 상태 2MSL 대기]       |
  |                                 |
  |  ===== 연결 종료 완료 =====      |
```

### 왜 4-way인가?
- TCP는 전이중(Full-Duplex) 통신
- 양방향 연결을 각각 독립적으로 종료해야 함
- 서버가 아직 전송할 데이터가 남아있을 수 있음
- FIN과 ACK를 분리하여 안전한 종료 보장

### TIME_WAIT이 필요한 이유
1. 마지막 ACK가 손실될 경우를 대비
2. 같은 포트로 새 연결 시 이전 패킷과 혼동 방지
3. 일반적으로 2MSL(Maximum Segment Lifetime) = 약 1~4분

## 면접 관련 포인트

### 자주 나오는 질문들

#### Q: 3-way는 되는데 2-way는 안 되나요?
**A**: 2-way로는 서버가 클라이언트의 ACK를 받았는지 확인할 수 없습니다. 네트워크 지연으로 중복 연결이 발생할 수 있어 3-way가 필요합니다.

#### Q: 4-way에서 2단계와 3단계를 합치면 안 되나요?
**A**: 서버가 즉시 종료할 준비가 안 된 경우가 많습니다. 아직 전송할 데이터가 남아있거나 처리 중인 작업이 있을 수 있습니다. 하지만 서버가 즉시 종료 가능하면 FIN+ACK로 합쳐서 3-way로 종료할 수도 있습니다.

#### Q: TIME_WAIT 상태가 왜 필요한가요?
**A**:
- 마지막 ACK가 유실되면 서버가 FIN을 재전송하는데, 클라이언트가 이미 종료되면 응답할 수 없습니다
- 이전 연결의 지연된 패킷이 새 연결에 영향을 주는 것을 방지합니다

#### Q: Half-Close란?
**A**: 한쪽만 FIN을 보내 송신은 종료하지만 수신은 계속 가능한 상태입니다. 클라이언트가 요청을 다 보내고 서버의 응답만 기다리는 경우 사용됩니다.

## 실무 관련

### Spring Boot에서의 영향
```java
// Connection Pool 설정
spring.datasource.hikari.maximum-pool-size=10
spring.datasource.hikari.connection-timeout=20000

// 각 연결마다 3-way handshake 발생
// Keep-Alive로 재사용하면 성능 향상
```

### 문제 상황과 해결

#### SYN Flooding 공격
- 공격자가 SYN만 보내고 ACK를 안 보내 서버 리소스 고갈
- 해결: SYN Cookie, 방화벽 설정

#### TIME_WAIT 소켓 고갈
- 대량 요청 시 TIME_WAIT 상태 소켓이 쌓임
- 해결: SO_REUSEADDR, 적절한 타임아웃 설정