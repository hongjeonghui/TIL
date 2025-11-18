# JavaScript 기본 개념 정리

## 1. 원시 자료형(Primitive Type)과 참조 자료형(Reference Type)

### 원시 자료형
- 값 자체가 변수에 직접 저장됨
- 불변(immutable)
- 변수에 할당될 때 값이 복사됨 → 서로 영향 없음
- 종류: Number, String, Boolean, null, undefined

#### 예시
```jsx
const a = 'bar'
console.log(a) // bar

a.toUpperCase()
console.log(a) // bar

let x = 10
let y = x

y = 20

console.log(x) // 10
console.log(y) // 20
```

### 참조 자료형
- 변수에 값이 아닌 **메모리 주소(참조)**가 저장됨
- 가변(mutable)
- 한 객체를 여러 변수가 참조하면 값 변경 시 모두 영향받음
- 종류: Object, Array, Function

#### 예시
```jsx
const obj1 = { name: 'Alice', age: 30 }
const obj2 = obj1

obj2.age = 40

console.log(obj1.age) // 40
console.log(obj2.age) // 40
```

---

## 2. 주요 원시 자료형 설명

### Number
```jsx
const a = 13
const b = -5
const c = 3.14
const d = 2.998e8
const e = Infinity
const f = -Infinity
const g = NaN
```

### String
```jsx
const firstName = 'Tony'
const lastName = 'Stark'
const fullName = firstName + lastName
console.log(fullName)
```

### Template Literals
```jsx
const age = 100
const message = `홍길동은 ${age}세 입니다.`
console.log(message)
```

### null vs undefined
```jsx
let a = null
let b
console.log(a) // null
console.log(b) // undefined
```

---

## 3. Boolean & 자동 형변환

| 타입 | false | true |
|------|-------|--------|
| undefined | 항상 false | X |
| null | 항상 false | X |
| Number | 0, -0, NaN | 나머지 숫자 |
| String | '' | 그 외 모든 문자열 |

---

## 4. 연산자

### 할당 연산자
```jsx
let a = 0
a += 10
a -= 3
a *= 10
a %= 7
```

### 증가 & 감소 연산자
```jsx
let x = 3
const y = x++
console.log(x, y) // 4 3

let a = 3
const b = ++a
console.log(a, b) // 4 4
```

### 비교 연산자
```jsx
3 > 2
'A' < 'B'
'가' < '나'
```

### 동등(==) vs 일치(===)
```jsx
console.log('1' == 1) // true
console.log('1' === 1) // false
```

### 논리 연산자
```jsx
1 && 0 // 0
0 || 1 // 1
!true // false
```

---

## 5. 조건문

### if 문
```jsx
const name = 'customer'
if (name === 'admin') {
  console.log('관리자님 환영해요')
} else if (name === 'customer') {
  console.log('고객님 환영해요')
} else {
  console.log(`반갑습니다. ${name}님`)
}
```

### 삼항 연산자
```jsx
const age = 20
const message = age >= 18 ? '성인' : '미성년자'
```

---

## 6. 반복문

### while
```jsx
let i = 0
while (i < 6) {
  console.log(i)
  i += 1
}
```

### for
```jsx
for (let i = 0; i < 6; i++) {
  console.log(i)
}
```

### for…in (객체 key 순회)
```jsx
const fruits = { a: 'apple', b: 'banana' }
for (const key in fruits) {
  console.log(key, fruits[key])
}
```

### for…of (배열 value 순회)
```jsx
const numbers = [0, 1, 2, 3]
for (const num of numbers) {
  console.log(num)
}
```

---

## 7. 함수

### 함수 선언 방식
#### 선언식
```jsx
function add(a, b) {
  return a + b
}
```

#### 표현식
```jsx
const sub = function (a, b) {
  return a - b
}
```

### 기본 매개변수
```jsx
const greeting = function (name = 'Anonymous') {
  return `Hi ${name}`
}
```

### Rest Parameter
```jsx
const myFunc = function (a, b, ...rest) {
  return [a, b, rest]
}
```

---

## 8. Spread Syntax

### 인자 확장
```jsx
function myFunc(x, y, z) {
  return x + y + z
}
const nums = [1, 2, 3]
myFunc(...nums)
```

### Rest와 Spread 관계
- Spread: 펼침
- Rest: 모음

---

## 9. 화살표 함수

```jsx
const arrow1 = name => `hello, ${name}`
```

변환 과정
```jsx
const arrow = function (name) { return `hello, ${name}` }

// function 제거
const arrow2 = (name) => { return `hello, ${name}` }

// 중괄호·return 제거
const arrow3 = name => `hello, ${name}`
```

---

## 학습정리
- 원시 vs 참조 자료형 차이 명확해짐
- == 대신 === 사용 중요성 파악
- for…in은 객체 중심, 배열에서는 for 또는 for…of 사용
- 함수 표현식과 선언식의 차이 및 호이스팅 이해
- Spread / Rest 활용 구조 숙지
- 화살표 함수 변환 패턴 파악

