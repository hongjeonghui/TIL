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
        self.capacity = capacity  # 큐의 최대 용량 설정
        self.items = [None] * capacity  # 큐를 저장할 리스트 초기화
        self.front = -1  # 큐의 맨 앞 요소 인덱스 (초기값 -1)
        self.rear = -1  # 큐의 맨 뒤 요소 인덱스 (초기값 -1)

    def is_empty(self):
        # front와 rear가 같으면 큐가 비어있음
        return self.front == self.rear

    def is_full(self):
        # rear가 큐의 최대 인덱스(capacity - 1)에 도달하면 큐가 가득 참
        return self.rear == self.capacity - 1

    def enqueue(self, item):
        if self.is_full():
            raise IndexError("Queue is full")  # 큐가 가득 찼을 때 예외 발생
        self.rear += 1  # rear 인덱스를 1 증가
        self.items[self.rear] = item  # 새 항목을 rear 위치에 추가

    def dequeue(self):
        if self.is_empty():
            raise IndexError("Queue is empty")  # 큐가 비어있을 때 예외 발생
        self.front += 1  # front 인덱스를 1 증가
        item = self.items[self.front]  # front 위치의 항목을 가져옴
        self.items[self.front] = None  # 해당 위치를 None으로 설정 
        
        return item  # 가져온 항목 반환

    def peek(self):
        if self.is_empty():
            raise IndexError("Queue is empty")  # 큐가 비어있을 때 예외 발생
        return self.items[self.front + 1]  # front 다음 위치의 항목 반환

    def get_size(self):
        # 현재 큐에 있는 항목의 개수 계산
        return self.rear - self.front


# 사용 예시
queue = Queue(5)  # 용량이 5인 큐 생성

queue.enqueue(1)  # 큐에 1 추가
queue.enqueue(2)  # 큐에 2 추가
queue.enqueue(3)  # 큐에 3 추가

print("Dequeued:", queue.dequeue())  # 큐에서 첫 번째 항목 제거 및 출력 (1)
print("Dequeued:", queue.dequeue())  # 큐에서 두 번째 항목 제거 및 출력 (2)

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
        # 실제 용량은 요청된 용량보다 1 크게 설정 (한 칸은 항상 비워둠)
        self.capacity = capacity + 1
        self.items = [None] * self.capacity  # 리스트 초기화
        self.front = 0  # 큐의 맨 앞 요소 바로 앞의 인덱스
        self.rear = 0  # 큐의 맨 뒤 요소의 인덱스

    def is_empty(self):
        # front와 rear가 같으면 큐가 비어있음
        return self.front == self.rear

    def is_full(self):
        # rear 다음 위치가 front와 같으면 큐가 가득 참
        return (self.rear + 1) % self.capacity == self.front

    def enqueue(self, item):
        if self.is_full():
            raise IndexError("Queue is full")  # 큐가 가득 찼을 때 예외 발생
        # rear를 다음 위치로 이동 (원형으로 순환)
        self.rear = (self.rear + 1) % self.capacity
        self.items[self.rear] = item  # 새 위치에 항목 삽입

    def dequeue(self):
        if self.is_empty():
            raise IndexError("Queue is empty")
        # front를 다음 위치로 이동 (원형으로 순환)
        self.front = (self.front + 1) % self.capacity
        item = self.items[self.front]  # front 위치의 항목을 가져옴
        self.items[self.front] = None  # 해당 위치의 데이터 제거
        return item

    def peek(self):
        if self.is_empty():
            raise IndexError("Queue is empty")
        # front 다음 위치의 항목 반환 (제거하지 않음)
        return self.items[(self.front + 1) % self.capacity]

    def get_size(self):
        # 현재 큐에 있는 항목의 개수 계산
        # rear가 front보다 앞에 있으면 capacity를 더해 음수가 되지 않게 함
        return (self.rear - self.front + self.capacity) % self.capacity


# 사용 예시
queue = CircularQueue(5)

queue.enqueue(1)
queue.enqueue(2)
queue.enqueue(3)
queue.enqueue(4)
queue.enqueue(5)
queue.enqueue(6)  # IndexError: 큐가 가득 찼습니다.
print(queue.items)  # [None, 1, 2, 3, 4, 5]
print(queue.get_size()) # 5

```

---

## 5. 큐 응용

### 5.1 버퍼(Buffer)
- 데이터를 임시 저장하는 공간
- 예: 키보드 입력 → 큐에 저장 → OS가 순서대로 처리

### 5.2 연습문제: 마이쮸 시뮬레이션
#### 문제
```python
total_candy = 20  # 총 마이쮸 개수
queue = []  # 사람들을 저장할 큐
queue.append((1, 1))  # 첫 번째 사람과 받을 마이쮸 개수를 큐에 추가

last_person = None  # 마지막으로 마이쮸를 받은 사람을 저장할 변수

pass


# 마지막으로 마이쮸를 받은 사람을 출력
print(f"마지막 마이쮸는 {last_person}번")
```
#### 코드
```python
total_candy = 20  # 총 마이쮸 개수
queue = []  # 사람들을 저장할 큐
queue.append((1, 1))  # 첫 번째 사람과 받을 마이쮸 개수를 큐에 추가

last_person = None  # 마지막으로 마이쮸를 받은 사람을 저장할 변수
next_person = 2

# 마이쮸가 남아있는동안 반복
while total_candy > 0:
    # 대기열 맨 앞 사람이 마이쮸를 수령할 예정
    person, count = queue.pop(0)

    # 남은 마이쮸가 현재 사람이 받을 마이쮸 개수보다 적거나 같은 경우
    if total_candy - count <= 0:
        last_person = person
        break

    # 현재 사람이 받은 마이쮸 개수를 총 마이쮸 개수에서 제거
    total_candy = total_candy - count
    
    # 직전에 마이쮸를 받은 사람이 바로 다시 줄을 섬
    queue.append((person, count + 1))

    # 이 모습을 본 다음 사람이 대기열에 이어서 줄을 섬 (처음이니까 받을 마이쮸는 1개)
    queue.append((next_person, 1))
    next_person += 1


# 마지막으로 마이쮸를 받은 사람을 출력
print(f"마지막 마이쮸는 {last_person}번")
```

---

## 6. 마무리

- 큐는 **FIFO** 구조
- 선형 큐는 공간 낭비 문제 → 원형 큐로 해결
- 실제 구현보다 파이썬에서는 `collections.deque`를 활용하는 것이 효율적
- 알고리즘(BFS 등)에서 자주 사용되므로 기본 구현 원리를 이해하는 것이 중요
