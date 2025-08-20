# 📌 TIL - Deque (Double-Ended Queue)

## 1. Deque란 무엇인가

- 하나의 자료구조에서 **양쪽(앞과 뒤)** 으로 삽입(enqueue)과 삭제(dequeue)를 모두 수행할 수 있도록 한 구조
- 큐(Queue)는 한쪽 끝에서만 삽입, 반대쪽 끝에서만 삭제를 수행하지만, Deque는 **앞뒤 양방향** 접근이 가능함

---

## 2. Deque의 구조와 특성

1. **양방향 삽입/삭제**
   - 앞(front)에서 데이터를 추가/제거 가능
   - 뒤(rear)에서도 데이터를 추가/제거 가능
2. **유연성**
   - 큐와 스택을 모두 흉내낼 수 있음
3. **다양한 응용**
   - 양방향 검색, 슬라이딩 윈도우 문제 등에서 사용
   - 파이썬에서는 `collections.deque`로 최적화된 구현 제공

---

## 3. Deque 기본 연산

| 연산 | 설명 |
| --- | --- |
| `append(x)` | 뒤(rear)쪽에 x 삽입 |
| `appendleft(x)` | 앞(front)쪽에 x 삽입 |
| `pop()` | 뒤(rear)쪽에서 원소 삭제 후 반환 |
| `popleft()` | 앞(front)쪽에서 원소 삭제 후 반환 |
| `clear()` | 모든 원소 제거 |
| `extend(iterable)` / `extendleft(iterable)` | 여러 원소를 뒤/앞으로 한 번에 추가 |

> 추가 메서드: `rotate()`, `reverse()` 등

---

## 4. 파이썬 collections.deque 예시

### 4.1 기본 사용
```python
from collections import deque

dq = deque()

dq.append(1)
dq.append(2)
dq.append(3)
print(dq)  # deque([1, 2, 3])

dq.appendleft(0)
print(dq)  # deque([0, 1, 2, 3])

print(dq.pop())      # 3
print(dq.popleft())  # 0
print(dq)            # deque([1, 2])
```

⚡ 리스트의 `pop(0)`은 O(n)인데, deque의 양쪽 삽입/삭제는 거의 O(1)

---

## 5. rotate()

### 5.1 기본 개념
- `deque.rotate(n)`  
  - n > 0 → 오른쪽으로 n칸 이동  
  - n < 0 → 왼쪽으로 |n|칸 이동

### 5.2 사용 예시
```python
from collections import deque

dq = deque([1, 2, 3, 4, 5])

dq.rotate(1)
print(dq)  # deque([5, 1, 2, 3, 4])

dq.rotate(-2)
print(dq)  # deque([2, 3, 4, 5, 1])
```

---

## 6. 회전 문제 예시

앞에서부터 K번째 원소를 “맨 뒤로 보내는” 회전

### 6.1 rotate 활용
```python
from collections import deque

dq1 = deque([1, 2, 3, 4, 5])
K = 3
dq1.rotate(-K)
print(dq1)  # deque([4, 5, 1, 2, 3])
```

### 6.2 rotate 없이 수동 구현
```python
dq2 = deque([1, 2, 3, 4, 5])
K = 3

for _ in range(K):
    dq2.append(dq2.popleft())

print(dq2)  # deque([4, 5, 1, 2, 3])
```

---

## 7. 직접 구현 (배열 기반 Deque)

```python
class ArrayDeque:
    def __init__(self, capacity=10):
        self.capacity = capacity + 1  # 한 칸 비워야 구분 가능
        self.items = [None] * self.capacity
        self.front = 0
        self.rear = 0

    def is_empty(self):
        return self.front == self.rear

    def is_full(self):
        return (self.rear + 1) % self.capacity == self.front

    def append(self, item):
        # 뒤(rear)쪽 삽입
        if self.is_full():
            raise IndexError("Deque is full")
        self.rear = (self.rear + 1) % self.capacity
        self.items[self.rear] = item

    def appendleft(self, item):
        # 앞(front)쪽 삽입
        if self.is_full():
            raise IndexError("Deque is full")
        # front 위치를 역으로 한 칸 이동 (원형)
        self.items[self.front] = item
        self.front = (self.front - 1 + self.capacity) % self.capacity

    def pop(self):
        # 뒤(rear)에서 삭제
        if self.is_empty():
            raise IndexError("Deque is empty")
        item = self.items[self.rear]
        self.items[self.rear] = None
        self.rear = (self.rear - 1 + self.capacity) % self.capacity
        return item

    def popleft(self):
        # 앞(front)에서 삭제
        if self.is_empty():
            raise IndexError("Deque is empty")
        self.front = (self.front + 1) % self.capacity
        item = self.items[self.front]
        self.items[self.front] = None
        return item

# 사용 예시
deque_obj = ArrayDeque(3)  # 실제 내부 용량은 4가 됨

deque_obj.append(1)
deque_obj.append(2)
deque_obj.append(3)  # 이 시점에서 데크이 꽉 참

# deque_obj.append(4) -> IndexError: Deque is full

print(deque_obj.pop())       # 3
deque_obj.appendleft(0)
print(deque_obj.popleft())   # 0
```

---

## 8. 실제 활용 예시

1. **슬라이딩 윈도우 (Sliding Window)**: 일정 범위 데이터를 빠르게 관리  
2. **Undo/Redo 기능**: 앞뒤 양쪽에서 접근  
3. **양방향 버퍼링**: 스트리밍, 로그 처리 등 실시간 데이터 처리

---

## 9. 정리

- **Deque**는 양쪽(앞뒤)에서 삽입과 삭제가 모두 가능한 자료구조  
- 스택과 큐를 모두 흉내낼 수 있어 유연성 제공  
- 파이썬 `collections.deque`는 O(1)에 가까운 성능 보장  
- `rotate()` 메서드로 전체 회전을 간단하게 구현 가능  
