# Socket Programming 정리

## Socket Programming이란?

### 개념
- 네트워크 통신을 위한 프로그래밍 기법
- 소켓(Socket)은 네트워크 통신의 엔드포인트(종착점)
- 프로세스 간 통신(IPC)을 네트워크로 확장한 개념
- TCP/IP 프로토콜을 사용하여 데이터 송수신

### 소켓의 비유
```
전화 통신에 비유:
- Socket = 전화기
- IP 주소 = 전화번호
- Port = 내선번호
- Connection = 통화 연결
```

## Socket의 종류

### 1. Stream Socket (TCP)
- **연결 지향적** (Connection-Oriented)
- **신뢰성 보장**: 데이터 순서 보장, 손실 시 재전송
- **양방향 통신** (Full-Duplex)
- **1:1 통신**
- **사용 예**: 웹 브라우저, 이메일, 파일 전송

### 2. Datagram Socket (UDP)
- **비연결 지향적** (Connectionless)
- **신뢰성 보장 안 함**: 순서 보장 X, 손실 가능
- **빠른 전송**
- **1:N, N:N 통신 가능**
- **사용 예**: 스트리밍, 온라인 게임, DNS

### 3. Raw Socket
- IP 레벨에서 직접 통신
- 커스텀 프로토콜 구현 가능
- 관리자 권한 필요
- **사용 예**: Ping, 네트워크 분석 도구

## TCP Socket의 동작 원리

### 서버 측 흐름
```
1. socket()      : 소켓 생성
2. bind()        : IP 주소와 포트 바인딩
3. listen()      : 연결 요청 대기 상태
4. accept()      : 클라이언트 연결 수락 (블로킹)
5. send/recv()   : 데이터 송수신
6. close()       : 연결 종료
```

### 클라이언트 측 흐름
```
1. socket()      : 소켓 생성
2. connect()     : 서버에 연결 요청
3. send/recv()   : 데이터 송수신
4. close()       : 연결 종료
```

### 시각적 표현
```
Server                           Client
  |                                 |
socket()                         socket()
  |                                 |
bind()                              |
  |                                 |
listen()                            |
  |                                 |
accept() [대기중]                    |
  |                                 |
  | <---------- connect() ----------|
  |                                 |
  | [3-way handshake]               |
  |                                 |
accept() 반환                    connect() 반환
  |                                 |
  | <======= send/recv ==========>  |
  |                                 |
close()                          close()
  |                                 |
  | [4-way handshake]               |
```

## Socket 관련 주요 개념

### Port (포트)

#### 개념
- 0 ~ 65535 범위의 논리적 연결 통로
- 하나의 IP에서 여러 서비스 구분

#### 포트 범위
- **Well-Known Ports (0-1023)**: HTTP(80), HTTPS(443), SSH(22), FTP(21)
- **Registered Ports (1024-49151)**: 애플리케이션 서버
- **Dynamic/Private Ports (49152-65535)**: 임시 포트

### Blocking vs Non-Blocking

#### Blocking I/O
- accept(), recv() 등이 데이터를 받을 때까지 대기
- 간단하지만 다중 클라이언트 처리 시 비효율적
- Thread/Process를 사용하여 해결

#### Non-Blocking I/O
- 즉시 반환, 데이터가 없으면 에러 반환
- Selector/Multiplexing 사용
- Java NIO (New I/O) 사용

### Socket 연결 과정과 TCP 관계

#### 연결 수립 시
- `connect()` 호출 → TCP 3-way handshake 자동 수행
- SYN → SYN-ACK → ACK 과정이 내부적으로 진행
- `connect()` 함수가 반환되면 연결 완료 (ESTABLISHED 상태)

#### 연결 종료 시
- `close()` 호출 → TCP 4-way handshake 자동 수행
- FIN → ACK → FIN → ACK 과정이 내부적으로 진행
- TIME_WAIT 상태도 자동으로 처리

#### 데이터 전송 시
- `send()`/`recv()` → TCP의 흐름 제어, 혼잡 제어 자동 적용
- ACK, 재전송 등이 모두 운영체제 레벨에서 자동 처리

