
## 📚 협업 필터링 실습 정리


### 1. 협업 필터링(Collaborative Filtering) 개요

- 사용자 행동(평점, 구매 등)을 기반으로 추천을 수행
- **비슷한 사용자/아이템** 간 유사성을 활용하여 추천
- 두 가지 유형:
    - **사용자 기반 CF**: 나와 비슷한 취향의 사용자가 좋아한 것을 추천
    - **아이템 기반 CF**: 내가 좋아한 아이템과 유사한 것을 추천 (정확도 ↑)

---

### 2. 데이터 로딩 및 전처리

### 🔹 데이터 구성

- `movies.csv`: 영화 정보 (movieId, title, genres)
- `ratings.csv`: 사용자 평점 정보 (userId, movieId, rating, timestamp)

### 🔹 전처리

```python
ratings = ratings.drop(columns="timestamp")
rating_movies = pd.merge(ratings, movies, on='movieId')
ratings_matrix = rating_movies.pivot_table(index='userId', columns='title', values='rating')
ratings_matrix.fillna(0, inplace=True)

```

- 결과: (610명, 9719개 영화)의 사용자-아이템 평점 행렬 완성

---

### 3. 영화 간 유사도 계산

### 🔹 아이템 기반 유사도: Cosine Similarity 사용

```python
from sklearn.metrics.pairwise import cosine_similarity

ratings_matrix_T = ratings_matrix.T
item_sim = cosine_similarity(ratings_matrix_T, ratings_matrix_T)
item_sim_df = pd.DataFrame(item_sim, index=ratings_matrix.columns, columns=ratings_matrix.columns)

```

- 특정 영화 기준 유사 영화 확인 가능:

```python
item_sim_df["Avengers: Infinity War - Part I (2018)"].sort_values(ascending=False)[1:6]

```

---

### 4. 개인화 추천 (아이템 기반 CF)

### 🔹 예측 평점 계산: **가중 평균 방식**

- 유사도 × 평점을 더하고, 유사도 총합으로 나눠 예측 평점 산출
- 사용자가 보지 않은 영화만 대상으로 예측 수행

```python
def predict_ratings_item_based(user_id, ratings_matrix, item_sim_df, k_neighbors=20):
    ...
    for movie in unseen_items:
        top_neighbors = movie_similarities[watched].nlargest(k_neighbors)
        weighted_sum = np.dot(top_neighbors.values, user_ratings[top_neighbors.index].values)
        normalization = np.abs(top_neighbors).sum()
        pred_rating = weighted_sum / normalization if normalization != 0 else 0

```

- 예시:

```python
user_id = 1
pred_ratings = predict_ratings_item_based(user_id, ratings_matrix, item_sim_df)
pred_ratings.sort_values(ascending=False).head(10)

```

---

### 5. 보완: Threshold 필터링

### 🔹 유사도가 일정 기준 이상인 아이템만 사용

```python
if similarity_threshold > 0:
    filtered = sims[watched][sims[watched] > similarity_threshold]

```

- 유사도가 낮은 이웃 제외 → **예측 평점 다양성 개선**

---

### 6. 평가 지표 계산 (MAE / RMSE)

### 🔹 사용자 평점을 학습/테스트 분할하여 평가

```python
from sklearn.metrics import mean_squared_error

# 테스트 평점은 masking (0으로)
ratings_matrix_train.loc[user_id, test_items] = 0

# 예측
pred_ratings_train = predict_ratings_item_based(...)

# 평가
y_true = true_ratings[test_items]
y_pred = pred_ratings_train[test_items]
rmse = mean_squared_error(y_true, y_pred) ** 0.5

```

---

### 7. 성능 개선 방안

| 방법 | 설명 |
| --- | --- |
| 유사도 지표 최적화 | Pearson, Cosine + Shrinkage 등 실험 |
| 유사도 threshold / 이웃 수 조절 | Top‑k 및 임계값 튜닝 |
| 정규화 / 중심화 | 사용자 평균 제거 또는 Z-score |
| Significance Weighting | 공통 평가 개수 고려한 가중치 |
| 차원 축소 기반 MF | SVD, ALS, NMF 등 |
| 암묵적 피드백 반영 | 클릭/구매 로그로 confidence 모델 구축 |

---

### 8. 모델 기반 협업 필터링

- **잠재 요인 모델(Matrix Factorization)**
    - 사용자-아이템 행렬을 SVD 등으로 분해 → 잠재 요인 추출
    - 대규모 데이터 처리에 유리
    - ALS, NMF 등이 대표 알고리즘

---

### 9. 하이브리드 추천 시스템

- **여러 추천 기법 결합**:
    - Weighted Hybrid
    - Switching
    - Cascade
    - Feature Combination
- 콘텐츠 기반 + 협업 필터링 혼합 가능
- Cold Start·희소성·다양성 문제 보완

---

### 🔚 추천 기법 비교 요약

| 기법 | 장점 | 단점 |
| --- | --- | --- |
| 사용자 기반 CF | 실제 사용자 취향 반영 가능 | 확장성 떨어짐, 데이터 희소성 문제 |
| 아이템 기반 CF | 정확도 ↑, 계산 효율 ↑ | Cold-start 아이템 추천 어려움 |
| 모델 기반 CF | 대규모 데이터 처리, 잡음 감소 | 학습 복잡도 ↑, 해석 어려움 |
| 콘텐츠 기반 필터링 | 설명력↑, Cold-start 대응 가능 | 사용자의 내면적 취향 반영 어려움 |
| 하이브리드 | 단점 보완, 다양한 상황에 대응 가능 | 설계 복잡도 ↑, 연산 자원 소모 ↑ |

---

필요하시면 이 내용을 마크다운, PDF, 또는 발표용 슬라이드로 변환해드릴 수도 있습니다. 추가 요청이 있으면 말씀해주세요!