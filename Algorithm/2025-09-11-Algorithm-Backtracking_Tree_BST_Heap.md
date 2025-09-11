# 📘 TIL - 백트래킹 · 트리 · BST · 힙


------------------------------------------------------------------------

## 🔙 백트래킹 (Backtracking)

### 개념 & 포인트

-   여러 **선택지** 중 하나를 고르고, 해가 아니면 **되돌아가서(백트랙)**
    다른 선택을 시도.
-   **Pruning(가지치기)** 로 해가 될 가능성이 없는 경로를 조기에 차단 →
    DFS 대비 탐색 공간 축소.
-   최악의 경우 여전히 지수 시간일 수 있음.

### N-Queens

-   8-Queens 실제 해: **92개**. 후보는 매우 많지만, **세로/대각선
    충돌**을 조건으로 가지치기.
-   4-Queens를 통해 개념 축소 및 상태공간트리로 이해.

#### 풀이 ①: 2차원 보드 + 검사 함수

``` python
def check(row, col):
    # 1. 같은 열에 놓은 적이 있는가?
    for i in range(row):
        if visited[i][col]:
            return False

    # 2. 좌상단 대각선에 놓은 적이 있는가? (\)
    i, j = row - 1, col - 1
    while i >= 0 and j >= 0:
        if visited[i][j]:
            return False

        i -= 1
        j -= 1

    # [참고] for문으로 하고싶다 !
    # for i, j in zip(range(row-1, -1, -1), range(col-1, -1, -1)):
    #     if visited[i][j]:
    #         return False


    # 3. 우상단 대각선에 놓은 적이 있는가? (/)
    i, j = row - 1, col + 1
    while i >= 0 and j < N:
        if visited[i][j]:
            return False

        i -= 1
        j += 1

    return True

# 종료 조건: N개의 행을 모두 고려하면 종료
# 가지의 수: N개의 열
def recur(row):
    global answer

    if row == N:
        answer += 1
        return

    for col in range(N):
        # 가지치기 : 같은 열을 못 고르도록
        #  --> 유망하지 않은 케이스를 모두 삭제 (세로, 대각선)
        if check(row, col) is False:
            continue

        # col을 선택했다
        visited[row][col] = 1
        recur(row + 1)
        visited[row][col] = 0

N = 4
answer = 0  # 가능한 정답 수
visited = [[0] * N for _ in range(N)]
recur(0)
print(f'N = {N} / answer = {answer}')

N = 20
answer = 0  # 가능한 정답 수
visited = [[0] * N for _ in range(N)]
recur(0)
print(f'N = {N} / answer = {answer}')


```

#### 풀이 ②: 1차원 배열(행→열)로 대각선/열 충돌 체크

``` python
#  일차원 배열로 효율적으로 하는 방법

def check(row):
    for prev_row in range(row):
        # 세로 체크
        if visited[row] == visited[prev_row]:
            return False

        # 열과 행의 차이가 같다 == 현재 col 의 좌우 대각선이다
        # visited 에 저장된 값 = 어느 col에 두었는 가 ?
        # row - prev_row = 행의 차이
        # visited[row] - visited[prev_row] = 열의 차이
        # 이 두 개가 같으면 대각선
        if abs(row - prev_row) == abs(visited[row] - visited[prev_row]):
            return False

    return True


def dfs(row):
    global cnt

    if row == N:
        cnt += 1
        return

    for col in range(N):
        visited[row] = col  # 현재 row 의 col 에 놓았다 라고 가정하고
        if not check(row):  # 세로와 대각선을 체크해준다.
            continue

        dfs(row + 1)

N = 8
visited = [0] * N
cnt = 0

dfs(0)
print(f'N = {N} / answer = {cnt}')

```

### 백트래킹 절차

1)  상태공간트리를 **깊이우선**으로 탐색
2)  매 단계에서 **유망성 판별**
3)  유망하지 않으면 **부모로 되돌아가** 다른 가지 탐색

