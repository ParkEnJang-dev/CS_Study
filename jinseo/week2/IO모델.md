# [CS] I/O 모델 심화: Blocking vs Non-Blocking & Sync vs Async

## 1. 2x2 매트릭스 상세 분석


### ① Sync-Blocking (가장 직관적)
- **동작**: 시스템 콜 호출 → 커널이 작업 완료할 때까지 앱은 대기(Wait Queue) → 완료 시 복귀.
- **예**: 일반적인 `read()`, `write()` 함수.

### ② Sync-NonBlocking (폴링 방식)
- **동작**: 앱이 커널에 물어봄 → 커널은 "아직 안 됨" 즉시 응답 → 앱은 제어권을 가졌으나 **반복적으로** "다 됐니?" 물어봄(Polling).
- **단점**: 무의미한 CPU 소모가 발생함.

### ③ Async-NonBlocking (완전 비동기)
- **동작**: 커널에 요청 전달 → 즉시 리턴 → 커널은 백그라운드에서 작업 수행 → 완료 시 **Signal**이나 **Callback**으로 알림.
- **예**: Node.js의 libuv 엔진, Java의 NIO AIO.

### ④ Async-Blocking (비효율의 극치)
- **동작**: 호출은 비동기인데, 내부적으로 결과 통지를 기다리는 함수(예: `select()`, `poll()`)가 실행을 막음.
- **예**: 비동기 드라이버를 쓰면서 데이터베이스 커넥션 풀에서 Blocking 대기가 일어나는 경우.

## 2. I/O 멀티플렉싱 (I/O Multiplexing)
- **개념**: 한 프로세스가 여러 개의 파일 디스크립터(Socket 등)를 동시에 감시하는 기법.
- **종류**: `select` (전체 탐색/느림), `poll`, `epoll` (이벤트 발생 시만 통지/Linux의 핵심).