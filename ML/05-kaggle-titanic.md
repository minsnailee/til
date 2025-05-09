타이타닉 생존자 예측 실습 코드

---

## 🎯 **1. 문제 정의**

- **목표**: 타이타닉 데이터셋을 활용하여 탑승객의 생존 여부를 예측하는 모델을 만든다.
- **문제 유형**: 지도학습 → 분류 → **이진분류 문제**
- **예측 대상(Label)**: `Survived` 컬럼 (0 = 사망, 1 = 생존)

---

## 📥 **2. 데이터 수집**

- **사용 데이터**: Kaggle 타이타닉 데이터셋
    - `train.csv` : 모델 학습에 사용
    - `test.csv` : 생존 여부 예측에 사용
- **데이터 불러오기**

```python

import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

train = pd.read_csv('./data/train.csv', index_col='PassengerId')
test = pd.read_csv('./data/test.csv', index_col='PassengerId')

```

---

## 🔍 **3. 데이터 확인 및 결측치 분석**

- **형식 및 결측치 확인**

```python

train.info()
test.info()

```

- `test.csv`에는 `Survived` 컬럼이 없어 정확도 측정은 불가하며, 교차 검증을 사용한다.

---

## 🧹 **4. 데이터 전처리**

### 4.1 **Embarked 결측치 처리 (train.csv)**

- **결측치 2개 확인**
- **최빈값 'S'로 대체**

```python

train['Embarked'].fillna('S', inplace=True)

```

### 4.2 **Fare 결측치 처리 (test.csv)**

- `Fare`는 수치형 → 평균이나 중앙값으로 대체
- `Pclass`와 `Sex`를 기준으로 중앙값 계산
- 결측치 하나에 대해 `Pclass=3, Sex=male`의 중앙값 `7.925`로 대체

```python

test['Fare'].fillna(7.925, inplace=True)

```

### 4.3 **Age 결측치 처리 (train/test 공통)**

- `Age`는 많은 결측치 존재 → 일일이 채우기 어려움
- `Pclass`, `Sex`에 따른 평균값으로 대체
- **평균값 테이블 생성**

```python

psa_mean = train[['Age','Pclass','Sex']].groupby(['Pclass','Sex']).mean()

```

- **함수 정의하여 결측치 채우기**

```python

def input_age(df):
    if np.isnan(df['Age']):
        return psa_mean.loc[(df['Pclass'], df['Sex'])].values[0]
    else:
        return df['Age']

train['Age'] = train.apply(input_age, axis=1)
test['Age'] = test.apply(input_age, axis=1)

```

### 4.4 **Cabin → Deck 전처리**

- `Cabin` 결측치 많음 → 삭제하거나 새로운 변수로 활용
- **결측치는 'M'으로 채우고**, 문자열의 첫 글자만 추출하여 **`Deck` 컬럼 생성**

```python

train['Deck'] = train['Cabin'].fillna('M').str[0]
test['Deck'] = test['Cabin'].fillna('M').str[0]

```

- **`Cabin` 컬럼 제거**

```python

train.drop('Cabin', axis=1, inplace=True)

```

---

## ✅ 정리 요약

| 전처리 항목 | 처리 방식 |
| --- | --- |
| `Embarked` | 최빈값 'S'로 채움 |
| `Fare` (test) | 3등석 남성의 중앙값 7.925로 채움 |
| `Age` | `Pclass`, `Sex` 기준 평균값으로 채움 (함수 사용) |
| `Cabin` | 결측치는 'M'으로 대체 후 첫 글자만 추출하여 `Deck` 생성, 기존 `Cabin`은 삭제 |