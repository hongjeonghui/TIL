# 📘 TIL - 최소 비용 신장 트리(MST) & 최단 경로

---

## 🌲 최소 비용 신장 트리 (MST)

### 📌 정의
- **그래프에서 최소 비용 문제**
  - *모든 정점을 연결하는 간선*들의 가중치 합이 최소가 되는 트리
  - 두 정점 사이의 비용이 최소인 경로를 찾는 문제와도 연결
- **신장 트리**
  - N개의 정점과 N-1개의 간선으로 구성된 트리
- **최소 신장 트리 (MST, Minimum Spanning Tree)**
  - 무방향 가중치 그래프에서 간선들의 가중치 합이 최소인 신장 트리

---

### 📌 그래프 표현
- 인접행렬
- 인접리스트 (실무에서 많이 사용, 튜플 기반이 메모리 효율적)

---

### 📌 MST 알고리즘
1. **Prim 알고리즘**
   - "정점 기준" 접근  
   - 하나의 정점에서 시작 → 연결된 간선 중 최소 비용 선택 → 모든 정점 선택될 때까지 반복  
   - **완전 그래프**일수록 유리  
   - 우선순위 큐(heapq) 활용

   ```python
   from heapq import heappush, heappop

   def prim(start_node):
       pq = [(0, start_node)]  # (가중치, 노드)
       MST = [0] * V
       min_weight = 0

       while pq:
           weight, node = heappop(pq)
           if MST[node]:
               continue

           MST[node] = 1
           min_weight += weight

           for next_node in range(V):
               if graph[node][next_node] == 0:
                   continue
               if MST[next_node]:
                   continue
               heappush(pq, (graph[node][next_node], next_node))

       return min_weight
   ```

---

2. **Kruskal 알고리즘**
   - "간선 기준" 접근  
   - 모든 간선을 **가중치 오름차순 정렬** 후, 작은 것부터 선택  
   - 사이클이 생기면 제외  
   - **Union-Find** 자료구조 사용

   ```python
   def find_set(x):
       if x == parents[x]:
           return x
       parents[x] = find_set(parents[x])
       return parents[x]

   def union(x, y):
       rx, ry = find_set(x), find_set(y)
       if rx == ry: return
       if rx < ry:
           parents[ry] = rx
       else:
           parents[rx] = ry

   edges.sort(key=lambda x: x[2])  # (u,v,w)
   cnt, result = 0, 0
   parents = [i for i in range(V)]

   for u, v, w in edges:
       if find_set(u) != find_set(v):
           union(u, v)
           cnt += 1
           result += w
           if cnt == V - 1:
               break
   ```

---

## 🛣️ 최단 경로

### 📌 정의
- 간선 가중치가 있는 그래프에서 **두 정점 사이의 경로 중 가중치 합이 최소**인 경로
- 종류
  1. 하나의 시작 정점 → 다른 모든 정점
     - 다익스트라 (음수 가중치 ❌)
     - 벨만-포드 (음수 가중치 ⭕)
  2. 모든 정점 쌍 사이
     - 플로이드-워샬

---

### 📌 Dijkstra 알고리즘
- 시작 정점에서 **거리 최소인 정점**을 선택해 나가며 최단 경로 확정
- **탐욕적(Greedy) 알고리즘**
- **Prim 알고리즘과 유사**

```python
from heapq import heappop, heappush

def dijkstra(start_node):
    pq = [(0, start_node)]  # (거리, 노드)
    dists = [INF] * V
    dists[start_node] = 0

    while pq:
        dist, node = heappop(pq)
        if dists[node] < dist:
            continue
        for next_dist, next_node in graph[node]:
            new_dist = dist + next_dist
            if dists[next_node] <= new_dist:
                continue
            dists[next_node] = new_dist
            heappush(pq, (new_dist, next_node))
    return dists
```

---

## ✅ 정리
1. **MST**
   - 모든 정점을 연결하면서 간선 가중치 합이 최소인 트리
   - Prim (정점 기준) ↔ Kruskal (간선 기준)
2. **최단 경로**
   - 다익스트라: 음수 가중치 ❌, 탐욕 기법
   - 벨만-포드: 음수 가중치 ⭕
   - 플로이드-워샬: 모든 쌍 최단 경로

---
