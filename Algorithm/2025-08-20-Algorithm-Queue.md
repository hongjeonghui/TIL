# 📌 TIL - 큐(Queue)

## 1. 큐란 무엇인가

- 선형 구조로 **먼저 들어온 데이터가 먼저 나가는 (FIFO, First-In First-Out)** 특징을 가짐  
- 삽입 → `enqueue`, 삭제 → `dequeue`  
- 활용 예시: 대기열(버퍼링), 프린터 대기 목록, 운영체제의 프로세스 스케줄링 등  

---

## 2. 큐의 구조

### 2.1 Front & Rear
- **front**: 큐의 맨 앞, **삭제(dequeue)** 연산이 일어남
- **rear**: 큐의 맨 뒤, **삽입(enqueue)** 연산이 일어남
- 공백 상태 → `front == rear`
- 포화 상태 → `rear == n-1` (선형 큐), `(rear+1) % capacity == front` (원형 큐)

### 2.2 주요 연산
- `is_empty` : 큐가 비어 있는지 확인
- `is_full` : 큐가 가득 찼는지 확인
- `enqueue` : rear에 데이터 삽입
- `dequeue` : front에서 데이터 제거 및 반환
- `peek` : front의 데이터 확인 (제거 X)
- `get_size` : 큐에 들어 있는 요소 개수 확인

---

## 3. 큐 구현 방법

### 3.1 리스트 활용
```python
queue = []
queue.append(1)  # enqueue
queue.append(2)
queue.append(3)

print(queue.pop(0))  # dequeue -> 1
print(queue.pop(0))  # dequeue -> 2
print(queue.pop(0))  # dequeue -> 3
```
⚠️ 단점: `pop(0)`은 O(N) 시간 소요 → 비효율적

---

### 3.2 클래스 구현 (선형 큐)
```python
class Queue:
    def __init__(self, capacity=10):
        self.capacity = capacity
        self.items = [None] * capacity
        self.front = -1
        self.rear = -1

    def is_empty(self):
        return self.front == self.rear

    def is_full(self):
        return self.rear == self.capacity - 1

    def enqueue(self, item):
        if self.is_full():
            raise IndexError("Queue is full")
        self.rear += 1
        self.items[self.rear] = item

    def dequeue(self):
        if self.is_empty():
            raise IndexError("Queue is empty")
        self.front += 1
        item = self.items[self.front]
        self.items[self.front] = None
        return item

    def peek(self):
        if self.is_empty():
            raise IndexError("Queue is empty")
        return self.items[self.front + 1]

    def get_size(self):
        return self.rear - self.front
```

---

### 3.3 선형 큐의 문제점
- front가 이동해도 rear가 `capacity-1`에 도달하면 **포화 상태로 잘못 인식**
- 해결 방법:
  1. 매번 원소들을 앞으로 이동 (비효율적)
  2. **원형 큐(Circular Queue)** 활용

---

## 4. 원형 큐 (Circular Queue)

### 4.1 원형 큐의 특징
- 배열을 원형처럼 연결해 공간을 재활용
- 포화 상태: `(rear + 1) % capacity == front`
- 공백 상태: `rear == front`

### 4.2 원형 큐 구현
```python
class CircularQueue:
    def __init__(self, capacity=10):
        self.capacity = capacity + 1
        self.items = [None] * self.capacity
        self.front = 0
        self.rear = 0

    def is_empty(self):
        return self.front == self.rear

    def is_full(self):
        return (self.rear + 1) % self.capacity == self.front

    def enqueue(self, item):
        if self.is_full():
            raise IndexError("Queue is full")
        self.rear = (self.rear + 1) % self.capacity
        self.items[self.rear] = item

    def dequeue(self):
        if self.is_empty():
            raise IndexError("Queue is empty")
        self.front = (self.front + 1) % self.capacity
        item = self.items[self.front]
        self.items[self.front] = None
        return item

    def peek(self):
        if self.is_empty():
            raise IndexError("Queue is empty")
        return self.items[(self.front + 1) % self.capacity]

    def get_size(self):
        return (self.rear - self.front + self.capacity) % self.capacity
```

---

## 5. 큐 응용

### 5.1 버퍼(Buffer)
- 데이터를 임시 저장하는 공간
- 예: 키보드 입력 → 큐에 저장 → OS가 순서대로 처리

### 5.2 연습문제: 마이쮸 시뮬레이션
```python
total_candy = 20
queue = [(1, 1)]
last_person = None
next_person = 2

while total_candy > 0:
    person, count = queue.pop(0)

    if total_candy - count <= 0:
        last_person = person
        break

    total_candy -= count
    queue.append((person, count + 1))
    queue.append((next_person, 1))
    next_person += 1

print(f"마지막 마이쮸는 {last_person}번")
```

---

## 6. 마무리

- 큐는 **FIFO** 구조
- 선형 큐는 공간 낭비 문제 → 원형 큐로 해결
- 실제 구현보다 파이썬에서는 `collections.deque`를 활용하는 것이 효율적
- 알고리즘(BFS 등)에서 자주 사용되므로 기본 구현 원리를 이해하는 것이 중요
