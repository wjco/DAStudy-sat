# 축구 선수 몸값 예측 모델링

## 프로젝트 배경 및 목적
- 스포츠에서 통계 및 예측 모델의 중요성 증가
- 다양한 스포츠 통계 회사에서 정교한 예측 모형 제공 (ex. opta)
- `Python` `Machine Learning` `EDA` 
- 선수의 통계 자료를 바탕으로, 해당 시즌 예상되는 몸값을 예측하는 모델 만드는 것이 목표

## 데이터 설명 
- Transfermarkt 웹사이트에서 스크레이핑한 각종 축구 데이터 (매주 한번 파일 업데이트) [Kaggle Data URL](https://www.kaggle.com/datasets/davidcariboo/player-scores)
    - 주요 대회 시즌별 60,000개 이상의 경기
    - 모든 대회의 400개 이상의 클럽들
    - 30,000 이상의 선수들
    - 400,000개 이상의 선수 가치 기록
    - 1,200,000개 이상의 선수 출전 기록


- 데이터베이스 스키마
    <img src="https://raw.githubusercontent.com/dcaribou/transfermarkt-datasets/master/resources/diagram.svg?sanitize=true" width="1700">

## 프로젝트 가설 및 분석 방법

### 가설
- 선수의 몸값에 영향을 가장 많이 주는 것은 공격 포인트 (골 + 어시스트)일 것이다
- 나이와 포지션에 따라, 비슷한 스탯이라도 선수의 몸값은 다를 것이다
- 전체 선수들의 분포에 비해, 고가의 선수들 (100M 유로 이상)의 몸값 분포는 다를 것이다

### 분석 방법
1. Data Collection
   - Kaggle 데이터셋 다운로드 (대용량) → 별도 저장 후 전처리 → [data 폴더](https://github.com/benidjor/DAStudy-sat/tree/main/Tek/football-data-from-transfermarkt/data)에 저장
     
2. Data Preprocessing & EDA
    - `appearances` `competitions` `players` `player_valuations_df` 데이터셋 사용 → group by 이후 merge 하여 새로운 데이터프레임 생성
    - 원본 데이터셋과 동일한 출처에서 가져온 [다른 데이터셋](https://www.kaggle.com/datasets/mexwell/football-data-from-transfermarkt?select=players.csv) 발견하여, merge 후 결측치 최대한 처리
   - 다중 공선성 확인 후 상관관계 높은 컬럼 제거
   - age, position, market_value, goals, assists 컬럼 시각화

3. Modeling
   - 19-20 시즌 부터 22-23 시즌까지의 데이터를 학습하여, 23-24 시즌 (현재 진행중) 몸값을 예측하는 모델 생성
   - 범주형 변수 처리를 위해 `Target Encoding` 사용
   - Target인 몸값에 이상치가 많기 때문에, `RMSLE`를 우선적인 모델 평가 지표로 사용
   - 다양한 회귀 모델들을 비교하여, 하이퍼 파라미터 튜닝을 거친 후 `XGBoost`로 최적화
   - `예시) 23-24 시즌 손흥민 실제 몸값 50,000,000 유로 / 예측 몸값 44,363,164 유로`
   - `RMSLE : 0.63` `R2 Score : 0.73`

## 프로젝트 회고 및 향후 개선 방안
- 아쉬웠던 점
    - Transfermarkt에서 경기 중 발생하는 선수의 세부적인 스탯 데이터까지는 제공하지 않아 몸값에 대한 세부적인 예측 어려움
    - 높은 몸값을 가진 선수는 잘 예측했지만 (`RMSLE 작다`), 낮은 몸값을 가진 선수는 잘 예측하지 못하는 모습 (`RMSLE 크다`)

- 향후 개선 계획
    - 경기중에 선수가 기록하는 세부적인 스탯을 제공하는 웹사이트를 크롤링하여, 다양한 스탯들을 반영하는 모델 생성
    - Feature Engineering, 하이퍼파라미터 튜닝 등을 통해 예측 모델 최적화

## References
- Preprocessing
    - 결측치 처리
        - https://syj9700.tistory.com/10
        - https://gooopy.tistory.com/43
        - https://cheris8.github.io/data%20analysis/DP-NA-Imputation/
          
- EDA
    - 다중 공선성
        - https://dacon.io/codeshare/4443
        - https://dacon.io/forum/408387
        - https://aliencoder.tistory.com/17
          
- Modeling
    - Target Encoding
        - https://velog.io/@seungwoong12/encoding
    - RMSLE
        - https://shinminyong.tistory.com/32
        - https://wikidocs.net/219419
    - Optuna
        - https://forecastegy.com/posts/xgboost-hyperparameter-tuning-with-optuna
