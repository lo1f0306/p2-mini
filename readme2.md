# 🏅 올림픽 개최국 효과(Home Advantage) 데이터 분석

올림픽 개최라는 국가적 이벤트가 실제 메달 획득 성적에 미치는 영향을 데이터로 검증한 EDA 프로젝트입니다.

---

## 1. 📝 프로젝트 개요
* **목적**: 올림픽 개최국의 성적 향상 유무와 지속성, 종목별 편차 분석
* **기간**: 2026.02.23 - 2026.02.24
* **데이터**: 120년간의 올림픽 선수 및 메달 획득 데이터 (`athlete_events.csv`)
* **사용 기술**: Python, Pandas, Matplotlib, Seaborn

---
## 2. 🛠️ 데이터 전처리
### 1. 기존 데이터와 변경 후 행/열 수: 
  - 행: 271,116 개
  - 열: 15 개

### 2. 데이터 구조 변경 및 전처리 확인

<div align="center">

| **[ 기존 데이터 구조 ]** | **[ 변경 후 데이터 구조 ]** |
| :--- | :--- |
| <table><tr><th>No</th><th>Column</th><th>Count</th><th>Dtype</th></tr><tr><td>0</td><td>**ID**</td><td>271,116</td><td>int64</td></tr><tr><td>1</td><td>Name</td><td>271,116</td><td>str</td></tr><tr><td>2</td><td>Sex</td><td>271,116</td><td>str</td></tr><tr><td>3</td><td>Age</td><td>261,642</td><td>float</td></tr><tr><td>4</td><td>Height</td><td>210,945</td><td>float</td></tr><tr><td>5</td><td>Weight</td><td>208,241</td><td>float</td></tr><tr><td>6</td><td>Team</td><td>271,116</td><td>str</td></tr><tr><td>7</td><td>NOC</td><td>271,116</td><td>str</td></tr><tr><td>8</td><td>Games</td><td>271,116</td><td>str</td></tr><tr><td>9</td><td>Year</td><td>271,116</td><td>int64</td></tr><tr><td>10</td><td>Season</td><td>271,116</td><td>str</td></tr><tr><td>11</td><td>City</td><td>271,116</td><td>str</td></tr><tr><td>12</td><td>Sport</td><td>271,116</td><td>str</td></tr><tr><td>13</td><td>Event</td><td>271,116</td><td>str</td></tr><tr><td>14</td><td>Medal</td><td>39,783</td><td>str</td></tr></table> | <table><tr><th>No</th><th>Column</th><th>Count</th><th>Dtype</th></tr><tr><td>0</td><td>**ID**</td><td>269,731</td><td>int64</td></tr><tr><td>1</td><td>Name</td><td>269,731</td><td>str</td></tr><tr><td>2</td><td>Sex</td><td>269,731</td><td>str</td></tr><tr><td>3</td><td>Age</td><td>260,416</td><td>float</td></tr><tr><td>4</td><td>Height</td><td>210,917</td><td>float</td></tr><tr><td>5</td><td>Weight</td><td>208,204</td><td>float</td></tr><tr><td>6</td><td>Team</td><td>269,731</td><td>str</td></tr><tr><td>7</td><td>NOC</td><td>269,731</td><td>str</td></tr><tr><td>8</td><td>Games</td><td>269,731</td><td>str</td></tr><tr><td>9</td><td>Year</td><td>269,731</td><td>int64</td></tr><tr><td>10</td><td>Season</td><td>269,731</td><td>str</td></tr><tr><td>11</td><td>City</td><td>269,731</td><td>str</td></tr><tr><td>12</td><td>Sport</td><td>269,731</td><td>str</td></tr><tr><td>13</td><td>Event</td><td>269,731</td><td>str</td></tr><tr><td>14</td><td>Medal</td><td>39,772</td><td>str</td></tr><tr><td>15</td><td>**host_NOC**</td><td>269,731</td><td>str</td></tr><tr><td>16</td><td>**is_host**</td><td>269,731</td><td>bool</td></tr></table> |

</div>



### 3. 📌기술 통계 및 데이터 요약

