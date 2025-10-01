# 📒 TIL: Cookie & Session, Django Authentication

## 1. HTTP

-   **HTTP (Hyper Text Transfer Protocol)**
    -   웹에서 모든 데이터 교환의 기초 규약
    -   브라우저 ↔ 서버 간 대화의 공통 언어
    -   Request/Response 기반 동작
-   **특징**
    1.  **비연결(Connectionless)**\
        요청-응답 후 연결 종료 → 서버 자원 낭비 방지
    2.  **무상태(Stateless)**\
        상태 저장 없음 → 장바구니, 로그인 유지 불가\
        (확장성과 단순성을 위해 설계됨)

------------------------------------------------------------------------

## 2. Cookie

-   **쿠키**: 서버가 브라우저에 저장하는 작은 데이터 조각 (key-value)
-   **용도**:
    -   세션 관리(로그인 유지, 장바구니)
    -   개인화(언어, 테마 설정)
    -   추적/분석 (광고, 사용자 행동 분석)
-   **동작 과정**
    1.  서버 → 클라이언트: `Set-Cookie` 헤더 전송
    2.  브라우저: 쿠키 저장
    3.  클라이언트 → 서버: 요청 시 `Cookie` 헤더로 전송
    4.  서버: 쿠키 확인 후 상태 유지 처리
-   **주의**
    -   민감정보 저장 ❌
    -   요청마다 전송 → 크기 최소화 필요

------------------------------------------------------------------------

## 3. Session

-   **세션**: 서버에 저장되는 사용자 상태 정보
-   **작동 원리**
    1.  로그인 성공 시 서버가 세션 생성
    2.  고유한 `Session ID` 발급 → 클라이언트 쿠키에 저장
    3.  클라이언트 요청 시 `Session ID` 포함
    4.  서버가 ID 확인 후 사용자 식별
-   **특징**
    -   데이터는 서버에 저장
    -   쿠키에는 `세션 ID`만 저장
    -   서버 자원 사용 → 효율적 관리 필요
    -   보안은 쿠키보다 강하지만, 세션 ID 탈취 위험 존재

------------------------------------------------------------------------

## 4. Django Authentication System

-   **필요성**: 상태 유지 + 사용자 식별 → 인증(Authentication)
-   **제공 기능**
    -   User 모델
    -   세션 관리
    -   기본 인증 (ID/PW)
    -   권한 관리
-   **기본 User Model 한계**
    -   username, password 등 필드 제한적
    -   생년월일, 주소 같은 추가 정보 불편
-   **Custom User Model**
    -   `AbstractUser` 상속 → User 확장
    -   `settings.py` → `AUTH_USER_MODEL = 'accounts.User'`
    -   admin 등록 필요

------------------------------------------------------------------------

## 5. Login 구현 (Django)

-   **절차**

    1.  사용자가 ID/PW 입력
    2.  서버가 인증 진행
    3.  세션 생성 → `Session ID` 발급
    4.  클라이언트 쿠키에 저장

-   **코드 예시**

    ``` python
    # accounts/views.py
    from django.shortcuts import render, redirect
    from django.contrib.auth import login as auth_login
    from django.contrib.auth.forms import AuthenticationForm

    def login(request):
        if request.method == 'POST':
            form = AuthenticationForm(request, request.POST)
            if form.is_valid():
                auth_login(request, form.get_user())
                return redirect('articles:index')
        else:
            form = AuthenticationForm()
        return render(request, 'accounts/login.html', {'form': form})
    ```

-   **템플릿 예시**

    ``` html
    <!-- accounts/login.html -->
    <h1>로그인</h1>
    <form action="{% url 'accounts:login' %}" method="POST">
      {% csrf_token %}
      {{ form }}
      <input type="submit">
    </form>
    ```

-   **세션 확인**

    -   DB: `django_session` 테이블
    -   브라우저: 개발자 도구 → Application → Cookies

------------------------------------------------------------------------

## 6. Template에서 인증 정보 활용

-   Django가 제공하는 `context_processors` 덕분에 `user` 객체 사용 가능

-   **예시**

    ``` html
    <!-- articles/index.html -->
    <h3>Hello, {{ user.username }}</h3>
    ```

------------------------------------------------------------------------

## 📝 핵심 정리

-   HTTP: 비연결 & 무상태
-   Cookie: 브라우저 저장, 상태 유지용
-   Session: 서버 저장, 보안 강화
-   Django Authentication:
    -   로그인/로그아웃, 세션 관리
    -   Custom User Model 권장
-   Login 구현: `AuthenticationForm` + `auth_login`
-   Template에서 `user` 객체 활용 가능