### Timeout 설정
- **연결 타임아웃**: 서버 연결 시도 시간 제한
- **읽기 타임아웃**: 데이터 수신 대기 시간 제한
- 무한 대기 방지를 위해 필수적

## 멀티 클라이언트 처리 방법

### 1. Thread per Connection
- 클라이언트마다 새 스레드 생성
- 간단하지만 많은 클라이언트 시 리소스 소모 큼
- C10K 문제 (동시 접속 10,000개 처리 어려움)

### 2. Thread Pool
- 미리 생성된 스레드 풀 사용
- 스레드 생성/소멸 오버헤드 감소
- 동시 처리 클라이언트 수 제한 가능

### 3. Non-Blocking I/O (NIO)
- 단일 스레드로 여러 클라이언트 처리
- Selector를 사용한 이벤트 기반 처리
- 높은 동시성 처리 가능

### 4. 비동기 I/O (AIO)
- 완전한 비동기 처리
- Callback 기반
- 가장 높은 성능

## 실무 고려사항

### 1. 리소스 관리
- **반드시 소켓을 close()**: 메모리 누수 방지
- **Try-with-resources 사용**: 자동 close 보장
- **Connection Pool**: 재사용으로 성능 향상

### 2. 에러 처리
- **ConnectException**: 연결 거부 (서버 미동작)
- **SocketTimeoutException**: 타임아웃
- **UnknownHostException**: 호스트를 찾을 수 없음
- **IOException**: 기타 I/O 에러

### 3. 버퍼 크기 설정
- **송신 버퍼**: 한 번에 보낼 수 있는 데이터 크기
- **수신 버퍼**: 한 번에 받을 수 있는 데이터 크기
- 적절한 크기 설정으로 성능 최적화

### 4. Keep-Alive 설정
- **TCP Keep-Alive**: 연결 유지 확인
- **TcpNoDelay**: Nagle 알고리즘 비활성화로 지연 감소
- 실시간 통신에서 중요

### 5. Half-Close
- 한쪽 방향만 종료하는 기법
- 송신은 종료하지만 수신은 계속 가능
- `shutdownOutput()`, `shutdownInput()` 사용

## Java의 Socket 관련 클래스

### TCP 관련
- **Socket**: 클라이언트 소켓
- **ServerSocket**: 서버 소켓
- **InputStream/OutputStream**: 바이트 스트림
- **BufferedReader/PrintWriter**: 문자 스트림

### UDP 관련
- **DatagramSocket**: UDP 소켓 (서버/클라이언트 공통)
- **DatagramPacket**: UDP 데이터 패킷

### NIO (New I/O)
- **SocketChannel**: Non-blocking TCP 소켓
- **ServerSocketChannel**: Non-blocking 서버 소켓
- **DatagramChannel**: Non-blocking UDP 소켓
- **Selector**: 여러 채널 동시 모니터링

## Socket vs HTTP

### Socket의 장점
- 지속적인 양방향 통신
- 낮은 오버헤드
- 실시간 데이터 전송
- 커스텀 프로토콜 구현 가능

### HTTP의 장점
- 표준화된 프로토콜
- 방화벽 친화적
- 캐싱, 압축 등 지원
- RESTful API로 간편한 사용

### 선택 기준

#### Socket 사용
- 실시간 채팅, 게임
- 스트리밍 서비스
- IoT 디바이스 통신
- 낮은 지연시간이 중요한 경우
- 지속적인 양방향 통신 필요

#### HTTP 사용
- 일반적인 웹 서비스
- RESTful API
- 파일 업로드/다운로드
- 요청-응답 패턴
- 간단한 통신

## WebSocket

### 개념
- HTTP 위에서 동작하는 양방향 통신 프로토콜
- HTTP Upgrade 헤더로 연결 전환
- 웹 브라우저에서 Socket 통신 가능
- 표준 프로토콜 (RFC 6455)

