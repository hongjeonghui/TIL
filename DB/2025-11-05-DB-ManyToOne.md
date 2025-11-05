# TIL: Django Many To One Relationships (N:1 관계)

## 1. 모델 관계 개념

### 관계의 정의
- 데이터베이스 내 **여러 테이블 간의 논리적 연결 관계**를 나타냄.

### 관계의 종류
| 관계 종류 | 설명 | 예시 |
|------------|--------|------|
| **1:1 (One to One)** | 한 테이블의 레코드는 다른 테이블의 한 레코드와만 연결됨 | 한 사람 ↔ 주민등록번호 |
| **N:1 (Many to One)** | 여러 개의 레코드가 하나의 레코드와 연결됨 | 여러 교육생(N) ↔ 한 강사(1) |
| **N:M (Many to Many)** | 여러 레코드가 여러 레코드와 상호 연결됨 | 여러 학생(N) ↔ 여러 과목(M) |
| **1:1 관계** | N:1 관계와 사용 방법이 유사함 | - |

- **N:1 관계의 핵심**  
  → 한 테이블의 여러 개 레코드가 다른 테이블의 한 레코드와 연결된다.

**예시**
- SSAFY Track(1) : Student(N)
- Comment(N) : Article(1)
- Order(N) : Customer(1)
- Country(1) : City(N)

---

## 2. 댓글과 게시글의 관계

- **Comment(N) : Article(1)**
  - 하나의 게시글에는 여러 개의 댓글이 달릴 수 있다.
  - 게시글이 없는 댓글은 존재하지 않는다.
  - 즉, "0개 이상의 댓글은 1개의 게시글에 작성될 수 있다."

---

## 3. ForeignKey를 이용한 관계 설정

### ForeignKey의 위치
- ForeignKey는 **항상 N 쪽 테이블**에 위치해야 함.
- Article(1) ↔ Comment(N) 관계에서는 `Comment` 모델에 ForeignKey를 둔다.

### ForeignKey 필드 문법
```python
class Comment(models.Model):
    article = models.ForeignKey(Article, on_delete=models.CASCADE)
    content = models.CharField(max_length=200)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
```

### on_delete 옵션 종류
| 옵션 | 설명 | 예시 |
|-------|-------|------|
| **CASCADE** | 부모가 삭제되면 자식도 함께 삭제 | 게시글 삭제 시 댓글 모두 삭제 |
| **PROTECT** | 자식이 존재하면 부모 삭제 불가 | 댓글이 존재하면 게시글 삭제 불가 |
| **SET_NULL** | 부모 삭제 시 외래키를 NULL로 설정 (null=True 필요) | 게시글 삭제 후 댓글의 FK를 NULL로 |

---

## 4. 댓글 생성 실습

### 기본 절차
```bash
$ python manage.py shell
>>> from articles.models import Article, Comment
>>> Article.objects.create(title='title', content='content')
```

### ForeignKey 누락 시 발생 오류
```python
comment = Comment(content='first comment')
comment.save()
# IntegrityError: NOT NULL constraint failed: articles_comment.article_id
```
- ForeignKey(게시글 정보)가 누락되었기 때문.

### 올바른 방법
```python
article = Article.objects.get(pk=1)
comment = Comment(content='first comment', article=article)
comment.save()
```

### 데이터 접근
```python
comment.article         # Article 객체 전체
comment.article_id      # Article의 PK 값
comment.article.content # 게시글의 내용
```

---

## 5. 관계 모델 참조 (정방향/역방향)

### 정방향 참조
- Comment → Article
```python
comment.article
```

### 역참조 (related manager)
- Article → Comment
- 기본 이름: `모델명_set`
```python
article.comment_set.all()
```

### 역참조 예시
```python
article = Article.objects.get(pk=1)
comments = article.comment_set.all()
for comment in comments:
    print(comment.content)
```

---

## 6. 댓글 CRUD 구현

### (1) CREATE (생성)

#### CommentForm 정의
```python
# articles/forms.py
from django import forms
from .models import Comment

class CommentForm(forms.ModelForm):
    class Meta:
        model = Comment
        fields = ('content', )
```
> ForeignKey(`article`)는 사용자 입력이 아닌 view에서 처리함.

