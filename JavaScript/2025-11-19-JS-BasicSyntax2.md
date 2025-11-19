# JavaScript 객체, this, 배열, Helper Methods 정리

## 1. 객체(Object)

### 1-1. 객체 개념
- key로 구분된 데이터 집합을 저장하는 자료형.
- JavaScript의 핵심 자료구조로, 중괄호 `{}` 내부에 key:value 쌍을 여러 개 작성할 수 있다.
- key는 문자열만 가능하며, value는 모든 자료형 허용.

### 1-2. 객체 구조 예시

```jsx
const user = {
  name: 'Alice',
  'key with space': true,
  greeting: function () {
    return 'hello'
  }
}
```

### 1-3. 객체 속성 참조
- 점 표기법: user.name
- 대괄호 표기법: user['key with space']  
  (띄어쓰기, 특수문자 있으면 반드시 대괄호 사용)

```jsx
console.log(user.name)
console.log(user['key with space'])

user.address = 'korea'
user.name = 'Bella'
delete user.name
```

### 1-4. in 연산자
- 객체에 특정 key가 존재하는지만 확인하는 연산자.

```jsx
console.log('greeting' in user)
console.log('country' in user)
```

- 값 존재 여부를 확인하는 기능은 아님.
- 상속된 속성까지 검사하므로, 값 포함 여부는 hasOwnProperty를 사용해야 정확함.

---

## 2. 메서드(Method)

### 2-1. 메서드란?
- 객체 속성 중 값이 함수인 경우를 메서드라고 함.
- object.method() 형태로 호출.

```jsx
console.log(user.greeting())
```

### 2-2. 메서드 기본 문법

```jsx
const myObj = {
  myFunc: function () {
    return 'Hello'
  }
}
```

단축 문법:

```jsx
const myObj = {
  myFunc() {
    return 'Hello'
  }
}
```

---

## 3. this 키워드

### 3-1. this 개념
- JavaScript의 this는 함수가 호출되는 방식에 따라 동적으로 결정됨.
- 메서드를 호출한 객체를 가리킴.

### 3-2. 호출 방식에 따른 this

| 호출 방식 | this가 가리키는 대상 |
|---------|-----------------------|
| 일반 함수 호출 | 전역 객체 |
| 메서드 호출 | 메서드를 호출한 객체 |

### 3-3. 예시

일반 함수 호출:

```jsx
const myFunc = function () {
  return this
}

console.log(myFunc())  // window
```

메서드 호출:

```jsx
const myObj = {
  data: 1,
  myFunc: function () {
    return this
  }
}

console.log(myObj.myFunc())  // myObj
```

### 3-4. 중첩 함수에서 this 문제점

```jsx
const myObj2 = {
  numbers: [1, 2, 3],
  myFunc: function () {
    this.numbers.forEach(function (number) {
      console.log(this)  // window
    })
  }
}
```

### 3-5. 해결: 화살표 함수

```jsx
const myObj3 = {
  numbers: [1, 2, 3],
  myFunc: function () {
    this.numbers.forEach((number) => {
      console.log(this)  // myObj3
    })
  }
}
```

---

## 4. 추가 객체 문법 정리

### 4-1. 단축 속성

```jsx
const name = 'Alice'
const age = 30

const user = { name, age }
```

### 4-2. 단축 메서드

```jsx
const obj = {
  hi() {
    return 'hello'
  }
}
```

### 4-3. 계산된 속성 (computed property)

```jsx
const key = 'score'
const obj = {
  [key]: 100
}
```

### 4-4. 구조 분해 할당

```jsx
const userInfo = {
  firstName: 'Alice',
  userId: 'alice123',
  email: 'a@gmail.com'
}

const { firstName, userId, email } = userInfo
```

함수 매개변수에서 활용:

```jsx
function printInfo({ name, age }) {
  console.log(name, age)
}
```

### 4-5. 객체 전개 연산자 (...)

```jsx
const obj = { b: 2, c: 3 }
const newObj = { a: 1, ...obj }
```

### 4-6. Object.keys / values / entries

```jsx
const profile = { name: 'Alice', age: 30 }

Object.keys(profile)
Object.values(profile)
Object.entries(profile)
```

### 4-7. Optional chaining (?.)

```jsx
console.log(user.address?.street)
console.log(user.nonMethod?.())
```

---

## 5. JSON

### 5-1. Object → JSON

```jsx
JSON.stringify(jsObject)
```

### 5-2. JSON → Object

```jsx
JSON.parse(jsonText)
```

---

## 6. 배열(Array)

### 6-1. 배열 개념
- 순서가 있는 데이터 컬렉션 구조.

### 6-2. 배열 예시

```jsx
const names = ['Alice', 'Bella', 'Cathy']
names[0]
names.length
```

---

## 7. 배열 메서드(push, pop, unshift, shift)

```jsx
names.push('Dan')
names.pop()

names.unshift('Eric')
names.shift()
```

---

## 8. Array Helper Methods

### 8-1. 콜백 함수 개념
- 다른 함수에 인자로 전달되는 함수.

### 8-2. forEach()

```jsx
names.forEach((name, index, array) => {
  console.log(name, index, array)
})
```

### 8-3. map()

```jsx
const result = numbers.map((num) => num * 2)
```

### 8-4. map 예시

```jsx
const persons = [{ name: 'Alice' }, { name: 'Bella' }]
const names = persons.map((p) => p.name)
```

---

## 9. 배열 순회 방식 비교

| 방식 | 특징 |
|------|------|
| for | break 가능 |
| for...of | 요소 직접 접근 가능 |
| forEach | break 불가 |

---

## 10. 기타 Helper Methods

| 메서드 | 설명 |
|--------|------|
| filter | 조건 true 요소만 반환 |
| find | 첫 true 요소 반환 |
| some | 하나라도 조건 만족 |
| every | 모두 만족 시 true |

---

## 11. 배열 + 전개 구문 (...)

```jsx
let parts = ['어깨', '무릎']
let lyrics = ['머리', ...parts, '발']
```

---

# 오늘 배운 핵심 총정리
1. 객체 구조, 메서드, this 동작 이해.
2. 단축 속성, 구조 분해 등 최신 JS 문법.
3. JSON 변환 방식.
4. 배열 메서드 및 Helper Methods 비교.
5. 순회 방식 3종(for, for...of, forEach) 차이.
