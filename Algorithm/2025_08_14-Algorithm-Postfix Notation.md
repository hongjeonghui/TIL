# 📌 후위 표기법 (Postfix Notation)

## 1. 개념

### 📍 중위 표기법 (Infix Notation)
- 연산자를 **피연산자 사이**에 표기
- 예: `A + B`, `(A + B) * C`

### 📍 후위 표기법 (Postfix Notation)
- 연산자를 **피연산자 뒤**에 표기
- 예: `AB+`, `AB+C*`

### 📍 후위 표기법의 장점
- **연산자 우선순위**나 **괄호** 고려 없이 연산 순서 명확
- 스택을 활용해 쉽게 **변환** 및 **계산** 가능
- 컴파일러, 계산기 등에서 식 처리를 단순화

---

## 2. 중위 → 후위 변환 알고리즘

### 2.1 변환 규칙
1. **피연산자**: 바로 결과(Postfix)에 추가
2. **연산자**: 스택 top의 연산자와 **우선순위** 비교 후 처리
3. **왼쪽 괄호 `(`**: 스택에 push
4. **오른쪽 괄호 `)`**: `(`가 나올 때까지 pop하여 결과에 추가, `(`는 제거
5. 모든 입력 처리 후, 스택에 남은 연산자를 pop하여 결과에 추가

---

### 2.2 연산자 우선순위
| 연산자 | isp(스택 내부) | icp(스택 외부) | 특징 |
|--------|---------------|---------------|------|
| `(`    | 0             | 4             | 스택 안에서는 최저, 들어올 때는 최고 |
| `^`    | 3             | 4             | 거듭제곱(오른쪽 결합성) |
| `*` `/`| 2             | 2             | 곱셈, 나눗셈 |
| `+` `-`| 1             | 1             | 덧셈, 뺄셈 |

---

### 2.3 변환 코드 예시
```python
def infix_to_postfix(expression):
    precedence = {'+': 1, '-': 1, '*': 2, '/': 2}
    stack = []
    result = []

    for token in expression:
        if token.isalnum():  # 피연산자
            result.append(token)
        elif token == '(':
            stack.append(token)
        elif token == ')':
            while stack and stack[-1] != '(':
                result.append(stack.pop())
            stack.pop()
        else:  # 연산자
            while (stack and stack[-1] != '(' and
                   precedence.get(stack[-1], 0) >= precedence.get(token, 0)):
                result.append(stack.pop())
            stack.append(token)

    while stack:
        result.append(stack.pop())

    return ''.join(result)

# 테스트
expr1 = '(A+B)*C'
expr2 = '(A+B)*(C-D)'
print(infix_to_postfix(expr1))  # "AB+C*"
print(infix_to_postfix(expr2))  # "AB+CD-*"
