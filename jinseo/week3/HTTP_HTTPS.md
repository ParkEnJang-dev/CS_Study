# HTTP & HTTPS 정리

## HTTP (HyperText Transfer Protocol)

### 개요
- 웹에서 데이터를 주고받기 위한 프로토콜
- 클라이언트-서버 구조
- 요청(Request)과 응답(Response) 기반
- TCP/IP 위에서 동작
- 기본 포트: 80

### 특징
- **Stateless**: 각 요청이 독립적, 이전 요청을 기억하지 않음
- **Connectionless**: 요청/응답 후 연결 종료 (HTTP/1.0)
- **평문 전송**: 데이터가 암호화되지 않음 → 보안 취약

## HTTP 버전별 특징

### HTTP/1.0 (1996년)
- 헤더 개념 도입
- POST, HEAD 메서드 추가
- 상태 코드(200, 404 등) 도입
- 매 요청마다 TCP 연결 수립/해제 → 성능 저하

### HTTP/1.1 (1997년)
- **Persistent Connection (Keep-Alive)**: 하나의 TCP 연결로 여러 요청/응답 처리
- **Pipelining**: 응답을 기다리지 않고 연속 요청 전송
- PUT, DELETE 등 추가 메서드
- Host 헤더 필수 (Virtual Host 가능)
- **단점**: HOL Blocking, 헤더 중복 전송

### HTTP/2 (2015년)
- **Binary Framing**: 바이너리로 전송하여 파싱 속도 향상
- **Multiplexing**: 하나의 TCP 연결에서 여러 요청/응답 동시 처리 → HOL Blocking 해결
- **Header Compression (HPACK)**: 헤더 압축으로 대역폭 절약
- **Server Push**: 클라이언트 요청 없이 필요한 리소스 미리 전송
- **Stream Prioritization**: 중요한 리소스 우선 처리

### HTTP/3 (2022년)
- **QUIC 프로토콜 사용**: UDP 기반으로 TCP의 HOL Blocking 완전 해결
- **0-RTT 연결 수립**: 재연결 시 handshake 생략
- **연결 마이그레이션**: IP 주소 변경 시에도 연결 유지 (모바일 환경 최적화)
- **내장 암호화**: TLS 1.3 기본 내장

## HTTPS (HTTP Secure)

### 개요
- HTTP에 SSL/TLS를 적용한 보안 프로토콜
- 모든 데이터가 암호화되어 전송
- 기본 포트: 443
- 중간자 공격(MITM) 방지

### HTTP vs HTTPS
```
HTTP:  클라이언트 ─────평문 전송─────> 서버
HTTPS: 클라이언트 ─────암호화 전송────> 서버
```

### HTTPS의 3가지 보안 요소
1. **기밀성 (Confidentiality)**: 데이터 암호화
2. **무결성 (Integrity)**: 데이터 변조 방지
3. **인증 (Authentication)**: 서버 신원 확인

## SSL/TLS

### SSL (Secure Sockets Layer)
- Netscape에서 개발
- SSL 2.0, SSL 3.0 (현재 사용 중단)

### TLS (Transport Layer Security)
- SSL의 후속 버전
- **TLS 1.2 (2008)**: 현재 널리 사용, 강력한 암호화
- **TLS 1.3 (2018)**: Handshake 단순화, 더 빠른 연결, 더 강한 보안

## SSL/TLS Handshake (TLS 1.2 기준)

### 목적
- 암호화 방식 협상
- 인증서 검증
- 세션 키 교환

### 전체 과정

#### 1단계: Client Hello
**클라이언트 → 서버**
- 지원하는 TLS 버전
- 지원하는 암호화 알고리즘 목록 (Cipher Suite)
- 클라이언트 랜덤 데이터
- Session ID (재연결용)

#### 2단계: Server Hello
**서버 → 클라이언트**
- 선택한 TLS 버전
- 선택한 암호화 알고리즘
- 서버 랜덤 데이터
- Session ID

#### 3단계: Certificate
**서버 → 클라이언트**
- 서버의 SSL/TLS 인증서 전송
- 인증서에는 서버의 공개키 포함
- CA(인증기관)의 서명 포함

#### 4단계: Server Hello Done
**서버 → 클라이언트**
- 서버가 보낼 정보를 다 보냈다는 신호

#### 5단계: Certificate Verify
**클라이언트 측에서 수행**
- 인증서가 유효한지 확인
- CA의 서명 검증
- 인증서 유효기간 확인
- 도메인 이름 일치 확인

#### 6단계: Client Key Exchange
**클라이언트 → 서버**
- Pre-Master Secret 생성
- 서버의 공개키로 암호화하여 전송
- 서버만 개인키로 복호화 가능

#### 7단계: Session Key 생성
**클라이언트와 서버 각각 수행**
- Pre-Master Secret + 클라이언트 랜덤 + 서버 랜덤
- 위 3가지를 조합하여 Master Secret 생성
- Master Secret으로부터 Session Key 생성
- 이후 모든 통신은 이 Session Key로 암호화

```
양쪽이 동일하게 계산:
Session Key = 함수(Pre-Master Secret, 
                   클라이언트 랜덤, 
                   서버 랜덤)
```

