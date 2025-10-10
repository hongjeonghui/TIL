# 🧱 TIL - Django Static & Media Files

## 📂 Static files

### ✅ Static files란?

* 서버 측에서 변경되지 않고 고정적으로 제공되는 파일
* 대표 예시: **CSS**, **JavaScript**, **이미지**, **폰트 파일**
* 반대 개념: **Dynamic files (동적 파일)**

---

### 🌐 웹 서버와 정적 파일

#### 🔹 웹 서버의 역할

* 클라이언트가 요청한 **URL에 해당하는 자원(Resource)** 을 찾아 응답하는 것
* 예: “A-3 구역의 3번째 책 주세요” → URL이 위치, 책이 자원

#### 🔹 정적 파일과 URL의 관계

1. 웹 서버는 URL을 기반으로 정적 파일을 찾아 제공
2. 정적 파일을 사용자에게 보여주려면 **고유한 주소(URL)** 가 필요

#### ⚙️ 처리 과정 요약

1. 사용자: `http://example.com/images/logo.png` 요청
2. 서버: `/images/logo.png` 파일 탐색
3. 서버: 이미지 파일을 응답으로 전송
4. 브라우저: 이미지를 화면에 렌더링

---

### 📁 Static files 기본 경로

#### 🔸 기본 경로

* `app_name/static/`

#### 🔸 CSS 파일 불러오기

```html
{% load static %}
<link rel="stylesheet" href="{% static 'stylesheets/style.css' %}">
```

#### 🔸 주요 태그 설명

* `{% load static %}` → static 관련 태그 사용 선언
* `{% static '경로' %}` → STATIC_URL 기준으로 파일 경로 계산

#### 🔸 STATIC_URL

* 정적 파일의 웹 접근 경로 접두사
* 예시:

  ```python
  STATIC_URL = 'static/'
  ```

  * `static/`이라는 URL 별명으로 접근 가능

#### 🔸 이미지 예시

```html
<img src="{% static 'images/sample-1.png' %}" alt="sample image">
```

정적 파일 URL 생성 구조:

```
URL + STATIC_URL + 정적파일경로
```

---

### 🧭 Static files 추가 경로

#### 🔸 STATICFILES_DIRS 설정

* 기본 경로 외 추가 경로 지정

```python
# settings.py
STATICFILES_DIRS = [
    BASE_DIR / 'static',
]
```

* 여러 앱에서 공통으로 쓰는 파일 관리용

#### 🔸 예시: 추가 경로 이미지 제공

```html
<img src="{% static 'sample-2.png' %}" alt="sample image">
```

#### 🔸 핵심 개념 정리

| 구분       | 개념         | 비유               |
| -------- | ---------- | ---------------- |
| 서버 내부    | 실제 파일 경로   | 내 방 책상 위 세 번째 서랍 |
| 웹 (브라우저) | URL (웹 주소) | 서울시 강남구 테헤란로 999 |

---

## 🖼️ Media files

### ✅ Media files란?

* 사용자가 직접 업로드하는 파일
* 예: 프로필 사진, 첨부 이미지 등
* **Static**은 개발자가 미리 준비한 파일, **Media**는 사용자 생성 파일

---

### 📸 이미지 업로드 설정

#### 🔹 ImageField()

* 이미지 업로드용 모델 필드
* 실제 파일은 서버 폴더(`MEDIA_ROOT`)에 저장, DB에는 경로만 저장됨

```python
class Article(models.Model):
    image = models.ImageField(upload_to='images/')
```

---

### ⚙️ Media 설정

#### 🔸 settings.py

```python
MEDIA_ROOT = BASE_DIR / 'media'
MEDIA_URL = 'media/'
```

#### 🔸 urls.py

```python
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path('admin/', admin.site.urls),
    path('articles/', include('articles.urls')),
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

📌 역할:

* `MEDIA_URL`: 웹에서 접근 가능한 주소 접두사
* `document_root`: 실제 파일이 저장된 폴더

---

### ✏️ 이미지 업로드 구현 절차

#### 1️⃣ 모델 수정

```python
class Article(models.Model):
    title = models.CharField(max_length=10)
    content = models.TextField()
    image = models.ImageField(upload_to='images/', blank=True)
```

#### 2️⃣ Pillow 설치

```
pip install Pillow
```

#### 3️⃣ HTML Form 수정

```html
<form action="{% url 'articles:create' %}" method="POST" enctype="multipart/form-data">
  {% csrf_token %}
  {{ form }}
  <input type="submit">
</form>
```

#### 4️⃣ views.py 수정

```python
def create(request):
    if request.method == 'POST':
        form = ArticleForm(request.POST, request.FILES)
```

📌 파일은 `request.FILES`로 함께 전달해야 함

---

### 🖼️ 업로드 이미지 제공

```html
{% if article.image %}
    <img src="{{ article.image.url }}" alt="image">
{% endif %}
```

* `.url` 속성: 업로드된 파일의 접근 가능한 웹 주소 반환

---

### 🔄 업로드 이미지 수정

#### update.html

```html
<form action="{% url 'articles:update' article.pk %}" method="POST" enctype="multipart/form-data">
  {% csrf_token %}
  {{ form }}
  <input type="submit">
</form>
```

#### views.py

```python
def update(request, pk):
    article = Article.objects.get(pk=pk)
    if request.method == 'POST':
        form = ArticleForm(request.POST, request.FILES, instance=article)
```

---

## 🧩 핵심 요약

| 구분    | Static Files                        | Media Files               |
| ----- | ----------------------------------- | ------------------------- |
| 주체    | 개발자                                 | 사용자                       |
| 내용    | CSS, JS, 이미지 등                      | 업로드된 이미지, 파일              |
| 저장 위치 | `app/static/` or `STATICFILES_DIRS` | `MEDIA_ROOT`              |
| 접근 경로 | `{% static %}`                      | `.url` 속성                 |
| 설정 파일 | `STATIC_URL`, `STATICFILES_DIRS`    | `MEDIA_URL`, `MEDIA_ROOT` |
