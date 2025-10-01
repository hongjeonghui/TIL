# 👤TIL: Django 회원 인증 (Login / Logout / Signup / Delete / 접근 제한)

## Logout (로그아웃)

- 로그아웃은 **Session 삭제 과정**
- 서버의 세션 데이터를 비우고, 클라이언트의 세션 쿠키도 삭제
- `django.contrib.auth.logout` 내장 함수 사용

```python
# accounts/views.py
from django.contrib.auth import logout as auth_logout
from django.shortcuts import redirect

def logout(request):
    auth_logout(request)
    return redirect('articles:index')
```

```html
<!-- articles/index.html -->
<form action="{% url 'accounts:logout' %}" method="POST">
  {% csrf_token %}
  <input type="submit" value="Logout">
</form>
```

---

## AbstractUser Class

- **Abstract Base Classes (추상 기본 클래스)**: 직접 DB에 테이블을 만들지 않고, 다른 모델의 기반으로 사용되는 클래스
- **AbstractUser**
  - Django 기본 `User` 모델의 모든 필드 제공
  - 프로필 사진, 닉네임 등 필드만 추가하고 싶을 때 주로 사용

| 구분 | AbstractBaseUser | AbstractUser |
| ---- | ---------------- | ------------ |
| 제공 필드 | 최소 인증 필드 (비번, last_login) | 기본 User 모델 모든 필드 (username, email 등) |
| 장점 | 유연성과 자유도 ↑ | 빠르고 편리함 |
| 사용 케이스 | 완전히 새로운 인증 체계 | 기존 방식 유지 + 필드만 추가 |
| 비유 | 기본 피자 도우 | 토핑 올린 피자 |

---

## 회원 가입 (Signup)

- 회원 가입은 **User 객체를 Create** 하는 과정
- 기본적으로 `UserCreationForm` 사용 → Custom User Model을 쓰려면 **get_user_model()** 기반으로 커스텀해야 함

```python
# accounts/forms.py
from django.contrib.auth import get_user_model
from django.contrib.auth.forms import UserCreationForm

class CustomUserCreationForm(UserCreationForm):
    class Meta(UserCreationForm.Meta):
        model = get_user_model()
```

```python
# accounts/views.py
from .forms import CustomUserCreationForm

def signup(request):
    if request.method == 'POST':
        form = CustomUserCreationForm(request.POST)
        if form.is_valid():
            form.save()
            return redirect('articles:index')
    else:
        form = CustomUserCreationForm()
    return render(request, 'accounts/signup.html', {'form': form})
```

```html
<!-- accounts/signup.html -->
<h1>회원가입</h1>
<form action="{% url 'accounts:signup' %}" method="POST">
  {% csrf_token %}
  {{ form }}
  <input type="submit">
</form>
```

⚠️ 에러 `"Manager isn't available; 'auth.User' has been swapped..."` →  
기본 `UserCreationForm`이 **기존 User 모델**을 참조해서 발생.  
👉 `CustomUserCreationForm`으로 해결.

---

## 회원 탈퇴 (Delete)

- 현재 로그인한 유저 객체 삭제

```python
# accounts/views.py
from django.shortcuts import redirect

def delete(request):
    request.user.delete()
    return redirect('articles:index')
```

```html
<!-- articles/index.html -->
<form action="{% url 'accounts:delete' %}" method="POST">
  {% csrf_token %}
  <input type="submit" value="회원탈퇴">
</form>
```

※ 실제 서비스에서는 물리적 삭제보다는 **계정 비활성화** 처리 방식을 더 자주 사용.

---

## 인증된 사용자 접근 제한

### 1) `is_authenticated` 속성
- 로그인 여부를 확인하는 **읽기 전용 속성**
- 인증 시 `True`, 비인증 시 `False`
- 로그인 여부에 따라 화면 출력 제어

```html
{% if request.user.is_authenticated %}
  <h3>Hello, {{ user.username }}</h3>
  <a href="{% url 'articles:create' %}">NEW</a>
  <form action="{% url 'accounts:logout' %}" method="POST">
    {% csrf_token %}
    <input type="submit" value="Logout">
  </form>
{% else %}
  <a href="{% url 'accounts:login' %}">Login</a>
  <a href="{% url 'accounts:signup' %}">Signup</a>
{% endif %}
```

### 2) `login_required` 데코레이터
- 인증된 사용자만 접근 허용
- 인증되지 않으면 `/accounts/login/` 으로 redirect

```python
from django.contrib.auth.decorators import login_required

@login_required
def create(request):
    pass

@login_required
def delete(request, article_pk):
    pass
```

```python
# accounts/views.py
@login_required
def logout(request):
    ...
    
@login_required
def delete(request):
    ...
    
@login_required
def update(request):
    ...
    
@login_required
def change_password(request):
    ...
```

---

## 오늘 배운 핵심 정리

- **로그아웃**: 세션 삭제, `auth_logout()` 사용  
- **AbstractUser**: Django User 모델 확장 방식  
- **회원가입**: `UserCreationForm` → `CustomUserCreationForm`으로 교체 필요  
- **회원탈퇴**: `request.user.delete()`  
- **접근제한**: `is_authenticated`, `login_required` 활용  
