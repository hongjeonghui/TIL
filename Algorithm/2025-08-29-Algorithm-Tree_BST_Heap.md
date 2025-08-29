# 🌳 TIL: 트리 순회 · 이진 탐색 트리(BST) · 힙(Heap) 

---

## 1) 연습문제: 전위 순회 (Pre-order Traversal) ✍️

전위 순회: **루트 → 왼쪽 → 오른쪽**

### 아이디어
- 간선 정보를 `(부모 p, 자식 c)` 쌍으로 받아 `left[]`, `right[]`, `par[]` 배열을 구성
- `par[i] == 0`인 노드가 **루트**
- 전위 순회 함수 `pre_order(T)`를 재귀로 구현

### 구현 (배열 기반 이진트리)
```python
def pre_order(T):
    if T > 0:
        print(T, end=' ')        # visited(T)
        pre_order(left[T])       # pre_order(T.left)
        pre_order(right[T])      # pre_order(T.right)

# 입력 예시
# V = 정점 수 (1~V 번호)
# arr = (p1 c1 p2 c2 ... p(E) c(E)) (E = V-1)
V = int(input().strip())
E = V - 1
arr = list(map(int, input().split()))

left  = [0] * (V + 1)
right = [0] * (V + 1)
par   = [0] * (V + 1)

for i in range(E):
    p, c = arr[i*2], arr[i*2 + 1]
    par[c] = p
    if left[p] == 0:
        left[p] = c
    else:
        right[p] = c

root = 1
for i in range(1, V + 1):
    if par[i] == 0:  # 부모가 없음 → 루트
        root = i
        break

pre_order(root)
print()
```

---

## 2) 이진 탐색 트리 (BST: Binary Search Tree) 🔎

### 정의
- **정렬 규칙**: `왼쪽 서브트리 < 루트 < 오른쪽 서브트리`
- 모든 노드는 **서로 다른 유일 키**
- **중위(in-order) 순회** 결과가 **오름차순** 정렬

### 탐색(검색) 알고리즘
1. 루트에서 시작
2. 찾을 값 `x`와 현재 노드 키 비교  
   - `x == key` → 성공  
   - `x < key` → **왼쪽**으로 진행  
   - `x > key` → **오른쪽**으로 진행  
3. `None`(없음)까지 내려가면 실패

### 삽입 알고리즘
1. **탐색**으로 내려가며 들어갈 위치 찾기
2. 실패가 결정된 그 지점에 새 노드 삽입
> **동일 키는 삽입 불가** (문제에서 "모든 원소는 유일 키" 조건)

### 삭제 알고리즘 (세 경우)
1. **자식 0개(리프)**: 그냥 제거
2. **자식 1개**: 자식을 부모에 **직접 연결**
3. **자식 2개**:  
   - **대체 노드**를 선택: (1) 오른쪽 서브트리의 **최솟값** or (2) 왼쪽 서브트리의 **최댓값**
   - 현재 노드 값을 대체 노드 값으로 바꾸고, **대체 노드**를 (1) 또는 (2)의 규칙으로 삭제

### 복잡도 비교표
| 구조/알고리즘 | 탐색 | 삽입 | 삭제 | 비고 |
|---|---:|---:|---:|---|
| 배열(순차 검색) | O(N) | - | - | 정렬 X |
| 정렬된 배열 + 이진탐색 | **O(log N)** | O(N) | O(N) | 삽입/삭제 비용 큼 |
| **BST (평균)** | **O(log N)** | **O(log N)** | **O(log N)** | 균형일 때 |
| **BST (최악)** | O(N) | O(N) | O(N) | 한쪽으로 기움 |
| 해시 | **O(1)** | **O(1)** | **O(1)** | 추가 메모리 필요 |

### 파이썬 간단 구현 
```python
class Node:
    __slots__ = ("key", "left", "right")
    def __init__(self, key):
        self.key = key
        self.left = None
        self.right = None

def bst_insert(root, key):
    if root is None:
        return Node(key)
    if key < root.key:
        root.left = bst_insert(root.left, key)
    elif key > root.key:
        root.right = bst_insert(root.right, key)
    return root  # 동일 키는 무시

def bst_search(root, key):
    if root is None or root.key == key:
        return root
    return bst_search(root.left, key) if key < root.key else bst_search(root.right, key)

def inorder(root):
    if root:
        inorder(root.left)
        print(root.key, end=' ')
        inorder(root.right)

# 테스트
nums = [8, 4, 12, 2, 6, 10, 14, 5]
root = None
for x in nums:
    root = bst_insert(root, x)
inorder(root)  # 2 4 5 6 8 10 12 14
print()
```

---

## 3) 힙 (Heap) 

### 정의
- **완전 이진 트리** 기반의 **우선순위** 자료구조
- **최대 힙**: 부모 ≥ 자식 → **루트 = 최대값**
- **최소 힙**: 부모 ≤ 자식 → **루트 = 최소값**

