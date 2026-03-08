
```table-of-contents
```
### Java 실행 흐름

---

```
.java → (javac) → .class → ClassLoader → Runtime Data Area → Execution Engine → 실행
```

### 1. 컴파일 단계

---
- 개발자가 작성한 `.java` 소스 파일을 **javac(Java Compiler)** 가 **바이트코드(.class)** 로 컴파일합니다.
- 바이트코드는 OS에 독립적인 중간 코드이며, JVM이 해석할 수 있는 명령어 집합입니다.

### 2. Class Loader (클래스 로더)

---
- `.class` 파일을 JVM 메모리(Runtime Data Area)에 적재하는 역할을 합니다.
- 3단계 과정
	- **Loading**: 클래스 파일을 읽어 바이너리 데이터를 Method Area에 저장
	- **Linking**
		- Verification: 바이트코드가 JVM 스펙에 맞는지 검증 (설계도 규격 검수)
		- Preparation: static 변수에 **기본값** 할당 (`int → 0`, `Object → null`)
		- Resolution: 심볼릭 참조를 **실제 메모리 주소**로 변환 (이름표 → 실제 주소)
	- **Initialization**: static 변수에 **실제 값** 할당, static 블록 실행

#### Class Loader 3단계 예시

```java
public class Main {
    static int COUNT = 10;
}
```

```
Loading:        Main.class를 읽어서 Method Area에 저장
Linking:
  Verification:   바이트코드 규격 검증
  Preparation:    COUNT = 0 (기본값)
  Resolution:     심볼릭 참조 → 실제 주소
Initialization:   COUNT = 10 (실제 값 할당)
```

> 면접 답변: "Class Loader는 Loading, Linking, Initialization 3단계를 거칩니다. Loading에서 .class를 읽고, Linking에서 검증/기본값 할당/참조 변환을 하고, Initialization에서 static 변수에 실제 값을 할당합니다."

---

#### Parent Delegation Model (위임 모델)

클래스 로딩 요청이 오면 자신이 직접 로딩하지 않고 **부모에게 먼저 위임**하는 구조.

```
Bootstrap Class Loader     ← 최상위 (Java 핵심 클래스: java.lang.String 등)
       ↑ 위임
Extension Class Loader     ← 확장 라이브러리 (javax.*, jdk.* 등)
       ↑ 위임
Application Class Loader   ← 우리가 작성한 코드 (.class)
```

```
동작 예시: Main.class 로딩
1) Application: "부모한테 먼저 물어볼게" → 위임
2) Extension: "나도 모르겠어, 부모한테 물어볼게" → 위임
3) Bootstrap: "나도 없어" → 못 찾음
4) Extension: "나한테도 없어" → 못 찾음
5) Application: "그럼 내가 로딩할게!" → Main.class 로딩
```

| 이유 | 설명 |
|---|---|
| **클래스 중복 로딩 방지** | 부모가 이미 로딩한 클래스는 다시 안 로딩 |
| **핵심 클래스 보호** | 누군가 `java.lang.String`을 위조해도, Bootstrap이 먼저 진짜를 로딩하므로 무시됨 |

> 면접 답변: "Class Loader는 Parent Delegation Model을 사용합니다. 클래스 로딩 요청이 오면 부모에게 먼저 위임하여, 핵심 클래스의 위조를 방지하고 중복 로딩을 막습니다."

### 3. Runtime Data Area

---
- 클래스 로더가 적재한 데이터가 **Method Area, Heap, Stack, PC Register, Native Method Stack**에 배치됩니다.
- (상세 구조는 "JVM의 메모리 구조" 문항 참고)

### 4. Execution Engine (실행 엔진)

---
- 바이트코드를 실제 기계어로 변환하여 실행합니다.
- 두 가지 방식을 혼합 사용 (**하이브리드 방식**)
	- **Interpreter**: 바이트코드를 한 줄씩 해석하여 실행. 시작은 빠르지만 반복 실행 시 느림
	- **JIT Compiler (Just-In-Time)**: 자주 실행되는 코드(핫스팟)를 감지하여 한꺼번에 네이티브 코드로 컴파일 후 캐싱. 반복 실행 시 빠름

#### Interpreter + JIT Compiler 동작 흐름

```
바이트코드 실행 시작
       │
       ▼
  Interpreter가 한 줄씩 해석하며 실행
       │
       ▼
  JVM이 "이 코드 자주 실행되네?" 감지 (핫스팟 감지)
       │
       ▼
  JIT Compiler가 해당 코드를 통째로 네이티브 코드(기계어)로 컴파일
       │
       ▼
  다음부터는 컴파일된 네이티브 코드를 바로 실행 (캐싱)
```

| | Interpreter | JIT Compiler |
|---|---|---|
| **비유** | 통역사가 한 문장씩 실시간 통역 | 자주 쓰는 문장을 미리 번역본으로 만들어 놓음 |
| **장점** | 바로 시작할 수 있음 (준비 시간 없음) | 반복 실행 시 매우 빠름 |
| **단점** | 같은 코드도 매번 다시 해석 | 처음 컴파일하는 데 시간이 걸림 |

> 왜 처음부터 전부 JIT로 안 하는가? → 모든 코드를 컴파일하면 시작 시간이 너무 오래 걸리고, 한 번만 실행되는 코드는 컴파일 비용이 오히려 낭비. **자주 쓰이는 것만 골라서** JIT로 컴파일하는 게 효율적이다.

- **GC(Garbage Collector)**: Execution Engine의 일부로, Heap 영역에서 참조되지 않는 객체를 탐지하고 메모리를 회수합니다. (상세는 GC 문항 참고)

### 5. JNI & Native Method Library

---
- **JNI(Java Native Interface)**: Java에서 C/C++ 등 네이티브 코드를 호출하기 위한 인터페이스
- **Native Method Library**: 실행에 필요한 네이티브 라이브러리 (OS 종속적)

### 전체 흐름 요약

---
1. `.java` → javac로 **컴파일** → `.class` (바이트코드)
2. **Class Loader**가 `.class`를 JVM 메모리에 **로딩 → 링킹 → 초기화**
3. **Runtime Data Area**에 클래스/객체 정보 배치
4. **Execution Engine**이 Interpreter + JIT Compiler로 바이트코드를 **기계어로 변환하여 실행**
5. 실행 중 GC가 Heap 메모리를 자동 관리

> 면접 포인트: "Java는 컴파일과 인터프리터 방식을 모두 사용하는 하이브리드 방식이다. javac가 바이트코드로 컴파일하고, JVM의 실행 엔진이 인터프리터 + JIT로 실행한다"는 점을 명확히 설명하면 좋습니다.
