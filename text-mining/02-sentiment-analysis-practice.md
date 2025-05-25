## 📌 1. 텍스트 데이터 수치화 (Tokenizing & Vectorizing)

텍스트는 기계학습 모델이 직접 처리할 수 없기 때문에 숫자 형태의 벡터로 변환하는 과정이 필요하다. 이를 텍스트의 수치화라고 한다. 주로 다음과 같은 방식들이 사용된다.

### ① **Tokenizer (Keras) - 정수 인코딩**

```python

from tensorflow.keras.preprocessing.text import Tokenizer
from tensorflow.keras.preprocessing.sequence import pad_sequences

tokenizer = Tokenizer(num_words=10)
tokenizer.fit_on_texts([text])
word_index = tokenizer.word_index
sequences = tokenizer.texts_to_sequences([text])
padded = pad_sequences(sequences, maxlen=20)

```

- **정수 인코딩(Integer Encoding)** 은 텍스트 내에서 자주 등장하는 단어부터 순서대로 인덱스를 부여하는 방식이다.

- num_words는 자주 등장하는 상위 n개의 단어만 사용할 수 있도록 제한하는 파라미터이다.

- pad_sequences는 길이가 서로 다른 문장을 동일한 길이로 맞추기 위해 사용하는 함수로, 딥러닝 모델에 입력으로 넣을 수 있게 도와준다.

📌 주로 RNN, CNN 같은 딥러닝 모델에 입력값으로 사용된다.

- **빈도수 기준 정렬** → **인덱스 부여**
- **패딩**으로 길이 맞춤

---

### ② **CountVectorizer (sklearn) - BoW (Bag of Words)**

```python

from sklearn.feature_extraction.text import CountVectorizer

cv = CountVectorizer()
cv_fit = cv.fit_transform(corpus).toarray()
print(cv.vocabulary_)
print(cv_fit)

```

- Bag of Words (BoW) 방식은 단어의 등장 빈도만을 사용하여 문장을 벡터로 표현하는 방법이다.

- 문장의 단어 순서는 고려하지 않으며, 단어가 몇 번 등장했는지만 반영된다.

- BoW는 각 문장을 고정된 길이의 벡터로 표현하므로 별도의 패딩이 필요하지 않다.

📌 단점은 단어 간 관계나 중요도를 반영하지 못한다는 것이다.


- **단어 순서 무시** / **등장 빈도수만 사용**
- 패딩 불필요
  

---

### ③ **TfidfVectorizer (sklearn)**

```python

from sklearn.feature_extraction.text import TfidfVectorizer

tv = TfidfVectorizer()
tv_fit = tv.fit_transform(corpus).toarray()
print(tv.vocabulary_)
print(tv_fit)

```

- **단어의 중요도 반영 (TF-IDF)**
- **빈도만 고려하는 BoW의 한계 보완**


- **TF-IDF (Term Frequency - Inverse Document Frequency)** 는 단어의 빈도뿐만 아니라, 다른 문서들에서 얼마나 자주 등장하는지를 반영하여 단어의 중요도를 계산한다.

- 흔하게 등장하는 단어는 중요도가 낮게, 특정 문서에만 등장하는 단어는 높게 평가된다.

- BoW 방식의 단점을 보완할 수 있다.

📌 문서 간 유사도 측정이나 정보 검색 시스템에 적합하다.


---

## 📌 2. 텍스트 분류 (감정 분석 모델 만들기)

감정 분석은 텍스트 데이터를 기반으로 문장의 긍정/부정 여부를 분류하는 작업이다.

### 데이터 준비

```python

corpus = [ ... ]  # 리뷰 데이터
labels = [1, 1, 1, 0, ...]  # 긍/부정 라벨

```

---

### BoW 벡터화 후 모델 학습

```python

cv = CountVectorizer()
X = cv.fit_transform(corpus).toarray()

from sklearn.linear_model import LogisticRegression
model = LogisticRegression(C=0.01)
model.fit(X, labels)
model.score(X, labels)  # 예: 1.0

```

