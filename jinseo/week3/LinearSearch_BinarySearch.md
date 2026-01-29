# [알고리즘] 선형 탐색 & 이진 탐색 (Java)

## 1. 선형 탐색 (Linear Search)

데이터가 저장된 배열을 처음부터 끝까지 순차적으로 하나씩 확인하며 원하는 값을 찾는 방식입니다.

* **특징**: 데이터의 정렬 여부와 상관없이 사용할 수 있습니다.
* **시간 복잡도**: ****
* **Java 코드**:

```java
public int linearSearch(int[] arr, int target) {
    for (int i = 0; i < arr.length; i++) {
        if (arr[i] == target) {
            return i; // 찾은 경우 해당 인덱스 반환
        }
    }
    return -1; // 찾지 못한 경우
}

```

---

## 2. 이진 탐색 (Binary Search)

정렬되어 있는 리스트에서 탐색 범위를 절반씩 좁혀가며 데이터를 찾는 방식입니다.

* **핵심 전제**: **데이터가 반드시 정렬되어 있어야 함.**
* **동작 원리**: 시작(Start), 끝(End), 중간(Mid) 포인터를 사용하여 중간값이 찾으려는 값보다 크면 끝을 내리고, 작으면 시작을 올립니다.
* **시간 복잡도**: **** (매 단계마다 범위가 절반으로 줄어듦)

### 🔹 Java 구현 (반복문 방식 - 추천)

코딩 테스트에서는 재귀 방식보다 스택 오버플로우 위험이 없는 반복문 방식을 더 선호합니다.

```java
public int binarySearch(int[] arr, int target) {
    int start = 0;
    int end = arr.length - 1;

    while (start <= end) {
        int mid = start + (end - start) / 2; // (start + end) / 2 시 오버플로우 방지

        if (arr[mid] == target) {
            return mid;
        } else if (arr[mid] > target) {
            end = mid - 1;
        } else {
            start = mid + 1;
        }
    }
    return -1;
}

```

---

## 3. 선형 탐색 vs 이진 탐색 비교

| 비교 항목 | 선형 탐색 (Linear) | 이진 탐색 (Binary) |
| --- | --- | --- |
| **정렬 필요 여부** | 필요 없음 | **반드시 정렬되어야 함** |
| **시간 복잡도** |  |  |
| **데이터 크기** | 소규모 데이터에 적합 | 대규모 데이터에 매우 효율적 |
| **구현 난이도** | 매우 쉬움 | 보통 (범위 설정 주의) |

---

## 4. 코딩 테스트 실전 팁

### ① `Arrays.binarySearch()` 라이브러리 활용

Java에서는 이미 구현된 표준 라이브러리를 제공합니다. (직접 구현할 필요가 없는 문제에서 사용)

```java
import java.util.Arrays;

int index = Arrays.binarySearch(arr, target);

```

### ② 이진 탐색의 응용: 파라메트릭 서치 (Parametric Search)

코딩 테스트에서 이진 탐색은 단순히 "숫자 찾기"로 나오지 않습니다. **"최적화 문제(최댓값/최솟값 찾기)"를 "결정 문제(Yes/No)"로 바꾸어 해결**할 때 주로 사용됩니다.

* 예: "나무 M미터를 얻기 위한 절단기 설정 높이의 최댓값은?"
* 예: "N명을 심사하는 데 걸리는 최소 시간은?"

### ③ 무한 루프 주의 (탈출 조건)

이진 탐색 구현 시 `start <= end` 조건과 `mid - 1`, `mid + 1` 처리를 정확히 하지 않으면 무한 루프에 빠질 수 있습니다. 반드시 중간값 계산 시 인덱스 범위를 체크하세요.

---