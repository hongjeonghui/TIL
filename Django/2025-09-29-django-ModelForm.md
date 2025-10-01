# 📚Django Form & ModelForm

---

## 1. Django Form

### HTML Form의 한계
- HTML Form은 데이터를 제출 받을 수 있지만 **비정상적/악의적 요청을 필터링 불가**
- 따라서 **유효성 검사(Validation)** 과정이 필요

### 유효성 검사
- 수집된 데이터가 정확/유효한지 확인
- Django Form은 자동으로 형식 검증 (빈 값, 이메일 형식 등)
- 잘못된 데이터가 DB에 저장되지 않도록 보호

### Form Class
- 사용자 입력을 수집/처리/유효성 검사하는 도구
- 잘못된 입력은 자동으로 오류 처리 → 안정성 ↑
- 예시:

```python
# articles/forms.py
from django import forms

class ArticleForm(forms.Form):
    title = forms.CharField(max_length=10)
    content = forms.CharField()
```

### Form 활용 (new)
```python
# articles/views.py
from .forms import ArticleForm

def new(request):
    form = ArticleForm()
    context = {'form': form}
    return render(request, 'articles/new.html', context)
```

```html
<!-- articles/new.html -->
<h1>NEW</h1>
<form action="{% url 'articles:create' %}" method="POST">
  {% csrf_token %}
  {{ form }}
  <input type="submit">
</form>
```

---

## 2. Widgets

- **Widgets**: 각 필드가 HTML에서 어떻게 렌더링될지 결정
- 예: `TextInput`, `Select`, `Textarea` 등

```python
# articles/forms.py
class ArticleForm(forms.Form):
    title = forms.CharField(max_length=10)
    content = forms.CharField(widget=forms.Textarea)
```

---

## 3. ModelForm

### Form vs ModelForm
- **Form**: DB 저장 필요 없는 입력 (검색, 로그인)
- **ModelForm**: DB 저장 필요한 입력 (게시글 작성, 회원가입)

### ModelForm 정의
```python
# articles/forms.py
from django import forms
from .models import Article

class ArticleForm(forms.ModelForm):
    class Meta:
        model = Article
        fields = '__all__'
```

### Meta class
- ModelForm의 설정 공간
- `fields` / `exclude` 로 사용 필드 지정

```python
class ArticleForm(forms.ModelForm):
    class Meta:
        model = Article
        fields = ('title',)   # 특정 필드만
```

```python
class ArticleForm(forms.ModelForm):
    class Meta:
        model = Article
        exclude = ('title',)  # 특정 필드 제외
```

---

## 4. ModelForm 적용

### create 로직
```python
def create(request):
    form = ArticleForm(request.POST)
    if form.is_valid():
        article = form.save()
        return redirect('articles:detail', article.pk)
    context = {'form': form}
    return render(request, 'articles/new.html', context)
```

### edit & update 로직
```python
def edit(request, pk):
    article = Article.objects.get(pk=pk)
    form = ArticleForm(instance=article)
    context = {'article': article, 'form': form}
    return render(request, 'articles/edit.html', context)
```

```python
def update(request, pk):
    article = Article.objects.get(pk=pk)
    form = ArticleForm(request.POST, instance=article)
    if form.is_valid():
        form.save()
        return redirect('articles:detail', article.pk)
    context = {'article': article, 'form': form}
    return render(request, 'articles/edit.html', context)
```

---

## 5. save() 메서드

- 유효한 폼 데이터 → DB 저장
- `instance` 인자 여부로 **생성/수정 구분**

```python
# CREATE
form = ArticleForm(request.POST)
form.save()

# UPDATE
form = ArticleForm(request.POST, instance=article)
form.save()
```

---

## 6. HTTP 요청과 View 구조화

### new & create 결합
```python
def create(request):
    if request.method == 'POST':
        form = ArticleForm(request.POST)
        if form.is_valid():
            article = form.save()
            return redirect('articles:detail', article.pk)
    else:
        form = ArticleForm()
    context = {'form': form}
    return render(request, 'articles/create.html', context)
```

- GET → 작성 페이지 보여주기
- POST → 데이터 저장

### edit & update 결합
```python
def update(request, pk):
    article = get_object_or_404(Article, pk=pk)
    if request.method == 'POST':
        form = ArticleForm(request.POST, instance=article)
        if form.is_valid():
            form.save()
            return redirect('articles:detail', article.pk)
    else:
        form = ArticleForm(instance=article)
    context = {'article': article, 'form': form}
    return render(request, 'articles/update.html', context)
```

---

## 7. 정리

- Django Form: 데이터 수집/유효성 검사 도구
- ModelForm: 모델과 결합, DB 저장 자동 처리
- save(): instance 여부로 생성/수정 구분
- View 구조화: new/create, edit/update 통합하여 효율성 ↑
