# 📘 TIL - Django CRUD & HTTP Methods

---

## 1. Read (조회)

### 전체 게시글 조회
- DB에서 모든 Article을 불러와 index 페이지에서 출력.

### 단일 게시글 조회
- URL 패턴에 `<int:pk>/` 를 사용.
- `Article.objects.get(pk=pk)` 로 특정 게시글 조회.

```python
# articles/urls.py
path('<int:pk>/', views.detail, name="detail")

# articles/views.py
def detail(request, pk):
    article = Article.objects.get(pk=pk)
    context = {'article': article}
    return render(request, 'articles/detail.html', context)
```

- index.html에서 각 게시글 제목에 `detail` 페이지로 이동하는 링크 추가.

---

## 2. Create (생성)

### Create에 필요한 View 함수
- `new`: 사용자 입력 form 페이지 렌더링
- `create`: form 데이터를 받아 DB에 저장

### new.html
```html
<form action="{% url 'articles:create' %}" method="POST">
  {% csrf_token %}
  <label for="title">Title:</label>
  <input type="text" name="title" id="title">

  <label for="content">Content:</label>
  <textarea name="content" id="content"></textarea>

  <input type="submit">
</form>
```

### views.py
```python
from django.shortcuts import render, redirect

def create(request):
    title = request.POST.get('title')
    content = request.POST.get('content')
    article = Article(title=title, content=content)
    article.save()
    return redirect('articles:detail', article.pk)
```

---

## 3. Delete (삭제)

- `detail.html` 에서 삭제 버튼을 POST 방식으로 제출
- CSRF token 필수

```python
# articles/urls.py
path('<int:pk>/delete/', views.delete, name="delete")

# articles/views.py
def delete(request, pk):
    article = Article.objects.get(pk=pk)
    article.delete()
    return redirect('articles:index')
```

```html
<form action="{% url 'articles:delete' article.pk %}" method="POST">
  {% csrf_token %}
  <input type="submit" value="DELETE">
</form>
```

---

## 4. Update (수정)

### Update에 필요한 View 함수
- `edit`: 수정 form 렌더링
- `update`: form 데이터를 받아 DB 저장

```python
# articles/urls.py
path('<int:pk>/edit/', views.edit, name="edit"),
path('<int:pk>/update/', views.update, name="update"),

# articles/views.py
def edit(request, pk):
    article = Article.objects.get(pk=pk)
    context = {'article': article}
    return render(request, 'articles/edit.html', context)

def update(request, pk):
    article = Article.objects.get(pk=pk)
    article.title = request.POST.get('title')
    article.content = request.POST.get('content')
    article.save()
    return redirect('articles:detail', article.pk)
```

```html
<!-- edit.html -->
<form action="{% url 'articles:update' article.pk %}" method="POST">
  {% csrf_token %}
  <input type="text" name="title" value="{{ article.title }}">
  <textarea name="content">{{ article.content }}</textarea>
  <input type="submit">
</form>
```

---

## 5. HTTP Request Methods

### GET
- **데이터 조회**
- URL Query String 사용 (데이터가 노출됨)
- 캐싱 가능

### POST
- **데이터 생성/변경**
- HTTP Body로 데이터 전송 (데이터 노출 안 됨)
- 캐싱 불가
- Django에서 DB 조작(생성/수정/삭제)은 POST 사용

---

## 6. CSRF (Cross-Site Request Forgery)

- 사용자가 모르게 위조된 요청을 보내는 공격.
- Django는 이를 막기 위해 `csrf_token` 제공.
- **POST 요청에는 반드시 CSRF Token 필요**

```html
<form method="POST">
  {% csrf_token %}
</form>
```

---

## 7. Redirect

- **POST-Redirect-GET 패턴**:  
  데이터 저장 후, 결과 페이지를 바로 반환하는 대신 `redirect()` 사용.
- 이유:
  - 새로고침 시 중복 저장 방지
  - UX 개선 (사용자는 detail 페이지로 이동)

```python
def create(request):
    ...
    return redirect('articles:detail', article.pk)
```

---

## 8. GET vs POST 정리

| 구분 | GET | POST |
|------|-----|------|
| 목적 | 조회 | 생성 / 수정 / 삭제 |
| 데이터 전송 | URL Query String | HTTP Body |
| 보안 | 노출됨 | 노출 안 됨 |
| 캐싱 | 가능 | 불가능 |
| 사용 예시 | 검색, 페이지 요청 | 로그인, 글쓰기, 파일 업로드 |

---

✅ 오늘은 **Django CRUD (Create, Read, Update, Delete)** 와 **HTTP Method / CSRF / Redirect** 의 기본기를 정리했다.  
특히 **POST-Redirect-GET 패턴**과 **CSRF Token** 개념이 중요하다.


---
## 9. Django POST-Redirect-GET 흐름 (중요)

Django에서 **데이터 생성(POST)** → **Redirect** → **데이터 조회(GET)** 흐름은 가장 중요한 기본 동작 원리입니다.  
이를 단계별로 살펴보면 다음과 같습니다.

---

### 📌 동작 순서

1. **[POST] 게시글 작성 요청 (Client → Django)**
   - 사용자가 글 작성 form에 입력 데이터를 작성 후 제출.
   - 브라우저가 `POST` 요청을 보내며 입력된 데이터(title, content 등)가 함께 전달됨.

2. **create view 함수 호출 (Django)**
   - Django 서버가 `create` view 함수를 실행.
   - 전달된 데이터로 Article 객체를 생성하고 DB에 저장.

3. **redirect 응답 (Django → Client)**
   - `create` view는 HTML을 바로 응답하지 않고, **redirect**를 반환.
   - 클라이언트(브라우저)에게 “detail 주소로 다시 요청하라”는 지시를 보냄.

4. **[GET] detail 페이지 요청 (Client → Django)**
   - 브라우저가 서버의 지시에 따라 새로 저장된 글의 detail 페이지 주소로 `GET` 요청을 보냄.

5. **detail view 함수 호출 (Django)**
   - Django 서버가 `detail` view를 실행.
   - 새로 작성된 게시글을 DB에서 불러옴.

6. **detail 페이지 응답 (Django → Client)**
   - Django가 detail 템플릿을 렌더링하여 사용자에게 응답.
   - 사용자는 자신이 작성한 글의 상세 페이지를 확인할 수 있음.

---

### 🔑 정리

- **POST**: 데이터 생성 요청 (글쓰기, DB에 영향)
- **Redirect**: 클라이언트에게 detail 페이지를 새로 요청하도록 유도
- **GET**: detail 페이지 조회 요청
- **최종 응답**: detail 페이지 화면 표시

👉 이 과정을 통해 **중복 게시물 작성 방지**와 **UX 향상**을 동시에 얻을 수 있음.  

