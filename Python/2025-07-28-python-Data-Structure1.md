# 🧮 Python - Data Structure & Methods

---

### 📌 1. 데이터 구조란?
- 여러 데이터를 효과적으로 사용, 관리하기 위한 구조 (`str`, `list`, `dict` 등).
- 프로그래밍에서 '자료구조'는 성능과 유지보수에 중요한 영향을 미침.

---

### 📌 2. 메서드란?
- 객체(데이터 구조)에 속한 **함수**.
- 각 데이터 타입별로 다양한 기능을 가진 메서드가 존재.

#### 메서드 예시
```python
# 문자열 메서드
print('hello'.capitalize())  # Hello

# 리스트 메서드
nums = [1, 2, 3]
nums.append(4)
print(nums)  # [1, 2, 3, 4]
```

---

### 📌 3. 문자열(String) 관련 메서드

#### 문자열 탐색/검증
```python
print('banana'.find('a'))     # 1
print('banana'.find('z'))     # -1
print('banana'.index('a'))    # 1
print('HELLO'.isupper())      # True
print('hello'.islower())      # True
print('abc'.isalpha())        # True
print('abc123'.isalpha())     # False
```

#### 문자열 조작
```python
text = 'hello world'
print(text.replace('world', 'python'))  # hello python

text = '   hello   '
print(text.strip())  # 'hello'

print('a,b,c'.split(','))  # ['a', 'b', 'c']
print('-'.join(['a', 'b', 'c']))  # a-b-c
print('python is fun'.title())  # Python Is Fun
```

---

### 📌 4. 리스트(List) 관련 메서드

#### 리스트 요소 추가/삭제
```python
lst = [1, 2, 3]
lst.append(4)
lst.extend([5, 6])
lst.append([7, 8])
lst.insert(2, 99)
lst.remove(99)
lst.pop()
lst.clear()
print(lst)
```

#### 리스트 탐색 및 정렬
```python
lst = [3, 1, 4, 1, 5, 9]
print(lst.index(4))   # 2
print(lst.count(1))   # 2
lst.reverse()
lst.sort()
print(lst)
```

---

### 📌 5. 중첩 리스트 순회
```python
elements = [['A', 'B'], ['c', 'd']]

for elem in elements:
    for item in elem:
        print(item)
```

---

### 📌 6. 얕은 복사 vs 깊은 복사

#### 얕은 복사
```python
a = [[1, 2], [3, 4]]
b = a[:]
b[0][0] = 100
print(a)  # [[100, 2], [3, 4]]
```

#### 깊은 복사
```python
import copy
a = [[1, 2], [3, 4]]
b = copy.deepcopy(a)
b[0][0] = 999
print(a)  # [[1, 2], [3, 4]]
print(b)  # [[999, 2], [3, 4]]
```

---

### 📌 7. List Comprehension
```python
squares = [x**2 for x in range(10) if x % 2 == 0]
matrix = [[0 for _ in range(3)] for _ in range(3)]
print(squares)
print(matrix)
```

---

### 📌 8. 메서드 체이닝
```python
text = ' heLLo, WoRLd '
result = text.strip().swapcase().replace('l', 'z')
print(result)  # HEzZo, wORLd
```

---

### 📌 9. 문자열 숫자 판별
```python
print('123'.isdecimal())   # True
print('③'.isdigit())       # True
print('½'.isnumeric())     # True
```

---

### 📌 10. map() 함수
```python
# 문자열 리스트 → 대문자로 변환
words = ['apple', 'banana', 'cherry']
upper_words = list(map(str.upper, words))
print(upper_words)  # ['APPLE', 'BANANA', 'CHERRY']

# 숫자 리스트 → 제곱 계산
nums = [1, 2, 3, 4]
squares = list(map(lambda x: x**2, nums))
print(squares)  # [1, 4, 9, 16]
```
