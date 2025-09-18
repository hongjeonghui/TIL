# 📘 TIL - Django 기초 & 웹 애플리케이션 흐름
---

## Web Application

- **웹 애플리케이션(Web Service)**
  - 인터넷을 통해 사용자에게 제공되는 소프트웨어 프로그램
  - 다양한 디바이스(모바일, 태블릿, PC)에서 웹 브라우저로 접근 가능
  - 예: 네이버, 쿠팡, Google Docs 등

---

## 클라이언트와 서버

- **Client (클라이언트)**
  - 서비스를 요청하는 주체 (웹 브라우저, 모바일 앱)
- **Server (서버)**
  - 요청을 받아 처리하고 응답하는 주체 (웹 서버, DB 서버)

**웹 페이지 로딩 과정**
1. 사용자가 브라우저에서 URL 입력 (예: `google.com`)
2. 브라우저가 서버에 요청
3. 서버는 DB 등에서 필요한 데이터를 찾아 응답
4. 브라우저는 받은 HTML을 해석하여 사용자에게 보여줌

---

## Frontend & Backend

- **Frontend (프론트엔드)**
  - 사용자 인터페이스(UI) 담당
  - HTML, CSS, JavaScript, Vue.js 등
- **Backend (백엔드)**
  - 서버에서 동작, 로직 & DB 처리 담당
  - Python, Java / Django 같은 프레임워크 사용

---

## Web Framework

- **Framework**: 개발에 필요한 구조·규칙·라이브러리 제공
- **Django**
  - Python 기반 대표적인 웹 프레임워크
  - 확장성, 보안성, 대규모 서비스에서도 안정적

---

## 가상 환경 (Virtual Environment)

- **개념**
  - 하나의 컴퓨터 안에서 독립된 Python 실행 환경 제공
  - 프로젝트별 라이브러리 버전 충돌 방지
- **생성 및 실행**
  ```bash
  $ python -m venv venv       # 가상 환경 생성
  $ source venv/Scripts/activate   # 활성화 (Windows)
  $ deactivate                # 비활성화
  ```
- **의존성 관리**
  ```bash
  $ pip freeze > requirements.txt   # 패키지 목록 저장
  $ pip install -r requirements.txt # 동일 환경 재현
  ```

---

## Django 프로젝트 시작

1. Django 설치
   ```bash
   $ pip install django
   ```
2. 프로젝트 생성
   ```bash
   $ django-admin startproject firstpjt .
   ```
3. 서버 실행
   ```bash
   $ python manage.py runserver
   ```

---

## Django Design Pattern

- **MVC 패턴**
  - Model: 데이터 & 비즈니스 로직
  - View: 사용자 화면
  - Controller: 입력을 받아 Model & View 제어
- **MTV 패턴 (Django)**
  - Model: DB 관련
  - Template: 사용자 화면
  - View: 요청 처리 & 응답 반환

---

## 프로젝트와 앱

- **Project**: 앱들의 집합 (전체 설정, DB, URL 연결)
- **App**: 독립된 기능 단위 (게시판, 댓글, 회원 관리 등)
  ```bash
  $ python manage.py startapp articles
  ```
- 앱 생성 후 반드시 `settings.py`의 `INSTALLED_APPS`에 등록해야 함

---

## 요청과 응답 흐름

- **데이터 흐름**
  ```
  URLs → View → Template
  ```
- **예시**
  - `urls.py`
    ```python
    path('articles/', views.index)
    ```
  - `views.py`
    ```python
    def index(request):
        return render(request, 'articles/index.html')
    ```
  - `templates/articles/index.html`

👉 이 흐름만 잘 지키면 오타가 나도 정상적으로 동작한다는 점이 중요!

---

## 오늘의 배움 정리

1. 웹은 **클라이언트-서버 구조**로 동작한다.
2. 프론트엔드와 백엔드의 역할이 다르다.
3. Django는 **MTV 패턴** 기반의 웹 프레임워크다.
4. 프로젝트와 앱의 개념을 명확히 구분해야 한다.
5. 요청과 응답의 흐름은 반드시 **URLs → View → Template** 순서를 따른다.
6. 가상 환경과 `requirements.txt`를 통해 **협업 및 동일 환경 재현**이 가능하다.

---