------------------------------------------------------------------------

## 🧩 백트래킹 연습문제

> `{1..10}`의 powerset 중 **합이 10**인 부분집합 모두 출력

``` python
# {1,2,3,4,5,6,7,8,9,10}의 powerset 중 원소의 합이 10인 부분집합을 모두 출력하시오.
arr = [i for i in range(1, 11)]
# visited = []  -> 이번 문제에서는 사용할 필요가 없다.

# level: N개의 원소를 모두 고려하면
# branch: 집합에 해당 원소를 포함 시키는 경우 or 안 시키는 경우 두 가지
# 누적값
#  - 부분집합의 총합
#  - 부분집합에 포함된 원소들
def dfs(cnt, total, subset):
    # 1. total 이 10이면 출력해라
    if total == 10:
        print(subset)
        return

    # 2. total 이 10을 넘으면 가지치기하자
    if total > 10:
        return

    if cnt == 10:
        # 1. total 이 10이면 출력해라 -> 여기는 해도 안된다. (어차피 앞에서 걸림)
        return

    dfs(cnt + 1, total + arr[cnt], subset + [arr[cnt]])  # 포함 하는 경우
    dfs(cnt + 1, total, subset)  # 집합에 포함 안 하는 경우

dfs(0, 0, [])

```

------------------------------------------------------------------------

## 🌳 트리(Tree) 기초

-   **사이클 없는 무향 연결 그래프**, 1:n 계층 구조.
-   용어: 루트, 리프, 형제, 조상/자손, 차수, 높이 등.

### 이진 트리(Binary Tree)

-   각 노드 자식 ≤ 2 (왼쪽/오른쪽).
-   종류: **포화/완전/편향** 이진 트리.
-   **배열 표현**: 인덱스 i의 자식은 `2i`, `2i+1`, 부모는 `i//2`.
-   **연결리스트 표현**: `Node(data, left, right)`.

### 순회(Traversal)

-   전위(VLR), 중위(LVR), 후위(LRV)

#### 간단 구현 (인접 리스트 기반, 없는 자식은 None 패딩)

``` python
arr = [1, 2, 1, 3, 2, 4, 3, 5, 3, 6, 4, 7, 5, 8, 5, 9, 6, 10, 6, 11, 7, 12, 11, 13]

# Todo: 일차원 리스트로 저장한다면 어떻게할까?


# [참고] 그래프처럼 저장하는 방식
nodes = [[] for _ in range(14)]

for i in range(0, len(arr), 2):
    parent_node = arr[i]
    child_node = arr[i + 1]
    nodes[parent_node].append(child_node)

# 자식이 없는 걸 표현하기 위해 None 을 삽입
for li in nodes:
    for _ in range(len(li), 2):
        li.append(None)


def order(node):
    if node == None:
        return

    # nodes[node]: node 에 연결된 번호들 (자식 번호들)
    # nodes[node][0]: 첫 번째 자식 번호
    # nodes[node][1]: 두 번째 자식 번호
    # print(node, end=' ')  # 전위순회
    order(nodes[node][0])
    # print(node, end=' ')  # 중위순회
    order(nodes[node][1])
    print(node, end=' ')  # 후위순회


order(1)  # 1번 노드부터 시작


```

#### 연결리스트(노드 클래스) 버전

