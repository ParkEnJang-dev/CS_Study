# [Network] TCP의 신뢰성 보장 메커니즘

## 1. 3-way Handshake (연결)
연결을 시작할 때 서로의 **Sequence Number**를 맞추는 과정입니다.
1. **Client**: SYN (내 번호는 100이야, 통신하자!)
2. **Server**: SYN + ACK (확인했어, 내 번호는 500이야. 너도 확인했니?)
3. **Client**: ACK (응, 나도 확인했어. 이제 보내자!)

## 2. 4-way Handshake (해제)
데이터 유실을 막기 위해 4단계로 이루어집니다.
1. **Client**: FIN (나 이제 보낼 거 다 보냈어.)
2. **Server**: ACK (알겠어, 일단 확인. 나도 남은 거 있나 볼게.) → **Close Wait**
3. **Server**: FIN (나도 다 보냈어, 진짜 끝!)
4. **Client**: ACK (알겠어!) → **Time Wait** (잠시 대기 후 종료)
    - *Tip: Time Wait이 필요한 이유는 마지막 ACK가 유실될 경우를 대비하고, 뒤늦게 도착하는 패킷을 처리하기 위함입니다.*



## 3. 흐름 제어 vs 혼잡 제어
- **흐름 제어 (Flow Control)**: 수신자가 감당할 만큼만 보내기 (**Sliding Window** 기법).
- **혼잡 제어 (Congestion Control)**: 네트워크가 막히면 천천히 보내기 (**Slow Start**, **Fast Retransmit**).