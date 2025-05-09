## 🔢 **1. 라이브러리 불러오기**

```python

import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

from sklearn.neighbors import KNeighborsClassifier
from sklearn.metrics import accuracy_score
from sklearn.datasets import load_iris

```

> 설명: 데이터 처리와 시각화를 위한 기본 라이브러리와 KNN 알고리즘을 구현할 도구를 불러온다.
> 

---

## 📦 **2. Iris 데이터 불러오기 및 탐색**

```python

iris_data = load_iris()
iris_data.keys()

```

> 설명: load_iris() 함수로 붓꽃(Iris) 데이터를 불러오고, 어떤 정보들이 있는지 키값을 확인한다.
> 

```python

iris_data.data           # X값 (특징값)
iris_data.target         # y값 (정답 레이블)
iris_data.feature_names  # 특성 이름
iris_data.target_names   # 품종 이름
print(iris_data.DESCR)   # 데이터 설명

```

> 설명: 입력 데이터, 타깃 레이블, 특성명 등을 확인하고 데이터셋의 구조를 파악한다.
> 

---

## 🧾 **3. 데이터프레임으로 변환**

```python

iris_df = pd.DataFrame(iris_data.data, columns=iris_data.feature_names)
iris_df.head()
iris_df.info()

```

> 설명: Numpy 배열 형태의 데이터를 Pandas의 DataFrame으로 변환하여 데이터 분석 및 시각화를 편리하게 만든다.
> 

---

## 🔀 **4. 훈련/테스트 데이터 분리**

```python

from sklearn.model_selection import train_test_split

X = iris_df
y = iris_data.target

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.25, random_state=3
)

print(X_train.shape)
print(y_train.shape)
print(X_test.shape)
print(y_test.shape)

```

> 설명: train_test_split을 통해 전체 데이터를 훈련용과 테스트용으로 나눈다. random_state로 랜덤 시드를 고정하여 재현 가능한 결과를 만든다.
> 

---

## 📊 **5. 데이터 시각화**

```python

pd.plotting.scatter_matrix(
    X_train, figsize=(10, 10), c=y_train, alpha=0.9
)
plt.show()

```

> 설명: 각 특성 간의 산점도를 통해 데이터 분포와 클래스 간 구분 가능성을 시각적으로 확인한다.
> 

---

## 🔧 **6. KNN 모델 생성 및 학습**

```python

iris_knn_model = KNeighborsClassifier(n_neighbors=11)
iris_knn_model.fit(X_train, y_train)

```

> 설명: K=11로 KNN 분류기를 생성하고, 훈련 데이터를 이용해 학습시킨다.
> 

---

## 🔮 **7. 예측 및 정확도 측정**

```python

pre = iris_knn_model.predict(X_test)
accuracy_score(y_test, pre)

```

> 설명: 테스트 데이터를 기반으로 품종을 예측하고, 실제 정답과 비교하여 정확도를 계산한다.
> 

---

## 🔁 **8. 최적의 K 찾기 (하이퍼파라미터 튜닝)**

```python

train_acc = []
test_acc = []

for k in range(1, 81):
    model = KNeighborsClassifier(n_neighbors=k)
    model.fit(X_train, y_train)

    train_pred = model.predict(X_train)
    test_pred = model.predict(X_test)

    train_acc.append(accuracy_score(y_train, train_pred))
    test_acc.append(accuracy_score(y_test, test_pred))

```

> 설명: 1부터 80까지 다양한 K 값에 대해 모델을 학습시키고, 훈련 및 테스트 정확도를 각각 저장한다.
> 

---

## 📈 **9. 정확도 시각화**

```python

plt.figure(figsize=(15, 5))
plt.plot(train_acc, label='train acc')
plt.plot(test_acc, label='test acc')
plt.xlabel('K')
plt.ylabel('Accuracy')
plt.xticks(range(1, 81, 2))
plt.grid()
plt.legend()
plt.show()

```

> 설명: K값에 따른 정확도 변화를 시각화하여 과적합/과소적합 여부를 파악하고, 적절한 K값을 선택한다.
> 

---

## ✅ **10. 최적 K로 모델 재학습 및 예측**

```python

knn_model_best = KNeighborsClassifier(n_neighbors=9)
knn_model_best.fit(X_train, y_train)

pre_final = knn_model_best.predict(X_test)
accuracy_score(y_test, pre_final)

```

> 설명: 시각화 결과를 바탕으로 적절한 K값(여기서는 9)을 선택하여 모델을 다시 학습시키고 성능을 확인한다.
> 

---

## 🧪 **11. 새 데이터 예측**

```python

knn_model_best.predict([[4.3, 3.0, 2.8, 0.5]])
iris_data.target_names

```

> 설명: 새로운 데이터 입력값을 바탕으로 품종을 예측하며, 예측된 결과의 품종명을 확인한다.
> 

---

### 📌 **최종 요약**

- Iris 데이터셋을 활용해 KNN 분류기를 구현하였다.
- `train_test_split`을 통해 데이터를 나누고, 다양한 K값을 실험하여 최적값을 찾았다.
- 최적의 `K=9`로 모델을 재학습시켜 좋은 성능을 보였으며, 새 데이터를 예측할 수 있도록 구현하였다.