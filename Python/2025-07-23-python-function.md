# 📘 Python 함수 정리 (TIL)

## 함수 (Function)

### 🔹 함수란?
- 특정 작업을 수행하는 **재사용 가능한 코드 묶음**
- `def` 키워드로 정의, 함수 이름 및 괄호 안에 **매개변수** 작성

```python
def get_sum(num1, num2):
    return num1 + num2

print(get_sum(5, 3))  # 8
```

### 🔹 함수의 반환 (`return`)
- 함수는 `return` 키워드로 값을 반환하며, 없으면 기본적으로 `None` 반환됨
- `print()` 함수는 출력만 하고 반환값이 없음

```python
def my_func():
    print("hello")

result = my_func()
print(result)  # None
```

### 🔹 Docstring (함수 설명)
```python
def greet():
    """이 함수는 인사 메시지를 출력합니다."""
    print("안녕하세요!")
```

---

## 매개변수 vs 인자

| 구분 | 설명 |
|------|------|
| 매개변수(Parameter) | 함수 정의 시 변수 |
| 인자(Argument) | 함수 호출 시 전달하는 실제 값 |

```python
def add(x, y):  # x, y는 매개변수
    return x + y

add(2, 3)  # 2, 3은 인자
```

---

## 다양한 인자 종류

### ✅ 위치 인자
```python
def greet(name, age):
    print(f'{name}, {age}세')

greet('Alice', 25)
```

### ✅ 기본 인자값
```python
def greet(name, age=20):
    print(f'{name}, {age}세')

greet('Bob')  # 기본값 20 사용
```

### ✅ 키워드 인자
```python
greet(age=30, name='Charlie')
```

### ✅ 가변 인자 (`*args`)
```python
def print_args(*args):
    print(args)

print_args(1, 2, 3)  # (1, 2, 3)
```

### ✅ 가변 키워드 인자 (`**kwargs`)
```python
def print_kwargs(**kwargs):
    print(kwargs)

print_kwargs(name='Eve', age=30)  # {'name': 'Eve', 'age': 30}
```

---

## 재귀 함수 (Recursion)

```python
def factorial(n):
    if n == 0:
        return 1
    return n * factorial(n - 1)

print(factorial(5))  # 120
```

> 🧠 종료 조건 없으면 무한 루프 (Stack Overflow)!

---

## Scope (변수 범위)

| 범위 | 설명 |
|------|------|
| Local | 함수 내부 변수 |
| Global | 전체에서 사용 가능한 변수 |

```python
num = 0

def increment():
    global num
    num += 1

increment()
print(num)  # 1
```

### 🔹 LEGB Rule
1. **L**ocal → 함수 내부
2. **E**nclosing → 중첩 함수
3. **G**lobal → 파일 내
4. **B**uilt-in → 파이썬 내장

---

## 함수 이름 스타일

- 소문자 + 언더스코어(`_`)
- 명확한 동작 중심 (ex. `get_user_name()`)
- 불리언 반환 함수는 `is_`, `has_` 시작

---

## Packing & Unpacking

### 🔹 Packing
```python
def my_func(*args):
    print(args)  # 튜플로 저장됨

my_func(1, 2, 3)
```

### 🔹 Unpacking
```python
numbers = (1, 2, 3)
a, b, c = numbers
```

### 🔹 함수 인자로 언패킹
```python
def greet(x, y):
    print(x, y)

args = ('Alice', 30)
greet(*args)

kwargs = {'x': 'Bob', 'y': 25}
greet(**kwargs)
```

---

## 함수 반환 관련

- **항상 하나의 값만 반환**
- 여러 개 반환처럼 보이지만 → 튜플로 반환

```python
def get_info():
    return 'Alice', 30

info = get_info()  # ('Alice', 30)
```

---

## 람다 함수 (Lambda)

```python
square = lambda x: x ** 2
print(square(4))  # 16
```

- 익명 함수 / 한 줄 표현 / 간단한 함수에 사용


---

## ✅ 단일 책임 원칙
- 함수는 **하나의 작업**만 하도록 설계
- 유지보수 용이, 테스트 쉬움

```python
# 나쁜 예
def process_user(user):
    validate(user)
    save(user)
    notify(user)

# 좋은 예
def validate_user(user): ...
def save_user(user): ...
def notify_user(user): ...
```
