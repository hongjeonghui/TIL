# TIL: REST API와 Django REST Framework (DRF)

## 1. API란?

### 1-1. API 정의
- API (Application Programming Interface)
- 두 소프트웨어가 서로 통신할 수 있게 하는 메커니즘
- 클라이언트와 서버처럼 다른 프로그램 간 요청과 응답을 주고받는 규칙과 형식

### 1-2. API 예시: 날씨 데이터 받기
- 기상청 서버가 날씨 데이터를 보유
- 스마트폰 앱과 웹은 기상청 API 메뉴얼을 따라 요청을 보내고 응답을 받음
- 지역, 날짜, 항목 등 요청 형식을 지켜야 함
- 결론: 소프트웨어 간 소통을 위한 약속된 언어가 API

### 1-3. API 역할 비유
- 냉장고 플러그를 콘센트에 꽂으면 복잡한 전기 배선을 몰라도 작동
- 복잡한 과정을 추상화하여 쉽게 쓰도록 제공하는 인터페이스가 API

### 1-4. Web API
- 웹 브라우저나 웹 서버를 위한 API
- 현대 웹 개발은 Open API 활용이 많음
- 예시: YouTube API, Google Maps API, Naver Papago API, Kakao Map API

## 2. REST API

### 2-1. REST 개념
- REST는 API 서버 설계를 위한 아키텍처 원칙
- RESTful API는 자원을 정의하고, 주소를 지정하며, 행위를 표준화
- 서로 다른 기술 스택 간에도 연동이 가능

### 2-2. REST 구성 요소
- 자원의 식별: URL
- 자원의 행위: HTTP Method (GET, POST, PUT, PATCH, DELETE)
- 자원의 표현: JSON
- URL만 보고 무엇을 어떻게 처리할지 예측 가능해야 함

## 3. 자원의 식별: URL 구성요소

- URI: 인터넷에서 자원을 식별하는 문자열
- URL: 자원의 위치를 나타내는 주소

항목 | 설명
--- | ---
Schema(Protocol) | 요청 규약 (http, https, ftp 등)
Domain Name | 서버 주소 (예: google.com)
Port | 접근용 게이트 (HTTP 80, HTTPS 443)
Path | 자원 경로 (예: /articles/1/)
Parameters | 추가 데이터 (예: ?key=value&...)
Anchor | 페이지 내부 위치 식별자, 서버로 전달되지 않음

## 4. 자원의 행위: HTTP Methods

메서드 | 설명
--- | ---
GET | 리소스 조회
POST | 리소스 생성
PUT | 전체 수정(덮어쓰기)
PATCH | 부분 수정(일부 필드만)
DELETE | 리소스 삭제

### 4-1. 상태 코드 요약
범위 | 의미
--- | ---
100~199 | 정보 전달
200~299 | 성공
300~399 | 리다이렉션
400~499 | 클라이언트 오류
500~599 | 서버 오류

자주 쓰는 예시: 200 OK, 201 Created, 400 Bad Request, 404 Not Found, 500 Internal Server Error

## 5. 자원의 표현과 서버 응답 방식

- 과거: Django가 HTML 템플릿을 렌더링하여 페이지를 응답
- 현재: REST API 서버는 JSON 데이터로 응답
- 장점: 페이로드 감소, 속도 향상, 프론트엔드와 백엔드 분리

## 6. Django REST Framework (DRF)

### 6-1. 정의
- Django에서 RESTful API를 쉽게 구축하도록 돕는 라이브러리
- 직렬화, 검증, 뷰, 라우팅을 표준화

### 6-2. Serializer와 ModelSerializer
- Serializer: 파이썬 객체를 JSON 같은 포맷으로 변환/복원
- ModelSerializer: Django 모델과 연결된 Serializer

예시
```python
from rest_framework import serializers
from .models import Article

class ArticleSerializer(serializers.ModelSerializer):
    class Meta:
        model = Article
        fields = '__all__'
```

## 7. CRUD 설계 예시

