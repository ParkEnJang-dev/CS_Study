# [자료구조] LinkedList 완벽 정리

## 1. 개요 및 구조

LinkedList는 각 데이터가 **노드(Node)**라는 객체에 담기며, 각 노드는 **데이터**와 **다음 노드에 대한 참조(주소)**를 포함하는 선형 자료구조입니다.

* **노드(Node)**: 데이터 필드와 링크 필드(포인터)로 구성된 기본 단위입니다.
* **헤드(Head)**: 리스트의 첫 번째 노드를 가리킵니다.

---

## 2. 주요 특징

| 특징 | 내용 |
| --- | --- |
| **메모리 할당** | 실행 시점에 동적으로 할당 (Dynamic Allocation) |
| **접근 속도** | 특정 인덱스 접근 시 첫 노드부터 탐색해야 하므로  |
| **삽입/삭제** | 포인터 변경만으로 가능하여  (단, 해당 위치를 찾은 후 기준) |
| **연속성** | 메모리상에 물리적으로 흩어져 존재함 |

---

## 3. Java 구현 (Singly LinkedList)

코딩 테스트에서는 직접 노드 구조를 정의하여 문제를 푸는 경우가 많습니다.

```java
class Node {
    int data;
    Node next;

    Node(int data) {
        this.data = data;
        this.next = null;
    }
}

class LinkedList {
    Node head;

    // 맨 뒤에 노드 추가
    public void append(int data) {
        if (head == null) {
            head = new Node(data);
            return;
        }
        Node curr = head;
        while (curr.next != null) {
            curr = curr.next;
        }
        curr.next = new Node(data);
    }

    // 데이터 삭제
    public void delete(int data) {
        if (head == null) return;
        
        if (head.data == data) {
            head = head.next;
            return;
        }

        Node curr = head;
        while (curr.next != null && curr.next.data != data) {
            curr = curr.next;
        }

        if (curr.next != null) {
            curr.next = curr.next.next;
        }
    }
}

```

---

## 4. 종류 및 비교

1. **Singly LinkedList (단일)**: 다음 노드만 가리킴.
2. **Doubly LinkedList (이중)**: 이전 노드와 다음 노드를 모두 가리킴. 양방향 탐색 가능.
3. **Circular LinkedList (원형)**: 마지막 노드가 다시 첫 번째 노드를 가리킴.

---

## 5. 코딩 테스트 실전 팁 (Java)

### ① 더미 노드 (Dummy Node) 활용

리스트의 맨 앞(Head)에 노드를 추가하거나 삭제할 때 `if (head == null)` 같은 예외 처리를 줄이기 위해 사용합니다.

```java
Node dummy = new Node(0);
dummy.next = head;
// 이후 로직 수행 후 dummy.next를 반환

```

### ② Runner(Fast & Slow Pointer) 기법

두 개의 포인터를 각각 다른 속도(예: 1칸, 2칸)로 이동시키는 기법입니다.

* **중간 노드 찾기**: 빠른 포인터가 끝에 도달할 때 느린 포인터는 중간에 위치합니다.
* **사이클 탐지**: 리스트에 순환 구조가 있다면 두 포인터는 결국 만납니다.

### ③ 시간 복잡도 주의

`list.get(i)`를 반복문 안에서 호출하면 매번 처음부터 탐색하므로 전체 시간 복잡도가 $O(N^2)$이 됩니다. 탐색이 빈번하다면 `ArrayList`를 고려하거나 `Iterator`를 사용해야 합니다.

---

## 6. 예상 질문 (Q&A)

**Q: Array와 LinkedList의 가장 큰 차이점은?**

> **A:** 배열은 인덱스로 즉시 접근()이 가능하지만 크기가 고정적입니다. 연결 리스트는 삽입/삭제가 유연하고 크기가 가변적이지만, 탐색 속도가 느립니다().

**Q: 언제 LinkedList를 사용하는 것이 유리할까요?**

> **A:** 데이터의 추가와 삭제가 빈번하게 일어나고, 데이터의 총 개수를 예측하기 어려울 때 유리합니다.
