# TIL: 가상환경 & Docker

## 1. 가상 환경이 필요한 이유

### 1) 의존성 충돌 방지

-   서로 다른 프로젝트가 같은 라이브러리의 다른 버전을 요구할 수 있음
-   가상 환경을 사용하면 프로젝트별로 독립적인 실행 환경 구성 가능
-   실행에 필요한 최소 구성만 담아 환경을 최적화할 수 있음

### 2) 재현성 보장

-   다른 개발 환경에서도 동일한 조건으로 실행 가능
-   단, OS 자체가 완전히 다를 경우 100% 동일한 재현이 어려울 수 있음

------------------------------------------------------------------------

## 2. Conda

### 1) 개념

-   범용 환경 + 패키지 관리 도구
-   Python뿐 아니라 C/C++, R, Node.js 등 다양한 언어 기반 라이브러리
    관리 가능
-   패키지뿐 아니라 OS 레벨 의존성(libssl, gcc 등)까지 함께 관리 가능

### 2) 배포판 종류

-   **Miniconda**
    -   conda + Python만 포함된 최소 구성
    -   필요한 패키지를 직접 설치하여 사용하는 방식
-   **Anaconda**
    -   데이터 분석용 주요 패키지가 기본 포함
-   일반적으로 Miniconda를 설치하고 필요한 패키지를 직접 구성하는 방식을
    사용

------------------------------------------------------------------------

## 3. venv vs conda 비교

| 구분                   | **venv**                                               | **conda**                                                 |
| ---------------------- | ------------------------------------------------------ | --------------------------------------------------------- |
| **개념**               | Python 표준 라이브러리에 포함된 **가상환경 관리 도구** | Anaconda/Miniconda에 포함된 **환경 + 패키지 관리 시스템** |
| **재현성**             | OS가 다를 경우 재현성 낮음                             | OS가 달라도 비교적 재현성 높음                            |
| **의존성 해결**        | 사용자가 직접 관리                                     | 의존성 충돌 자동 해결                                     |
| **시스템 의존성 관리** | 불가능                                                 | 가능 (libssl, gcc 등 함께 관리)                           |
| **속도 및 용량**       | 가볍고 빠름                                            | 느리고 무겁지만 안정성 높음                               |
| **주 사용처**          | 단순한 Python 프로젝트                                 | 데이터 분석, ML, 복잡한 프로젝트                          |

------------------------------------------------------------------------

## 4. Conda CLI 정리

### 가상 환경 관리

``` bash
conda create -n <env_name> python=3.12
conda activate <env_name>
conda deactivate
conda remove -n <env_name> --all
conda env list
```

### 패키지 관리

``` bash
conda install numpy==1.26.4
pip install requests
conda remove requests
conda list
```

### 환경 내보내기 / 복제

``` bash
conda env export > environment.yml
conda env export --from-history > environment.yml
conda env create -f environment.yml
conda list --export > requirements.txt
```

------------------------------------------------------------------------

## 5. 가상화(Virtualization)

### 개념

-   물리적 리소스를 추상화하여 여러 개의 가상 환경을 만드는 기술
-   논리적 독립성을 보장
-   클라우드 컴퓨팅의 기반 기술

### 대표 개념

-   VM
-   Container

------------------------------------------------------------------------

## 6. VM vs Container

| 구분          | VM(Virtual Machine)         | **Container**                  |
| ------------- | --------------------------- | ------------------------------ |
| **개념**      | 하드웨어 수준 가상화        | OS 수준 가상화                 |
| **구성 요소** | Guest OS + App + 라이브러리 | App + 라이브러리(Host OS 공유) |
| **시작 속도** | 느림(보통 수십 초 이상)     | 빠름(보통 수 초 이내)          |
| **격리 수준** | 강력(완전한 OS 단위 분리)   | 중간(프로세스 단위 격리)       |
| **유지보수**  | 어려움(OS마다 패치 필요)    | 쉬움(이미지 기반 관리)         |


※ VM과 Container는 배타적인 개념이 아니며, VM 위에서 Container를 함께
사용하는 경우가 많음.

------------------------------------------------------------------------

## 7. Docker 핵심 개념

### Docker

-   Container 기술 기반 실행 환경 관리 도구
-   OS 레벨 가상화를 통해 앱 실행 공간을 논리적으로 분리

### Image

-   실행 환경 스냅샷
-   Container 생성을 위한 설계도

### DockerHub

-   Image 저장 및 공유를 위한 원격 저장소
-   tag를 통해 버전 관리

------------------------------------------------------------------------

## 8. Docker 특징

-   가볍고 빠른 실행
-   이미지 재사용을 통한 리소스 효율성
-   컨테이너 단위 테스트 및 배포 가능
-   CI/CD 환경 구축에 적합
-   인프라를 코드 기반으로 관리 가능

------------------------------------------------------------------------

## 9. Docker 주요 명령어

### 이미지 관련

``` bash
docker build -t <image_name:tag> .
docker tag <image_name(:tag)> <id/image_name:tag>
docker push <id/image_name:tag>
docker pull <id/image_name:tag>
docker image ls
docker image rm <image_name>
```

### 컨테이너 실행

``` bash
docker run <id/image_name:tag>
```

옵션: - `--name <container_name>` - `-d` (백그라운드 실행) -
`-p <외부포트>:<내부포트>` - `--env-file <env파일 경로>`

### 컨테이너 관리

``` bash
docker container ls
docker container ls -a
docker logs <container_name>
docker logs -f <container_name>
docker exec -it <container_name> bash
docker container stop <container_name>
docker container rm <container_name>
```

------------------------------------------------------------------------

## 10. 정리

-   가상 환경은 의존성 충돌 방지와 재현성을 위해 필수
-   conda는 환경 + 패키지 + 시스템 의존성까지 관리 가능
-   Container는 OS 수준 가상화로 빠르고 효율적인 실행 환경 제공
-   Docker는 컨테이너 기반 배포와 인프라 관리의 핵심 도구