### 연산 원리
- **삽입(enq)**: 맨 마지막 자리에 넣고 **부모와 비교하며 위로(상향) 교환(Heapify-up)**
- **삭제(deq)**: **루트 제거 → 마지막 원소를 루트로 올림 → 자식과 비교하며 아래로(하향) 교환(Heapify-down)**  
  (힙에서는 **루트만 삭제** 가능)

### 복잡도
- 삽입/삭제: **O(log N)** (트리 높이만큼 이동)
- 최댓값/최솟값 조회: **O(1)** (루트 참조)

---

## 4) 실습: 배열로 구현한 **최대 힙** (1번 인덱스부터 사용)


```python
# 최대 힙 구현 (배열 인덱스 1부터 사용)
heap = [0] * 101  # 충분한 크기
last = 0          # 힙에 들어있는 마지막 인덱스

def enq(n):
    global last
    last += 1
    heap[last] = n
    c = last
    p = c // 2
    # 상향 이동 (부모 < 자식이면 교환) → 최대 힙
    while p > 0 and heap[p] < heap[c]:
        heap[p], heap[c] = heap[c], heap[p]
        c = p
        p = c // 2

def deq():
    global last
    if last == 0:
        raise IndexError("deq from empty heap")
    # 루트 백업
    root_val = heap[1]
    # 마지막 노드를 루트로 올리고 last 감소
    heap[1] = heap[last]
    last -= 1

    # 하향 이동 (부모 < 더 큰 자식 → 교환)
    p = 1
    c = p * 2
    while c <= last:
        # 오른쪽 자식이 존재하고 더 크면 오른쪽으로
        if c + 1 <= last and heap[c] < heap[c + 1]:
            c += 1
        # 자식이 부모보다 크면 교환
        if heap[p] < heap[c]:
            heap[p], heap[c] = heap[c], heap[p]
            p = c
            c = p * 2
        else:
            break
    return root_val

# 사용 예시
for x in [2, 5, 7, 3, 4, 6]:
    enq(x)

print("힙 내부(1~last):", heap[1:last+1])  # level-order 상태 확인

# 최댓값부터 꺼내기
popped = []
while last > 0:
    popped.append(deq())
print("꺼낸 순서(내림차순):", popped)  # [7, 6, 5, 4, 3, 2]
```

---

## 5) BST vs 리스트 vs 힙: 언제 무엇을 쓰나? 🤔

- **정렬 유지 + 빠른 검색**이 필요하면 → **BST(또는 균형 트리/트립/레드블랙/AVL)**  
  - 중위순회로 **정렬된 순회**가 필요할 때 특히 유리
- **최댓값/최솟값을 자주 꺼냄** → **힙**  
  - `k`번째 큰 값 유형, 우선순위 큐, 스케줄링
- **단순 저장/전체 순회** → **리스트**  
  - 삽입/삭제 위치 제약 적고 구현 간단

---

## 6) 자주 하는 실수 & 디버깅 포인트 

- 전위순회 연습 코드
  - `root`를 찾았으면 **반드시 `pre_order(root)` 호출**해야 출력됨
  - `left[p]`가 비어있을 때만 왼쪽에 배치, 아니면 오른쪽으로 (현재 로직 OK)
- 힙 `deq()` 구현
  - `last = -1` ❌ → **`last -= 1`**  
  - 스왑 오타: `heap[p], heap[c] = heap[c]. heap[p]` ❌ → **`,`로 스왑**  
  - while 조건에서 **경계 체크** (`c <= last`, `c+1 <= last`) 필수
- BST
  - **중복 키** 허용 안 함(문제 정의)
  - 최악의 경우(편향 트리) 성능 저하 → **균형 기법** 고려

---

## 7) 핵심 요약 

- **전위 순회**: 루트-왼-오 → 재귀 기본형 꼭 숙지
- **BST**: 평균 **O(log N)**, 중위 순회 = 오름차순, **삭제 3케이스** 필수 암기
- **힙**: 완전 이진 트리, **루트가 최댓/최솟**, 삽입/삭제 **O(log N)**

## 8) 정리

1. **트리(Tree)**: 비선형 자료구조, 그래프의 일종. 노드들 간 계층 관계를 표현  
2. **이진 트리(Binary Tree)**: 노드가 최대 2개 자식, 완전·포화·편향 등 형태  
3. **트리 순회(Traversal)**: 전위·중위·후위 등, 모든 노드를 빠짐없이 방문  
4. **이진 트리 표현**: 배열 인덱스 방식 / 연결 리스트 방식  
5. **이진 탐색 트리(BST)**: 왼쪽 < 루트 < 오른쪽, 중위 순회 시 오름차순, 탐색/삽입/삭제 평균 $O(\log n)$  
6. **힙(Heap)**: 완전 이진 트리에서 최대/최소값을 효율적으로 추출, 우선순위 큐 구현에 활용  


