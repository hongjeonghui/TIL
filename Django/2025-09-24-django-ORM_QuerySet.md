# 📘 TIL - Django ORM & QuerySet API

---

## 🔹 ORM (Object-Relational Mapping)

- **정의**: 객체 지향 언어의 객체(Object)와 데이터베이스의 데이터를 매핑(Mapping)하는 기술
- **문제 상황**: Python ↔ SQL → 언어 차이로 직접 소통 불가
- **해결**: ORM은 번역자 역할을 하여 Python 코드로 DB 조작 가능
- **Django에서의 ORM 도구**: **QuerySet API**

---

## 🔹 QuerySet API

- **정의**: SQL 쿼리를 Python 코드로 직관적으로 다룰 수 있게 하는 ORM의 핵심 도구
- **주요 특징**
  - `.all()`, `.filter()`, `.get()` 같은 메서드 제공
  - SQL 작성 없이 CRUD 구현 가능
  - DB ↔ Django 간 데이터 변환을 ORM이 처리

- **동작 방식**
  1. Django → DB 요청: QuerySet API → ORM → SQL 변환
  2. DB → Django 응답: SQL 결과 → ORM → Python 객체(QuerySet/Instance) 변환

---

## 🔹 QuerySet API 기본 구조

```python
Article.objects.all()
```

- **Article (모델 클래스)**: DB 테이블을 표현하는 Python 클래스
- **objects (매니저)**: DB 조회(Query) 진입점
- **.all() (메서드)**: 모든 레코드를 조회하는 명령

---

## 🔹 Query (쿼리)

- **정의**: 데이터베이스에 원하는 데이터를 요청하는 것
- **Django Query 처리 과정**
  1. Python 코드(QuerySet API) 작성
  2. ORM이 SQL로 변환
  3. DB가 SQL 실행 후 결과 반환
  4. ORM이 QuerySet/객체로 변환

- **QuerySet**: 데이터베이스에서 전달되는 객체 목록(순회 가능)
- **단일 객체 반환 시**: 모델 인스턴스로 반환

---

## 🔹 CRUD with QuerySet API

### 1. Create (데이터 생성)

세 가지 방법:
1. 빈 객체 생성 후 값 할당 + `save()`
2. 초기 값과 함께 객체 생성 + `save()`
3. `create()` 메서드로 한 번에 생성 + 저장

```python
# 방법 1
article = Article()
article.title = 'Hello'
article.save()

# 방법 2
article = Article(title='Hi', content='Django ORM')
article.save()

# 방법 3
Article.objects.create(title='Hey', content='Django!')
```

---

### 2. Read (데이터 조회)

- **all()**: 전체 데이터 조회  
- **filter()**: 조건에 맞는 다수 데이터 조회 (QuerySet 반환)  
- **get()**: 조건에 맞는 단일 데이터 조회 (인스턴스 반환, 없으면 `DoesNotExist`, 여러 개면 `MultipleObjectsReturned` 발생)

```python
Article.objects.all()
Article.objects.filter(content='django!')
Article.objects.get(pk=1)
```

---

### 3. Update (데이터 수정)

```python
article = Article.objects.get(pk=1)
article.title = 'byebye'
article.save()
```

---

### 4. Delete (데이터 삭제)

```python
article = Article.objects.get(pk=1)
article.delete()
```

---

## 🔹 ORM with View

- View 함수 안에서 QuerySet API 활용 가능
- 예: 전체 게시글 조회

```python
# articles/views.py
from .models import Article
from django.shortcuts import render

def index(request):
    articles = Article.objects.all()
    context = {'articles': articles}
    return render(request, 'articles/index.html', context)
```

```html
<!-- articles/index.html -->
<h1>Articles</h1>
<hr>
{% for article in articles %}
  <p>글 번호: {{ article.pk }}</p>
  <p>글 제목: {{ article.title }}</p>
  <p>글 내용: {{ article.content }}</p>
  <hr>
{% endfor %}
```

---

## 🔹 Field Lookups (데이터 필터링)

- **정의**: 단순 일치(=) 외에도 다양한 조건으로 조회할 수 있는 기능
- **사용법**: `fieldname__조건` 형태 (더블 언더스코어 `__` 사용)

```python
# 'dja'가 content에 포함된 게시글 조회
Article.objects.filter(content__contains='dja')

# title이 'he'로 시작하는 게시글 조회
Article.objects.filter(title__startswith='he')
```

- **자주 쓰는 Lookups**
  - `exact` / `iexact` : 정확히 일치 / 대소문자 무시
  - `contains` / `icontains` : 문자열 포함 여부
  - `gt`, `gte`, `lt`, `lte` : 숫자/날짜 비교 (크다, 작다, 이상, 이하)

---

## ✅ 오늘의 정리

- ORM은 **SQL ↔ Python 객체 번역자**
- QuerySet API는 ORM의 **핵심 도구**
- CRUD 작업을 SQL 없이 **파이썬 코드로 직관적 처리 가능**
- Field Lookups을 통해 **복잡한 조건 검색도 간단히 구현 가능**
