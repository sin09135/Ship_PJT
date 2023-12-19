# ⛴️ 항만 선박 대기시간 예측 및 운영 시스템 개선 제안

- 주제 선정 배경 : 항만 서비스의 핵심 지표인 '선박 대기시간' 감소를 위한 '분' 단위 명확한 대기시간 예측으로 물류 비용 절감 및 항구 경쟁력 증대, 대기 오염 감소 효과를 기대
- 데이터 : PORT-MIS 선박 입출항 현황, 시설사용허가현황, 울산항 선박제원정보, 해양기상정보
- 결과 : 울산항 선박 대기시간 '분' 단위 예측 모델 & 울산항 선석 점유율 개선 방안 제안
  - lightGBM 회귀 모델
  - 순작업시간 점유율 하위 선석 - 대기 선박 매칭 방안 제안
- 기대효과 : 선박 운영 비용 감소, 대기오염 배출량 감소

<br>

### Requirements

![Jupyter](https://img.shields.io/badge/jupyter-F37626?style=for-the-badge&logo=jupyter&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-FFDC28?style=for-the-badge&logo=Pandas&logoColor=white)
![Numpy](https://img.shields.io/badge/numpy-013243?style=for-the-badge&logo=numpy&logoColor=white)
![plotly](https://img.shields.io/badge/plotly-3F4F75?style=for-the-badge&logo=plotly&logoColor=white)
![scikitlearn](https://img.shields.io/badge/scikitlearn-F7931E?style=for-the-badge&logo=scikitlearn&logoColor=white)

<br>

### 분석 프로세스

1. 1차 데이터 수집 및 전처리 ▶︎ 후보 항만 대기율 도출  ▶︎ target 항만 선정(울산항)
2. target 항만(울산항) 2차 데이터 수집 및 전처리 
3. 울산항 데이터 EDA & Feature Engineering
4. 울산항 액체화물 선박 '분' 단위 대기시간 예측 모델링
5. 예측 결과 활용 예시 및 기대효과 도출
6. 항만 운영시스템 개선사항 제안

<br>

### 전처리

- 1차 전처리(후보 항만 데이터)

  - 사용목적명 기준, 서비스 시작 및 종료 시점과 대기 시작 및 종료 시점 도출
  - 입출항 1건 당(호출부호, 입항일시 기준) 서비스 시간 및 대기 시간 도출(중복행 제거)
  - 후보 항만 대기율 도출

  

  > 대기율 산출 목적 : 후보 항만 중 target 항만 선정 지표로 채택
  >
  > - 선박 대기율은 항만 서비스 주요 지표로, 정확한 측정과 최적값 유지가 중요함
  > - 대기율 = 대기시간 / 서비스시간
  > - 대기율 비교 결과 : target 항만 울산항 선정

- 2차 전처리(울산항 데이터)
  - 선박 제원 정보 결측치 처리 : 선박용도별 중앙값 대체
  - 기상 데이터 결측치 처리 : 선형 보간

<br>

### 대기율 산출을 위한 입출항 Case 정의

- 입출항 Case 정의 필요성 : 시설사용목적별 시작-종료 시점 및 지속 시간 부재
- 일반적인 8단계의 선박 입출항 프로세스를 대기/서비스 시점에 영향을 미치는 시점 중심으로 압출 및 Case 재정의

<img title="" src="./img/ship_process.png" alt="local" data-align="center" width="453">

<br>

### 울산항 데이터 EDA

- 전체 입항 건 중 대기 발생 건 비율
- 선박 용도별 방문횟수 비율
- 선박 용도별 평균 대기 시간 및 대기율
- 선석(계선장소)별 평균 대기율
- 상관분석(액체/비액체 화물 구분)
- K-S 검정

👉 **<u>결과 : 울산항의 액체화물 선박의 대기시간 비중을 고려하여 단독 모델링 결정</u>**

<br>

### Feature Engineering

- 선석 기준 파생변수 생성 : 시간대별 누적 선박 합계, 연평균 선석 점유율, 연평균 톤 처리량, 연평균 접안 척수
- 선박 기준 파생변수 생성 : 선박 평균 서비스 시간, 선박 평균 대기 시간

<img title="" src="./img/features.png" alt="local" data-align="center" width="453">

<br>

### Model Fitting & Application

- CatBoost, XGBoost, LightGBM 3가지 모델 사용, RMSE 최저 모델(LGBM) 채택

  - 하이퍼 파라미터 튜닝 : Bayesian Optimization

  - 최적모델 RMSE 최적 모델 : 1028.99

- 대기시간 예측 결과 예시

  <img title="" src="./img/result.png" alt="local" data-align="center" width="453">

<br>

### 기대효과

- 선박 대기비용 4조 738억 원 감소
  - 대기비용 산출식 재구성 : 선박대기시간 예측모형 개발을 위한 방법론 연구(2023) 참고

- 온실가스 배출량 4,668,901kg 감소
  - 온실가스 배출량 산출식 재구성 : 선박 대기오염 물질 배출량 산정 및 배출 기여도 분석연구(2021) 참고


<img title="" src="./img/expect.png" alt="local" data-align="center" width="453">

<br>

### 항만 운영 시스템 개선 제안사항

- 순작업시간 점유율 30% 이하 선석(문제 선석)의 활용 방안 : 

  👉 대기중 선박 정보와 문제 선석의 정보를 매칭하여 선박 대기율을 낮추고 서비스 시간을 높여 항만 운영 효율성을 제고
  
  <img title="" src="./img/expect2.png" alt="local" data-align="center" width="453">
