# 📌 DFS (Depth First Search) — 깊이 우선 탐색

## 1. 개념

- **깊이 우선 탐색(DFS)**은 시작 노드에서 출발해 **한 경로를 끝까지** 탐색한 후, 더 이상 진행할 수 없으면 **이전 노드로 되돌아가** 다른 경로를 탐색하는 방식입니다.
- **되돌아가는(Backtracking)** 과정은 **마지막에 추가된 노드부터** 방문해야 하므로, 스택(Stack)의 **후입선출(LIFO)** 특성과 잘 맞습니다.
- 재귀 함수로 구현할 경우, 내부적으로 **호출 스택(Call Stack)**을 활용하게 됩니다.

---

## 2. DFS 구현 방식

DFS는 그래프 표현 방식에 따라 여러 형태로 구현할 수 있습니다.  
이번 예시는 **인접 행렬(Adjacency Matrix)** + **스택(Stack)** 방식입니다.

- **인접 행렬**
  - 2차원 배열로 노드 간 연결 여부 저장
  - 노드 수가 적거나 간선이 많은 경우 유리
  - 공간 복잡도: $O(N^2)$
- **인접 리스트**
  - 각 노드에 연결된 노드 목록만 저장
  - 노드 수가 많지만 간선이 적은 경우 유리
  - 공간 복잡도: $O(N + E)$

---

## 3. 코드 — 인접 행렬 + 스택

```python
import sys
sys.stdin = open('input.txt')

V, E = map(int, input().split())  # V: 정점 수, E: 간선 수
data = list(map(int, input().split()))

# 1. 인접 행렬 초기화 (정점 번호가 1부터 시작하므로 V+1 크기)
adj_matrix = [[0] * (V + 1) for _ in range(V + 1)]

# 2. 간선 정보 입력 (양방향 그래프)
for i in range(E):
    n1, n2 = data[i * 2], data[i * 2 + 1]
    adj_matrix[n1][n2] = 1
    adj_matrix[n2][n1] = 1

def DFS_stack(start):
    """
    스택을 활용한 DFS (깊이 우선 탐색)
    - start: 시작 노드 번호
    - visited: 방문 여부 확인용 리스트
    - adj_matrix: 인접 행렬
    """
    stack = [start]                # 스택 초기화
    visited = [0] * (V + 1)        # 방문 여부 기록

    while stack:
        current_node = stack.pop() # 스택의 마지막 노드 꺼내기

        if visited[current_node] == 0:  # 아직 방문 안한 경우
            visited[current_node] = 1   # 방문 처리
            print(current_node, end='') # 노드 출력

            # 인접 노드 탐색 (큰 번호부터 push하면 작은 번호가 먼저 pop됨)
            for next_node in range(V, 0, -1):
                if adj_matrix[current_node][next_node] == 1 and visited[next_node] == 0:
                    stack.append(next_node)

# DFS 실행 (시작 노드: 1)
DFS_stack(1)