- BoW 또는 TF-IDF 방식으로 벡터화된 데이터를 바탕으로 로지스틱 회귀 모델을 학습시킨다.

- 예측 시에도 동일한 전처리 및 벡터화 과정을 적용해야 한다.

📌 간단한 텍스트 분류 작업에 적합한 접근법이다.

---

### 예측하기

```python

X_new1 = ["제품의 너무 나빠요"]
X_new2 = ["디자인이 좋아요"]
en1 = cv.transform(X_new1).toarray()
en2 = cv.transform(X_new2).toarray()
print(model.predict(en1))  # [0]
print(model.predict(en2))  # [1]

```

---

## 📌 3. FastText 임베딩 사용하기

FastText는 Facebook에서 개발한 워드 임베딩 기법이다. 단어를 부분 단어(subword) 단위까지 쪼개어 학습하기 때문에, 오타나 신조어에도 강한 특성을 보인다.

### ① 형태소 분석 후 토큰화

```python

from konlpy.tag import Okt
okt = Okt()
token_texts = [okt.morphs(text) for text in corpus]

```
- 한국어는 조사, 어미 등으로 인해 형태소 단위의 분석이 중요하므로 Okt와 같은 형태소 분석기를 이용하여 단어를 분리한다.

---

### ② FastText 임베딩 학습

```python

from gensim.models import FastText

fasttext_model = FastText(sentences=token_texts, vector_size=100, window=5, min_count=1, sg=1)
fasttext_model.wv.most_similar("좋아요")

```

- 각 단어를 고정된 차원의 벡터로 학습하며, 의미적 유사성을 파악할 수 있게 된다.

- most_similar("좋아요") 등의 함수를 통해 유사 단어를 탐색할 수 있다.

---

### ③ 문장 벡터 생성 (평균 벡터)

```python

import numpy as np

def get_sentence_vector(tokens):
    vectors = [fasttext_model.wv[word] for word in tokens if word in fasttext_model.wv]
    return np.mean(vectors, axis=0) if vectors else np.zeros(fasttext_model.vector_size)

X = np.array([get_sentence_vector(t) for t in token_texts])
y = np.array(labels)

```
- 문장의 모든 단어 벡터를 평균 내어 하나의 고정된 문장 벡터로 변환한다.

- 이를 통해 벡터 크기를 일정하게 유지할 수 있다.
---

### ④ 학습 및 예측

```python

model = LogisticRegression(C=0.01)
model.fit(X, y)
model.score(X, y)  # 예: 0.916

# 예측용 문장
t1 = okt.morphs("제품의 너무 나빠요")
t2 = okt.morphs("디자인이 좋아요")
v1 = get_sentence_vector(t1)
v2 = get_sentence_vector(t2)
print(model.predict([v1]))  # [0]
print(model.predict([v2]))  # [1]

```
- 위에서 만든 문장 벡터를 입력으로 하여 로지스틱 회귀 모델을 학습한다. (로지스틱 회귀는 회귀라는 이름이지만 사실상 분류 모델)

- 예측 시에도 동일한 방식으로 문장을 벡터화한 뒤, 모델에 입력한다.

📌 의미 정보를 반영한 벡터를 사용함으로써 성능 향상이 가능하다.

---

## ✅ 요약 비교

| 방법 | 특징 | 순서 고려 | 패딩 필요 | 임베딩 |
| --- | --- | --- | --- | --- |
| `Tokenizer` | 단어 빈도 기반 인덱싱 | 일부 | 필요 | ❌ |
| `CountVectorizer` | BoW 방식, 단어 빈도 | ❌ | ❌ | ❌ |
| `TfidfVectorizer` | TF-IDF 가중치 | ❌ | ❌ | ❌ |
| `FastText` | 임베딩 학습 + 유사도 분석 가능 | ✅ | ❌ | ✅ |
