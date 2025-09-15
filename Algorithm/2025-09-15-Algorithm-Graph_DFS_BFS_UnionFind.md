# 📘 TIL - 그래프 · DFS · BFS · Union-Find

------------------------------------------------------------------------

## 🔹 그래프 기본

### 그래프란?

-   아이템(사물/개념)과 그 사이의 연결 관계를 표현하는 자료구조
-   **정점(Vertex)** + **간선(Edge)** 로 구성
-   선형 구조/트리로 표현하기 어려운 **N:N 관계**를 표현하기 용이
-   최대 간선 수: \|E\| = \|V\| \* (\|V\|-1) / 2

👉 예: 정점 5개일 때 최대 간선 = 10

### 그래프 유형

-   무향 그래프 (Undirected Graph)
-   유향 그래프 (Directed Graph)
-   가중치 그래프 (Weighted Graph)
-   DAG (Directed Acyclic Graph, 방향 비순환 그래프)
-   완전 그래프 (모든 정점이 연결)
-   부분 그래프 (일부 정점/간선만 포함)

### 인접 정점 / 경로

-   **인접(Adjacency)**: 간선이 존재하면 두 정점은 인접
-   **경로**: 간선을 순서대로 나열한 것 (예: 0-2-4-6)
-   **단순 경로**: 정점을 한 번만 지나는 경로
-   **사이클(Cycle)**: 시작 정점 == 끝 정점인 경로

------------------------------------------------------------------------

## 🔹 그래프 표현

### 1. 인접 행렬 (Adjacency Matrix)

-   \|V\| × \|V\| 크기 배열
-   연결 O → 1, 연결 X → 0
-   무향 그래프: i행의 합 = i열의 합 = 차수
-   유향 그래프: 행의 합 = 진출 차수 / 열의 합 = 진입 차수
-   장점: 구현 쉽고, 연결 여부 검색 O(1)
-   단점: 간선이 적으면 메모리 낭비

### 2. 인접 리스트 (Adjacency List)

-   각 정점에 인접한 정점을 리스트로 관리
-   장점: 메모리 효율적
-   단점: 특정 간선 검색 시 느림

### 3. 간선 배열

-   (시작, 끝) 쌍을 배열에 저장

------------------------------------------------------------------------

## 🔹 DFS (Depth First Search, 깊이 우선 탐색)

### 특징

-   재귀 호출 or 스택 이용
-   깊이 우선으로 탐색
-   모든 정점을 중복 없이 방문 가능

### DFS (재귀)

``` python
def dfs(node):
    print(node, end=' ')
    for next_node in graph[node]:
        if visited[next_node]:
            continue
        visited[next_node] = 1
        dfs(next_node)
```

### DFS (스택)

``` python
def dfs_iter(start):
    stack = [start]
    visited[start] = True
    while stack:
        v = stack.pop()
        print(v, end=' ')
        for w in graph[v]:
            if not visited[w]:
                stack.append(w)
                visited[w] = True
```

------------------------------------------------------------------------

## 🔹 BFS (Breadth First Search, 너비 우선 탐색)

### 특징

-   큐(Queue) 사용
-   가까운 정점부터 탐색

### BFS 코드

``` python
from collections import deque

def bfs(start):
    q = deque([start])
    visited[start] = 1
    while q:
        now = q.popleft()
        print(now, end=' ')
        for next_node in graph[now]:
            if visited[next_node]:
                continue
            visited[next_node] = 1
            q.append(next_node)
```

------------------------------------------------------------------------

## 🔹 Union-Find (서로소 집합, Disjoint Set)

### 개념

-   교집합이 없는 집합들 관리
-   각 집합에 **대표자(representative)** 존재
-   주요 연산:
    -   `Make-Set(x)` : 집합 생성
    -   `Find-Set(x)` : 집합 대표자 찾기
    -   `Union(x, y)` : 두 집합 합치기

### 기본 구현

``` python
def make_set(n):
    parents = [i for i in range(n+1)]
    ranks = [0] * (n+1)
    return parents, ranks

def find_set(x):
    if x != parents[x]:
        parents[x] = find_set(parents[x])  # Path Compression
    return parents[x]

def union(x, y):
    rep_x = find_set(x)
    rep_y = find_set(y)
    if rep_x == rep_y:
        return
    if ranks[rep_x] < ranks[rep_y]:
        parents[rep_x] = rep_y
    elif ranks[rep_x] > ranks[rep_y]:
        parents[rep_y] = rep_x
    else:
        parents[rep_y] = rep_x
        ranks[rep_x] += 1
```

### 최적화

-   **Path Compression**: 탐색 시 경로 단축
-   **Union by Rank**: 높이(rank)가 낮은 트리를 높은 트리에 병합

------------------------------------------------------------------------

## 📌 요약

-   **그래프 표현**: 인접 행렬(빠르지만 공간낭비), 인접 리스트(메모리
    효율)
-   **DFS**: 깊이 우선, 스택/재귀
-   **BFS**: 너비 우선, 큐
-   **Union-Find**: 상호배타 집합 관리 (사이클 탐지 등에서 활용)

------------------------------------------------------------------------
