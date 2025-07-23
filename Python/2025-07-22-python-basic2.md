# 📝 Python 자료형 실습 - 2025-07-22

## 📌 List

```python
# 리스트 생성
my_list = [1, 'a', 3, 'b', 5]

# 인덱싱
print(my_list[1])        # 'a'

# 슬라이싱
print(my_list[2:4])      # [3, 'b']
print(my_list[::-1])     # [5, 'b', 3, 'a', 1]

# 길이 확인
print(len(my_list))      # 5

# 값 수정
my_list[1] = 'two'
print(my_list)           # [1, 'two', 3, 'b', 5]

# 중첩 리스트 접근
nested = [1, 2, ['hello', 'world']]
print(nested[2][1])      # 'world'
```

---

## 📌 Tuple

```python
# 튜플 생성
my_tuple = (1, 'a', 3, 'b', 5)

# 인덱싱
print(my_tuple[1])       # 'a'

# 슬라이싱
print(my_tuple[::2])     # (1, 3, 5)

# 길이 확인
print(len(my_tuple))     # 5

# 값 수정 시도 (불변!)
# my_tuple[1] = 'z'      # ❌ TypeError 발생

# 다중 할당 (Packing/Unpacking)
x, y = 10, 20
print(x, y)              # 10 20

# 값 교환
x, y = y, x
print(x, y)              # 20 10
```

---

## 📌 Range

```python
# 기본 사용
print(list(range(5)))               # [0, 1, 2, 3, 4]
print(list(range(1, 6)))            # [1, 2, 3, 4, 5]
print(list(range(1, 10, 2)))        # [1, 3, 5, 7, 9]
print(list(range(5, 0, -1)))        # [5, 4, 3, 2, 1]

# 반복문과 함께 사용
for i in range(3):
    print(i)                        # 0, 1, 2
```

---

## 📌 Dictionary

```python
# 딕셔너리 생성
my_dict = {'name': 'Alice', 'age': 25}

# 값 접근
print(my_dict['name'])             # 'Alice'

# 값 추가
my_dict['city'] = 'Seoul'
print(my_dict)

# 값 변경
my_dict['age'] = 30
print(my_dict)
```

---

## 📌 Set

```python
# 세트 생성
my_set = {1, 2, 2, 3}
print(my_set)                      # {1, 2, 3}

# 집합 연산
a = {1, 2, 3}
b = {3, 4, 5}

print(a | b)  # 합집합: {1, 2, 3, 4, 5}
print(a & b)  # 교집합: {3}
print(a - b)  # 차집합: {1, 2}
```

---

## 📌 Boolean & None

```python
# Boolean
print(True + 1)                    # 2
print(False + 10)                  # 10
print(3 > 2)                       # True

# None
x = None
print(x is None)                  # True
```

---

## 📌 Type Conversion (형변환)

```python
# 명시적 형변환
print(int(3.14))                   # 3
print(str(100))                    # '100'
print(list("abc"))                # ['a', 'b', 'c']

# 암시적 형변환
print(3 + 5.0)                     # 8.0 (int → float)
```

---

## 📌 연산자 실습

```python
# 산술
print(10 // 3)                     # 3
print(2 ** 3)                      # 8

# 비교
print(1 == True)                   # True
print(1 is True)                   # False

# 논리
print(True and False)             # False
print(True or False)              # True

# 멤버십
print('a' in ['a', 'b', 'c'])     # True
print(3 not in [1, 2, 3])         # False
```

---

## 📌 불변 vs 가변 비교

```python
# 불변: str
s = "hello"
# s[0] = "H"         # ❌ TypeError

# 가변: list
l = [1, 2, 3]
l[0] = 100
print(l)                         # [100, 2, 3]
```

---

## 📌 Trailing Comma

```python
# 권장 스타일
colors = [
    "red",
    "blue",
    "green",  # 마지막에도 쉼표
]
```

---

끝! 실습으로 감각 잡고, 오류도 일부러 한 번씩 내보면서 익혀봐 😎  
