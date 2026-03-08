

```table-of-contents
```

## 기본개념

### JVM 메모리 구조 (Runtime Data Area)

---
![](attachments/Pasted%20image%2020260304003330.png)

JVM은 프로그램을 실행할 때 OS로부터 메모리를 할당받아서, 용도별로 나누어 관리합니다.
이걸 **Runtime Data Area**라고 부릅니다.

크게 5가지 영역으로 나뉘는데, 하나씩 차근차근 살펴보겠습니다.

---

### 먼저, 예제 코드를 하나 놓고 갈게요

```java
public class Main {
    static int COUNT = 10;

    public static void main(String[] args) {
        int num = 5;
        User user = new User("minsub");
        user.sayHello();
    }
}

public class User {
    private String name;

    public User(String name) {
        this.name = name;
    }

    public void sayHello() {
        System.out.println("안녕하세요, " + name);
    }
}
```

이 코드가 **컴파일 → 로딩 → 실행**되는 과정에서 각 메모리 영역에 뭐가 올라가는지 따라가 볼게요.

---

### STEP 1. 컴파일 (javac)

```
Main.java  →  javac  →  Main.class  (바이트코드)
User.java  →  javac  →  User.class  (바이트코드)
```

이 시점에는 아직 JVM 메모리와 아무 관련이 없습니다.
그냥 디스크에 `.class` 파일이 만들어진 것뿐이에요.

---

### STEP 2. Class Loader가 .class를 읽어서 Method Area에 적재

`java Main` 명령을 치면 JVM이 기동되고, **Class Loader**가 `Main.class`를 찾아서 읽습니다.
읽은 내용은 **Method Area**에 올라갑니다.

---

### 1. Method Area (메서드 영역) — "클래스의 설계도 창고"

모든 스레드가 **공유**하는 영역입니다.
Class Loader가 `.class` 파일을 읽어서 여기에 정보를 올려놓습니다.

구체적으로 뭐가 들어가냐면:

| 저장되는 것                    | 설명                                     | 예시 (위 코드 기준)                                  |
| ------------------------- | -------------------------------------- | --------------------------------------------- |
| **클래스 정보**                | 클래스 이름, 부모 클래스, 인터페이스 목록, 접근 제어자 등     | `Main`은 `Object`를 상속, `public` 클래스            |
| **필드(변수) 정보**             | 어떤 변수가 있는지, 타입은 뭔지, 접근 제어자는 뭔지         | `User` 클래스에 `private String name` 필드가 있다      |
| **메서드 정보**                | 메서드 이름, 리턴 타입, 매개변수, 접근 제어자, **바이트코드** | `sayHello()`는 리턴 void, 매개변수 없음, 바이트코드는 이러이러하다 |
| **생성자 정보**                | 생성자도 메서드의 일종으로 저장                      | `User(String name)` 생성자 정보                    |
| **static 변수**             | 클래스 레벨 변수. 객체 생성 없이 클래스에 딱 하나 존재       | `static int COUNT = 10` → 값 `10`이 여기 저장       |
| **Runtime Constant Pool** | 리터럴 상수, 클래스/메서드 참조 정보                  | 문자열 `"minsub"`, 숫자 `10`, `User` 클래스 참조 정보 등   |

쉽게 말하면, **"이 클래스가 어떻게 생겼는지"에 대한 모든 정보**가 여기 들어갑니다.
건물을 짓기 전에 **설계도(도면)** 를 보관하는 창고라고 생각하면 돼요.

> Java 8 이전에는 이 영역을 **PermGen**(Permanent Generation)이라 불렀는데,
> Java 8부터 **Metaspace**로 대체되었습니다. Metaspace는 JVM Heap이 아니라 **네이티브 메모리**를 사용하기 때문에, 기존 PermGen의 고정 크기 문제(`OutOfMemoryError: PermGen space`)가 해결되었습니다.

#### PermGen → Metaspace 변경의 장단점

