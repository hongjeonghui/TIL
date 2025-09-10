# 🗓️ TIL — 분할 정복, 병합 정렬, 퀵 정렬, 이진 검색

> **분할 정복(Divide & Conquer)** 관점에서 **병합 정렬**, **퀵 정렬(호어/로무토)**, **이진 검색(반복/재귀)**을 정리 

---

## 📌 한눈에 보는 요약

| 알고리즘 | 핵심 아이디어 | 평균/최악 시간복잡도 | 추가 메모리 | 안정성(Stable) | 구현 난이도 | 비고 |
|---|---|---:|---:|:---:|:---:|---|
| **병합 정렬** | 반으로 쪼개고(재귀) → 정렬된 두 배열을 병합 | `O(N log N)` / `O(N log N)` | `O(N)` | ✅ | 중 | 큰 데이터에 일관된 성능, 외부정렬/병렬화에 유리 |
| **퀵 정렬(호어)** | 피벗 기준 양끝에서 좁혀오며 교환 | `O(N log N)` / `O(N^2)` | `O(1)` | ❌ | 중~상 | 평균적으로 매우 빠름(스왑 적음), 피벗 선택 주의 |
| **퀵 정렬(로무토)** | 오른쪽 피벗, 한 포인터 누적 구간 교환 | `O(N log N)` / `O(N^2)` | `O(1)` | ❌ | 하~중 | 구현 간단, 스왑 상대적으로 많을 수 있음 |
| **이진 검색** | 정렬 자료에서 범위를 절반씩 줄이며 탐색 | `O(log N)` / `O(log N)` | `O(1)` | - | 하 | **정렬 전제**가 필수, 범위/경계 처리 중요 |

---

## 🔧 분할 정복

- **Divide**: 문제를 더 작은 두(또는 그 이상) 하위 문제로 쪼갠다.  
- **Conquer**: 하위 문제를(대개 재귀로) 해결한다.  
- **Combine**: 필요하면 하위 해답을 합쳐 원래 문제의 해답을 만든다.  
→ 대표 예: **병합 정렬**, **퀵 정렬의 파티션-재귀**, **거듭제곱 분할정복**, **이진 검색**

---

## 1) 병합 정렬 (Merge Sort)

### 🧠 핵심
- 리스트를 **반으로 분할**하며 길이 1까지 재귀.
- **정렬된 두 리스트**를 **병합(merge)** 하며 올라오면 전체가 정렬된다.
- **안정 정렬(Stable)**, 언제나 `O(N log N)` 보장.  
- 단점: **임시 배열**로 `O(N)` 추가 메모리.

### ✅ 파이썬 구현
```python
# 1. 분할: 리스트의 길이가 1일 때까지 분할
# 2. 정복: 리스트의 길이가 1이 되면 자동으로 정렬됨
# 3. 병합
#   - 왼쪽, 오른쪽 리스트 중
#       작은 원소부터 정답 리스트에 추가하면서 진행
def merge(left, right):
    # 두 리스트를 병합한 결과 리스트
    result = [0] * (len(left) + len(right))
    l = r = 0

    # 두 리스트에서 비교할 대상이 남아있을 때 까지 반복
    while l < len(left) and r < len(right):
        if left[l] < right[r]:
            result[l + r] = left[l]
            l += 1
        else:
            result[l + r] = right[r]
            r += 1

    # 왼쪽 리스트에 남은 데이터들을 모두 result 에 추가
    while l < len(left):
        result[l + r] = left[l]
        l += 1

    # 오른쪽 리스트에 남은 데이터들을 모두 result 에 추가
    while r < len(right):
        result[l + r] = right[r]
        r += 1

    return result


def merge_sort(li):
    if len(li) == 1:
        return li

    # 1. 절반 씩 분할
    mid = len(li) // 2
    left = li[:mid]    # 리스트의 앞쪽 절반
    right = li[mid:]   # 리스트의 뒤쪽 절반

    left_list = merge_sort(left)
    right_list = merge_sort(right)

    # print(left_list, right_list)
    # 분할이 완료되면
    # 2. 병합
    merged_list = merge(left_list, right_list)
    return merged_list


arr = [69, 10, 30, 2, 16, 8, 31, 22]
sorted_arr = merge_sort(arr)
print(sorted_arr)

```

---

## 2) 퀵 정렬 (Quick Sort)

### 🧠 핵심
- **피벗**을 기준으로 **작은 그룹 / 큰 그룹**으로 **분할(Partition)**.
- 피벗의 **최종 위치가 확정**되며 왼쪽/오른쪽 부분 배열을 재귀 정렬.
- 평균 `O(N log N)`이지만, **최악 `O(N^2)`** → 피벗 선택(무작위, median-of-three 등)으로 회피.

