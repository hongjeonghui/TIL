# 📝TIL - Bootstrap Grid System & UX/UI

## Bootstrap Grid System

- **Bootstrap Grid System**
  - 웹 페이지의 레이아웃을 조정하는 데 사용되는 12개의 컬럼으로 구성된 시스템
  - 반응형 디자인을 지원해 웹 페이지를 모바일, 태블릿, 데스크탑 등 다양한 기기에서 적절하게 표시할 수 있도록 도와줌

- **반응형 웹 디자인 (Responsive Web Design)**
  - 디바이스 종류나 화면 크기에 상관없이, 어디서든 일관된 레이아웃 및 사용자 경험을 제공하는 디자인 기술
  - 32인치 모니터, 태블릿, 스마트폰 등, 화면 크기에 따라 요소의 배치를 변경하여 일관된 사용자 경험을 제공할 수 있음

- **12개의 컬럼 사용 예시**
  - 12칸 중 크기에 따라 필요한 만큼의 컬럼을 차지하게 요소를 배치
  - 내부의 요소도 12칸으로 나눠서 활용

---

### Grid System 구조

- **Grid system 기본요소**
  - **Container**  
    Column들을 담고 있는 공간
  - **Column**  
    실제 컨텐츠를 포함하는 부분
  - **Gutter**  
    컬럼과 컬럼 사이의 여백 영역(상하좌우)

- 1개의 row 안에 12개의 column 영역이 구성됨
  - 각 요소는 12개 중 몇 개를 차지할 것인지 지정됨

---

### Grid System 실습

- **기본**
  - 12칸을 분배하여 배치
    ```html
      <div class="container">
        <div class="row">
          <div class="col-4">col-4</div>
          <div class="col-4">col-4</div>
          <div class="col-4">col-4</div>
        </div>
      </div>
    ```
    
- **중첩 (Nesting)**
  - 하나의 Column에 또 다른 Row를 넣는 것
    ```html
      <div class="container">
        <div class="row">
          <div class="col-4 offset-2">col-4 offset-2</div>
          <div class="col-4">col-4</div>
        </div>
      </div>
    ```
    
- **상쇄 (Offset)**
  - Offset으로 Column을 비워두는 것
    ```html
      <div class="container">
        <div class="row">
          <div class="col-4 offset-2">col-4 offset-2</div>
          <div class="col-4">col-4</div>
        </div>
      </div>

    ```

    
- **Gutters**
  - Grid system에서 column 사이의 여백 영역
  - `gx-0` → col 사이 여백 제거
  - `gy-5` → row 사이 여백 증가
  - `g-5` → x, y 방향 모두 여백 증가
    
    ```html
      <div class="container">
        <!-- col 사이 여백 제거 -->
        <div class="row gx-0">
          <div class="col">No gutter 1</div>
          <div class="col">No gutter 2</div>
        </div>
      
        <!-- row 사이 여백 크게 -->
        <div class="row gy-5 mt-3">
          <div class="col-6">gy-5 col-6</div>
          <div class="col-6">gy-5 col-6</div>
        </div>
      </div>

    ```


---

## Grid System for Responsive Web

- **Responsive Web Design**
  - 디바이스 종류나 화면 크기에 상관없이 일관된 사용자 경험 제공
  - Bootstrap grid system에서는 12개의 column과 6개의 breakpoints를 사용하여 반응형 웹 구현

---

### Grid System Breakpoints

- **Breakpoints**
  - 다양한 화면 크기에서 적절하게 배치하기 위한 분기점
  - 6개의 분기점 제공 (xs, sm, md, lg, xl, xxl)
  - 예시: `.col-sm-4` → sm 크기 이상일 때 4칸 차지
- 각 breakpoints마다 설정된 최대 너비 값 “이상으로” 화면이 커지면 grid system 동작이 변경됨

---

### Breakpoints 실습

- 화면 사이즈에 따라 column의 배치 변경
- offset과 함께 사용 가능
- Bootstrap Grid System은 내부적으로 **Media Query**로 작성됨
  ```html
    <div class="container">
      <div class="row">
        <div class="col-sm-6 col-md-4">col-sm-6 col-md-4</div>
        <div class="col-sm-6 col-md-4">col-sm-6 col-md-4</div>
        <div class="col-sm-12 col-md-4">col-sm-12 col-md-4</div>
      </div>
    </div>

  ```

  

---

## UX & UI

### UX (User Experience)

- **UX**
  - 제품이나 서비스를 사용하는 사람들이 느끼는 전체적인 경험과 만족도를 개선하고 최적화하기 위한 디자인과 개발 분야
- **예시**
  - 백화점 1층에서 맡는 좋은 향수 향기
  - 러쉬 매장 근처에서 맡을 수 있는 독특한 향기
  - 검색 기능이 빠르고 정확하게 작동하는 경험
- **UX 설계**
  - 사람들의 마음과 생각을 이해하고 제품에 반영
  - 유저 리서치, 데이터 설계 및 정제, 유저 시나리오, 프로토타입 설계

---

### UI (User Interface)

- **UI**
  - 서비스와 사용자 간의 상호작용을 가능하게 하는 디자인 요소
- **예시**
  - 리모컨: 버튼을 누르면 TV가 켜지고, 채널/볼륨 조절 가능
  - 웹사이트: 로그인 버튼을 누르면 이동하는 화면의 디자인 및 레이아웃
- **UI 설계**
  - 예쁜 디자인보다 사용자가 더 쉽고 편리하게 사용하도록 고려
  - 디자인 시스템, 중간 산출물, 프로토타입이 필요
- **역할**
  - UX/UI를 함께하는 디자이너도 있고
  - UX는 기획자, UI는 디자이너가 맡는 경우도 있음