| 장점 | 단점 |
|---|---|
| OOM 감소 (고정 크기 제한 해소) | 제한 없으면 클래스 로더 누수 시 OS 메모리까지 영향 |
| 필요한 만큼 동적 할당/해제 | JVM Heap 밖이라 기존 모니터링 도구로 추적 어려움 |
| `-XX:MaxPermSize` 튜닝 부담 감소 | 안전을 위해 `-XX:MaxMetaspaceSize` 별도 설정 권장 |

#### 실무에서 MaxMetaspaceSize 크기 잡는 방법

```bash
java -XX:MaxMetaspaceSize=256m -jar app.jar
```

1. **제한 없이 먼저 실행**해서 실제 사용량을 모니터링 (`jstat -gcmetacapacity <PID>`, Grafana, VisualVM 등)
2. **안정 상태의 사용량 확인** (앱 기동 후 안정되면 보통 더 이상 안 늘어남)
3. 안정 사용량의 **1.5~2배 정도**를 `MaxMetaspaceSize`로 설정

> 클래스가 많아지는 대표 케이스: Spring (프록시/AOP 동적 클래스 생성), Hibernate (엔티티별 프록시), 리플렉션 많이 쓰는 라이브러리

---

### STEP 3. main 스레드 시작 → Stack, PC Register 생성

JVM이 `main()` 메서드를 찾아서 실행하려고 main 스레드를 만듭니다.
스레드가 만들어지면 그 스레드 전용으로 **Stack**, **PC Register**, **Native Method Stack**이 각각 생성됩니다.

---

### 2. Heap — "실제 물건(객체)을 놓는 창고"

모든 스레드가 **공유**하는 영역입니다.
`new` 키워드로 생성된 **객체 인스턴스**와 **배열**이 여기에 저장됩니다.

위 코드에서 `new User("minsub")`를 하면:
- Heap의 **Young Generation(Eden 영역)** 에 User 객체가 만들어집니다.
- 그 객체 안에 `name = "minsub"` 값이 들어갑니다.

**GC(Garbage Collector)** 가 관리하는 주요 대상이 바로 이 Heap입니다.

