# TIL: Django 팔로우 기능 구현 및 ORM 최적화 
---

## 1. 프로필 페이지 구현

### (1) 개요
- 각 회원의 개인 프로필 페이지를 만들어 팔로우 기능과 연동할 준비를 함.
- 다른 사용자 프로필에도 접근할 수 있어야 하며, 각 사용자가 작성한 글/댓글/좋아요한 글을 표시.

### (2) URL 설정
```python
# accounts/urls.py
app_name = 'accounts'

urlpatterns = [
    path('profile/<username>/', views.profile, name='profile'),
]
```

### (3) View 함수 작성
```python
# accounts/views.py
from django.contrib.auth import get_user_model
from django.shortcuts import render

def profile(request, username):
    User = get_user_model()
    person = User.objects.get(username=username)
    context = {'person': person}
    return render(request, 'accounts/profile.html', context)
```

### (4) Template 구성
```html
<!-- accounts/profile.html -->
<h1>{{ person.username }}님의 프로필</h1>
<hr>

<h2>{{ person.username }}가 작성한 게시글</h2>
{% for article in person.article_set.all %}
  <div>{{ article.title }}</div>
{% endfor %}
<hr>

<h2>{{ person.username }}가 작성한 댓글</h2>
{% for comment in person.comment_set.all %}
  <div>{{ comment.content }}</div>
{% endfor %}
<hr>

<h2>{{ person.username }}가 좋아요한 게시글</h2>
{% for article in person.like_articles.all %}
  <div>{{ article.title }}</div>
{% endfor %}
```

### (5) 프로필 접근 링크 추가
```html
<!-- articles/index.html -->
{% if request.user.is_authenticated %}
  <h3>Hello, {{ user.username }}</h3>
  <a href="{% url 'accounts:profile' user.username %}">내 프로필</a>
{% endif %}

{% for article in articles %}
  <p>작성자 : 
     <a href="{% url 'accounts:profile' article.user.username %}">
       {{ article.user }}</a></p>
{% endfor %}
```

---

## 2. 팔로우 기능 구현

### (1) 모델 관계 설정
- **User ↔ User** : M:N 관계  
- 자기 자신과의 관계이므로 `'self'` 사용  
- 단방향 관계이므로 `symmetrical=False` 설정

```python
# accounts/models.py
class User(AbstractUser):
    followings = models.ManyToManyField('self', symmetrical=False, related_name='followers')
```

### (2) 팔로우 기능 URL
```python
# accounts/urls.py
path('<int:user_pk>/follow/', views.follow, name='follow'),
```

### (3) View 함수
```python
# accounts/views.py
from django.contrib.auth.decorators import login_required
from django.shortcuts import redirect

@login_required
def follow(request, user_pk):
    User = get_user_model()
    person = User.objects.get(pk=user_pk)

    if person != request.user:
        if request.user in person.followers.all():
            person.followers.remove(request.user)
        else:
            person.followers.add(request.user)

    return redirect('accounts:profile', person.username)
```

### (4) Template
```html
<!-- accounts/profile.html -->
<div>
  팔로잉 : {{ person.followings.all|length }} /
  팔로워 : {{ person.followers.all|length }}
</div>

{% if request.user != person %}
  <form action="{% url 'accounts:follow' person.pk %}" method="POST">
    {% csrf_token %}
    {% if request.user in person.followers.all %}
      <input type="submit" value="Unfollow">
    {% else %}
      <input type="submit" value="Follow">
    {% endif %}
  </form>
{% endif %}
```

---

## 3. Fixtures (데이터 초기화 및 공유)

### (1) 개념
- Django의 **fixtures**는 DB 데이터를 JSON 등으로 저장하여 초기 세팅, 테스트, 협업 시 동일 데이터 환경을 유지하게 함.

### (2) 명령어
#### 데이터 추출 (`dumpdata`)
```bash
$ python manage.py dumpdata --indent 4 articles.article > articles.json
$ python manage.py dumpdata --indent 4 articles.comment > comments.json
$ python manage.py dumpdata --indent 4 accounts.user > users.json
```

#### 데이터 불러오기 (`loaddata`)
```bash
$ python manage.py loaddata users.json
$ python manage.py loaddata articles.json
$ python manage.py loaddata comments.json
```
- 순서 중요: **User → Article → Comment**
- 마이그레이션 완료 후 실행해야 함.
- 같은 PK 존재 시 중복 오류 발생 가능.

---

## 4. ORM 쿼리 최적화 (Improve Query)

### (1) N+1 Problem
- 기본 데이터(1) + 각 객체별 연관 데이터 조회(N) → 총 N+1 쿼리 발생.
- 데이터가 많을수록 **성능 저하**와 **DB 부하** 초래.

---

## 5. ORM 최적화 기법

### (1) annotate
```python
from django.db.models import Count
articles = Article.objects.annotate(comment_count=Count('comment')).order_by('-pk')
```
→ 쿼리: `11 → 1`개로 감소.

### (2) select_related
```python
articles = Article.objects.select_related('user').order_by('-pk')
```
→ 쿼리: `11 → 1`개로 감소.

### (3) prefetch_related
```python
articles = Article.objects.prefetch_related('comment_set').order_by('-pk')
```
→ 쿼리: `11 → 2`개로 감소.

### (4) select_related + prefetch_related 동시 사용
```python
from django.db.models import Prefetch
articles = Article.objects.prefetch_related(
    Prefetch('comment_set', queryset=Comment.objects.select_related('user'))
).order_by('-pk')
```
→ 쿼리: `111 → 2`개로 감소.

---

## 6. 핵심 요약

| 구분 | 목적 | 주요 사용 대상 | 쿼리 감소 효과 |
|------|------|----------------|----------------|
| `annotate` | 행별 계산 필드 추가 | 집계함수(Count, Sum 등) | 1회로 통합 |
| `select_related` | FK / OneToOne JOIN | 단방향 관계 | 1회로 통합 |
| `prefetch_related` | M:N / 역참조 미리 로드 | ManyToMany, 역참조 | 2회로 통합 |
| `Prefetch` | select_related와 병용 | 다단계 관계 최적화 | 2회 이하로 감소 |

---

## 7. 마무리 인용
> “작은 효율성에 대해서는 잊어버려라.  
> 섣부른 최적화는 모든 악의 근원이다.”  
> — Donald Knuth

---

📘 **오늘의 학습 요약**
- 팔로우 기능: `ManyToManyField('self')` + `symmetrical=False`
- Fixtures: `dumpdata` / `loaddata`로 DB 초기화 및 공유
- ORM 최적화: `annotate`, `select_related`, `prefetch_related`로 N+1 문제 해결