#### 3-1. 수치형 변수
- **ID 및 Year 제외:** 분석적 의미가 낮은 고유 식별 번호와 연도 데이터는 통계 대상에서 제외하였습니다.
- **Age (나이):** 중앙값이 평균값보다 약간 작게 나타나, 데이터 분포가 오른쪽으로 살짝 쏠려(Right-skewed) 있음을 확인하였습니다.
- **Height & Weight (신체 조건):** 중앙값과 평균값이 거의 일치하여 정규 분포에 가까운 고른 분포를 보이고 있습니다.

![Describe.png](image/Describe.png)

#### 3-2. 범주형 변수
- **Name (선수 이름):** 이름의 중복 빈도가 높습니다. 이는 한 선수가 여러 종목에 참여하거나 여러 회차의 올림픽에 출전했음을 나타냅니다. (Full Name 기재 방식으로 동명이인 가능성은 매우 낮음)
- **Sex (성별):** 남성 선수의 수가 여성 선수보다 약 **2.5배** 더 많습니다.
- **Team vs NOC (국적 코드):** 초창기 연합팀 제도 등으로 인해 `Team` 컬럼의 고유값이 `NOC`보다 많습니다. 정확한 국가별 분석을 위해서는 고유 코드인 **NOC** 컬럼을 활용하는 것이 적절합니다.
- **참가 기록:** **2000년 시드니 올림픽**에 가장 많은 선수가 참가하였습니다.
- **Season (시즌):** 하계 올림픽 참가자 수가 동계 올림픽에 비해 압도적으로 많습니다.
- **City (개최 도시):** **런던**에서 총 3회(1908, 1948, 2012)로 가장 많이 개최되었으며, 해당 도시에서는 육상 종목이 가장 활발하게 열렸습니다.
- **Sport (종목):** 전체 참가 선수 기록 중 **남자 축구** 종목의 데이터가 가장 많습니다.
- **Medal (메달):** 금, 은, 동 중 **금메달**의 수여 기록이 가장 빈번하게 나타납니다.

![Describe2.png](image/Describe2.png)

---
## 4. 🔍 주요 분석 질문 및 결과
---
## 5.💡 결론 및 시사점
### 5-1. 개최국 여부에 따른 메달 획득 수 비교
"안방에서는 메달 효율이 최소 2배 이상 증가한다"
분석: 개최 경험이 있는 국가들을 대상으로 분석한 결과, 동일 국가가 '원정' 대비 '홈' 경기에서 거두는 평균 메달 수는 150~210% 가량 폭증하였다.
인사이트: 이는 단순한 심리적 요인을 넘어, 개최국에게 부여되는 자동 출전권과 인프라 익숙도가 결합된 결과다. 특히 동계 올림픽은 하계보다 지형 적응력이 중요하여 버프의 강도가 더 선명하게 나타났다.
### 5-2. 개최 전후 메달 성적 변화 추이
올림픽 효과는 개최 전 8년부터 시작
분석: 하계와 동계 시즌을 엄격히 분리하여 4년 주기 성적을 추적한 결과, 개최 8년 전(결정 시점)부터 투자가 시작되어 성적이 우상향하는 **'빌드업 구간'**이 확인되었다.
인사이트: 개최 당해(Year 0)에 피크를 찍은 뒤 성적은 하락하지만, 개최 12년 후(+12)의 성적은 개최 12년 전(-12)보다 통계적으로 유의미하게 높았다. 이는 올림픽 유치가 국가 스포츠 시스템의 '체급' 자체를 높였음을 증명한다.
### 5-3. 종목 유형에 따른 메달 획득 수 비교
판정의 주관성보다, 자본이 투입된 기록 종목에서 버프가 더 정직하게 나타난다"
분석: 종목을 **Objective(명확한 기록 존재), Subjective(심판의 주관적 점수로 채점), Others(복합적)**로 분류했을 때, 의외로 Objective 종목의 상승률이 가장 높게 나타났다.
인사이트: 이는 개최국 버프가 단순히 '심판의 호의'에서 오는 노이즈가 아니라, 메달이 대량으로 걸린 기록 종목에 대한 국가적 자본 투여와 스포츠 과학의 집중(Optimization) 결과임을 시사한다.