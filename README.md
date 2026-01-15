




# 🚀 Backend Core Competency Deep Dive

백엔드 개발자로서 필수적으로 갖춰야 할 CS(OS, Network, Database) 기초부터 Java, Spring Framework 심화 역량까지 내재화하기 위한 2인 스터디 저장소입니다.

## 🎯 스터디 목표
1. **기술의 내재화:** 단순 암기를 넘어, 원리를 이해하고 누군가에게 설명할 수 있는 수준("Feynman Technique")을 지향합니다.
2. **실무형 협업:** Git Flow, Pull Request, Code Review를 통해 실제 현업과 유사한 방식으로 학습합니다.
3. **지식의 자산화:** 학습한 내용을 체계적인 문서로 남겨 향후 면접 대비 및 기술 레퍼런스로 활용합니다.

## 👥 Members
|    이름    |         역할          | GitHub  | 비고  |
| :------: | :-----------------: | :-----: | :-: |
| **참여자1** | Reviewer / Approver | @jinseo |     |
| **참여자2** | Reviewer / Approver | @minsub |     |

---

## 📅 진행 규칙 (Rules)

### 1. 일정 관리
* **학습 기간:** 202X.XX.XX ~ 202X.XX.XX (총 12주 예정)
* **오프라인/온라인 미팅:** 매주 [일요일] [오후 2시]
* **PR 마감:** 미팅 전날 [토요일] [자정]까지

### 2. 진행 방식
1.  **Issue 생성:** 매주 학습할 주제를 Issue로 등록합니다.
2.  **개인 학습:** 각자 맡은 부분(혹은 전체)을 깊이 있게 공부하고 정리합니다.
3.  **Markdown 작성:** 학습 내용은 `/영역/주제.md` 형태로 정리하여 Commit 합니다.
4.  **PR & Review:** `main` 브랜치로 PR을 날리고, 상대방의 **Approve**를 받아야 Merge 할 수 있습니다.
5.  **발표 및 토론:** 미팅 시간에는 단순 낭독이 아닌, **"가장 어려웠던 점"**, **"면접 예상 질문"**, **"실무 적용 포인트"** 위주로 토론합니다.

### 3. 벌칙 (Option)
* PR 마감 시간 미준수 시: [커피 쏘기 / 벌금 n원]
* 무단 불참 시: [벌칙 내용]

---

## 📚 Curriculum & Progress

### 🖥️ 1. Operating System (OS)
- [x] **프로세스와 스레드** (Process vs Thread, Multi-thread)
- [ ] **메모리 관리** (Virtual Memory, Paging, Segmentation)
- [ ] **스케쥴링** (CPU Scheduling Algorithms)
- [ ] **동기화와 통신** (Mutex, Semaphore, IPC)
- [ ] **네트워킹** (OS 관점의 소켓 통신)
- [ ] **파일 시스템**
- [ ] **I/O** (Blocking, Non-blocking, Sync, Async)

### 🌐 2. Network
- [ ] **OSI 7 Layer & TCP/IP**
- [ ] **TCP & UDP** (특징 비교, Header 구조)
- [ ] **3 Way Handshake & 4 Way Handshake**
- [ ] **HTTP & HTTPS** (Versions, SSL/TLS Handshake)
- [ ] **Socket Programming**
- [ ] **인증, 보안, 방화벽**
- [ ] **Load Balancer** (L4 vs L7)
- [ ] **CDN & DNS**
- [ ] 쿠키 세션
- [ ] CORS, CSRF, XSS 보안 개념

### 💾 3. Database
- [ ] **RDBMS vs NoSQL** 특징 및 비교
- [ ] **트랜잭션** (ACID, 격리 수준, 전파 레벨)
- [ ] **인덱스 설계와 최적화** (B-Tree, Clustered/Non-Clustered)
- [ ] **조인 메서드** (NL Join, Hash Join, Merge Join)
- [ ] **쿼리 튜닝 & 실행계획 분석**
- [ ] **옵티마이저의 원리**
- [ ] **정규화, 비정규화, 반정규화**
- [ ] **엔티티와 모델링** (PK, FK, 복합키 설계)
- [ ] **고가용성 및 확장성** (Replication, Sharding)
- [ ] **백업 및 복구 / SQL 종류** (DML, DDL 등)

