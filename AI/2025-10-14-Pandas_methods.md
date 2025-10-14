# 🧩 Pandas 주요 메서드 정리


---

## 📘 기본 구조 관련
| 메서드 | 설명 | 예시 |
|--------|------|------|
| `head()` | 상위 5행 미리보기 | `df.head()` |
| `info()` | 데이터 타입, 결측치, 열 정보 | `df.info()` |
| `describe()` | 숫자형 요약 통계 | `df.describe()` |

---

## 💡 데이터 선택 & 필터링
| 메서드 | 설명 | 예시 |
|--------|------|------|
| `df['col']` | 열 선택 | `df['tip']` |
| `df[...]` | 조건 필터링 | `df[df['tip'] > 5]` |
| `isin()` | 여러 조건 필터링 | `df[df['day'].isin(['Sat', 'Sun'])]` |
| `query()` | SQL처럼 조건문 작성 | `df.query('tip_percent >= 0.2')` |

---

## 🧮 그룹 & 집계
| 메서드 | 설명 | 예시 |
|--------|------|------|
| `groupby()` | 특정 기준으로 그룹 묶기 | `df.groupby('day')['tip'].mean()` |
| `agg()` | 여러 통계 동시에 계산 | `df.groupby('day')['tip'].agg(['mean','max'])` |
| `apply()` | 그룹별 사용자 정의 함수 적용 | `df.groupby('size').apply(lambda x: x['tip'].mean())` |
| `mean()` / `sum()` / `max()` | 평균 / 합계 / 최대값 | `df['tip'].mean()` |

---

## 🔄 인덱스 & 구조 조정
| 메서드 | 설명 | 예시 |
|--------|------|------|
| `reset_index()` | 인덱스를 일반 열로 복원 | `df.groupby('day')['tip'].mean().reset_index()` |
| `set_index()` | 특정 열을 인덱스로 지정 | `df.set_index('day')` |
| `sort_values()` | 값 기준으로 정렬 | `df.sort_values('tip_percent', ascending=False)` |

---

## 🔁 형태 변환 (Reshaping)
| 메서드 | 설명 | 예시 |
|--------|------|------|
| `melt()` | wide → long 변환 | `pd.melt(df, id_vars=['size'], value_vars=['total_bill','tip'])` |
| `pivot()` | long → wide 변환 | `df.pivot(index='day', columns='sex', values='tip')` |
| `pivot_table()` | pivot + 집계 기능 포함 | `df.pivot_table(values='tip', index='sex', columns='day', aggfunc='mean')` |

---

## 🧹 계산 및 열 추가
| 메서드 | 설명 | 예시 |
|--------|------|------|
| `assign()` | 새 컬럼 추가 | `df.assign(tip_percent=df['tip']/df['total_bill'])` |
| `apply()` | 행/열 단위로 함수 적용 | `df['tip_percent'] = df.apply(lambda x: x['tip']/x['total_bill'], axis=1)` |
| `round()` | 반올림 | `df['tip_percent'].round(2)` |

---

## 🔍 탐색 & 확인
| 메서드 | 설명 | 예시 |
|--------|------|------|
| `value_counts()` | 범주형 데이터 빈도 확인 | `df['day'].value_counts()` |
| `unique()` / `nunique()` | 고유값 조회 / 개수 | `df['sex'].unique()` |
| `isnull()` / `notnull()` | 결측치 확인 | `df.isnull().sum()` |

---

## 🧠 참고 요약표

| 범주 | 자주 쓰는 메서드 |
|------|------------------|
| 구조 확인 | `head`, `info`, `describe` |
| 선택/필터링 | `[]`, `query`, `isin` |
| 집계/그룹 | `groupby`, `agg`, `apply` |
| 형태 변환 | `melt`, `pivot`, `pivot_table` |
| 정렬/인덱스 | `sort_values`, `reset_index` |
| 탐색/확인 | `value_counts`, `unique`, `isnull` |


