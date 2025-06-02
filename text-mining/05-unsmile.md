- 스마일게이트 한국어 혐오표현 데이터셋을 이용한 텍스트 분류 실습
- 빈도분석, GBM 계열 모델

---

## ✅ 1. 데이터 로드 및 확인

```python
import pandas as pd

train = pd.read_csv('./data/unsmile_train_v1.0.tsv', delimiter="\t")
valid = pd.read_csv('./data/unsmile_valid_v1.0.tsv', delimiter="\t")

train.info()
valid.info()

```

📌 *훈련 데이터는 15,005개, 검증 데이터는 3,737개이며 결측치는 없음*

---

## ✅ 2. 특정 혐오표현 단어 빈도 분석

```python
from collections import Counter

text_train = train[train["지역"]==1]["문장"]
tokens = [t for doc in text_train for t in doc.split(" ")]
counter = Counter(tokens)

print(counter.most_common(20))

```

📌 *‘지역’ 관련 혐오 문장에서 가장 많이 사용된 단어들을 확인*

---

## ✅ 3. 정제: 불필요한 문자 제거 및 단어 필터링

```python
import re

p = re.compile("[^가-힣\s]")

def clean_texts(texts):
    cleaned = []
    for doc in texts:
        temp = []
        for token in doc.split(" "):
            if len(token) < 2: continue
            if p.search(token): continue
            temp.append(token)
        cleaned.append(" ".join(temp))
    return cleaned

train_clean = clean_texts(train["문장"])
valid_clean = clean_texts(valid["문장"])

```

📌 *한 글자 또는 특수문자, 숫자가 포함된 단어 제거*

---

## ✅ 4. 띄어쓰기 교정 (Kiwi)

```python
!pip install -q kiwipiepy konlpy
from kiwipiepy import Kiwi

kiwi = Kiwi()

train_clean2 = list(kiwi.space(train_clean))
valid_clean2 = list(kiwi.space(valid_clean))

```

📌 *올바른 띄어쓰기 교정*

---

## ✅ 5. 불용어 처리 및 형태소 분석

```python
from kiwipiepy.utils import Stopwords
from konlpy.tag import Okt

stopwords = Stopwords()
stopwords.add(("있", "VA"))  # 사용자 정의 불용어 추가
okt = Okt()

def clean_and_tokenize(text):
    tokens = kiwi.tokenize(text, stopwords=stopwords)
    clean_text = " ".join([token.form for token in tokens])
    morphs = okt.morphs(clean_text)
    return " ".join(morphs)

train_clean3 = [clean_and_tokenize(doc) for doc in train_clean2]
valid_clean3 = [clean_and_tokenize(doc) for doc in valid_clean2]

```

📌 *불용어 제거 후 형태소 단위로 토큰화*

---

## ✅ 6. 벡터화 (BoW, TF-IDF)

```python
from sklearn.feature_extraction.text import CountVectorizer, TfidfVectorizer

cv = CountVectorizer(ngram_range=(1,2), max_df=0.7, min_df=10)
tv = TfidfVectorizer(ngram_range=(1,2), max_df=0.7, min_df=10)

X_train_cv = cv.fit_transform(train_clean3)
X_valid_cv = cv.transform(valid_clean3)

X_train_tv = tv.fit_transform(train_clean3)
X_valid_tv = tv.transform(valid_clean3)

```

📌 *문장을 수치화. unigram, bigram 포함 / 너무 자주/드물게 등장하는 단어는 제외*

---

## ✅ 7. 라벨 데이터 생성

```python
train["혐오표현"] = train.loc[:, "여성/가족":"기타 혐오"].sum(axis=1)
valid["혐오표현"] = valid.loc[:, "여성/가족":"기타 혐오"].sum(axis=1)

def label(row):
    if row["혐오표현"] > 0:
        return 0  # 혐오
    elif row["악플/욕설"] > 0:
        return 1  # 악플
    else:
        return 2  # 클린

new_train = train[["문장", "혐오표현", "악플/욕설", "clean"]]
new_valid = valid[["문장", "혐오표현", "악플/욕설", "clean"]]

y_train = new_train.apply(label, axis=1)
y_valid = new_valid.apply(label, axis=1)

```

📌 *3개 라벨: 0=혐오, 1=욕설, 2=클린*

---

## ✅ 8. 학습 및 교차검증

```python
from sklearn.linear_model import LogisticRegression
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import cross_val_score

lr = LogisticRegression(C=10, max_iter=1000)
rf = RandomForestClassifier(random_state=42, n_estimators=100)

# Logistic Regression + BoW / TF-IDF
print(cross_val_score(lr, X_train_cv, y_train, cv=5).mean())
print(cross_val_score(lr, X_train_tv, y_train, cv=5).mean())

# Random Forest + BoW / TF-IDF
print(cross_val_score(rf, X_train_cv, y_train, cv=5).mean())
print(cross_val_score(rf, X_train_tv, y_train, cv=5).mean())

```

📌 *성능 일반화를 위한 교차검증 진행*

---

## ✅ 9. GBM 계열 모델 적용 (Gradient Boosting)

```python
from sklearn.ensemble import GradientBoostingClassifier

gbm = GradientBoostingClassifier(random_state=42)

score_gbm_cv = cross_val_score(gbm, X_train_cv, y_train, cv=5)
score_gbm_tv = cross_val_score(gbm, X_train_tv, y_train, cv=5)

print(score_gbm_cv.mean())
print(score_gbm_tv.mean())

```

📌 *Boosting 방식으로 하나의 모델이 반복적으로 학습*

---

## ✅ 향후 할 수 있는 것들

- `XGBoost`, `LightGBM`도 유사하게 적용 가능
- `GridSearchCV`로 하이퍼파라미터 튜닝
- `confusion matrix`, `classification report`로 모델 성능 세부 평가 가능

---

- **한국어 자연어 처리 파이프라인** 전반을 경험
- 실제 텍스트 분류 프로젝트의 좋은 기초 구조
- 추가적인 시각화, 모델 앙상블, 테스트셋 평가 등도 이어서 적용 가능