아래는 보스턴 주택 가격 예측 실습을 코드와 설명 중심으로 정리한 내용이다. 전반적인 흐름은 데이터 수집 → 전처리 → 모델 학습 → 평가 → 성능 개선 → 규제 적용 순서로 구성되어 있다.

---

## 📌 1. 문제 정의

보스턴 주택 가격 데이터를 활용하여 **회귀 모델(Linear Regression, SGDRegressor)** 을 통해 주택 가격(MEDV)을 예측한다.

---

## 📌 2. 데이터 수집 및 준비

### (1) 데이터 로딩

```python

import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

# 로컬 파일 사용
boston_data = pd.read_csv('./data/boston_housing.csv')

# 입력(X), 출력(y) 정의
X = boston_data.loc[:, 'CRIM':'LSTAT']
y = boston_data['MEDV']

```

### (2) 컬럼 설명 요약

| 컬럼 | 의미 |
| --- | --- |
| CRIM | 범죄 발생률 |
| ZN | 대형 주거지역 비율 |
| INDUS | 비상업지역 비율 |
| CHAS | 찰스강 여부 |
| NOX | 일산화질소 농도 |
| RM | 주택당 방 수 |
| AGE | 오래된 주택 비율 |
| DIS | 고용센터 거리 |
| RAD | 고속도로 접근 용이성 |
| TAX | 재산세율 |
| PTRATIO | 교사/학생 비율 |
| B | 흑인 비율 지수 |
| LSTAT | 저소득층 비율 |
| MEDV | 주택 가격 (목표값) |

---

## 📌 3. 데이터 전처리

### (1) 스케일링(StandardScaler 사용)

```python

from sklearn.preprocessing import StandardScaler

scaler = StandardScaler()
X_scaled = pd.DataFrame(scaler.fit_transform(X), columns=X.columns)

```

---

## 📌 4. 데이터 분리

```python

from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(
    X_scaled, y, test_size=0.3, random_state=50
)

```

---

## 📌 5. 모델 학습 및 평가

### (1) 선형 회귀 (Linear Regression)

```python

from sklearn.linear_model import LinearRegression
from sklearn.metrics import mean_squared_error

lr_model = LinearRegression()
lr_model.fit(X_train, y_train)

print("R^2:", lr_model.score(X_test, y_test))  # 0.668
print("MSE:", mean_squared_error(y_test, lr_model.predict(X_test)))  # 약 33.86

```

### (2) SGD 회귀 (SGDRegressor)

```python

from sklearn.linear_model import SGDRegressor

sgd_model = SGDRegressor(eta0=0.01, max_iter=5000, verbose=1)
sgd_model.fit(X_train, y_train)

print("R^2:", sgd_model.score(X_test, y_test))  # 0.665
print("MSE:", mean_squared_error(y_test, sgd_model.predict(X_test)))  # 약 34.17

```

---

## 📌 6. 성능 개선 - 특성 확장 (Polynomial Features)

### (1) 컬럼 조합하여 곱하기

```python

for i in range(X.columns.size):
    for j in range(i, X.columns.size):
        X[X.columns[i] + '*' + X.columns[j]] = X[X.columns[i]] * X[X.columns[j]]

```

- 총 104개의 특성으로 확장됨.

### (2) 다시 스케일링

```python

X_trans = pd.DataFrame(scaler.fit_transform(X), columns=X.columns)

```

### (3) 다시 분리 후 학습

```python

X_train, X_test, y_train, y_test = train_test_split(
    X_trans, y, test_size=0.3, random_state=60
)

lr_model2 = LinearRegression()
lr_model2.fit(X_train, y_train)

print("R^2:", lr_model2.score(X_test, y_test))  # 약 0.854

```

→ **특성 확장**을 통해 성능이 크게 향상되었음을 확인할 수 있다.

---

## 📌 7. 정규화 (규제 모델 적용)

### (1) Ridge (L2 규제)

```python

from sklearn.linear_model import Ridge

ridge = Ridge(alpha=0.1)
ridge.fit(X_train, y_train)

print("Train R^2:", ridge.score(X_train, y_train))  # 0.93
print("Test R^2:", ridge.score(X_test, y_test))    # 0.854

```

- 모든 특성을 일정 비율로 줄여서 과적합을 방지하는 데 효과적이다.

---

### (2) Lasso (L1 규제)

```python

from sklearn.linear_model import Lasso

lasso = Lasso(alpha=0.001)
lasso.fit(X_train, y_train)

print("Train R^2:", lasso.score(X_train, y_train))  # 0.921
print("Test R^2:", lasso.score(X_test, y_test))    # 0.836
print("사용된 특성 수:", np.sum(lasso.coef_ != 0))  # 104

```

- 일부 특성의 가중치를 0으로 만들어서 **특성 선택 기능**도 함께 수행한다.

---

## ✅ 최종 요약

| 모델 | 특성 수 | R²(테스트) | 특징 |
| --- | --- | --- | --- |
| Linear Regression | 13 | 0.668 | 기본 모델 |
| SGDRegressor | 13 | 0.665 | 경사하강법 |
| Linear + 확장 | 104 | **0.854** | 특성 조합으로 성능 향상 |
| Ridge | 104 | 0.854 | 과적합 방지에 효과적 |
| Lasso | 104 | 0.836 | 특성 선택 가능 |