URL | GET | POST | PUT | DELETE
--- | --- | --- | --- | ---
articles/ | 전체 조회 | 생성 | 전체 수정 | 전체 삭제
articles/1/ | 단일 조회 | . | 단일 수정 | 단일 삭제

주의 사항
- URL에는 동작명을 넣지 않고 자원 중심으로 설계
- 복수형/단수형 혼용 금지
- 깊은 중첩은 피하고 필요한 경우 관계를 명확히 표현
- 동작 구분은 HTTP 메서드로 처리

## 8. GET

### 8-1. List
```python
@api_view(['GET'])
def article_list(request):
    articles = Article.objects.all()
    serializer = ArticleListSerializer(articles, many=True)
    return Response(serializer.data)
```

### 8-2. Detail
```python
@api_view(['GET'])
def article_detail(request, article_pk):
    article = Article.objects.get(pk=article_pk)
    serializer = ArticleSerializer(article)
    return Response(serializer.data)
```

## 9. POST

```python
@api_view(['GET', 'POST'])
def article_list(request):
    if request.method == 'POST':
        serializer = ArticleSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

메모
- 201 Created가 생성 성공
- 400 Bad Request는 주로 입력 데이터 문제
- 에러 시 serializer.is_valid()로 어디가 잘못됐는지 확인

## 10. DELETE

### 10-1. 기본 삭제 (204 No Content)
```python
@api_view(['GET', 'DELETE'])
def article_detail(request, article_pk):
    article = Article.objects.get(pk=article_pk)
    if request.method == 'DELETE':
        article.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
```

### 10-2. 삭제 후 메시지 반환 (200 OK)
```python
@api_view(['GET', 'DELETE'])
def article_detail(request, article_pk):
    article = Article.objects.get(pk=article_pk)
    if request.method == 'DELETE':
        pk = article.pk
        title = article.title
        article.delete()
        data = {'message': f'{pk}번 게시글 "{title}"이 삭제되었습니다.'}
        return Response(data, status=status.HTTP_200_OK)
```

Response 함수 시그니처 요약
```python
class Response(
    data=None,
    status=None,
    template_name=None,
    headers=None,
    exception=False,
    content_type=None
)
```

## 11. PUT (전체 수정)

```python
@api_view(['GET', 'DELETE', 'PUT'])
def article_detail(request, article_pk):
    article = Article.objects.get(pk=article_pk)
    if request.method == 'PUT':
        serializer = ArticleSerializer(article, data=request.data)
        if serializer.is_valid(raise_exception=True):
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

## 12. PATCH (부분 수정)와 partial 인자

- partial 기본값은 False
- False이면 모든 필수 필드가 요청에 포함되어야 함
- 일부 필드만 수정하려면 partial=True

```python
@api_view(['GET', 'DELETE', 'PATCH'])
def article_detail(request, article_pk):
    article = Article.objects.get(pk=article_pk)
    if request.method == 'PATCH':
        serializer = ArticleSerializer(article, data=request.data, partial=True)
        if serializer.is_valid(raise_exception=True):
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

## 13. PUT vs PATCH

항목 | PUT | PATCH
--- | --- | ---
수정 대상 | 전체 리소스 | 일부 필드
요청 데이터 요구 | 모든 필수 필드 포함 | 수정할 필드만 포함 가능
사용 목적 | 전체 덮어쓰기 | 부분 수정
DRF 설정 | 기본 (partial=False) | partial=True 필요

메모
- 일부 필드 수정은 PATCH가 원칙
- PUT에서 partial=True 사용은 편의상 가능하지만 REST 원칙과는 다름

## 14. JSON 응답을 파이썬에서 다루기

요청 코드 예시
```python
import requests
from pprint import pprint

response = requests.get('http://127.0.0.1:8000/api/v1/articles/')
result = response.json()

print(type(result))
pprint(result)
pprint(result[0])
pprint(result[0].get('title'))
```

실행
```bash
python python-request-sample.py
```

## 15. 오늘의 정리
- REST API는 자원을 URL로 식별하고, 메서드로 행위를 구분한다
- DRF로 CRUD를 간결하게 구현할 수 있다
- PATCH는 부분 수정, PUT은 전체 수정으로 구분한다
