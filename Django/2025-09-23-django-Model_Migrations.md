# 📘 Django Model & Migrations 정리 (TIL)

## 1. Model

-   **정의**: 데이터베이스(DB) 테이블을 Python 클래스(객체)로 표현한 것
-   **역할**: DB와 직접 SQL을 쓰지 않고 상호작용 가능 → 개발 효율성 증가
-   **구조**
    -   `urls.py`: 요청 시작점
    -   `views.py`: 요청 처리, models.py 호출
    -   `models.py`: 데이터베이스 구조 정의
    -   `templates`: 데이터를 사용자에게 보여줌

👉 Model은 **DB 테이블 설계도(blueprint)** 역할을 한다.

------------------------------------------------------------------------

## 2. Model Field

-   **정의**: 테이블의 열(column), 데이터 타입 + 제약 조건 정의
-   **종류 (Field types)**
    -   문자열: `CharField`, `TextField`
    -   숫자: `IntegerField`, `FloatField`
    -   날짜/시간: `DateField`, `TimeField`, `DateTimeField`
    -   파일: `FileField`, `ImageField`
-   **옵션 (Field options)**
    -   `null`: DB에서 NULL 허용 여부
    -   `blank`: Form에서 빈 값 허용 여부
    -   `default`: 기본값 지정

------------------------------------------------------------------------

## 3. Migrations

-   **정의**: Model 변경사항을 DB에 반영하는 과정
-   **추가 필드 시 주의점**
    -   기존 테이블에 데이터가 있으면 새 필드의 **기본값** 필요
    -   `DateTimeField` 옵션
        -   `auto_now`: 저장될 때마다 현재 시간 기록
        -   `auto_now_add`: 처음 생성될 때만 현재 시간 기록

👉 모델 클래스 수정 → `makemigrations` → `migrate` 필수

------------------------------------------------------------------------

## 4. Admin site

-   **정의**: Django가 기본 제공하는 웹 기반 관리 도구
-   **주요 기능**: CRUD (생성/읽기/수정/삭제) 가능
-   **사용 순서**
    1.  관리자 계정 생성

        ``` bash
        $ python manage.py createsuperuser
        ```

    2.  `http://127.0.0.1:8000/admin` 접속

    3.  `admin.py`에 모델 등록 → admin 페이지에서 데이터 관리 가능

👉 개발자가 직접 SQL 관리 도구를 만들지 않아도, Django가 **자동으로
관리자 페이지 제공**

------------------------------------------------------------------------

# ⚡ Django 명령어 정리 (배운 것 위주)

### 1. 마이그레이션 파일 생성

``` bash
$ python manage.py makemigrations
```

### 2. 마이그레이션 적용

``` bash
$ python manage.py migrate
```

### 3. 관리자(superuser) 계정 생성

``` bash
$ python manage.py createsuperuser
```

### 4. 개발 서버 실행

``` bash
$ python manage.py runserver
```

------------------------------------------------------------------------

## ✅ 핵심 기억하기

1.  `makemigrations` → 설계도 생성
2.  `migrate` → DB 반영
3.  `createsuperuser` → 관리자 계정 생성
4.  `runserver` → 개발 서버 실행
