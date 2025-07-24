# 📘 Python 문법 정리 (TIL)

---

## 📚 모듈 (Module)

### 🔹 정의
- 한 파일로 묶인 변수와 함수의 모음
- `.py` 파일로 저장된 특정 기능의 코드

### 🔹 내장 모듈 사용 예
```python
import math

print(math.pi)        # 3.14159...
print(math.sqrt(4))   # 2.0
```

### 🔹 import 방식
```python
import math
print(math.pi)
print(math.sqrt(9))
```

```python
from math import pi, sqrt
print(pi)
print(sqrt(16))
```

### 🔹 별칭 사용 (`as`)
```python
import pandas as pd
import matplotlib.pyplot as plt
```

### 🔹 사용자 정의 모듈
- 같은 폴더 내 다른 `.py` 파일의 함수, 변수 등을 import하여 사용

---

## 📦 패키지 (Package)

### 🔹 정의
- 관련 모듈들을 디렉토리 구조로 구성한 것

### 🔹 설치 (pip 사용)
```bash
pip install SomePackage
pip install SomePackage==1.0.5
```

### 🔹 예시: `requests` 설치 및 사용
```bash
pip install requests
```

```python
import requests
res = requests.get("https://example.com")
print(res.text)
```

---

## 🔁 제어문 (Control Statement)

### ✅ 조건문 (if, elif, else)
```python
x = 10

if x > 0:
    print("양수")
elif x == 0:
    print("0")
else:
    print("음수")
```

---

### ✅ 반복문 (for, while)

#### 🔹 for문
```python
for i in range(3):
    print(i)
```

#### 🔹 while문
```python
x = 0
while x < 3:
    print(x)
    x += 1
```

---

### 🔹 반복 제어문
```python
# break 예시
for i in range(5):
    if i == 3:
        break
    print(i)

# continue 예시
for i in range(5):
    if i % 2 == 0:
        continue
    print(i)
```
---


## 🔁 중첩 리스트 순회

### ✅ 중첩 리스트 순회란?
- 바깥 리스트를 순회하면서 각 안쪽 리스트 요소를 반복하는 방식
- 안쪽 리스트 요소에 접근하려면 **중첩 for문** 사용

```python
elements = [['A', 'B'], ['c', 'd']]

# 바깥 리스트만 순회
for elem in elements:
    print(elem)

# 출력
# ['A', 'B']
# ['c', 'd']

elements = [['A', 'B'], ['c', 'd']]

# 중첩 반복문을 사용한 안쪽 요소 순회
for elem in elements:
    for item in elem:
        print(item)

# 출력
# A
# B
# c
# d
```
---


## ✏️ map 함수

```python
numbers = [1, 2, 3]
result = map(str, numbers)
print(list(result))  # ['1', '2', '3']
```


## ✏️ zip 함수

```python
girls = ['jane', 'ashley']
boys = ['peter', 'jay']
pairs = zip(girls, boys)
print(list(pairs))  # [('jane', 'peter'), ('ashley', 'jay')]
```

---

## ✏️ for-else 구문

```python
for i in range(5):
    if i == 3:
        break
    print(i)
else:
    print("정상 종료")
```

---

## ✏️ enumerate()

```python
fruits = ['apple', 'banana', 'cherry']
for idx, fruit in enumerate(fruits, start=1):
    print(idx, fruit)
```