### ☕ 4. Java
- [ ] **JVM 구조 & 메모리 영역** (Heap, Stack, Method)
- [ ] **Garbage Collection** (알고리즘, 동작 원리, 튜닝)
- [ ] **OOP & 객체 지향 설계 원칙** (SOLID)
- [ ] **자료형** (Primitive vs Reference vs Wrapper)
- [ ] **String Constant Pool**
- [ ] **상속, 다형성, 추상클래스, 인터페이스**
- [ ] **Collections Framework** (List, Set, Map 구현체 비교)
- [ ] **Generics & Reflection**
- [ ] **Exception Handling** (Checked vs Unchecked)
- [ ] **Modern Java** (Lambda, Stream API, Optional)
- [ ] **Concurrency** (Thread, Runnable, ExecutorService, Synchronized, Volatile)
- [ ] **Design Patterns** (Singleton, Factory, Strategy, etc.)
- [ ] **File I/O (NIO)**

### 🍃 5. Spring Framework
- [ ] **IoC & DI** (Container, Dependency Injection)
- [ ] **Bean Lifecycle & Scope**
- [ ] **AOP** (Aspect Oriented Programming)
- [ ] **Spring MVC** (DispatcherServlet 구조 및 동작 원리)
- [ ] **Spring Boot Configuration** (Auto Configuration)
- [ ] **JPA & Hibernate** (영속성 컨텍스트, N+1 문제, Fetch Join)
- [ ] **Transaction Management** (전파, 격리 레벨 @Transactional)
- [ ] **Spring Security** (Filter Chain, 인증/인가)
- [ ] **QueryDSL & JDBC**
- [ ] **Build Tool** (Gradle vs Maven)
- [ ] **Testing** (JUnit, Mockito, TestContext)
- [ ] **Logging & Actuator**
- [ ] **Spring Batch & Event**
- [ ] **Caching Strategy** (Redis, Local Cache)

---

## 🗓️ 12 Weeks Roadmap

|   주차    |      영역       | 주제 (Focus)                          | 상태  |
| :-----: | :-----------: | :---------------------------------- | :-: |
| **1주**  |      OS       | 프로세스/스레드, 스케쥴링, 메모리 관리              | ✅ |
| **2주**  |   OS / Net    | 동기화, I/O 모델, OSI 7 Layer, TCP/IP    |  ⬜  |
| **3주**  |    Network    | HTTP/S, Handshake, DNS, 로드밸런서       |  ⬜  |
| **4주**  |    DB (기초)    | RDBMS/NoSQL, 트랜잭션, 정규화, 모델링         |  ⬜  |
| **5주**  |    DB (심화)    | 인덱스, 실행계획, 튜닝, 옵티마이저                |  ⬜  |
| **6주**  |    DB (운영)    | 조인, 서브쿼리, 고가용성, 백업                  |  ⬜  |
| **7주**  |  Java (Core)  | JVM, GC, OOP, SOLID, 자료형            |  ⬜  |
| **8주**  | Java (Modern) | 람다, 스트림, 콜렉션, 제네릭, 리플렉션             |  ⬜  |
| **9주**  |  Java (Adv)   | 동시성(Multi-thread), Executor, 파일 I/O |  ⬜  |
| **10주** | Spring (Core) | IoC/DI, Bean, AOP, MVC, 설정          |  ⬜  |
| **11주** | Spring (Data) | JPA, 트랜잭션, JDBC, N+1문제              |  ⬜  |
| **12주** | Spring (Eco)  | Security, Batch, Actuator, 테스트      |  ⬜  |

---

## 📂 Repository Structure

```text
.
├── OS
│   ├── process_thread.md
│   └── memory_management.md
├── Network
│   └── http_https.md
├── Database
│   └── index_optimization.md
├── Java
│   └── garbage_collection.md
└── Spring
    └── ioc_di.md
```



# 🧩 Algorithm & Data Structure Sprint

백엔드 성능 최적화의 기초이자 코딩 테스트의 핵심인 알고리즘과 자료구조를 집중적으로 학습합니다.

