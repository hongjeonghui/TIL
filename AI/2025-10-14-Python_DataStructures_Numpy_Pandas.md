# 📄 TIL: Python 기본 자료구조 & Numpy · Pandas 기초 정리

---

## 📗 1. 숫자형 (int / float)

| 기능 | 코드 예시 | 설명 |
|------|------------|------|
| 절댓값 | `abs(a)` | 음수를 양수로 변환 |
| 반올림 | `round(a)` | 가장 가까운 정수로 반올림 |
| 제곱 | `pow(a, 2)` 또는 `a ** 2` | a² 계산 |
| 근사 비교 | `math.isclose(a, b)` | 두 수가 거의 같은지 확인 |

📘 예시:
```python
import math

a = -3.14159
a_abs = abs(a)
a_rounded = round(a)
a_squared = pow(a, 2)
is_close_to_pi = math.isclose(a_abs, math.pi)
```

---

## 📘 2. 문자열 (str)

| 기능 | 코드 | 설명 |
|------|------|------|
| 소문자 변환 | `"Hello".lower()` | 모든 문자를 소문자로 |
| 대문자 변환 | `"Hello".upper()` | 모든 문자를 대문자로 |
| 문자열 반복 | `"Hi" * 2` | "HiHi" |
| 특정 문자로 끝나는지 | `s.endswith("!")` | 끝 문자가 `'!'`인지 확인 |
| 숫자 여부 확인 | `s.isdigit()` | 모든 문자가 숫자일 때만 True |
| 숫자만 추출 | `[ch for ch in s if ch.isdigit()]` | 문자열 중 숫자만 선택 |
| 문자 ↔ 숫자 변환 | `int(ch)` | 문자 숫자를 정수로 변환 |

📘 예시:
```python
s = "a1b2c3"
digits = [ch for ch in s if ch.isdigit()]  # ['1', '2', '3']
total = sum(int(ch) for ch in digits)      # 6
```

---

## 📙 3. 리스트 (list)

| 기능 | 코드 | 설명 |
|------|------|------|
| 길이 | `len(l)` | 원소 개수 |
| 포함 여부 | `1 in l` | 1이 리스트 안에 있나 |
| 이어붙이기 | `l + l` | 리스트 병합 |
| 슬라이싱 | `l[1:3]` | 부분 리스트 추출 |
| 짝수 인덱스 | `l[1::2]` | 2, 4, 6번째 원소 |
| 추가 | `l.append(x)` | 맨 뒤에 추가 |
| 삭제 | `l.remove(x)` | 해당 값 제거 |
| 삽입 | `l.insert(i, x)` | i번째 위치에 삽입 |
| 뒤집기 | `l[::-1]` | 역순 리스트 생성 |
| 비우기 | `l.clear()` | 모든 원소 삭제 |

📘 예시:
```python
l = [1, 2, 3, 4]
l.remove(3)
l.insert(2, 3)
l.append(5)
reversed_l = l[::-1]
```

---

## 📒 4. 튜플 (tuple)

| 기능 | 코드 | 설명 |
|------|------|------|
| 길이 | `len(t)` | 원소 개수 |
| 포함 여부 | `1 in t` | 존재 여부 |
| 이어붙이기 | `t + t` | 새로운 튜플 반환 |
| 슬라이싱 | `t[1:3]` | 부분 튜플 생성 |
| 개수 | `t.count(x)` | 특정 값 개수 |
| 위치 | `t.index(x)` | 특정 값 인덱스 |
| 불변성 | `t.remove()` ❌ | 튜플은 수정 불가 |

📘 예시:
```python
t = (1, 2, 3, 4)
combined = t + (5, 6, 7, 8)
reversed_t = t[::-1]
```

---

## 📘 5. 집합 (set)

