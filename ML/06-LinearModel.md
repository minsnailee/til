## 🔷 선형 모델(Linear Model)이란?

입력값 `x`에 따라 예측값 `y`가 **선형 관계**를 가진다고 가정하고 모델링하는 머신러닝 방법입니다.

### 선형 함수 식:

```

y = wx + b

```

- `w`: 기울기(가중치)
- `b`: 절편(편향)
- 목표: **오차(MSE)**가 최소가 되는 `w`, `b`를 찾는 것

---

## 📊 예시: 공부시간으로 성적 예측하기

```python

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# 성적 데이터 생성
data = pd.DataFrame(
    [[2, 20], [4, 40], [8, 80], [9, 90]],
    index=['A학생', 'B학생', 'C학생', 'D학생'],
    columns=['공부시간', '성적']
)

data

```

---

## 🧮 해석적 방법(수학 공식 기반 선형 회귀)

```python

from sklearn.linear_model import LinearRegression

# 모델 생성
linear_model = LinearRegression()

# 모델 학습 (2차원 입력, 1차원 타겟)
linear_model.fit(data[['공부시간']], data['성적'])

# 예측하기
print("7시간 공부 예상 성적:", linear_model.predict([[7]]))  # [70.]

# 결정 계수(R² score)
print("R² score:", linear_model.score(data[['공부시간']], data['성적']))  # 1.0 (완벽한 예측)

# 가중치(w), 절편(b) 확인
print("기울기 w:", linear_model.coef_)        # [10.]
print("절편 b:", linear_model.intercept_)     # 0.0 (실제 값은 미세한 오차)

```

---

## 📉 비용 함수(Cost Function) 이해하기

```python

# 선형 함수 정의: h(x) = wx + b (여기선 b=0)
def h(w, x):
    return w * x + 0

# 비용 함수 정의: MSE 계산
def cost(data, target, weight):
    y = h(weight, data)
    mse = ((y - target) ** 2).mean()
    return mse

# 다양한 w 값에 대한 비용 계산
cost_list = []
for w in range(1, 20):
    error = cost(data['공부시간'], data['성적'], w)
    cost_list.append(error)

# 시각화
plt.plot(range(1, 20), cost_list)
plt.title("가중치(w)에 따른 비용(MSE)")
plt.xlabel("가중치(w)")
plt.ylabel("비용(MSE)")
plt.grid(True)
plt.show()

```

> ✅ w = 10일 때 MSE가 0이므로, 가장 적합한 기울기!
> 

---

## 📉 경사하강법(SGD) 사용한 선형회귀

경사하강법은 **오차를 줄이는 방향으로 조금씩 이동**하면서 `w`, `b`를 업데이트합니다.

```python

from sklearn.linear_model import SGDRegressor

# SGD 모델 생성
sgd_model = SGDRegressor(
    eta0=0.001,       # 학습률
    max_iter=5000,    # 반복 횟수
    verbose=1         # 학습 로그 출력
)

# 학습 진행
sgd_model.fit(data[['공부시간']], data['성적'])

# 예측 결과
print("SGD 예측 결과:", sgd_model.predict([[7]]))

# 가중치 및 절편 확인
print("SGD 기울기(w):", sgd_model.coef_)
print("SGD 절편(b):", sgd_model.intercept_)

```

---

## 📘 용어 정리

| 용어 | 설명 |
| --- | --- |
| `H(x) = wx + b` | 선형 함수 |
| `Cost Function` | 오차(MSE)를 계산하는 함수 |
| `MSE` | 평균 제곱 오차 (회귀에서 가장 많이 사용) |
| `MAE` | 평균 절대 오차 |
| `RMSE` | 평균 제곱근 오차 |
| `SGD` | Stochastic Gradient Descent, 경사하강법 |

---

## ✅ 요약

- 선형 회귀는 `y = wx + b` 형태로 예측
- 오차를 줄이는 것이 목표 → `MSE` 사용
- `LinearRegression`: 수학적 공식 기반
- `SGDRegressor`: 경사하강법으로 가중치 업데이트