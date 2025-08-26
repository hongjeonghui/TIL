# 📘TIL: CSS Layout

> 날짜: 2025-08-26  
>
> 목적: **CSS 레이아웃 기초**(박스 모델, display, normal flow, position, z-index, Flexbox) 정리

---

## 0. 모든 HTML 요소는 “박스(BOX)”
HTML의 모든 요소(`div`, `p`, `span`, `a`, `img` 등)는 **네 겹의 상자**로 이루어진다.

```
[ margin ]   ← 바깥 여백 (이웃과 거리)
[ border ]   ← 테두리
[ padding ]  ← 안쪽 여백 (내용과 테두리 사이)
[ content ]  ← 내용(글자/이미지 등)
```
- **content**: 실제 내용이 들어가는 공간  
- **padding**: 내용과 테두리 사이의 쿠션  
- **border**: 테두리 선  
- **margin**: 바깥 여백(이웃 박스와의 거리)


### 0-1) 크기 계산과 `box-sizing`
기본값은 `content-box` 이며, 이때 지정한 `width`에는 `padding/border`가 **포함되지 않음**.

- 예) `width: 200px; padding: 20px; border: 2px;`  
  실제 총 너비 = `200 + 20*2 + 2*2 = 244px`

**정확히 200px 안에 padding/border까지 포함**하고 싶으면:
```css
* { box-sizing: border-box; }  
```

### 0-2) 눈으로 확인하는 디버깅 팁
```css
* { outline: 1px dashed #aaa; }
```
> `outline`은 레이아웃에 영향을 주지 않고 윤곽만 보여준다.

---

## 1. display 속성 (박스의 화면 배치 방식)

### 1-1) 박스 타입
- 박스 타입에 따라 페이지에서의 배치 흐름과 동작 방식이 달라짐
- 대표 타입: **block**, **inline**, **inline-block**, **none**, **flex**

### 1-2) Block 타입
- 하나의 독립된 덩어리처럼 동작 (**문단**과 비슷)
- **항상 새로운 줄**에서 시작, 가로 너비를 기본적으로 **전부** 차지
- `width`, `height`, `margin`, `padding` 모두 정상 동작
- `padding/margin/border`로 **다른 요소를 밀어낼 수 있음**
- `width` 지정이 없으면 상위 컨테이너의 사용가능한 공간을 **inline 방향으로 모두 차지**(결과적으로 너비 100%처럼 동작)

대표 태그: `h1~h6`, `p`, `div`, `ul`, `li`

```css
.index {
  display: block;
}
```

#### block 타입의 대표: `div`
- 다른 HTML 요소들을 **그룹화**하여 레이아웃 구성/스타일 적용
- 헤더/푸터/사이드바 등 웹 페이지의 다양한 **섹션 구조화**에 가장 많이 쓰임

### 1-3) Inline 타입
- **문장 안의 단어**처럼 줄 속에 자연스럽게 배치
- **줄바꿈이 일어나지 않음**, 콘텐츠 길이만큼만 차지
- **`width`/`height` 직접 지정 불가**
- 수직(상하) 방향의 `padding/margin/border`는 **줄 높이에 제한적으로만 영향**, 다른 요소를 실제로 밀어내진 못함
- 수평(좌우) 방향의 `padding/margin/border`는 **다른 요소를 밀어낼 수 있음**

대표 태그: `a`, `img`, `span`, `strong`, `em`

```css
.index {
  display: inline;
}
```

#### inline 타입의 대표: `span`
- 자체적인 시각적 변화 없음(스타일 적용 전에는 변화 X)
- 문장 내 특정 단어나 구문에만 스타일 적용할 때 유용
- 블록 요소처럼 줄바꿈을 일으키지 않음(문서 구조에 큰 변화 X)

### 1-4) inline-block 타입
- **inline + block** 특징 결합: **줄바꿈 없이 나란히** 배치되면서도 **크기 지정 가능**
- `width`, `height` 정상 동작
- `padding/margin/border`로 **다른 요소를 밀어낼 수 있음**
- 가로로 정렬된 내비게이션, 여러 버튼, 이미지 갤러리 등에 유용