### 특징
- **양방향 실시간 통신**: 서버에서 클라이언트로 푸시 가능
- **낮은 오버헤드**: 한 번 연결 후 지속적 사용
- **방화벽 친화적**: HTTP 포트(80, 443) 사용
- **표준 API**: 브라우저 지원

### 일반 Socket과의 차이
- **WebSocket**: HTTP 기반, 브라우저 지원, 표준 프로토콜
- **Raw Socket**: TCP/UDP 직접 사용, 서버 간 통신, 커스텀 프로토콜

### 사용 사례
- 실시간 채팅
- 주식 시세 업데이트
- 온라인 게임
- 협업 도구 (Google Docs 등)
- 알림 시스템

## Spring Boot와 Socket

### WebSocket 지원
- Spring WebSocket 모듈 제공
- STOMP 프로토콜 지원 (메시징)
- SockJS (WebSocket Fallback)
- 브로커 통합 (RabbitMQ, ActiveMQ)

### 사용 시나리오
- 채팅 애플리케이션
- 실시간 알림
- 대시보드 실시간 업데이트
- 협업 툴

## 면접 단골 질문

### Q: Socket이란 무엇인가요?
**A**: 네트워크 통신을 위한 엔드포인트로, 프로세스 간 데이터를 주고받을 수 있는 인터페이스입니다. IP 주소와 포트 번호로 식별됩니다.

### Q: TCP Socket과 UDP Socket의 차이는?
**A**: TCP는 연결 지향적이고 신뢰성을 보장하며 순서를 보장하지만 느립니다. UDP는 비연결성이고 빠르지만 신뢰성과 순서를 보장하지 않습니다.

### Q: accept()는 블로킹 함수인가요?
**A**: 네, 클라이언트의 연결 요청이 올 때까지 대기합니다. 멀티 스레드, Thread Pool, Non-Blocking I/O로 해결할 수 있습니다.

### Q: 서버에서 여러 클라이언트를 어떻게 처리하나요?
**A**: Thread per Connection, Thread Pool, Non-Blocking I/O (NIO), 비동기 I/O 등의 방법을 사용합니다. 동시 접속자가 많을 경우 NIO나 비동기 방식이 효율적입니다.

### Q: Socket과 WebSocket의 차이는?
**A**: Socket은 TCP/UDP 레벨의 저수준 API이고, WebSocket은 HTTP 위에서 동작하는 고수준 프로토콜로 웹 브라우저에서 사용하기 적합합니다.

### Q: 포트 번호는 왜 필요한가요?
**A**: 하나의 IP 주소에서 여러 서비스를 구분하기 위해 필요합니다. 예를 들어 같은 서버에서 웹(80), SSH(22), DB(3306) 등을 동시에 운영할 수 있습니다.

### Q: TIME_WAIT 상태는 소켓에서도 발생하나요?
**A**: 네, TCP 소켓을 close()할 때 4-way handshake가 일어나며, 능동적으로 종료한 쪽에서 TIME_WAIT 상태가 발생합니다. 이는 지연된 패킷 문제를 방지하기 위함입니다.

### Q: Socket의 send()와 recv() 호출 시 내부적으로 어떤 일이 일어나나요?
**A**: send()는 데이터를 송신 버퍼에 복사하고 TCP가 자동으로 패킷화하여 전송합니다. recv()는 수신 버퍼에서 데이터를 읽어오며, 모두 TCP의 흐름 제어와 혼잡 제어가 자동으로 적용됩니다.

### Q: 왜 HTTP를 쓰지 않고 Socket을 직접 사용하나요?
**A**: HTTP는 요청-응답 패턴이라 서버가 클라이언트에게 먼저 데이터를 보낼 수 없습니다. 실시간 양방향 통신이나 커스텀 프로토콜이 필요한 경우 Socket을 직접 사용합니다.

### Q: Non-Blocking I/O의 장점은?
**A**: 단일 스레드로 수천 개의 동시 연결을 처리할 수 있어 리소스 효율이 높습니다. 하지만 구현이 복잡하고 디버깅이 어렵다는 단점이 있습니다.