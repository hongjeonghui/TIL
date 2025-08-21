# 🌳 BFS (Breadth First Search)

## 1. 개념

- **너비 우선 탐색(BFS)**  
  시작 노드에서 가까운 노드부터 방문하고, 그 다음 한 단계 더 멀리 있는 노드를 순서대로 탐색하는 방식  
- **큐(Queue)의 FIFO(선입선출)** 특성과 잘 맞음  
- 탐색 과정: 노드를 방문하면 인접한 모든 노드를 큐에 넣고, 먼저 들어온 순서대로 꺼내며 진행

---

## 2. BFS 구현 방식

BFS는 보통 **인접 행렬(Adjacency Matrix)** 또는 **인접 리스트(Adjacency List)**를 사용해 **큐**로 구현한다.

### 예시 입력
```python
7 8
1 2 1 3 2 4 2 5 4 6 5 6 6 7 3 7
```

- 총 8개의 간선: (1,2), (1,3), (2,4), (2,5), (4,6), (5,6), (6,7), (3,7)  
- BFS 시작 노드가 1일 때 탐색 결과: **1234576**

---

### 2.1 인접 행렬 + 큐
```python
import sys

sys.stdin = open('input.txt')

V, E = map(int, input().split())
data = list(map(int, input().split()))

# 인접 행렬 생성
adj_matrix = [[0] * (V + 1) for _ in range(V + 1)]

# 간선 정보를 인접 행렬에 기록 (무방향 그래프)
for i in range(E):
    n1, n2 = data[2 * i], data[2 * i + 1]
    adj_matrix[n1][n2] = 1
    adj_matrix[n2][n1] = 1


def BFS_matrix(start):
    visited = [0] * (V + 1)  # 방문 여부 리스트
    q = []  # 큐 초기화

    # 시작 노드 방문 처리 후 큐에 삽입 (enqueue)
    # 작성 순서는 상관 없지만 ‘방문 처리 → 큐에 삽입’ 순서가 조금 더 자연스러움
    # “큐에 넣었다”는 것은 “이 노드를 이미 발견(visited)해서 다음에 처리할 예정”이라는 의미
    visited[start] = 1
    q.append(start)

    while q:
        current_node = q.pop(0)  # 리스트 맨 앞에서 원소 제거 (dequeue)
        print(current_node, end=' ')

        # current_node와 인접한 노드 탐색
        for next_node in range(1, V + 1):
            # 연결되어 있고, 아직 방문 안 했다면 => 방문 처리 후 큐에 삽입 (enqueue)
            if (
                adj_matrix[current_node][next_node] == 1
                and visited[next_node] == 0
            ):
                visited[next_node] = 1
                q.append(next_node)


# BFS 실행
BFS_matrix(1)
```

- **특징**
  - `pop(0)` 연산은 리스트 전체를 이동 → 시간복잡도 **O(N)**
  - 작은 문제엔 괜찮지만 큰 그래프에는 비효율적

---

### 2.2 인접 리스트 + 큐
```python
import sys

sys.stdin = open('input.txt')

V, E = map(int, input().split())
data = list(map(int, input().split()))

# 인접 리스트 생성
adj_list = [[] for _ in range(V + 1)]

# 간선 정보를 인접 리스트에 기록 (무방향 그래프)
for i in range(E):
    n1, n2 = data[2 * i], data[2 * i + 1]
    adj_list[n1].append(n2)
    adj_list[n2].append(n1)


# 방문 시, 작은 번호부터 접근하기 위해 각 간선정보 리스트를 오름차순 정렬
for i in range(1, V + 1):
    adj_list[i].sort()


def BFS_list(start):
    visited = [0] * (V + 1)  # 방문 여부 리스트
    q = []  # 큐 초기화

    # 초기 설정: 시작 노드 방문 후 큐에 삽입
    visited[start] = 1
    q.append(start)

    while q:
        current_node = q.pop(0)  # 리스트 맨 앞에서 원소 제거
        print(current_node, end=' ')

        # current_node와 인접한 노드 탐색
        for next_node in adj_list[current_node]:
            # 방문 안 했다면 => 방문 후 큐에 삽입
            if visited[next_node] == 0:
                visited[next_node] = 1
                q.append(next_node)


# BFS 실행
BFS_list(1)

```

- **특징**
  - 각 노드의 인접 노드를 리스트로 저장
  - 정렬하면 작은 번호부터 방문 가능
  - 리스트 큐 사용 → `pop(0)`의 비효율 존재

---

### 2.3 Deque 활용 (효율적인 큐)
```python
from collections import deque

def BFS_matrix(start):
    visited = [0]*(V+1)
    q = deque()

    visited[start] = 1
    q.append(start)

    while q:
        current = q.popleft()
        print(current, end='')
        for next_node in range(1, V+1):
            if adj_matrix[current][next_node] and not visited[next_node]:
                visited[next_node] = 1
                q.append(next_node)
```

- **`deque`**를 사용하면 `popleft()`가 **O(1)**이라 효율적

---

## 3. BFS 알고리즘 핵심 순서

1. 시작 노드를 방문 처리 후 큐에 삽입
2. 큐가 빌 때까지 반복
   - 큐에서 노드를 꺼냄
   - 인접 노드 중 방문하지 않은 노드를 방문 처리 후 큐에 삽입
3. 큐가 공백이면 탐색 종료

---

## 4. DFS와의 비교

| 구분 | DFS | BFS |
|------|-----|-----|
| 탐색 방식 | 깊이 우선 | 너비 우선 |
| 자료구조 | 스택(Stack), 재귀 | 큐(Queue) |
| 특징 | 한 경로 끝까지 탐색 후 백트래킹 | 가까운 노드부터 레벨 순 탐색 |

---

## 5. 마무리

- **BFS 핵심**: 큐에 넣은 순서대로 꺼내며, 방문하지 않은 인접 노드를 차례로 방문  
- **DFS ↔ 스택(재귀)**, **BFS ↔ 큐**가 가장 자연스럽고 효율적인 구현 방식  
