## 📚 TIL - 비시퀀스 데이터 구조 (딕셔너리, 세트, 해시 테이블)

### 📌 Dictionary (딕셔너리)
- **정의**: `key-value` 쌍으로 데이터를 저장하는 해시 기반 자료구조
- **특징**:
  - key는 **고유하고 hashable**
  - value는 **중복 가능**, 모든 자료형 허용
  - 내부적으로 **해시 테이블 사용 → 빠른 접근**
- **주요 메서드**:
  - `.get(key, default)` : 안전한 조회
  - `.keys() / .values() / .items()` : 각각 key/value/쌍 확인
  - `.pop(key[, default])` : key 제거 후 value 반환
  - `.setdefault(key, default)` : key 없을 때 추가 후 반환
  - `.update(dict)` : 병합 및 값 덮어쓰기
  - `.clear()` : 전체 삭제

### 📌 Set (세트)
- **정의**: **중복 없는** 요소들의 **순서 없는** 집합
- **특징**:
  - 내부적으로 해시 테이블 기반
  - `add`, `remove`, `in` 연산이 빠름
- **주요 메서드**:
  - `.add(x)` : 요소 추가
  - `.update(iterable)` : 여러 요소 추가
  - `.remove(x)` : 요소 제거 (없으면 에러)
  - `.discard(x)` : 요소 제거 (없어도 에러 없음)
  - `.pop()` : **임의의 요소** 제거 및 반환 (순서 보장 X)
  - `.clear()` : 전체 삭제
- **집합 연산 메서드**:
  - `.union()`, `.intersection()`, `.difference()`
  - `.issubset()`, `.issuperset()`

### 📌 Hash Table (해시 테이블)
- **정의**: `key`를 해시 함수로 변환 → 해시 값으로 위치 계산 → 빠른 저장/검색
- **파이썬 동작 원리**:
  - `dict`, `set`은 모두 해시 테이블 기반
  - `dict`는 **삽입 순서 유지**, `set`은 **순서 없음**
- **hashable 조건**:
  - `hash()` 가능한 객체만 key/set 요소로 사용 가능
  - **불변 타입**만 허용 (`int`, `str`, `tuple` 등)
  - `list`, `dict`, `set`은 **unhashable**
- **해시 난수화**:
  - 보안 목적: 문자열 등은 실행마다 해시값 달라질 수 있음
  - → `set`에서 `pop()`은 실행마다 결과 달라질 수 있음

### ✅ 요약
| 구조     | 순서 | 중복 | 빠른 검색 | 활용 |
|----------|------|------|------------|------|
| dict     | O    | 값 가능 | key로 빠름 | key-value 저장 |
| set      | X    | X    | 빠름       | 중복 제거, 집합 연산 |
| hash table | 내부 구조 | - | O | dict, set 내부 동작 원리 |