```css
.index {
  display: inline-block;
}
```

### 1-5) none 타입
- 요소를 화면에 **표시하지 않음 + 공간도 차지하지 않음**
- “명단에는 있지만 경기장에는 없는 후보 선수” 비유

```css
.index {
  display: none;
}
```

### 1-6) Normal flow
- 레이아웃을 변경하지 않은 **기본 배치 방식**
- **block**은 위에서 아래로 **한 줄 전체**를 차지하며 쌓임
- **inline**은 **왼쪽에서 오른쪽**으로 한 줄에 나란히 배치
- 워드 프로세서에서 **엔터** → 문단(block), 엔터 없이 계속 타이핑 → 인라인 흐름과 유사

---

## 2. CSS Position (요소의 위치 제어)

### 2-1) 개념
- display가 “줄을 어떻게 서느냐”라면, **position은 “정확히 어디에 둘까”**
- 필요 시 Normal flow에서 **떼어내거나(absolute/fixed)** 또는 **원래 자리에서 살짝 이동(relative)**

핵심 값: `static`, `relative`, `absolute`, `fixed`, `sticky`  
또한, `top/right/bottom/left` 방향 속성으로 좌표 이동 가능, 겹칠 때는 `z-index`로 **쌓임(앞뒤)** 순서 조절

### 2-2) static (기본값)
- Normal flow에 따라 배치
- `top/right/bottom/left` **적용 안 됨**

```css
.static {
  position: static;
  background-color: lightcoral;
}
```

### 2-3) relative
- Normal flow **자기 원래 자리**를 기준으로 살짝 이동
- 이동해도 **원래 자리가 차지한 공간은 그대로 유지** → 주변 레이아웃에 영향 최소화
- 주로 **absolute 자식의 기준점**(containing block)을 만들 때도 사용

```css
.relative {
  position: relative;
  background-color: lightblue;
  top: 100px;
  left: 100px;
}
```

### 2-4) absolute (절대 위치)
- 요소를 **Normal flow에서 제거**
- 가장 가까운 **position이 static이 아닌 조상(보통 relative)** 을 **기준**으로 배치  
  (없으면 **body 태그**/viewport 기준)
- 문서에서 **공간을 차지하지 않음**

```css
.absolute {
  position: absolute;
  background-color: lightgreen;
  top: 100px;
  left: 100px;
}
```

### 2-5) fixed
- 요소를 **Normal flow에서 제거**
- **브라우저 viewport**를 기준으로 위치
- 스크롤해도 항상 같은 화면 위치에 머무름
- 공지배너/플로팅 버튼 등에 활용

```css
.fixed {
  position: fixed;
  background-color: gray;
  top: 0;
  left: 0;
}
```

### 2-6) sticky
- **relative + fixed**의 하이브리드
- 임계점(예: `top: 0`)에 도달하기 전에는 **relative처럼** 스크롤과 함께 이동
- 임계점에 닿으면 **fixed처럼** 화면에 고정
- 다음 sticky 요소가 오면 **자리 교대**(겹침 방지)

```css
.sticky {
  position: sticky;
  top: 0;           /* 임계점 */
  background: gold;
}
```

> 스티키가 먹히려면 **부모 컨테이너의 높이/스크롤 상황**이 만들어져 있어야 한다.

---

## 3. z-index (쌓임 순서)

### 3-1) 기본 원리
- 요소가 겹칠 때 **앞뒤 순서**를 결정하는 속성
- **값이 클수록 앞(위)** 에 보임
- 기본값은 `auto`

```css
.layer {
  position: relative; /* 보통 position이 필요 */
  z-index: 10;
}
```

### 3-2) 주의 사항
- 전통적으로 **position이 static이 아닌 요소**에서 동작(최근 사양에서는 **flex/grid item**도 적용 가능)
- **같은 부모(같은 스태킹 컨텍스트)** 안에서만 z-index 비교
- **부모의 z-index가 낮으면** 자식이 아무리 커도 **부모 위로 넘어갈 수 없음**
- 일부 속성(예: `opacity < 1`, `transform`, `filter` 등)은 **새 스태킹 컨텍스트**를 만들어 예상과 다르게 겹칠 수 있음
- **음수 z-index**는 뒤로 보내지만, 부모/컨텍스트에 따라 보이지 않을 수 있음