#### 8단계: Change Cipher Spec
**클라이언트 → 서버**
- "이제부터 암호화된 통신을 시작합니다"

#### 9단계: Finished (Client)
**클라이언트 → 서버**
- 지금까지의 Handshake 메시지를 해시한 값을 암호화하여 전송
- 정상적으로 키 교환이 이루어졌는지 확인

#### 10단계: Change Cipher Spec
**서버 → 클라이언트**
- "저도 암호화 준비 완료했습니다"

#### 11단계: Finished (Server)
**서버 → 클라이언트**
- Handshake 메시지의 해시값 전송
- 검증 완료

#### 12단계: 암호화 통신 시작
- Session Key로 암호화된 HTTP 데이터 전송
- 대칭키 암호화 사용 (빠름)

### 시각적 표현
```
Client                                    Server
  |                                          |
  | -------- Client Hello -----------------> |  [1]
  |                                          |
  | <------- Server Hello ------------------ |  [2]
  | <------- Certificate ------------------- |  [3]
  | <------- Server Hello Done ------------- |  [4]
  |                                          |
  | [인증서 검증]                              |  [5]
  |                                          |
  | -------- Client Key Exchange ----------> |  [6]
  |                                          |
  | [Session Key 생성]     [Session Key 생성]  |  [7]
  |                                          |
  | -------- Change Cipher Spec -----------> |  [8]
  | -------- Finished ----------------------> |  [9]
  |                                          |
  | <------- Change Cipher Spec ------------ |  [10]
  | <------- Finished ---------------------- |  [11]
  |                                          |
  | <======= 암호화된 HTTP 통신 =============> |  [12]
```

## TLS 1.3 Handshake (간소화)

### 주요 개선사항
- 1-RTT (Round Trip Time)로 단축
- 불필요한 단계 제거
- 더 강력한 암호화만 지원

### 과정
1. Client Hello + Key Share (동시 전송)
2. Server Hello + Key Share + Certificate + Finished
3. 암호화 통신 시작

### 0-RTT (재연결 시)
- 이전 세션 정보 재사용
- Handshake 없이 바로 데이터 전송
- 극도로 빠른 연결

## 실무 관련

### Spring Boot에서 HTTPS 설정
```java
// application.properties
server.port=443
server.ssl.enabled=true
server.ssl.key-store=classpath:keystore.p12
server.ssl.key-store-password=password
server.ssl.key-store-type=PKCS12
```

### SSL 인증서 종류

#### DV (Domain Validation)
- 도메인 소유권만 확인
- 발급 빠름, 저렴
- 개인 블로그, 소규모 사이트

#### OV (Organization Validation)
- 조직 실재 확인
- 기업 정보 표시
- 일반 기업 사이트

#### EV (Extended Validation)
- 엄격한 검증
- 주소창에 기업명 표시
- 금융, 전자상거래

### 성능 고려사항

#### Handshake 오버헤드
- TLS 1.2: 2-RTT
- TLS 1.3: 1-RTT
- HTTP/2, HTTP/3와 함께 사용 권장

#### 세션 재사용
- Session ID
- Session Ticket
- 재연결 시 Handshake 생략

#### HSTS (HTTP Strict Transport Security)
```
Strict-Transport-Security: max-age=31536000
```
- 브라우저가 항상 HTTPS로 접속하도록 강제

## 면접 단골 질문

### Q: HTTP와 HTTPS의 차이는?
**A**: HTTP는 평문 전송으로 보안에 취약하고, HTTPS는 SSL/TLS로 암호화하여 데이터 기밀성, 무결성, 인증을 보장합니다.

### Q: SSL과 TLS의 차이는?
**A**: SSL은 초기 버전이고 TLS는 그 후속 표준입니다. 현재는 TLS 1.2, 1.3을 사용하며 SSL은 보안 취약점으로 사용 중단되었습니다.

### Q: TLS Handshake 과정을 설명하세요.
**A**: Client Hello로 시작해서 서버가 인증서를 보내고, 클라이언트가 검증 후 암호화된 키를 교환하여 Session Key를 생성, 이후 대칭키로 통신합니다.

### Q: 대칭키와 비대칭키를 언제 사용하나요?
**A**: Handshake 단계에서는 비대칭키(공개키/개인키)로 Session Key를 안전하게 교환하고, 실제 데이터 전송에는 빠른 대칭키를 사용합니다.

### Q: HTTP/2의 주요 개선점은?
**A**: Multiplexing으로 HOL Blocking 해결, 헤더 압축, Server Push, Binary Framing 등으로 성능을 크게 개선했습니다.

### Q: HTTP/3가 UDP를 사용하는 이유는?
**A**: TCP의 HOL Blocking을 완전히 해결하고, 빠른 연결 수립, 연결 마이그레이션 등 모바일 환경에 최적화하기 위해서입니다.

### Q: HTTPS는 왜 느리지 않나요?
**A**: 초기 Handshake는 오버헤드가 있지만, 세션 재사용, TLS 1.3의 1-RTT, HTTP/2의 Multiplexing 등으로 전체적인 성능은 오히려 향상됩니다.