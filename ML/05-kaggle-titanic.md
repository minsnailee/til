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

---

## 🔍 4. EDA (탐색적 데이터 분석)

데이터의 분포와 특징을 시각화해서 인사이트를 얻는 과정입니다.

### 🎯 목표

- 각 변수들이 생존(`Survived`)에 어떤 영향을 미쳤는지 확인
- 이상치(outlier) 탐색

### 📊 시각화 예시

```python

import seaborn as sns

# 성별 생존률
sns.countplot(data=train, x='Sex', hue='Survived')
plt.title("Sex vs Survived")
plt.show()

# Pclass 생존률
sns.countplot(data=train, x='Pclass', hue='Survived')
plt.title("Pclass vs Survived")
plt.show()

# Age 분포
sns.histplot(data=train, x='Age', hue='Survived', multiple='stack', bins=30)
plt.title("Age distribution by Survived")
plt.show()

# Fare 분포
sns.histplot(data=train, x='Fare', hue='Survived', multiple='stack', bins=30)
plt.title("Fare distribution by Survived")
plt.show()

# Deck별 생존률
sns.countplot(data=train, x='Deck', hue='Survived')
plt.title("Deck vs Survived")
plt.show()

```

---

## 🧪 5. 특성 공학 (Feature Engineering)

모델의 성능을 높이기 위한 중요한 과정입니다.

### 🧹 컬럼 정리

- 불필요한 컬럼 제거: `Name`, `Ticket`
- 범주형 데이터 변환: `Sex`, `Embarked`, `Deck` 등 → 수치형 변환 필요

```python

# Name, Ticket 제거
train.drop(['Name', 'Ticket'], axis=1, inplace=True)
test.drop(['Name', 'Ticket'], axis=1, inplace=True)

# Label Encoding
from sklearn.preprocessing import LabelEncoder

cols = ['Sex', 'Embarked', 'Deck']
le = LabelEncoder()

for col in cols:
    train[col] = le.fit_transform(train[col])
    test[col] = le.transform(test[col])  # 동일하게 변환해야 함

```

---

## 🧠 6. 머신러닝 모델링

### 🎯 목표

- 이진 분류 모델을 사용해 `Survived` 예측

### 💡 사용할 모델 예시

- `LogisticRegression`
- `RandomForestClassifier`
- `GradientBoostingClassifier`

### 예시 코드

```python

from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import accuracy_score

# 데이터 분리
X = train.drop('Survived', axis=1)
y = train['Survived']

X_train, X_val, y_train, y_val = train_test_split(X, y, test_size=0.2, random_state=42)

# 모델 학습
model = RandomForestClassifier(random_state=42)
model.fit(X_train, y_train)

# 예측
pred = model.predict(X_val)

# 정확도 평가
print("Validation Accuracy:", accuracy_score(y_val, pred))

```

---

## 📤 7. 예측 및 제출 파일 만들기

```python

# test 데이터에 대해 예측
test_pred = model.predict(test)

# submission 파일 생성
submission = pd.DataFrame({
    'PassengerId': test.index,
    'Survived': test_pred
})

submission.to_csv('submission.csv', index=False)

```

---

## ✅ 마무리 정리

| 단계 | 내용 |
| --- | --- |
| 문제 정의 | 타이타닉 생존자 분류 (이진 분류) |
| 데이터 수집 | Kaggle 데이터셋 (train/test.csv) |
| 전처리 | 결측치 채우기, Cabin → Deck, Age 채우기 등 |
| EDA | 분포 확인, 이상치 확인 |
| 특성공학 | 불필요한 컬럼 제거, 범주형 → 수치형 변환 |
| 모델링 | 랜덤 포레스트로 예측 및 평가 |
| 제출 | submission.csv 생성 |