---

## 4. CSS Flexbox (1차원 레이아웃)

### 4-1) 개념
- 요소를 **행(row)** 또는 **열(column)** 방향으로 **유연하게 배치**하는 1차원 레이아웃
- 부모에 `display: flex;` 혹은 `display: inline-flex;`를 주면 **바로 적용**

```css
.container {
  display: flex;
}
```

### 4-2) 축 이해 (매우 중요)
- **main axis(주 축)**: 아이템이 **배치되는 기본 축** (기본: 가로 방향)
- **cross axis(교차 축)**: 주 축에 **수직인 축** (기본: 세로 방향)

### 4-3) Flex 구성 요소
- **Flex Container**: `display: flex|inline-flex`가 설정된 **부모**
- **Flex Item**: 컨테이너의 **직계 자식**들

### 4-4) Flex 속성 목록
**(1) 컨테이너 관련**  
- `display`  
- `flex-direction` (주 축 방향 지정)  
- `flex-wrap` (줄바꿈 허용/금지)  
- `justify-content` (주 축 정렬)  
- `align-items` (교차 축 단일 줄 정렬)  
- `align-content` (교차 축 **여러 줄** 간 간격/정렬)

**(2) 아이템 관련**  
- `order` (아이템 순서 변경)  
- `align-self` (아이템 개별 교차 축 정렬)  
- `flex-grow` (남는 공간 **확장 비율**)  
- `flex-basis` (기본 크기)  

> **속성 쉽게 이해하는 방법**:  
> - `justify-*`는 **주 축** 정렬  
> - `align-*`는 **교차 축** 정렬

### 4-5) 핵심 속성 간단 예시

#### A) 방향과 줄바꿈
```css
.container {
  display: flex;
  flex-direction: row;   /* row | row-reverse | column | column-reverse */
  flex-wrap: nowrap;     /* nowrap | wrap | wrap-reverse */
}
```

#### B) 주 축 정렬 (`justify-content`)
```css
.container {
  display: flex;
  justify-content: flex-start; /* flex-start | center | flex-end | space-between | space-around | space-evenly */
}
```

#### C) 교차 축 정렬 (`align-items`, 단일 줄)
```css
.container {
  display: flex;
  align-items: stretch; /* stretch | flex-start | center | flex-end | baseline */
}
```

#### D) 여러 줄 정렬 (`align-content`, wrap일 때)
```css
.container {
  display: flex;
  flex-wrap: wrap;
  align-content: flex-start; /* flex-start | center | flex-end | space-between | space-around | stretch */
}
```

#### E) 아이템 개별 정렬 (`align-self`)
```css
.item.special {
  align-self: center; /* auto | flex-start | center | flex-end | baseline | stretch */
}
```

#### F) 남는 공간 나눠 갖기 (`flex-grow`)
```css
.item.a { flex-grow: 1; }  /* 남는 공간 1 비율 */
.item.b { flex-grow: 2; }  /* 남는 공간 2 비율 */
.item.c { flex-grow: 0; }  /* 확장하지 않음(기본값 0) */
```

#### G) 기본 크기 (`flex-basis`)
```css
.item {
  flex-basis: 200px;  /* 주 축 기준 기본 크기 */
  /* flex: grow shrink basis; 의 단축형도 자주 사용 */
  /* 예: flex: 1 1 200px; */
}
```

### 4-6) flex-wrap 응용 – 반응형 레이아웃
```html
<!doctype html>
<meta charset="utf-8">
<style>
  * { box-sizing: border-box; }
  .grid {
    display: flex;
    flex-wrap: wrap;
    gap: 12px;
  }
  .card {
    flex: 1 1 240px;   /* 최소 240px 확보, 남는 공간 비례 확장 */
    border: 2px solid #333;
    padding: 16px;
    background: #fff;
  }
</style>
<div class="grid">
  <div class="card">카드 1</div>
  <div class="card">카드 2</div>
  <div class="card">카드 3</div>
  <div class="card">카드 4</div>
</div>
```
- 화면이 넓으면 **여러 개가 가로로**, 좁으면 **자동으로 줄바꿈**되어 세로로 떨어짐  
- `flex-basis`와 `flex-grow` 조합으로 **반응형**이 쉬워짐