``` python
arr = [1, 2, 1, 3, 2, 4, 3, 5, 3, 6, 4, 7, 5, 8, 5, 9, 6, 10, 6, 11, 7, 12, 11, 13]


class TreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

    # 왼쪽부터 들어간다고 가정하고 구현
    # - insert 삽입 순서는 상황마다 다르다
    def insert(self, child):
        if(not self.left):
            self.left = child
            return
        if(not self.right):
            self.right = child
            return
        return

    def preorder(self):
        if self != None:
            print(self.value, end=' ')
            if self.left:
                self.left.preorder()
            if self.right:
                self.right.preorder()

    # 중위 순회
    def inorder(self):
        if self != None:
            if self.left:
                self.left.inorder()
            print(self.value, end=' ')
            if self.right:
                self.right.inorder()

    # 후위 순회
    def postorder(self):
        if self != None:
            if self.left:
                self.left.postorder()
            if self.right:
                self.right.postorder()
            print(self.value, end=' ')

# 이진 트리 만들기
nodes = [TreeNode(i) for i in range(0, 14)]
for i in range(0, len(arr), 2):
    parentNode = arr[i]
    childNode = arr[i + 1]
    nodes[parentNode].insert(nodes[childNode])

nodes[1].preorder()
print()
nodes[1].inorder()
print()
nodes[1].postorder()

```

------------------------------------------------------------------------

## 🔎 이진탐색트리 (BST)

-   조건: `왼쪽 < 루트 < 오른쪽` (각 서브트리도 BST)
-   **중위순회** → 오름차순
-   시간복잡도: 평균 **O(log n)**, 최악(편향) **O(n)**

> 삭제는 3케이스\
> ① 리프(차수0): 바로 삭제\
> ② 자식1: 자식과 교체\
> ③ 자식2: **중위 후속자(오른쪽 최소)** 또는 **전위 선행자(왼쪽 최대)**
> 로 대체 후 삭제

------------------------------------------------------------------------

## ⛰️ 힙 (Heap) & heapq

-   **완전 이진 트리** 기반.\
    최대힙: 부모 ≥ 자식 / 최소힙: 부모 ≤ 자식
-   연산: 삽입/삭제 **O(log n)**, 루트 조회 **O(1)**
-   활용: **우선순위 큐**, **힙 정렬(O(n log n))**

``` python
import heapq

arr = [20, 15, 19, 4, 13, 11]

# 1. 기본 리스트를 heap 으로 만들기
# heapq.heapify(arr)  # 최소힙으로 바뀐다.
# 디버깅 시에 이진 트리로 그림을 그려야 한다!
# -> 딱 봤을때는 정렬이 안된 것 처럼 보인다.
# print(arr)

# 2. 하나 씩 데이터를 추가
min_heap = []
for num in arr:
    heapq.heappush(min_heap, num)
print(min_heap)

# 최대힙?
max_heap = []
for num in arr:
    heapq.heappush(max_heap, -num)

while max_heap:
    pop_num = heapq.heappop(max_heap)
    print(-pop_num, end=' ')

# ------------------ 전자사전 예제
# 1. 길이 순서로 먼저 출력
# 2. 길이가 같다면, 사전 순으로 출력

arr = ['apple', 'banana', 'kiwi', 'abcd', 'abca', 'lemon', 'peach', 'grape', 'pear']
# sort 를 쓰면 아래와 같다.
# 즉, 우선순위가 2가지
# arr.sort(key=lambda x: (len(x), x))
dictionary = []

# 단어를 삽입 (길이, 단어) 형태로 삽입
for word in arr:
    heapq.heappush(dictionary, (len(word), word))

# 전자사전에서 단어를 하나씩 꺼내기
print("전자사전 순서:")
while dictionary:
    length, word = heapq.heappop(dictionary)
    print(f"{word} (길이: {length})")

```

> **왜 힙?**\
> 삽입/삭제가 잦으면 `sort()`는 매번 **O(n log n)**, 힙은 각 연산
> **O(log n)** 으로 유리.

------------------------------------------------------------------------

## ✅ 오늘의 핵심 정리

-   **백트래킹**: 재귀 + 가지치기 → 탐색 효율화
-   **트리**: 계층 구조 자료형, 이진 트리 응용 폭넓음
-   **BST**: 평균 O(log n) 탐색/삽입/삭제, 중위순회=정렬
-   **힙/heapq**: 우선순위 큐/힙정렬, 빈번한 삽입·삭제에 최적
