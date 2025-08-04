# 📘 TIL - 상속, super(), 예외 처리

---

## ✅ 클래스 상속 (Inheritance)

### ✅ 기본 상속

```python
# 부모 클래스
class Animal:
    def eat(self):
        print('먹는 중')

# 자식 클래스
class Dog(Animal):
    def bark(self):
        print('멍멍')

my_dog = Dog()
my_dog.bark()  # 멍멍
my_dog.eat()   # 먹는 중
```

### ✅ 상속을 활용한 중복 제거

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def talk(self):
        print(f'반갑습니다. {self.name}입니다.')

class Professor(Person):
    def __init__(self, name, age, department):
        super().__init__(name, age)
        self.department = department

class Student(Person):
    def __init__(self, name, age, gpa):
        super().__init__(name, age)
        self.gpa = gpa

p1 = Professor('박교수', 49, '컴퓨터공학과')
s1 = Student('김학생', 20, 3.5)

p1.talk()  # 반갑습니다. 박교수입니다.
s1.talk()  # 반갑습니다. 김학생입니다.
```

---

## ✅ 메서드 오버라이딩 (Overriding)

```python
class Animal:
    def eat(self):
        print('Animal이 먹는 중')

class Dog(Animal):
    def eat(self):  # 오버라이딩
        print('Dog가 먹는 중')

my_dog = Dog()
my_dog.eat()  # Dog가 먹는 중
```

---

## ✅ 다중 상속 & MRO

```python
class Person:
    def __init__(self, name):
        self.name = name

class Mom(Person):
    gene = 'XX'
    def swim(self):
        return '엄마가 수영'

class Dad(Person):
    gene = 'XY'
    def walk(self):
        return '아빠가 걷기'

class FirstChild(Dad, Mom):
    def swim(self):
        return '첫째가 수영'
    def cry(self):
        return '첫째가 응애'

baby = FirstChild('아가')
print(baby.cry())      # 첫째가 응애
print(baby.swim())     # 첫째가 수영
print(baby.walk())     # 아빠가 걷기
print(baby.gene)       # XY (상속 순서에 따라 Dad의 gene 사용)
```

---

## ✅ super() 사용 예시

### 단일 상속

```python
class Person:
    def __init__(self, name, age, number, email):
        self.name = name
        self.age = age
        self.number = number
        self.email = email

class Student(Person):
    def __init__(self, name, age, number, email, student_id):
        super().__init__(name, age, number, email)
        self.student_id = student_id
```

### 다중 상속

```python
class ParentA:
    def __init__(self):
        self.value_a = 'ParentA'
    def show_value(self):
        print(f'Value from ParentA: {self.value_a}')

class ParentB:
    def __init__(self):
        self.value_b = 'ParentB'
    def show_value(self):
        print(f'Value from ParentB: {self.value_b}')

class Child(ParentA, ParentB):
    def __init__(self):
        super().__init__()  # MRO에 따라 ParentA 호출
        self.value_c = 'Child'
    def show_value(self):
        super().show_value()  # ParentA의 show_value 호출
        print(f'Value from Child: {self.value_c}')

child = Child()
child.show_value()
print(child.value_c)  # Child
print(child.value_a)  # ParentA
```

---

## ✅ 예외 처리 (try-except)

### 기본 구조

```python
try:
    x = int(input('숫자를 입력하세요: '))
    y = 10 / x
except ZeroDivisionError:
    print('0으로 나눌 수 없습니다.')
except ValueError:
    print('유효한 숫자가 아닙니다.')
else:
    print(f'결과: {y}')
finally:
    print('프로그램이 종료되었습니다.')
```

### 예외 처리 예시

```python
try:
    result = 10 / 0
except ZeroDivisionError:
    print('0으로 나눌 수 없습니다.')

try:
    num = int(input('숫자 입력 : '))
except ValueError:
    print('숫자가 아닙니다.')
```

---

## ✅ 예외 요약

| 구분         | 설명                                       |
|--------------|--------------------------------------------|
| 문법 에러    | 실행 전 발생. 코드 구조 자체가 잘못됨       |
| 예외         | 실행 중 발생. 조건에 따라 오류 발생         |
| 예외 처리    | try-except-finally로 안정적 흐름 유지       |

---

## ✅ super() / 상속 정리 요약

| 항목        | 설명                                                   |
|-------------|--------------------------------------------------------|
| 상속        | 부모 클래스의 속성과 메서드를 자식 클래스가 물려받음  |
| 오버라이딩  | 부모의 메서드를 자식에서 재정의                        |
| 다중상속    | 여러 부모 클래스로부터 상속, MRO로 순서 결정           |
| super()     | 부모 메서드를 명시적으로 호출할 때 사용 (MRO 기반 작동) |

