# 📘 Git 기초 정리 - 2025-07-17

## 🔹 버전 관리란?

- 변화를 기록하고 추적하는 것

---

## 🔹 Git이란?

- 분산 버전 관리 시스템 (DVCS)
- 중앙 서버가 아닌 **각자 로컬에 저장소를 가짐**
- Git은 클라우드 저장 방식이 아님

### 📌 중앙 vs 분산

| 중앙 집중식 | 분산형 |
|-------------|--------|
| 중앙 서버에 모든 버전 저장 | 로컬에도 저장소 있음 |
| 서버 없으면 작업 불가 | 오프라인에서도 작업 가능 |

### 📌 분산 구조의 장점

- 중앙 서버 없이 작업 가능 → 개발 생산성 향상
- 백업/복구가 용이
- 인터넷 연결 없어도 커밋 등 가능

---

## 🔹 Git의 역할

- 코드의 버전(히스토리) 관리
- 변경 사항 추적
- 이전 버전과 비교 가능

---

## 🔹 Git의 3가지 영역

| 영역 | 설명 |
|------|------|
| **Working Directory (WD)** | 작업 중인 실제 파일 영역 |
| **Staging Area** | 커밋할 파일들을 준비하는 임시 영역 |
| **Repository** | 커밋이 영구 저장되는 공간 |

---

## 🔹 Git 기본 명령어

### ✅ 저장소 초기화

```bash
git init
```

- 로컬 저장소 생성
- 저장소 안에 다시 `git init` ❌ (중첩 안 됨)

### ✅ 파일 추가 → 커밋

```bash
git add .
git commit -m "메시지"
```

- `add`: 변경된 파일을 스테이징 영역에 올림
- `commit`: 스냅샷 생성 (버전 기록)

### ✅ 상태 확인

```bash
git status
git log
git log --oneline
```

### ✅ 계정 설정 확인

```bash
git config --global -l
```

### ✅ 커밋 수정

```bash
git commit --amend
```

- 직전 커밋 메시지 또는 내용 수정
- 불필요한 커밋 쌓지 않고 깔끔하게 유지

---

## 🔹 로컬 vs 원격 저장소

| 구분 | 설명 |
|------|------|
| **로컬 저장소** | 내 컴퓨터에 있는 저장소 |
| **원격 저장소 (Remote)** | GitHub, GitLab 등과 연결된 서버 저장소 |

### ✅ Remote 연결

```bash
git remote add origin <repo_url>
```

- `origin`: 원격 저장소 별칭

### ✅ Push / Pull / Clone

```bash
git push origin master   # 원격 업로드
git pull origin master   # 원격에서 변경 내용 받아오기
git clone <repo_url>     # 저장소 복제
```

---

## 🔹 gitignore 설정

- Git이 무시할 파일 목록을 작성

### ✅ 주의사항

- 이미 추적 중인 파일은 `.gitignore`에 추가해도 무시되지 않음

```bash
git rm --cached 파일명
```

- [gitignore.io](https://gitignore.io) 사용 추천

---

## 🔚 마무리

Git의 기본 구조와 명령어를 다시 정리하면서 개념이 많이 명확해졌다.  
처음에는 복잡하게 느껴졌던 git이 조금씩 익숙해지는 중이다.
