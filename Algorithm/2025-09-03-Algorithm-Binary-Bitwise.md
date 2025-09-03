# 📘 TIL - Binary System & Bitwise Operations

## 기억 포인트
- AND 연산 (`&`)
- (`<<`)연
- 부분집합에서의 활용

---

## 1. Binary System (이진법)

- **이진법**: 0과 1 두 가지 숫자로 표현하는 체계  
- **십진법 vs 이진법**
  - 10진수: 인간에게 직관적 (0~9)
  - 2진수: 하드웨어 구현에 유리 (전류 ON/OFF)

| 십진수 | 이진수 |
| ------ | ------ |
| 1 | 1 |
| 2 | 10 |
| 3 | 11 |
| 4 | 100 |
| 5 | 101 |
| 6 | 110 |
| 7 | 111 |
| 8 | 1000 |

⚡ **특징**: 2진법은 자릿수가 길어질 수 있지만, 하드웨어 구현이 단순해서 실제 컴퓨터는 2진법을 사용.

---

## 2. 비트 연산자 (Bitwise Operators)

| 연산자 | 의미 | 설명 |
| ------ | ---- | ---- |
| `&` | AND | 두 비트가 모두 1이면 1 |
| `\|` | OR | 둘 중 하나라도 1이면 1 |
| `^` | XOR | 두 비트가 서로 다르면 1 |
| `~` | NOT | 비트를 반전 |
| `<<` | Left Shift | 비트를 왼쪽 이동 (2배씩 증가) |
| `>>` | Right Shift | 비트를 오른쪽 이동 (1/2씩 감소) |

### AND (`&`)
- 예: `1101 & 1011 = 1001`

### OR (`|`)
- 예: `1101 | 1011 = 1111`

### XOR (`^`)
- 예: `1101 ^ 1011 = 0110`

### NOT (`~`)
- 예: `~1010 = 0101`  (언어/정수폭에 따라 결과 해석 주의)

---

## 3. 시프트 연산자 (Shift Operators)

### `<<` (Left Shift) — 2배씩 증가
```python
0b1111 << 1  # 11110 -> 30
0b1111 << 2  # 111100 -> 60
0b1111 << 3  # 1111000 -> 120
```

### `>>` (Right Shift) — 1/2씩 감소
```python
0b1111 >> 1  # 0111 -> 7
0b1111 >> 2  # 0011 -> 3
0b1111 >> 3  # 0001 -> 1
```

📌 비유
- 10진수: 77 뒤에 0 붙이면 → 770 (10배)
- 2진수: 110 뒤에 0 붙이면 → 1100 (2배)

---

## 4. 부분집합(Bitmask)에서의 활용

- 전체 부분집합 수: `1 << n` (= 2^n)
- j번째 원소 포함 여부: `if i & (1 << j)`

예시: 모든 부분집합 나열
```python
nums = [3, 6, 7, 1, 5, 4]
n = len(nums)

for i in range(1 << n):  # 0 ~ 2^n-1
    subset = []
    for j in range(n):
        if i & (1 << j):  # j번째 비트가 1이면 포함
            subset.append(nums[j])
    print(subset)
```

---

## 5. 연습 문제

### ✅ 문제 1. (중첩 for문 vs 비트연산)
- 중첩 for문 없이 비트마스크로 단일 반복으로 표현

```python
ingredients = ['egg', 'cheese', 'rice']
N = 3

for i in range(1 << N):  # 0 ~ 7
    result = []
    for j in range(N):
        if i & (1 << j):
            result.append(ingredients[j])
    print(result)
```

---

### ✅ 문제 2. 부분집합 개수와 원소 출력

```python
nums = [3, 6, 7, 1, 5, 4]
n = len(nums)
cnt = 0

for i in range(1 << n):  # 64
    cnt += 1
    for j in range(n):
        if i & (1 << j):
            print(nums[j], end=' ')
    print()
print(cnt)  # 64
```

---

### ✅ 문제 3. 합이 0인 부분집합 개수

```python
nums = [-3, 3, -9, 6, 7, -6, 1, 5, 4, -2]

cnt = 0
for i in range(1, 1 << len(nums)):  # 공집합 제외
    total = 0
    for j in range(len(nums)):
        if i & (1 << j):
            total += nums[j]
    if total == 0:
        cnt += 1

print(cnt)  # 42
```

---

### ✅ 문제 4. SWEA 4837 - 부분집합의 합
- 집합 A = {1..12}의 부분집합 중
  - 원소 개수가 N개이고
  - 합이 K인 부분집합의 개수 출력

아이디어
- 길이 12의 배열 A = [1..12]
- i를 0 ~ (1<<12)-1 까지 순회
- 비트 수가 N개(= popcount(i))인지 확인
- 합이 K인지 확인

```python
A = list(range(1, 13))
def popcount(x):
    c = 0
    while x:
        x &= x - 1  # 최하위 1비트 제거
        c += 1
    return c

def count_subsets(N, K):
    ans = 0
    for i in range(1 << 12):
        if popcount(i) != N:
            continue
        s = 0
        for j in range(12):
            if i & (1 << j):
                s += A[j]
        if s == K:
            ans += 1
    return ans

# 예) N=3, K=6 -> {1,2,3} 1가지
print(count_subsets(3, 6))  # 1
```

---

## 6. 핵심 포인트 요약
1) `AND (&)` : 두 비트가 모두 1일 때만 1  
2) `<<` : 왼쪽 시프트 = 값이 2배씩 증가  
3) 부분집합(bitmask) :  
   - 전체 개수 `1 << n`  
   - 포함 여부 `i & (1 << j)`  
   - 공집합 제외하려면 `range(1, 1 << n)`