## 🎯 학습 목표
1. **구현 능력:** 각 알고리즘을 라이브러리 없이 Java 코드로 직접 구현한다.
2. **복잡도 분석:** 시간 복잡도(Big-O)와 공간 복잡도를 이해하고 설명한다.
3. **실무 적용:** LinkedHashMap, LRU 등이 실제 시스템(캐시, OS 등)에서 어떻게 쓰이는지 연결한다.

---

## 📚 Curriculum List

### 1. Sorting (정렬)
*각 정렬의 동작 원리와 Best/Average/Worst 시간 복잡도 비교 필수*
- [ ] **버블 정렬 (Bubble Sort)**: 인접한 두 원소를 비교하여 Swap (구현 난이도: 하)
- [ ] **삽입 정렬 (Insertion Sort)**: 앞의 정렬된 부분과 비교하여 위치 삽입
- [ ] **병합 정렬 (Merge Sort)**: 분할 정복(Divide and Conquer), 안정 정렬
- [ ] **퀵 정렬 (Quick Sort)**: Pivot 기준 분할, 비안정 정렬 (최악의 경우 O(n^2) 방지법 고민)
- [ ] **힙 정렬 (Heap Sort)**: 힙 자료구조 기반, 우선순위 큐와 연관

### 2. Search (검색)
- [ ] **선형 검색 (Linear Search)**: 순차 탐색
- [ ] **이진 검색 (Binary Search)**: 정렬된 데이터에서 탐색 (Up/Down), O(log n)
- [ ] **DFS (깊이 우선 탐색)**: Stack 또는 재귀(Recursion) 활용, 미로 찾기 등
- [ ] **BFS (너비 우선 탐색)**: Queue 활용, 최단 경로 탐색

### 3. Data Structures (자료구조)
- [ ] **Stack**: LIFO (Last In First Out), 메서드 호출 스택, 뒤로 가기
- [ ] **Queue**: FIFO (First In First Out), 버퍼, 프린터 대기열
- [ ] **Priority Queue**: 우선순위가 높은 데이터가 먼저 나옴 (Heap 활용)
- [ ] **LinkedList**: 노드 기반 연결, 삽입/삭제 용이, 검색 불리
- [ ] **LinkedHashMap**: 입력 순서 보장 및 LRU 캐시 구현의 기초

### 4. Paging & Cache Strategy
*OS 메모리 관리 및 DB 버퍼 캐시에서 사용되는 알고리즘*
- [ ] **LRU (Least Recently Used)**: 가장 오랫동안 참조되지 않은 페이지 교체 (LinkedHashMap으로 구현 실습)
- [ ] **LFU (Least Frequently Used)**: 참조 횟수가 가장 적은 페이지 교체


# 📅 12-Week Algorithm & Data Structure Curriculum

제공된 학습 주제를 기반으로 **기초 자료구조 → 탐색/정렬 → 고급 자료구조 → 캐시 전략** 순으로 배치한 12주 완성 로드맵입니다.

## 📌 Syllabus

