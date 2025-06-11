## 🎵 Doc2Vec 기반 콘텐츠 기반 음악 추천 시스템 실습 정리

이 노트북은 `Doc2Vec`을 활용하여 가사 기반 음악 추천 시스템을 만드는 과정을 다룹니다. 각 단계마다 **설명과 코드**를 함께 정리했습니다.

---

### 📌 1. 데이터 불러오기

데이터는 연도별로 정리된 음악 가사 데이터입니다.

```python
import pandas as pd

# CSV 파일 불러오기
data = pd.read_csv("./data/lyrics_by_year_1964_2023.csv")
print(data.columns)
data.head()

```

---

### 📌 2. 결측치 제거 및 인덱스 재설정

가사(lyric) 컬럼에 결측치가 있는 행은 삭제합니다.

```python
data = data.dropna(subset=["lyric"]).reset_index(drop=True)

```

---

### 📌 3. 특수문자 제거

모델 학습에 불필요한 특수문자를 제거해 텍스트 전처리를 수행합니다.

```python
import re

pattern = r"[^a-zA-Z0-9가-힣\s\?\!\.]"
data["cleaned_lyric"] = [re.sub(pattern, "", lyric) for lyric in data["lyric"]]

```

---

### 📌 4. 형태소 분석 및 불용어 제거

한글 형태소 분석기는 `Okt`, 영어 단어와 띄어쓰기는 `Kiwi`를 사용합니다. 불용어도 함께 제거합니다.

```python
!pip install -q kiwipiepy konlpy

from konlpy.tag import Okt
from kiwipiepy import Kiwi
from kiwipiepy.utils import Stopwords

okt = Okt()
kiwi = Kiwi()
stopword = Stopwords()
stopword_list = [word for word, tag in stopword.stopwords]

def pos_tagging(text):
    text = kiwi.space(text)  # 띄어쓰기 보정
    pos_word = okt.pos(text, norm=True, stem=True)
    return [word for word, tag in pos_word if tag in ['Adjective', 'Adverb', 'Alpha', 'Exclamation', 'Noun', 'Number', 'Verb'] and word not in stopword_list]

```

**전체 데이터에 대해 토큰화 적용:**

```python
from tqdm import tqdm
data["tagged_lyric"] = [pos_tagging(text) for text in tqdm(data["cleaned_lyric"])]

```

---

### 📌 5. 결과 저장 및 불러오기 (선택)

형태소 분석 결과는 시간이 오래 걸릴 수 있어 파일로 저장해둡니다.

```python
import pickle

# 저장
with open("./data/tagged_lyric.pkl", "wb") as f:
    pickle.dump(data, f)

# 불러오기
with open("./data/tagged_lyric.pkl", "rb") as f:
    data = pickle.load(f)

```

---

### 📌 6. Doc2Vec 입력 데이터 준비

Doc2Vec에서는 각 문서에 고유 태그를 붙여 학습합니다.

```python
from gensim.models.doc2vec import TaggedDocument

tagged_corpus_list = [
    TaggedDocument(words=tokens, tags=[f"document_{i}"])
    for i, tokens in enumerate(data["tagged_lyric"])
]

```

---

### 📌 7. Doc2Vec 모델 학습

Doc2Vec에는 여러 파라미터가 있습니다. 여기서는 `PV-DM`(분산 메모리) 방식을 사용합니다.

```python
from gensim.models import Doc2Vec

model = Doc2Vec(
    vector_size=300,  # 임베딩 차원 수
    alpha=0.025,      # 초기 학습률
    min_alpha=0.001,  # 최소 학습률
    window=8,         # 문맥 창 크기
    min_count=2,      # 최소 등장 빈도
    dm=1,             # 1: PV-DM / 0: PV-DBOW
    workers=4         # 병렬 처리 수
)

model.build_vocab(tagged_corpus_list)
model.train(tagged_corpus_list, total_examples=model.corpus_count, epochs=5)

```

---

### 📌 8. 각 문서의 벡터 추출

학습이 완료된 후 각 문서(노래)의 벡터를 추출합니다.

```python
vector_list = [model.dv[f"document_{i}"] for i in range(len(data))]
data["doc2vec_vector"] = vector_list

```

---

### 📌 9. 유사도 기반 추천

특정 곡의 벡터와 다른 곡들과의 **코사인 유사도**를 비교해 추천합니다.

```python
from sklearn.metrics.pairwise import cosine_similarity
import numpy as np

target_index = 0  # 첫 번째 곡을 기준으로 예시
target_vector = data["doc2vec_vector"][target_index].reshape(1, -1)
all_vectors = np.stack(data["doc2vec_vector"].values)

similarities = cosine_similarity(target_vector, all_vectors)[0]

# 상위 5곡 출력 (자기 자신 제외)
top5_idx = similarities.argsort()[-6:-1][::-1]
data.iloc[top5_idx][["title", "singer", "year"]]

```

---

## ✅ 다음 확장 가능성

- **하이브리드 추천**: 가사 유사도 + 연도 유사도 + 가수 유사도 조합
- **Streamlit 웹 앱**으로 인터페이스 제공
- **가사 생성 모델**과 연계한 창작 도구로 발전