| 기능 | 코드 | 설명 |
|------|------|------|
| 길이 | `len(s)` | 원소 개수 |
| 포함 여부 | `1 in s` | 존재 확인 |
| 추가 | `s.add(x)` | 원소 추가 |
| 제거 | `s.remove(x)` | 원소 삭제 |
| 교집합 | `s & s2` | 공통 원소 |
| 합집합 | `s | s2` | 전체 원소 |
| 차집합 | `s - s2` | s에만 있는 원소 |
| 대칭차집합 | `s ^ s2` | 서로 다른 원소 |

📘 예시:
```python
s = {1, 2, 3, 4}
s2 = {3, 4, 5, 6}
intersection = s & s2
union = s | s2
difference = s - s2
```

---

## 📗 6. 딕셔너리 (dict)

| 기능 | 코드 | 설명 |
|------|------|------|
| 길이 | `len(d)` | key-value 개수 |
| key 목록 | `list(d.keys())` | key 리스트 |
| key 존재 | `4 in d` | key 확인 |
| 안전한 조회 | `d.get(4)` | 없으면 None |
| 병합 (3.9+) | `d | d2` | 딕셔너리 합치기 |

📘 예시:
```python
d = {1: "apple", 2: "banana"}
d2 = {3: "cherry"}
merged = d | d2
```

---

## 🧮 7. Numpy 기본

| 기능 | 코드 | 설명 |
|------|------|------|
| 단위행렬 | `np.identity(3)` | 3×3 단위행렬 |
| 표준정규분포 | `np.random.randn(3,3)` | 평균0,분산1 랜덤값 |
| 행렬곱 | `@` 또는 `np.dot(A, B)` | 행렬 간 곱 |
| 자료형 변환 | `.astype(type)` | 데이터 타입 변경 |
| 복사 | `.copy()` | 원본 유지 복사 |
| 슬라이싱 | `arr[1:3, :]` | 부분 행렬 |
| 평탄화 | `.flatten()` | 1차원 배열로 변환 |
| 반복 행렬 | `np.tile(v, (3,1))` | v를 3번 반복 |

📘 예시:
```python
x = np.identity(3)
y = np.random.randn(3,3)
z = x @ y
x_bool = x.astype(bool)
x_float = x.astype(float) * 1.1
```

---

## 📊 8. Pandas 기초

| 기능 | 코드 | 설명 |
|------|------|------|
| DataFrame 생성 | `pd.DataFrame(data)` | 기본 생성 |
| columns 지정 | `columns=["A","B"]` | 열 이름 지정 |
| insert | `df.insert(0, "Name", names)` | 첫 열에 삽입 |
| dtype 변환 | `df["Age"].astype(int)` | 형 변환 |
| 비교 | `df.equals(df2)` | 완전 동일 여부 확인 |

📘 예시:
```python
names = ["Alice", "Bob", "Charlie"]
nums = np.array([[25,85.5],[30,90.3],[35,78.9]])

df = pd.DataFrame({
    "Name": names,
    "Age": nums[:,0].astype(int),
    "Score": nums[:,1]
})
```

---

## 🎯 핵심 요약

| 분류 | 주요 메서드 | 특징 |
|------|--------------|------|
| 숫자형 | `abs()`, `round()`, `pow()`, `math.isclose()` | 수학 연산 |
| 문자열 | `.lower()`, `.upper()`, `.endswith()`, `.isdigit()` | 텍스트 처리 |
| 리스트 | `.append()`, `.remove()`, `.insert()`, `.clear()` | 가변 시퀀스 |
| 튜플 | `.count()`, `.index()` | 불변 시퀀스 |
| 집합 | `&`, `|`, `-`, `^` | 중복 없는 집합 연산 |
| 딕셔너리 | `.get()`, `.keys()`, `|` | key-value 구조 |
| Numpy | `.astype()`, `.flatten()`, `@` | 수치 연산에 최적화 |
| Pandas | `.insert()`, `.astype()`, `.equals()` | 표 형태 데이터 처리 |

---

> 💬 **한 줄 요약:**  
> Python의 기본 자료형은 데이터 구조마다 메서드가 다르고,  
> Numpy는 수학적 계산, Pandas는 표형 데이터 분석에 특화되어 있다.
