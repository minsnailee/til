버섯 데이터를 이용해 **Decision Tree** 분류 모델을 학습하고 평가한 실습 내용을 코드와 함께 정리한 것이다. 전반적으로 데이터 전처리, 모델 학습, 평가, 시각화 과정을 포함하고 있다.

# 🍄 버섯 데이터로 Decision Tree 분류 실습 정리

---

## 1. 📦 라이브러리 불러오기

```python

import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

from sklearn.model_selection import train_test_split
from sklearn.tree import DecisionTreeClassifier
from sklearn.metrics import accuracy_score
from sklearn.model_selection import cross_val_score

```

- **NumPy, Pandas**: 데이터 조작 및 배열 처리
- **Matplotlib**: 데이터 시각화
- **Scikit-learn**: 머신러닝 도구 (모델링, 평가 등)

---

## 2. 📂 데이터 불러오기 및 구조 확인

```python

data = pd.read_csv('./data/mushroom.csv')
data.head()
data.info()

```

- CSV 파일을 불러오고 데이터가 어떻게 구성되어 있는지 확인한다.
- `poisonous` 컬럼: 버섯이 독이 있는지 여부 → **목표 변수(label)**
- 나머지 컬럼: 버섯의 다양한 특징들 → **입력 변수(feature)**

---

## 3. 🎯 특성과 라벨 분리

```python

X = data.loc[:, 'cap-shape':]
y = data['poisonous']

```

- `X`: 모델이 학습할 수 있는 **입력 데이터**
- `y`: 정답 레이블. 여기서는 **독(1)/식용(0) 이진 분류**

```python

print(X.shape)
print(y.value_counts())

```

- 샘플의 수, 레이블 분포를 확인하여 **데이터 불균형 여부**를 확인한다.

---

## 4. 🧼 범주형 데이터 인코딩 (One-Hot Encoding)

```python

one_hot_X = pd.get_dummies(X)

```

- 머신러닝 알고리즘은 **숫자 형태의 데이터**를 요구한다.
- 범주형 데이터를 숫자로 바꾸기 위해 **One-Hot 인코딩**을 사용한다.
    - 예: `cap-shape` = {x, b, c} → cap-shape_x, cap-shape_b, cap-shape_c 컬럼 생성
- `pd.get_dummies()`는 범주형 데이터를 자동으로 One-Hot 인코딩 해준다.

```python

one_hot_X.shape

```

- 인코딩 후 feature 개수가 어떻게 늘어났는지 확인한다.

---

## 5. ✂️ 데이터 분할 (학습/테스트)

```python

X_train, X_test, y_train, y_test = train_test_split(
    one_hot_X,
    y,
    test_size=0.3,
    random_state=3
)

```

- 데이터를 **학습용(70%)과 테스트용(30%)으로 분할**
- 모델의 일반화 성능을 확인하기 위해 필수적인 작업이다.
- `random_state`: 재현성을 위해 랜덤 분할을 고정

---

## 6. 🌳 기본 Decision Tree 모델 학습

```python

tree_model1 = DecisionTreeClassifier()
tree_model1.fit(X_train, y_train)

```

- **하이퍼파라미터 조정 없이 기본 설정**으로 모델 생성
- Decision Tree는 데이터를 반복적으로 분할하면서 트리를 구성한다.
    - 각 노드는 예/아니오(Yes/No) 질문처럼 작동
    - 최종 리프 노드는 **예측 결과(독/식용)**를 반환

---

## 7. 📈 예측 및 정확도 평가

```python

pre1 = tree_model1.predict(X_test)
accuracy_score(y_test, pre1)

```

- 테스트 데이터를 이용하여 **예측값(pre1)**을 생성하고,
- 정답(y_test)과 비교하여 **정확도**를 계산한다.

---

## 8. 🔁 교차검증(Cross Validation)

```python

result1 = cross_val_score(tree_model1, X_train, y_train, cv=5)
result1

```

- **교차검증**은 모델이 학습데이터에 너무 의존하는 것을 막고, 일반화 성능을 평가하기 위한 방법이다.
- 5개로 데이터를 나누고, 4개로 학습하고 1개로 평가하는 과정을 5번 반복한다.

---

## 9. 🧠 특성 중요도 확인

```python

fi = tree_model1.feature_importances_
fi_df = pd.DataFrame(fi, index=X_train.columns, columns=['important'])
fi_df_sorted = fi_df.sort_values(by='important', ascending=False)
fi_df_sorted.head(5)

```

- 트리가 예측할 때 **어떤 특성을 얼마나 사용했는지**를 수치화한 것
- 중요도가 높은 특성은 트리의 상단에서 자주 사용된다.
- 예: `odor`나 `gill-color` 등이 중요할 수 있다.

---

## 10. 🌲 트리 시각화

```python

from sklearn.tree import export_graphviz
import graphviz

export_graphviz(
    tree_model1,
    out_file = './tree.dot',
    class_names = ['독','식'],
    feature_names = X_train.columns,
    filled = True,
    impurity = False
)

with open('tree.dot', encoding = 'UTF-8') as f:
    dot_graph = f.read()

display(graphviz.Source(dot_graph))

```

- `export_graphviz`: 트리를 시각화 가능한 `.dot` 형식으로 저장
- `graphviz`: dot 파일을 실제 트리 형태로 출력
- **트리 구조**를 통해 모델이 어떻게 예/아니오 질문을 하며 분류하는지 시각적으로 확인 가능

---

## 11. ✂️ 사전 가지치기 (max_depth=3)

```python

tree_model2 = DecisionTreeClassifier(max_depth=3)
tree_model2.fit(X_train, y_train)

```

- `max_depth`: 트리의 최대 깊이를 제한 → **과대적합 방지**
    - 깊이가 깊을수록 훈련 데이터에 더 정확하게 맞추지만, **새로운 데이터에는 오히려 성능이 떨어질 수 있다.**
- *사전 가지치기(pruning)**는 이런 과대적합을 막는 중요한 기법이다.

```python

pre2 = tree_model2.predict(X_test)
accuracy_score(y_test, pre2)

```

→ 정확도 확인: depth=3인 모델도 일반적으로 높은 정확도를 유지하면서 과대적합을 줄일 수 있다.

---

## 12. 🌳 가지치기된 트리 시각화

```python

export_graphviz(
    tree_model2,
    out_file = './tree2.dot',
    class_names = ['독','식'],
    feature_names = X_train.columns,
    filled = True,
    impurity = False
)

with open('tree2.dot', encoding = 'UTF-8') as f:
    dot_graph = f.read()

display(graphviz.Source(dot_graph))

```

- 간단한 트리 구조를 통해 어떻게 예측이 이루어지는지 쉽게 확인할 수 있다.

---

## ✅ 실습 요약

| 항목 | 설명 |
| --- | --- |
| 데이터 | 버섯의 다양한 특징 (모양, 색깔, 냄새 등) |
| 목표 | 독버섯과 식용버섯 이진 분류 |
| 모델 | `DecisionTreeClassifier` |
| 전처리 | One-Hot 인코딩, 학습/테스트 분할 |
| 가지치기 | `max_depth=3`으로 사전 가지치기 적용 |
| 평가 지표 | 정확도, 교차검증, 특성 중요도 |
| 시각화 | 트리 구조를 `.dot`파일로 시각화 |
| 주요 교훈 | **단순하지만 과대적합에 취약한 모델**, 따라서 하이퍼파라미터 튜닝이 중요 |