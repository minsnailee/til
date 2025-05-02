# 📘 ML

---

## ✅ 머신러닝 종류

### 1. 지도학습 (Supervised Learning)

- *정답(Label)**이 있는 데이터를 바탕으로 학습
- 목표: 입력 → 출력(Label)을 예측하는 모델 생성
- 주요 유형:
    - **분류(Classification)**: 범주형 결과 예측
        - 예: 붓꽃 품종 분류, 암 양성/음성 판별
        - 이진 분류 / 다중 분류
    - **회귀(Regression)**: 연속적인 수치 예측
        - 예: 연간 소득 예측, 주택 가격 예측
- 🔍 구분 기준
    - **분류** → 범주형 데이터
    - **회귀** → 수치형 데이터

### 2. 비지도학습 (Unsupervised Learning)

- 정답(Label)이 없이 **패턴이나 구조를 찾는 학습**
- 주요 유형:
    - **클러스터링(Clustering)**: 비슷한 데이터끼리 그룹화
        - 예: 고객 세분화, 동물 이미지 자동 분류
    - **차원 축소(Dimensionality Reduction)**: 데이터 시각화나 압축
- 예시:
    - 얼굴 인식
    - 소비자 행동 분석

### 3. 강화학습 (Reinforcement Learning)

- 명확한 정답은 없고, **보상(Reward)**을 통해 학습
- 에이전트가 환경 속에서 **최대한의 보상**을 얻는 방향으로 스스로 학습
- 주로 **게임, 로봇 제어, 자동 운전** 등에 사용



## ✅ 머신러닝 기본 과정

1. 🧭 문제 정의 (Problem Identification)
    - 비즈니스 목적 명확히 설정
    - 지도/비지도/강화학습 판단
    - 분류/회귀 판단
2. 📥 데이터 수집 (Data Collection)
    - CSV, JSON, DB, 웹 크롤링, IoT 센서, 설문 등 다양한 소스
3. 🧹 데이터 전처리 (Data Preprocessing)
    - 결측치/이상치 처리
    - **Feature Engineering (특성 공학)**
        - Scaling (정규화, 표준화)
        - Encoding (범주형 → 숫자형)
        - Binning (숫자형 → 범주형)
        - Transform (새로운 피처 생성)
4. 📊 탐색적 데이터 분석 (EDA)
    - 통계 분석, 변수 간 상관관계
    - 시각화 (pandas, matplotlib, seaborn 등)
5. 🧩 특성 선택 (Feature Selection)
    - 중요한 피처만 선별해 사용
6. 🧠 모델 선택 & 하이퍼파라미터 튜닝
    - 모델 예시: KNN, SVM, 선형 회귀, 결정트리, 랜덤 포레스트, CNN, RNN 등
    - **하이퍼파라미터(Hyperparameter)**
        
        → 학습 전에 사람이 지정하는 값 (예: KNN의 k값, SVM의 C값 등)
        
        → 모델 성능에 큰 영향
        
7. 🏋️‍♀️ 학습 (Training)
    
    ```python
    model.fit(X_train, y_train)  # 학습
    model.predict(X_test)        # 예측
    ```
    
    - train/test 데이터는 일반적으로 7:3 비율로 분할
8. 📈 평가 (Evaluation)
    - 정확도, 정밀도, 재현율, F1-score 등 사용