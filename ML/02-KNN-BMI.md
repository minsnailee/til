## 🧪 프로젝트 개요

- **문제정의**
    - 500명의 키와 몸무게를 이용해 비만도(`Label`)를 예측하는 분류 모델을 만든다.
    - **지도학습 > 분류** 문제
- **데이터**
    - CSV 파일: `bmi_500.csv`
    - 컬럼: Height, Weight, Label

---

## 📘 코드 정리 + 해설

### 1. 필요한 도구 불러오기

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
```

- 데이터 분석 및 시각화를 위한 필수 라이브러리

---

### 2. 데이터 불러오기 및 정보 확인

```python
data = pd.read_csv('./data/bmi_500.csv')
data.info()
```

- CSV 파일을 읽어오고 `info()`로 데이터 개수, 컬럼 수, 결측치 유무 확인
- 결과: **500개 샘플, 결측치 없음**

---

### 3. 입력(x), 출력(y) 데이터 분리

```python
x = data.loc[:, 'Height':'Weight']  # 입력 데이터 (키, 몸무게)
y = data.loc[:, 'Label']            # 정답 라벨 (비만도)
```

- 슬라이싱을 활용해 독립 변수(x)와 종속 변수(y)를 나눔

---

### 4. 학습/테스트 데이터 분할 (7:3 비율)

```python
x_train = x.iloc[:350]
x_test = x.iloc[350:]
y_train = y.iloc[:350]
y_test = y.iloc[350:]
```

- 데이터셋을 순서대로 나누고 있음 → **주의: 무작위 분할 아님**, 실제에선 `train_test_split()` 사용을 권장

---

### 5. 모델 생성 및 학습

```python
from sklearn.neighbors import KNeighborsClassifier
from sklearn.metrics import accuracy_score

knn_model = KNeighborsClassifier()
knn_model.fit(x_train, y_train)
```

- `KNeighborsClassifier()`를 기본값(`n_neighbors=5`)으로 생성
- `fit()` 메서드로 모델 학습

---

### 6. 모델 예측 및 평가

```python
pre = knn_model.predict(x_test)
accuracy_score(y_test, pre)
```

- 테스트 데이터를 예측한 결과(`pre`)와 실제 라벨(`y_test`)을 비교해 정확도 측정
- 정확도(예시): **높은 정확도 → 모델이 잘 학습됨**

---

### 7. 이웃 수(k)를 99로 설정한 경우

```python
knn_model2 = KNeighborsClassifier(n_neighbors=99)
knn_model2.fit(x_train, y_train)
pre2 = knn_model2.predict(x_test)
accuracy_score(y_test, pre2)
```

- `k=99`로 설정 → 너무 많은 이웃을 참고함
- 결과적으로 예측이 **단순화되며 정확도 하락** (예: **0.686** 등)

---

## 📌 정리

| 항목 | 설명 |
| --- | --- |
| **KNN 모델 특징** | 주변 `k개`의 이웃의 다수결로 분류를 결정하는 단순하고 직관적인 알고리즘 |
| **정확도 하락 이유 (`k=99`)** | 너무 많은 이웃을 참고하면, **대부분의 데이터를 차지하는 클래스 쪽으로 편향**됨 → 과소적합 |
| **학습 vs 데이터** | 학습 자체보다도 **데이터가 얼마나 정제되어 있는가**가 예측 성능에 더 큰 영향을 준다 |
| **데이터 분할 방식 개선점** | 무작위로 데이터를 나누지 않고 순서대로 나누었기 때문에 편향 가능성 있음 → `train_test_split()` 사용 권장 |
| **정규화 필요성** | 키와 몸무게는 단위가 다르기 때문에 **스케일 조정(StandardScaler 등)**이 필요함 (현재 코드에는 없음) |