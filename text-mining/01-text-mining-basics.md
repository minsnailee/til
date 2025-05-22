# ✅ 데이터 마이닝 기초 정리

## 1. 📚 주요 라이브러리

| 라이브러리 | 설명 |
| --- | --- |
| `nltk` | 영문 텍스트 처리용 라이브러리 (토큰화, 불용어, 어간 추출 등) |
| `konlpy` | 한글 자연어 처리를 위한 라이브러리 |
| `re` | 정규표현식 처리 (정규화용) |
| `tensorflow.keras.preprocessing.text` | 단어 인코딩, 패딩 처리 등 |

---

## 2. 🛠️ 자연어 처리 (Text Preprocessing) 단계별 정리

### 🔹 1. 전처리 (Preprocessing)

- 오타 수정, 결측치 제거
- 이상치(문자 데이터는 거의 없음) 제외

---

### 🔹 2. 정규화 (Normalization)

- 정규표현식을 이용해 **불필요한 기호/문자 제거**

```python
re.compile("[^가-힝A-Za-z ]")

```

---

### 🔹 3. 토큰화 (Tokenization)

- 문장을 **단어(또는 형태소)** 단위로 분리
- `nltk.word_tokenize(text)` → 영문
- `Okt().morphs(text)` → 한글 (형태소 단위로 분리)

---

### 🔹 4. 품사 태깅 / 명사 추출

- `Okt().pos(text)` : 품사 정보까지 함께 추출
- `Okt().nouns(text)` : 명사만 추출

---

### 🔹 5. 표제어 추출 (Lemmatization)

- `WordNetLemmatizer()` 사용
- **문법적으로 올바른 형태**로 변환
    
    예: *leaves → leaf*, *lives → life*
    

---

### 🔹 6. 어간 추출 (Stemming)

- `PorterStemmer()` 사용
- **단순히 접미어 제거로 어간 추출** (정확성보다 속도 중심)
    
    예: *children → children*, *dies → die*
    

---

### 🔹 7. 불용어 제거 (Stopword Removal)

- 의미 없는 단어 제거 (ex: 는, 을, 그리고, etc.)

```python
result = [w for w in result if not w in stop_words]

```

---

## 3. 🔢 인코딩 (Encoding)

- 컴퓨터는 문자를 이해하지 못하므로, 숫자로 변환하는 과정

### 🔸 Tokenizer

```python
from tensorflow.keras.preprocessing.text import Tokenizer

tokenizer = Tokenizer(num_words=10)
tokenizer.fit_on_texts([result])

```

- 단어 빈도 기준 인덱싱
- `texts_to_sequences()` 로 숫자 인코딩

---

### 🔸 패딩 (Padding)

```python
pad_sequences(en, maxlen=5, truncating="pre")

```

- 문장의 길이를 **동일하게 맞추기 위해 0을 앞/뒤로 채움**
- RNN/LSTM 등에서는 입력 길이 통일이 필수

---

# 💬 추가적으로 알면 좋은 것들

## ✅ 한글 형태소 분석기 종류

- `Okt` : 트위터에서 만든 오픈소스, 가장 많이 사용
- `Mecab` : 속도 빠름, 정확도 높음 (설치 복잡)
- `Komoran`, `Hannanum` 등도 있음

---

## ✅ 벡터화 방식 (Encoding 심화)

| 방식 | 설명 |
| --- | --- |
| One-hot Encoding | 단어마다 고유 번호를 부여하고, 해당 인덱스만 1로 표시 |
| CountVectorizer | 단어 등장 횟수를 세서 벡터화 |
| TfidfVectorizer | 단어의 **중요도** 반영 (문서 빈도 기반) |
| Word2Vec, FastText, BERT | **단어 의미 간 유사성**을 벡터로 표현 (임베딩 방식) |

---

## ✅ 텍스트 데이터 증강이 중요한 이유

- 데이터가 부족할수록 **모델이 일반화에 실패**할 수 있음
- 증강을 통해 **문장 다양성** 확보

---
