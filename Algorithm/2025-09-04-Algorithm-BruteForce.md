# 📘 TIL: 완전 탐색(Brute Force)

## 1. 개념

-   가능한 **모든 경우의 수**를 생성하고, 조건을 검사하여 해결하는 방식
-   **Generate-and-Test** 방식 → "Just do it"
-   특징:
    -   거의 모든 문제에 적용 가능 (단, 경우의 수가 작을 때)
    -   문제 접근/설계가 빠름
    -   경우의 수가 많아지면 **시간 폭발(기하급수적 증가)**

------------------------------------------------------------------------

## 2. 다른 알고리즘과 비교

1.  **완전 탐색 (Brute Force)**
    -   모든 경우를 나열 후 검사
    -   구현 난이도 낮음, 정답 보장
    -   경우의 수 많으면 **시간 초과 위험**
2.  **탐욕 (Greedy)**
    -   매 순간 최적 선택
    -   전역 최적 보장은 아님
3.  **분할 정복 (Divide and Conquer)**
    -   큰 문제를 작은 문제로 나누어 해결 후 결합
    -   예: 퀵 정렬, 병합 정렬
4.  **동적 프로그래밍 (DP)**
    -   부분 문제 결과를 **메모**해서 중복 계산 방지
    -   최적화 문제에 자주 활용

> 보통 문제 풀 때, **완전 탐색으로 정답 확인 후 → 성능 개선(Greedy/DP
> 등)** 하는 경우가 많음.

------------------------------------------------------------------------

## 3. 완전 탐색과 조합적 문제

-   완전 탐색은 **순열 / 조합 / 부분집합** 전부 생성 → 검사하는 방식
-   대표 예시:
    -   **배낭 문제**: 작은 N일 때 부분집합 전부 검사
    -   **여행 경로 문제**: 모든 순열을 생성해 최소 비용 경로 탐색

------------------------------------------------------------------------

## 4. 예시 문제: Baby-Gin Game

### 규칙

-   카드 6장을 3장씩 나누어 **run**(연속 3장) 또는 **triplet**(같은 숫자
    3장) 판별
-   두 그룹이 모두 run/triplet이면 Baby-gin

### 접근 방법

1.  6장을 전부 **순열 생성** (6! = 720가지)
2.  앞 3장/뒤 3장을 각각 판별
3.  둘 다 run/triplet이면 성공

### 카운팅 배열 활용

-   숫자(0\~9)별 개수를 세는 배열 `visited`를 활용해 효율적으로
    triplet/run 확인 가능

``` python
def check_babygin(numbers):
    count = [0]*10
    for num in numbers:
        count[num] += 1

    result, idx = 0, 0
    while idx < 10:
        if count[idx] >= 3:  # triplet
            count[idx] -= 3
            result += 1
            continue
        if idx <= 7 and count[idx] and count[idx+1] and count[idx+2]:  # run
            count[idx] -= 1
            count[idx+1] -= 1
            count[idx+2] -= 1
            result += 1
            continue
        idx += 1
    return result >= 2
```

------------------------------------------------------------------------

## 5. 완전 탐색 구현 방법

### 반복 vs 재귀

-   **반복문**: for, while → 코드를 n번 반복
-   **재귀**: 큰 문제를 작은 문제로 쪼개 호출
    -   종료 조건(기저 조건) 필수
    -   무한 재귀 시 `RecursionError` 발생 (Python은 약 1000번 제한)

### 예시: 1,2,3으로 만들 수 있는 두 자리 수

``` python
for i in range(1, 4):
    for j in range(1, 4):
        print(i, j)
```

### 예시: N자리 수를 재귀로 만들기

``` python
path = []

def recur(cnt, N):
    if cnt == N:
        print(*path)
        return
    for num in [1, 2, 3]:
        path.append(num)
        recur(cnt+1, N)
        path.pop()

recur(0, 3)  # 3자리
```

------------------------------------------------------------------------

## 6. 순열과 중복순열

-   **순열**: 서로 다른 N개 중 R개를 순서 있게 나열 (중복 ❌)
-   **중복순열**: N개 중 R개를 순서 있게 나열 (중복 허용)

### 중복순열 구현

``` python
path = []
def recur(cnt):
    if cnt == 2:
        print(*path)
        return
    for num in [0,1,2]:
        path.append(num)
        recur(cnt+1)
        path.pop()

recur(0)
```

### 중복 없는 순열 구현

-   `visited` 배열을 사용해 중복 제거

``` python
path, used = [], [0]*7  # 1~6

def recur(cnt):
    if cnt == 3:
        print(*path)
        return
    for num in range(1,7):
        if used[num]: continue
        used[num] = 1
        path.append(num)
        recur(cnt+1)
        path.pop()
        used[num] = 0

recur(0)
```

------------------------------------------------------------------------

## 7. 완전 탐색 문제 예시

### 문제 1: 🎲 주사위 눈의 합

-   주사위 3개를 던져 합이 **10 이하**인 경우의 수

``` python
def recur(cnt, total):
    global result
    if total > 10: return
    if cnt == 3:
        if total <= 10:
            result += 1
        return
    for num in range(1,7):
        recur(cnt+1, total+num)

result = 0
recur(0,0)
print(result)
```

------------------------------------------------------------------------

### 문제 2: 🃏 연속 3장의 트럼프

-   카드 5장을 뽑았을 때, 같은 카드가 **3장 연속** 등장하는 경우

``` python
cards = ['A','J','Q','K']
path, result = [], 0

def count_three():
    return (path[0]==path[1]==path[2] or
            path[1]==path[2]==path[3] or
            path[2]==path[3]==path[4])

def recur(cnt):
    global result
    if cnt == 5:
        if count_three(): result += 1
        return
    for card in cards:
        path.append(card)
        recur(cnt+1)
        path.pop()

recur(0)
print(result)
```

------------------------------------------------------------------------

## ✨ 정리

-   완전 탐색은 **알고리즘 문제를 처음 접할 때 가장 먼저 떠올려야 하는
    접근**
-   구현 난이도는 낮지만, 시간 복잡도는 크므로 **가지치기(pruning)**가
    필수적
-   순열/조합/부분집합 문제와 함께 자주 등장
-   성능이 필요하면 → **탐욕 / DP / 분할정복**으로 최적화