#### View 수정
```python
def detail(request, pk):
    article = Article.objects.get(pk=pk)
    comment_form = CommentForm()
    context = {
        'article': article,
        'comment_form': comment_form,
    }
    return render(request, 'articles/detail.html', context)
```

#### Template 수정
```html
<!-- articles/detail.html -->
<form action="{% url 'articles:comments_create' article.pk %}" method="POST">
    {% csrf_token %}
    {{ comment_form }}
    <input type="submit" value="댓글 작성">
</form>
```

#### URL 설정
```python
# articles/urls.py
urlpatterns = [
    path('<int:pk>/comments/', views.comments_create, name='comments_create'),
]
```

#### 댓글 생성 로직
```python
def comments_create(request, pk):
    article = Article.objects.get(pk=pk)
    comment_form = CommentForm(request.POST)
    if comment_form.is_valid():
        comment = comment_form.save(commit=False)
        comment.article = article
        comment.save()
        return redirect('articles:detail', article.pk)
    context = {'article': article, 'comment_form': comment_form}
    return render(request, 'articles/detail.html', context)
```
> `commit=False` → DB 저장 전에 인스턴스만 생성하여 게시글 정보를 추가한 뒤 저장 가능.

---

### (2) READ (조회)

#### View
```python
def detail(request, pk):
    article = Article.objects.get(pk=pk)
    comment_form = CommentForm()
    comments = article.comment_set.all()
    context = {
        'article': article,
        'comment_form': comment_form,
        'comments': comments,
    }
    return render(request, 'articles/detail.html', context)
```

#### Template
```html
<h4>댓글 목록</h4>
<ul>
    {% for comment in comments %}
        <li>{{ comment.content }}</li>
    {% endfor %}
</ul>
```

---

### (3) DELETE (삭제)

#### URL 설정
```python
path(
    '<int:article_pk>/comments/<int:comment_pk>/delete/',
    views.comments_delete,
    name='comments_delete'
),
```

#### View
```python
def comments_delete(request, article_pk, comment_pk):
    comment = Comment.objects.get(pk=comment_pk)
    comment.delete()
    return redirect('articles:detail', article_pk)
```

#### Template
```html
<ul>
    {% for comment in comments %}
        <li>
            {{ comment.content }}
            <form action="{% url 'articles:comments_delete' article.pk comment.pk %}" method="POST">
                {% csrf_token %}
                <input type="submit" value="DELETE">
            </form>
        </li>
    {% endfor %}
</ul>
```

---

## 7. 핵심 정리 요약

| 개념 | 설명 |
|------|------|
| **N:1 관계** | 여러 개의 객체가 하나의 객체를 참조하는 관계 |
| **ForeignKey 위치** | N 쪽 모델에 설정 |
| **on_delete 옵션** | 부모 객체 삭제 시 자식 처리 방식 |
| **정방향 참조** | `comment.article` |
| **역방향 참조** | `article.comment_set.all()` |
| **commit=False** | 저장 전 객체를 수정할 수 있도록 반환 |
| **댓글 CRUD 구현 위치** | 게시글 상세 페이지(detail) 하단 |
| **단일 책임 원칙** | detail view와 댓글 작성 view 분리 |

---

## 8. 오늘의 학습 요약

- Django ORM에서 **Many To One(N:1)** 관계는 실무에서 매우 자주 등장하며,  
  댓글-게시글, 주문-고객, 학생-강사 등의 관계를 표현할 때 사용된다.
- ForeignKey는 항상 **N 쪽 모델에 위치**하며, on_delete 옵션으로 데이터 무결성을 관리한다.
- 역참조(`_set`)를 통해 **참조가 없는 모델에서도 연결된 데이터를 쉽게 조회**할 수 있다.
- `commit=False`를 사용하면 DB에 저장하기 전 객체를 조작할 수 있다.
- 댓글 CRUD를 구현하며 Django ORM의 관계형 구조와 QuerySet 활용법을 익혔다.