---

## 5. Normal Flow · Display · Position의 연결 이해

| 개념 | 핵심 질문 | 답 |
|---|---|---|
| **display** | “**줄을 어떻게** 설까?” | block(문단처럼 한 줄 차지), inline(글자처럼 옆으로), inline-block(옆으로 + 크기 지정), none(숨김), flex(자식들을 플렉스 규칙으로 배치) |
| **normal flow** | “**아무 설정 없을 때** 기본 배치?” | block은 위→아래, inline은 좌→우로 흐름 |
| **position** | “**정확히 어디에 둘까?** 기본 자리에서 떼어낼까?” | static(기본), relative(원래 자리 기준 이동), absolute(흐름 제거 + 조상 기준 배치), fixed(화면에 고정), sticky(스크롤 임계점부터 고정) |
| **z-index** | “겹치면 누가 앞?” | 숫자 커질수록 앞. 같은 컨텍스트 내 비교, 부모보다 앞으로 못 나감 |

---

## 6. 실습 코드 모음

### 6-1) block vs inline
```html
<!doctype html>
<meta charset="utf-8">
<style>
  .box { padding: 8px; border: 2px solid #333; margin: 8px 0; }
  .b1 { display: block;  background: #ffe6e6; }
  .b2 { display: inline; background: #e6f0ff; }
</style>

<p class="box b1">나는 block이야 (줄을 혼자 써요)</p>
<span class="box b2">나는 inline이야</span>
<span class="box b2">나도 inline이라 옆으로 붙어요</span>
```

### 6-2) inline은 width/height가 거의 안 먹힘
```html
<!doctype html>
<meta charset="utf-8">
<style>
  .tag { display:inline; border:2px solid #333; margin:4px; }
  .try { display:inline; border:2px solid #333; margin:4px; width:200px; height:80px; }
</style>

<span class="tag">기본 inline</span>
<span class="try">width/height 줘도 큰 변화 없음</span>
```

### 6-3) inline-block은 크기 지정 가능
```html
<!doctype html>
<meta charset="utf-8">
<style>
  .chip {
    display:inline-block;
    padding:6px 12px;
    border:2px solid #333;
    margin:4px;
    width:120px;  /* ← 잘 먹힘 */
    text-align:center;
    background:#f5ffea;
  }
</style>

<span class="chip">나는 inline-block</span>
<span class="chip">크기 지정 O</span>
<span class="chip">옆으로 나란히</span>
```

### 6-4) relative + absolute 기준점
```html
<!doctype html>
<meta charset="utf-8">
<style>
  .parent {
    position: relative;   /* 자식 absolute의 기준이 됨 */
    width: 300px; height: 180px; border: 2px solid #333; margin: 16px;
  }
  .child {
    position: absolute;   /* 부모의 안쪽(좌상단) 기준 */
    top: 20px; left: 30px;
    width: 100px; height: 60px; background: #c8f7c5; border: 2px solid #333;
  }
</style>
<div class="parent">
  부모 박스
  <div class="child">자식(absolute)</div>
</div>
```

### 6-5) fixed vs sticky
```html
<!doctype html>
<meta charset="utf-8">
<style>
  body { height: 1500px; margin:0; }
  .fixed {
    position: fixed; top: 16px; right: 16px;
    padding: 8px 12px; background: #333; color: #fff;
  }
  header {
    position: sticky; top: 0;
    background: gold; padding: 12px; font-weight: bold;
  }
</style>
<header>나는 sticky 헤더!</header>
<div class="fixed">나는 fixed 버튼!</div>
<p style="margin:16px">스크롤을 내려서 차이를 보세요</p>
```