### (A) 호어(Hoare) 파티션
```python
arr = [3, 2, 4, 6, 9, 1, 8, 7, 5]
# arr = [11, 45, 23, 81, 28, 34]
# arr = [11, 45, 22, 81, 23, 34, 99, 22, 17, 8]
# arr = [1, 1, 1, 1, 1, 0, 0, 0, 0, 0]


# 피벗: 제일 왼쪽 요소
# 이미 정렬된 배열이나 역순으로 정렬된 배열에서 최악의 성능을 보일 수 있음
def hoare_partition1(left, right):
    pivot = arr[left]  # 피벗을 제일 왼쪽 요소로 설정
    i = left + 1
    j = right

    while i <= j:  # 교차가 되면 끝
        while i <= j and arr[i] <= pivot:  # i 는 pivot 보다 큰 값을 검색 (작거나 같으면 i += 1)
            i += 1

        while i <= j and arr[j] >= pivot:  # j 는 pivot 보다 작은 값을 검색 (크거나 같으면 j -= 1)
            j -= 1

        if i < j:  # swap
            arr[i], arr[j] = arr[j], arr[i]

    # pivot 과 j 위치를 swap
    arr[left], arr[j] = arr[j], arr[left]
    return j


# 피벗: 제일 오른쪽 요소
# 이미 정렬된 배열이나 역순으로 정렬된 배열에서 최악의 성능을 보일 수 있음
def hoare_partition2(left, right):
    pivot = arr[right]  # 피벗을 제일 오른쪽 요소로 설정
    i = left
    j = right - 1

    while i <= j:
        while i <= j and arr[i] <= pivot:
            i += 1
        while i <= j and arr[j] >= pivot:
            j -= 1
        if i < j:
            arr[i], arr[j] = arr[j], arr[i]

    arr[i], arr[right] = arr[right], arr[i]
    return i


# 피벗: 중간 요소로 설정
# 일반적으로 더 균형 잡힌 분할이 가능하며, 퀵 정렬의 성능을 최적화할 수 있습니다.
def hoare_partition3(left, right):
    mid = (left + right) // 2
    pivot = arr[mid]  # 피벗을 중간 요소로 설정
    arr[left], arr[mid] = arr[mid], arr[left]  # 중간 요소를 왼쪽으로 이동 (필요 시)
    i = left + 1
    j = right

    while i <= j:
        while i <= j and arr[i] <= pivot:
            i += 1
        while i <= j and arr[j] >= pivot:
            j -= 1
        if i < j:
            arr[i], arr[j] = arr[j], arr[i]

    arr[left], arr[j] = arr[j], arr[left]
    return j


def quick_sort(left, right):
    if left < right:
        pivot = hoare_partition1(left, right)
        # pivot = hoare_partition2(left, right)
        # pivot = hoare_partition3(left, right)
        quick_sort(left, pivot - 1)
        quick_sort(pivot + 1, right)


quick_sort(0, len(arr) - 1)
print(arr)

```

### (B) 로무토(Lomuto) 파티션
```python
arr = [3, 2, 4, 6, 9, 1, 8, 7, 5]


def lomuto_partition(left, right):
    pivot = arr[right]

    i = left - 1
    for j in range(left, right):
        if arr[j] <= pivot:
            i += 1
            arr[i], arr[j] = arr[j], arr[i]

    arr[i + 1], arr[right] = arr[right], arr[i + 1]
    return i + 1


def quick_sort(left, right):
    if left < right:
        pivot = lomuto_partition(left, right)
        quick_sort(left, pivot - 1)
        quick_sort(pivot + 1, right)


quick_sort(0, len(arr) - 1)
print(arr)
```

---

## 3) 이진 검색 (Binary Search)

### 🧠 핵심 개념
- **정의**: 정렬된 자료에서 **중앙(mid)**을 기준으로 탐색 범위를 반씩 줄여가며 목표 값을 찾는 알고리즘.
- **전제 조건**: 반드시 **정렬된 배열**이어야 적용 가능.
- **시간 복잡도**:  
  - 평균/최악 모두 `O(log N)`  
  - 순차 검색(`O(N)`)보다 훨씬 빠름.
- **단점**: 삽입/삭제가 잦을 경우 정렬 상태 유지 비용이 커짐.

### 📖 탐색 과정
1. 배열의 **중앙 원소**를 확인.
2. 중앙 값과 목표 값을 비교.  
   - 목표 < 중앙 → 왼쪽 절반 탐색  
   - 목표 > 중앙 → 오른쪽 절반 탐색  
3. 찾을 때까지 ①~② 반복.  
4. `left > right` 되면 탐색 종료 (실패).


### (A) 반복문 방식
```python
def binary_search(arr, target):
    left, right = 0, len(arr) - 1
    while left <= right:
        mid = (left + right) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] > target:
            right = mid - 1
        else:
            left = mid + 1
    return -1

nums = [2, 4, 7, 9, 11, 19, 23]
print(binary_search(nums, 11))  # 4
print(binary_search(nums, 10))  # -1
```

### (B) 재귀 방식
```python
def binary_search_recursive(arr, left, right, target):
    if left > right:
        return -1
    mid = (left + right) // 2
    if arr[mid] == target:
        return mid
    if arr[mid] > target:
        return binary_search_recursive(arr, left, mid - 1, target)
    else:
        return binary_search_recursive(arr, mid + 1, right, target)

nums = [2, 4, 7, 9, 11, 19, 23]
print(binary_search_recursive(nums, 0, len(nums)-1, 9))  # 3
```

---

## ✍️ 분할 정복 알고리즘 정리
- **병합 정렬**: 안정 정렬 + 일관된 `O(N log N)` → 대용량/외부정렬 시 강함  
- **퀵 정렬**: 평균 성능 최고, 구현 시 **파티션 정확성**이 관건  
- **이진 검색**: 정렬 전제가 핵심, **경계 처리**가 생명


