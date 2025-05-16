## 📝 손글씨 숫자 분류 모델 개발 요약 정리

### 1. 문제 정의

- **목표**: 손글씨 이미지 데이터를 기반으로 0~9 숫자를 분류하는 머신러닝 모델을 학습.
- **입력 데이터**: `28x28 = 784`개의 픽셀로 이루어진 이미지 (1차원 배열 형태).
- **출력 데이터**: 0부터 9까지의 정수(Label, 즉 정답).

---

### 2. 데이터 로딩 및 탐색

```python

data = pd.read_csv('./data/digit_train.csv')

```

- 총 **42,000개**의 데이터 샘플이 존재.
- 각 샘플은 **784개의 픽셀 값**과 1개의 라벨(정답)로 구성 (`label`, `pixel0 ~ pixel783`).

---

### 3. 이미지 시각화

- 이미지 확인을 위해 `reshape` 사용:

```python

img0 = data.iloc[0,1:].values.reshape(28, 28)
plt.imshow(img0, cmap='gray')

```

- 이미지와 라벨 값 일치 여부 확인 (`data.iloc[0,0]` → 라벨 1 확인됨).

---

### 4. 데이터 분리

```python

X = data.iloc[:, 1:]  # 입력값
y = data.iloc[:, 0]   # 라벨

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.3, random_state=5
)

```

- 학습:검증 비율 = 7:3
- 총 29,400개 학습 데이터 / 12,600개 테스트 데이터

---

### 5. 모델 선택 및 학습

사용한 머신러닝 모델:

- **K-최근접 이웃 (KNN)**
- **로지스틱 회귀 (Logistic Regression)**
- **선형 서포트 벡터 머신 (LinearSVC)**

```python

knn_model = KNeighborsClassifier()
logi_model = LogisticRegression()
svm_model = LinearSVC()

knn_model.fit(X_train, y_train)
logi_model.fit(X_train, y_train)
svm_model.fit(X_train, y_train)

```

※ `LogisticRegression`에서 수렴 경고 발생 → `max_iter` 조정 필요할 수 있음.

---

### 6. 모델 평가

```python

print(knn_model.score(X_test, y_test))
print(logi_model.score(X_test, y_test))
print(svm_model.score(X_test, y_test))

```

| 모델 | 정확도 |
| --- | --- |
| KNN | 96.5% |
| 로지스틱 회귀 | 91.5% |
| SVM | 90.6% |
- KNN 모델이 가장 높은 정확도 달성

---

### 7. 평가지표

- *분류 문제이므로 정확도(Accuracy)**를 주요 평가지표로 사용.
- 향후에는 정밀도(Precision), 재현율(Recall), F1-score 등도 고려 가능.