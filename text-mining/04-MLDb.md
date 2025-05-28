# ✅ IMDb 영화리뷰 감정분석 프로젝트 정리

---

## 🗂️ 1. 데이터 로딩 및 전처리

```python

# 구글 드라이브 마운트 및 작업 디렉토리 이동
from google.colab import drive
drive.mount('/content/drive')

%cd /content/drive/MyDrive/Colab Notebooks/study

```

```python

# pandas를 이용해 데이터 불러오기
import pandas as pd
imdb = pd.read_csv('./data/imdb_master.csv', encoding="latin-1")

# review와 label 컬럼만 추출
imdb2 = imdb[['review', 'label']]

```

### 🔍 라벨 분포 확인

```python

imdb2["label"].value_counts()

```

- `unsup`: 라벨 없는 데이터 (50,000개)
- `neg`, `pos`: 각각 25,000개씩

### ❌ 라벨 없는 데이터 제거

```python

imdb3 = imdb2[imdb2['label'] != 'unsup']

```

### 🧼 텍스트 정제 (영문자만 남기기)

```python

import nltk
imdb3['review'] = imdb3['review'].str.replace("[^a-zA-Z]", "", regex=True)

```

### ❗빈 문자열 제거

```python

imdb3 = imdb3[imdb3['review'] != ""]

```

---

## ✂️ 2. 데이터 샘플링 (훈련 시간 단축 목적)

```python

imdb3 = imdb3.sample(n=5000)  # 5000개 샘플만 사용

```

---

## 🔀 3. 훈련 / 검증 데이터 분리

```python

from sklearn.model_selection import train_test_split

X_train, X_val, y_train, y_val = train_test_split(
    imdb3["review"], imdb3["label"],
    test_size=0.25,
    random_state=42
)

```

---

## 🔠 4. 벡터화 (토큰화 및 인코딩)

### ✅ CountVectorizer

```python

from sklearn.feature_extraction.text import CountVectorizer

cv = CountVectorizer(max_features=10000)
cv.fit(X_train)

X_train_cv = cv.transform(X_train).toarray()
X_val_cv = cv.transform(X_val).toarray()

```

### ✅ TfidfVectorizer

```python

from sklearn.feature_extraction.text import TfidfVectorizer

tv = TfidfVectorizer(max_features=10000)
tv.fit(X_train)

X_train_tv = tv.transform(X_train).toarray()
X_val_tv = tv.transform(X_val).toarray()

```

---

## 🤖 5. 머신러닝 모델 학습

### 📌 로지스틱 회귀 - CountVectorizer

```python

from sklearn.linear_model import LogisticRegression

lr_cv = LogisticRegression(C=0.1, max_iter=15000)
lr_cv.fit(X_train_cv, y_train)

print("훈련 정확도 :", lr_cv.score(X_train_cv, y_train))
print("검증 정확도 :", lr_cv.score(X_val_cv, y_val))

```

### 📌 로지스틱 회귀 - TF-IDF

```python

lr_tv = LogisticRegression(C=10, max_iter=15000)
lr_tv.fit(X_train_tv, y_train)

print("훈련 정확도 :", lr_tv.score(X_train_tv, y_train))
print("검증 정확도 :", lr_tv.score(X_val_tv, y_val))

```

---

## 📈 6. 예측 결과 확인

```python

pred_tv = lr_tv.predict(X_val_tv)

for i in range(10):
    print(pred_tv[i], "==>", y_val.iloc[i])

```

---

## 🧠 7. Word Embedding (FastText)

```python

from gensim.models import FastText
from nltk.tokenize import word_tokenize

# 토큰화
def tokenize_texts(texts):
  return [word_tokenize(text) for text in texts]

X_train_token = tokenize_texts(X_train)
X_val_token = tokenize_texts(X_val)

# FastText 훈련
fasttext_model = FastText(
    sentences=X_train_token,
    vector_size=100,
    window=3,
    min_count=1,
    sg=1
)

```

### 📌 문장을 벡터로 바꾸기

```python

import numpy as np

def get_sentence_vector(tokens, model):
  vectors = [model.wv[word] for word in tokens if word in model.wv]
  return np.mean(vectors, axis=0) if vectors else np.zeros(model.vector_size)

X_train_vec = [get_sentence_vector(tokens, fasttext_model) for tokens in X_train_token]
X_val_vec = [get_sentence_vector(tokens, fasttext_model) for tokens in X_val_token]

```

---

## 🤖 8. FastText 벡터 + 로지스틱 회귀

```python

lr_fasttext = LogisticRegression(C=10, max_iter=15000)
lr_fasttext.fit(X_train_vec, y_train)

print("훈련 정확도 :", lr_fasttext.score(X_train_vec, y_train))
print("검증 정확도 :", lr_fasttext.score(X_val_vec, y_val))

```

---

## 🌲 9. FastText 벡터 + 랜덤 포레스트

```python

from sklearn.ensemble import RandomForestClassifier

rfc_fasttext = RandomForestClassifier(random_state=10, n_estimators=100)
rfc_fasttext.fit(X_train_vec, y_train)

print("훈련 점수 :", rfc_fasttext.score(X_train_vec, y_train))
print("검증 점수 :", rfc_fasttext.score(X_val_vec, y_val))

```

---

## 🔍 예측 결과 확인 (FastText + 로지스틱 회귀)

```python

pred_fasttext = lr_fasttext.predict(X_val_vec)

for i in range(10):
    print(pred_fasttext[i], "==>", y_val.iloc[i])

```

---

# ✅ 마무리 요약

| 방식 | 훈련 정확도 | 검증 정확도 |
| --- | --- | --- |
| CountVectorizer + LR | 0.9917 | 0.868 |
| TF-IDF + LR | 0.9978 | 0.8856 |
| FastText + LR | 0.8504 | 0.8491 |
| FastText + RF | 1.0000 | 0.8073 |