|   주차    |         분류         | 학습 주제 (Topics)              | 상세 내용 및 목표                                                                                | 상태  |
| :-----: | :----------------: | :-------------------------- | :---------------------------------------------------------------------------------------- | :-: |
| **1주**  | **Data Structure** | **Stack & Queue**           | - **Stack:** LIFO, 메서드 호출 스택, 뒤로 가기<br>- **Queue:** FIFO, 버퍼, 프린터 대기열 구현                  |  ✅  |
| **2주**  | **Data Structure** | **LinkedList**              | - 노드 기반 연결 구조 이해 (Next 포인터)<br>- 삽입/삭제의 용이성과 검색의 불리함($O(N)$) 비교                           |  ⬜  |
| **3주**  |     **Search**     | **Linear & Binary Search**  | - **Linear:** 순차 탐색 구현<br>- **Binary:** 정렬된 데이터에서의 탐색 (Up/Down), $O(\log N)$ 증명           |  ⬜  |
| **4주**  |    **Sorting**     | **Bubble & Insertion Sort** | - **Bubble:** 인접 원소 비교 및 Swap<br>- **Insertion:** 앞부분과 비교하여 위치 삽입<br>- $O(N^2)$ 정렬의 원리 이해 |  ⬜  |
| **5주**  |    **Sorting**     | **Merge Sort**              | - 분할 정복(Divide and Conquer) 알고리즘<br>- 안정 정렬(Stable Sort)의 의미 확인                           |  ⬜  |
| **6주**  |    **Sorting**     | **Quick Sort**              | - Pivot 기준 분할 로직 구현<br>- 비안정 정렬 특징 및 최악의 경우($O(N^2)$) 방지법 고찰                              |  ⬜  |
| **7주**  |   **Search**<br>   | **DFS (깊이 우선 탐색)**          | - Stack 또는 재귀(Recursion)를 활용한 구현<br>- 미로 찾기 등 경로 탐색 문제 실습                                 |  ⬜  |
| **8주**  |     **Search**     | **BFS (너비 우선 탐색)**          | - Queue를 활용한 구현<br>- 최단 경로 탐색 원리 이해                                                       |  ⬜  |
| **9주**  | **Data Structure** | **Priority Queue**          | - 우선순위가 높은 데이터 처리 (Heap 구조 활용)<br>- Enqueue/Dequeue 시 정렬 유지 로직                            |  ⬜  |
| **10주** |    **Sorting**     | **Heap Sort**               | - 힙 자료구조(9주차)를 기반으로 한 정렬 구현<br>- 완전 이진 트리 구조 이해 및 활용                                      |  ⬜  |
| **11주** | **Cache Strategy** | **LinkedHashMap & LRU**     | - **LinkedHashMap:** 순서 보장 원리<br>- **LRU:** 가장 오래된 데이터 교체 알고리즘 구현 실습                      |  ⬜  |
| **12주** | **Cache Strategy** | **LFU Cache**               | - **LFU:** 참조 횟수 기반 교체 알고리즘<br>- LRU와의 차이점 비교 및 빈도수 관리 로직 구현                              |  ⬜  |

---

## 📝 Study Guide

### 1. Sorting (정렬)
> **Check Point:** 각 정렬의 동작 원리와 Best/Average/Worst 시간 복잡도 비교 필수
- [ ] **버블 정렬 (Bubble Sort)**: 인접한 두 원소를 비교하여 Swap (구현 난이도: 하)
- [ ] **삽입 정렬 (Insertion Sort)**: 앞의 정렬된 부분과 비교하여 위치 삽입
- [ ] **병합 정렬 (Merge Sort)**: 분할 정복(Divide and Conquer), 안정 정렬 
- [ ] **퀵 정렬 (Quick Sort)**: Pivot 기준 분할, 비안정 정렬 (최악의 경우 O(n^2) 방지법 고민)
- [ ] **힙 정렬 (Heap Sort)**: 힙 자료구조 기반, 우선순위 큐와 연관

### 2. Search (검색)
- [ ] **선형 검색 (Linear Search)**: 순차 탐색
- [ ] **이진 검색 (Binary Search)**: 정렬된 데이터에서 탐색 (Up/Down), O(log n)
- [ ] **DFS (깊이 우선 탐색)**: Stack 또는 재귀(Recursion) 활용, 미로 찾기 등
- [ ] **BFS (너비 우선 탐색)**: Queue 활용, 최단 경로 탐색 

### 3. Data Structures (자료구조)
- [ ] **Stack**: LIFO (Last In First Out), 메서드 호출 스택, 뒤로 가기
- [ ] **Queue**: FIFO (First In First Out), 버퍼, 프린터 대기열
- [ ] **Priority Queue**: 우선순위가 높은 데이터가 먼저 나옴 (Heap 활용)
- [ ] **LinkedList**: 노드 기반 연결, 삽입/삭제 용이, 검색 불리
- [ ] **LinkedHashMap**: 입력 순서 보장 및 LRU 캐시 구현의 기초

### 4. Paging & Cache Strategy
> **Check Point:** OS 메모리 관리 및 DB 버퍼 캐시에서 사용되는 알고리즘
- [ ] **LRU (Least Recently Used)**: 가장 오랫동안 참조되지 않은 페이지 교체 (LinkedHashMap으로 구현 실습) 
- [ ] **LFU (Least Frequently Used)**: 참조 횟수가 가장 적은 페이지 교체