### 6-6) z-index 기본 동작
```html
<!doctype html>
<meta charset="utf-8">
<style>
  .stage { position: relative; height: 160px; border: 2px solid #333; margin: 16px 0; }
  .a, .b, .c { position: absolute; width: 120px; height: 120px; }
  .a { left: 30px;  top: 20px;  background: #ffb3b3; z-index: 1; }
  .b { left: 70px;  top: 40px;  background: #b3d1ff; z-index: 10; }
  .c { left: 110px; top: 60px;  background: #b3ffcc; z-index: 5; }
</style>
<div class="stage">
  <div class="a">z=1</div>
  <div class="b">z=10 (가장 앞)</div>
  <div class="c">z=5</div>
</div>
```

---

## 7. 자주 헷갈리는 포인트 
- “왜 박스가 생각보다 커졌지?” → **padding/border** 합산했나?
- “왜 줄이 바뀌지/안 바뀌지?” → **block vs inline** 확인
- “왜 width/height가 안 먹지?” → **inline**은 크기 지정 거의 불가
- “옆으로 나란히 + 크기 지정하고 싶다” → **inline-block** 혹은 **flex**
- “간격이 왜 이렇게 넓지?” → **margin**(바깥 여백) 때문일 가능성 큼
- “absolute가 어디 기준으로 움직이지?” → **가장 가까운 비-static 조상**(보통 `position: relative`) 확인
- “sticky가 안 먹는다?” → **부모 컨테이너 높이/스크롤 문맥** 체크
- “z-index 올렸는데도 뒤에 있다?” → **스태킹 컨텍스트**(부모의 z-index/opacity/transform 등) 확인

---

## 8. 속성 요약표

| 분류 | 속성 | 설명 | 비고 |
|---|---|---|---|
| 박스 크기 | `box-sizing` | `content-box`(기본) vs `border-box` | 실무는 `border-box` 선호 |
| 디스플레이 | `display` | `block`, `inline`, `inline-block`, `none`, `flex` | 레이아웃의 뼈대 |
| 여백/테두리 | `margin`/`padding`/`border` | 바깥/안쪽 여백, 테두리 | `outline`은 레이아웃 영향 X |
| 위치 | `position` | `static`, `relative`, `absolute`, `fixed`, `sticky` | 좌표: `top/right/bottom/left` |
| 쌓임 | `z-index` | 앞뒤 순서 | 같은 컨텍스트 내 비교 |
| 플렉스(컨테이너) | `flex-direction` | 주 축 방향 | `row`/`column` |
| 〃 | `flex-wrap` | 줄바꿈 | `nowrap`/`wrap` |
| 〃 | `justify-content` | 주 축 정렬 | 시작/끝/중앙/간격 |
| 〃 | `align-items` | 교차 축(단일 줄) 정렬 | stretch/center 등 |
| 〃 | `align-content` | 교차 축(여러 줄) 간격 | wrap일 때 의미 |
| 플렉스(아이템) | `order` | 순서 | 시각적 순서만 변경 |
| 〃 | `align-self` | 개별 교차 축 정렬 | `align-items` 오버라이드 |
| 〃 | `flex-grow` | 남는 공간 확장 비율 | 0이 기본 |
| 〃 | `flex-basis` | 기본 크기 | 축 기준 |

---

## 9. 요점 복습
- **block**: 문단처럼 **새 줄**에서 시작, 줄 하나 **독점**
- **inline**: 문장 속 **단어**처럼 옆으로 나란히
- **inline-block**: 단어처럼 나란히 + **크기 지정 가능**
- **none**: **명단엔 있지만** 경기장엔 없는 후보 선수
- **relative**: **내 자리**에서 살짝 이동 (자리 흔적은 남음)
- **absolute**: **줄에서 빠져나와** 부모 기준으로 배치
- **fixed**: **화면에 붙어** 스크롤해도 그대로
- **sticky**: 스크롤 임계점 전엔 relative, 후엔 fixed
- **z-index**: 무대 앞뒤 **순번표** (큰 수가 앞)
- **Flexbox**: 책장에 책을 정리하듯 **행/열**로 유연 정렬