- 구조: Young Generation(새 객체) → Old Generation(오래 살아남은 객체). 상세 내용은 아래 [Heap의 Young / Old Generation과 GC](#Heap의%20Young%20/%20Old%20Generation과%20GC) 참고.

---

### 3. Stack (JVM Stack) — "작업 순서를 쌓아두는 접시탑"

**스레드마다 하나씩** 독립적으로 생성됩니다.

메서드를 호출할 때마다 **스택 프레임(Stack Frame)** 이 하나씩 쌓이고(push),
메서드가 끝나면 빠집니다(pop). 접시를 쌓았다 빼는 것과 같아요.

위 코드의 흐름을 따라가 보면:

```
1) main() 호출 → main 스택 프레임 push
   ┌─────────────────────────┐
   │ main 프레임              │
   │  args = String[]         │
   │  num = 5                 │
   │  user = (Heap 참조 주소)  │
   └─────────────────────────┘

2) new User("minsub") → User 생성자 스택 프레임 push
   ┌─────────────────────────┐
   │ User() 생성자 프레임      │  ← 위에 쌓임
   │  this = (Heap 참조)      │
   │  name = "minsub"        │
   ├─────────────────────────┤
   │ main 프레임              │
   └─────────────────────────┘

3) 생성자 끝 → User() 프레임 pop

4) user.sayHello() → sayHello 스택 프레임 push
   ┌─────────────────────────┐
   │ sayHello 프레임          │  ← 위에 쌓임
   │  this = (Heap 참조)      │
   ├─────────────────────────┤
   │ main 프레임              │
   └─────────────────────────┘

5) sayHello 끝 → pop, main 끝 → pop → Stack 비어짐
```

스택 프레임 안에는 이런 것들이 들어갑니다:
- **지역 변수 배열(Local Variable Array)**: 매개변수, 지역 변수 (`num`, `user`, `args` 등)
- **오퍼랜드 스택(Operand Stack)**: 연산의 중간값 (예: `5 + 3` 계산 시 중간 결과)
- **프레임 데이터**: 상수 풀 참조, 예외 테이블 등

---

### 4. PC Register — "지금 몇 번째 줄 실행 중인지 기록하는 메모장"

**스레드마다 하나씩** 존재합니다.

현재 실행 중인 **바이트코드 명령어의 주소**를 저장합니다.
OS가 CPU에게 "다음에 이거 실행해"라고 알려주는 Program Counter와 같은 역할이에요.

멀티스레드 환경에서 스레드가 번갈아 실행될 때(Context Switching),
돌아왔을 때 **"어디까지 했더라?"** 를 기억하기 위해 필요합니다.

> 네이티브 메서드를 실행 중일 때는 undefined 상태가 됩니다. (JVM 바이트코드가 아니니까요)

---

### 5. Native Method Stack — "C/C++ 코드 실행을 위한 별도 스택"

**스레드마다 하나씩** 존재합니다.

Java가 아닌 **C, C++ 등 네이티브 코드**를 실행할 때 사용하는 스택입니다.
**JNI(Java Native Interface)** 를 통해 호출되는 네이티브 메서드의 정보를 관리합니다.

예를 들어, `Thread.currentThread()`나 `System.arraycopy()` 같은 메서드는 내부적으로 네이티브 코드로 구현되어 있어서 이 영역을 사용합니다.

---

### STEP 4. 실행이 끝나면 → GC가 Heap 정리

`main()`이 끝나면 Stack의 `user` 변수가 사라지고,
Heap에 있는 User 객체는 **아무도 참조하지 않는 상태**가 됩니다.
GC가 이걸 감지해서 메모리를 회수합니다.

---

### 한 줄의 코드로 보는 메모리 영역 구분

```java
User user = new User("minsub");
```

이 한 줄에서 3가지 메모리 영역이 모두 사용됩니다.

```
User                   user            =     new User("minsub")
 ↓                      ↓                         ↓
Method Area            Stack                     Heap
(클래스 설계도)        (지역 변수)                (실제 객체)
```

| 코드 부분 | 메모리 영역 | 역할 |
|---|---|---|
| `User` (타입) | **Method Area** | "User가 어떻게 생긴 클래스인지" 설계도를 참조. 이걸 보고 객체를 만듦 |
| `user` (변수) | **Stack** | main 메서드의 지역 변수. **객체 자체가 아니라 Heap 주소(참조값)** 를 저장 |
| `new User("minsub")` (객체) | **Heap** | 실제 객체가 만들어지는 곳. `name = "minsub"` 값이 여기 저장됨 |

그림으로 보면:

```
Stack                             Heap
┌──────────────────┐        ┌──────────────────┐
│ main 프레임       │        │ User 객체         │
│                  │        │                  │
│ user = 0x3A ──────────>   │ name = "minsub"  │
│                  │        │                  │
└──────────────────┘        └──────────────────┘
                                    ↑
                          Method Area의 User 클래스
                          설계도를 보고 만들어진 것
```

> 핵심: **Stack에는 객체가 아니라 "주소(참조값)"만 들어갑니다.**
> 이 개념이 나중에 Java의 **Call By Value** 문항과 직접 연결됩니다.
> Java에서 메서드에 객체를 넘기면, 객체 자체가 아니라 **참조값의 복사본**이 넘어가기 때문입니다.

---

### 전체 그림 정리

```
 Method Area (공유)              Heap (공유)
┌───────────────────────┐    ┌──────────────────┐
│ Main 클래스 정보        │    │                  │
│  - static COUNT = 10  │    │  User 객체        │
│  - main() 바이트코드   │    │   name = "minsub" │
│ User 클래스 정보        │    │                  │
│  - name 필드 정보      │    └──────────────────┘
│  - sayHello() 바이트코드│            ↑ 참조
│  - 생성자 바이트코드    │            │
│ Runtime Constant Pool  │    ┌──────┴───────────┐
│  - "minsub" 리터럴     │    │ Stack (스레드별)   │
└───────────────────────┘    │  user → Heap 주소  │
                              │  num = 5          │
                              │  args             │
                              ├──────────────────┤
                              │ PC Register       │
                              │ Native Method Stack│
                              └──────────────────┘
```

### 공유 vs 스레드별 정리

| 공유 영역 (모든 스레드가 함께 사용) | 스레드별 독립 영역 (각자 하나씩) |
|:-:|:-:|
| Method Area, Heap | Stack, PC Register, Native Method Stack |

> 면접 포인트: **공유 영역(Heap, Method Area)은 여러 스레드가 동시에 접근할 수 있어서 동시성 이슈가 발생할 수 있고, 스레드별 영역(Stack, PC Register, Native Method Stack)은 각 스레드 전용이라 안전합니다.** 이 점을 함께 언급하면 좋습니다.

---

## 면접 심화 정리

---

### 면접 용어 주의: 필드 vs 지역 변수

면접에서 혼용하면 감점될 수 있으니 구분해서 사용할 것.

| 용어 | 의미 | 저장 위치 | 예시 |
|---|---|---|---|
| **필드(field)** | 클래스에 선언된 멤버 변수 | Heap (인스턴스 변수) / Method Area (static 변수) | `private String name` |
| **지역 변수(local variable)** | 메서드 안에서 선언된 변수 | Stack | `int num = 5`, `User user = ...` |

---

### Runtime Constant Pool이란?

Method Area 안에 있는 **상수 저장소**. `.class` 파일의 Constant Pool이 클래스 로딩 시 여기에 올라온다.

| 저장되는 것 | 예시 |
|---|---|
| 리터럴 상수 | `"minsub"`, `10`, `5` |
| 클래스 참조 | `User` 클래스가 어디 있는지 |
| 메서드 참조 | `sayHello()`가 어디 있는지 |
| 필드 참조 | `name` 필드가 어디 있는지 |

> 바이트코드가 클래스/메서드/상수를 찾아가기 위한 **"주소록"** 역할.
> 예: `new User("minsub")` 실행 시 Runtime Constant Pool에서 User 클래스 참조를 찾고 → Method Area의 클래스 정보로 → Heap에 객체 생성

---

### Stack vs Heap 속도 비교

| | Stack | Heap |
|---|---|---|
| **할당 방식** | 스택 포인터만 이동 (push/pop) | 빈 공간을 탐색해서 할당 |
| **해제 방식** | 메서드 끝나면 자동 pop | GC가 돌아야 해제 |
| **구조** | 연속된 메모리 (LIFO) | 불규칙하게 흩어져 있음 |
| **속도** | **빠름** | 상대적으로 느림 |

> 면접 답변: "Stack은 포인터 이동만으로 할당/해제가 되어 빠르고, Heap은 동적 할당과 GC 비용이 있어서 상대적으로 느립니다."

---

### Heap의 Young / Old Generation과 GC

대부분의 객체는 생성 직후 금방 소멸된다 (**Weak Generational Hypothesis**).
그래서 세대를 나눠서 GC 효율을 높인다.

```
Young Generation
├── Eden       ← 객체가 처음 생성되는 곳
├── Survivor 0 ← Minor GC에서 살아남은 객체가 이동
└── Survivor 1 ← S0 ↔ S1 번갈아가며 이동

Old Generation  ← Young에서 여러 번 살아남은 객체가 승격(Promotion)
```

#### Minor GC vs Full GC

| | Minor GC | Full GC |
|---|---|---|
| **대상** | Young Generation만 | Heap 전체 (Young + Old) + Method Area |
| **발생 시점** | Eden이 꽉 찼을 때 | Old Generation이 꽉 찼을 때 |
| **속도** | 빠름 (수~수십 ms) | 느림 (수백 ms ~ 수 초) |
| **STW(Stop-The-World)** | 짧음 | **길다** → 서비스 응답 지연/장애 가능 |
| **빈도** | 자주 | 드물게 |

> **STW(Stop-The-World)**: GC 실행 중 모든 애플리케이션 스레드가 멈추는 현상.
> GC 튜닝의 목표는 **Full GC 빈도와 STW 시간을 최소화**하는 것.

---

### 공유 영역의 동시성 이슈

Heap은 모든 스레드가 공유하므로, 같은 객체의 필드를 동시에 수정하면 **데이터 정합성 문제**가 발생한다.

```java
public class Counter {
    private int count = 0; // Heap에 저장 (공유)

    public void increment() {
        count++; // 읽기 → 더하기 → 쓰기 (3단계)
    }
}
```

```
스레드 A: count 읽음 (0) → +1 → 저장 (1)
스레드 B: count 읽음 (0) → +1 → 저장 (1)  ← 같은 값을 읽어버림
기대값: 2, 실제값: 1
```

#### 해결 방법

| 방법               | 설명                                                         | 예시                              |
| ---------------- | ---------------------------------------------------------- | ------------------------------- |
| **synchronized** | 한 번에 한 스레드만 접근                                             | `synchronized void increment()` |
| **volatile**     | 캐시가 아닌 메인 메모리에서 직접 읽고/씀 (가시성만 보장, `count++` 같은 복합 연산에는 부족) | `volatile int count`            |
| **Atomic 클래스**   | CAS(Compare-And-Swap) 기반 락 없는 동기화                          | `AtomicInteger count`           |
| **Lock**         | synchronized보다 세밀한 제어                                      | `ReentrantLock`                 |

> 면접 답변: "Heap은 모든 스레드가 공유하므로 동시 수정 시 데이터 정합성 문제가 발생합니다. synchronized, Atomic 클래스, Lock 등으로 해결할 수 있고, Stack은 스레드별 독립이라 이런 문제가 없습니다."

---

### StackOverflowError vs OutOfMemoryError

| | StackOverflowError | OutOfMemoryError |
|---|---|---|
| **발생 영역** | Stack | Heap (또는 Metaspace) |
| **주요 원인** | 메서드 호출이 너무 깊게 쌓여 Stack이 꽉 찰 때 | 객체가 너무 많이 생성되어 Heap이 꽉 찰 때 |
| **실무 예시** | 무한 재귀, JPA/Jackson 순환 참조 | 페이징 없이 대량 데이터 조회, 메모리 누수 |

#### StackOverflowError 실무 발생 케이스

| 케이스 | 설명 |
|---|---|
| **재귀 탈출 조건 누락** | 가장 흔함. 재귀 호출이 끝나지 않음 |
| **엔티티 순환 참조** | JPA 양방향 연관관계에서 `toString()`, JSON 직렬화 시 무한 호출 |
| **Jackson/Lombok 순환** | `@Data`의 `toString()`이 양방향 연관관계를 타고 무한 호출 |
| **AOP 프록시 무한 루프** | 프록시가 자기 자신을 다시 호출하는 설정 실수 |

```java
// JPA 양방향 연관관계 순환 참조 예시
@Entity
public class User {
    @OneToMany(mappedBy = "user")
    private List<Order> orders; // User → Order
}

@Entity
public class Order {
    @ManyToOne
    private User user; // Order → User
}
// Jackson 직렬화 시: User → orders → Order → user → User → ... 무한루프
```

> 해결: `@JsonIgnore`, `@JsonManagedReference/@JsonBackReference`, 또는 **DTO로 변환**해서 응답

> 면접 답변: "StackOverflowError는 재귀 호출이 너무 깊어져 Stack이 꽉 찰 때 발생하고, OutOfMemoryError는 Heap에 객체가 과도하게 쌓일 때 발생합니다. 실무에서는 JPA 양방향 연관관계의 순환 참조나, 페이징 없이 대량 데이터를 조회하는 경우가 대표적입니다."
