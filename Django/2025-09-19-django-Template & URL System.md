# 📘 TIL - Django Template & URL System

---

## 🎨 Django Template System
 
### 개념
- **Django Template System**
  - 파이썬 데이터(`context`)를 HTML 문서(`template`)와 결합해 **동적인 웹페이지 생성**
  - 로직과 표현을 분리하여 효율적인 유지보수 가능
  - 예: 뉴스 사이트 → 공통 레이아웃(템플릿) + 각기 다른 기사 데이터

---

## 📝 Django Template Language (DTL)

### 개념
- HTML에서 조건, 반복, 변수 등을 다룰 수 있는 **템플릿 전용 언어**
- 주요 구성 요소
  1. **Variable** → `{{ 변수명 }}` : context 데이터 출력
  2. **Filters** → `{{ 변수|필터 }}` : 값 가공
  3. **Tags** → `{% for %}`, `{% if %}` : 제어문
  4. **Comments** → `{# 주석 #}` : 주석 처리

### 코드 예시

#### (1) Variable
```python
# views.py
from django.shortcuts import render

def index(request):
    context = {
        "name": "Alice",
        "age": 20,
    }
    return render(request, "index.html", context)
```

```html
<!-- index.html -->
<p>이름: {{ name }}</p>
<p>나이: {{ age }}</p>
```

#### (2) Filters
```html
<p>{{ name|upper }}</p>   <!-- ALICE -->
<p>{{ age|add:"10" }}</p> <!-- 30 -->
```

#### (3) Tags
```html
{% if age >= 20 %}
  <p>성인입니다.</p>
{% else %}
  <p>미성년자입니다.</p>
{% endif %}

<ul>
  {% for fruit in fruits %}
    <li>{{ fruit }}</li>
  {% endfor %}
</ul>
```

#### (4) Comments
```html
{# 한 줄 주석 #}
{% comment %} 여러 줄 주석 {% endcomment %}
```

---

## 🧩 템플릿 상속

### 개념
- 여러 페이지에 공통 요소를 중복 작성하지 않도록 함
- **extends**: 상위 템플릿 상속
- **block**: 자식 템플릿이 덮어쓸 수 있는 영역

### 코드 예시

#### base.html
```html
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>{% block title %}기본 제목{% endblock %}</title>
</head>
<body>
  <header>공통 헤더</header>
  <main>
    {% block content %}{% endblock %}
  </main>
  <footer>공통 푸터</footer>
</body>
</html>
```

#### child.html
```html
{% extends "base.html" %}

{% block title %}자식 페이지{% endblock %}

{% block content %}
  <h1>여기는 자식 페이지입니다!</h1>
{% endblock %}
```

---

## 📡 요청과 응답 (Form)

### 개념
- `form` 태그를 통해 사용자 입력 데이터를 서버로 전송
- 주요 속성
  - `action`: 데이터를 보낼 URL
  - `method`: 요청 방식 (GET, POST)
  - `input` 요소에 **name** 속성이 있어야 서버에서 인식 가능

### 코드 예시

#### throw.html
```html
<form action="/catch/" method="GET">
  <input type="text" name="message">
  <button type="submit">보내기</button>
</form>
```

#### views.py
```python
def throw(request):
    return render(request, "throw.html")

def catch(request):
    msg = request.GET.get("message")   # form에서 넘어온 데이터 추출
    context = {"msg": msg}
    return render(request, "catch.html", context)
```

#### catch.html
```html
<p>당신이 보낸 메시지: {{ msg }}</p>
```

---

## 🌐 Django URLs

### 개념
- URL → View 함수 매핑 역할
- **Variable Routing**: URL 일부를 변수로 사용 가능
- **Path Converter**: `<int:num>`, `<str:name>` 등으로 타입 지정

### 코드 예시
```python
# urls.py
from django.urls import path
from . import views

urlpatterns = [
    path("articles/<int:num>/", views.detail, name="detail"),
    path("greet/<str:name>/", views.greeting, name="greeting"),
]
```

```python
# views.py
from django.http import HttpResponse

def detail(request, num):
    return HttpResponse(f"{num}번 글입니다.")

def greeting(request, name):
    return HttpResponse(f"안녕하세요, {name}님!")
```

---

## 📂 App URL 정의

### 개념
- 앱마다 urls.py를 분리하여 관리
- 프로젝트 urls.py에서 `include()`로 연결

### 코드 예시

#### 프로젝트 urls.py
```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path("admin/", admin.site.urls),
    path("articles/", include("articles.urls")),
    path("pages/", include("pages.urls")),
]
```

#### 앱 urls.py (articles/urls.py)
```python
from django.urls import path
from . import views

app_name = "articles"

urlpatterns = [
    path("index/", views.index, name="index"),
    path("<int:num>/", views.detail, name="detail"),
]
```

---

## 🏷️ URL 이름 지정

### 개념
- URL 변경 시 직접 경로를 수정하는 대신 **name 속성**으로 참조
- 템플릿에서 `{% url 'app_name:url_name' %}` 형태로 사용

### 코드 예시
```python
# urls.py
path("index/", views.index, name="index")
```

```html
<a href="{% url 'articles:index' %}">게시판으로 이동</a>
<a href="{% url 'articles:detail' 10 %}">10번 글 보기</a>
```

---

## 🗂️ URL 이름 공간 (Namespace)

### 개념
- 앱마다 같은 이름의 URL 패턴이 있을 수 있음 → 충돌 방지
- **app_name** 변수 지정 후 `{% url 'app_name:url_name' %}`로 사용

### 코드 예시
```python
# articles/urls.py
app_name = "articles"

urlpatterns = [
    path("index/", views.index, name="index"),
]
```

```html
<a href="{% url 'articles:index' %}">게시판으로 이동</a>
```

---

# ✅ 오늘 배운 핵심 요약
1. **Template System**: context + template → 동적 페이지
2. **DTL**: 변수, 필터, 태그, 주석
3. **템플릿 상속**: `extends`, `block`
4. **Form 데이터 전송**: `action`, `method`, `name`
5. **요청/응답 흐름**: throw → catch
6. **Variable Routing**: `<int:num>`, `<str:name>`
7. **App URL 분리**: `include()` 활용
8. **URL 이름 지정 & Namespace**: `{% url 'app_name:url_name' %}`
