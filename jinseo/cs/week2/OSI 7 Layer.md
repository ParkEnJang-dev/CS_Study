# [Network] OSI 7계층과 캡슐화 (Encapsulation)

## 1. 데이터 흐름과 캡슐화
상위 계층에서 하위 계층으로 내려갈 때마다 **헤더(Header)**가 붙는 과정입니다.
- **L7 ~ L5**: Data (메시지 본체)
- **L4 (Transport)**: Data + TCP/UDP 헤더 = **Segment** (포트 번호 포함)
- **L3 (Network)**: Segment + IP 헤더 = **Packet** (IP 주소 포함)
- **L2 (Data Link)**: Packet + 프레임 헤더/트레일러 = **Frame** (MAC 주소 포함)
- **L1 (Physical)**: 0과 1의 전기적 신호 (**Bit**)



## 2. 핵심 계층 상세
### 🔹 L2 Data Link vs L3 Network
- **L2 (MAC)**: 같은 네트워크 대역(LAN) 내에서 옆집 컴퓨터를 찾는 용도.
- **L3 (IP)**: 수많은 네트워크를 거쳐 전 세계의 목적지를 찾는 용도(라우팅).

### 🔹 L4 Transport
- **포트 번호(Port)**를 통해 어떤 프로그램(Process)으로 데이터를 전달할지 결정함.