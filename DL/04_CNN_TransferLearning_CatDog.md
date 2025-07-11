# Cats vs Dogs 분류 실습 (DNN, CNN, 전이학습, 이미지 증강)

## 📌 목표

-   고양이와 강아지를 분류하는 이미지 분류기를 제작한다.
-   다양한 딥러닝 기법을 적용해 성능 향상을 실험한다.
    -   DNN, CNN, 이미지 증강, 전이학습(VGG16), 미세조정(Fine-tuning)
    -   과적합 방지 기법: Dropout, BatchNormalization, GlobalAveragePooling

---

## 📁 1. Colab 환경 준비 및 데이터 로딩

### 📂 Google Drive 연동

```python
from google.colab import drive
drive.mount('/content/drive')
%cd /content/drive/MyDrive/Colab Notebooks/deeplearning_2025

```

### 📥 데이터 로딩

-   이전에 `.npz` 형태로 저장한 전처리된 데이터를 불러온다.

```python
import numpy as np

data = np.load("./data/np_cat_vs_dog.npz")
X_train, X_test = data["X_train"], data["X_test"]
y_train, y_test = data["y_train"], data["y_test"]

```

---

## 🧱 2. DNN 모델 설계

### ✅ 구성

-   입력층: `Flatten`을 통해 1D로 변환
-   은닉층: Dense(256) → Dense(128) → Dense(64)
-   출력층: `sigmoid` (이진 분류)

### 🛠 코드

```python
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import InputLayer, Dense, Flatten
from tensorflow.keras.callbacks import EarlyStopping

model1 = Sequential([
    InputLayer(input_shape=(224, 224, 3)),
    Flatten(),
    Dense(256, activation='relu'),
    Dense(128, activation='relu'),
    Dense(64, activation='relu'),
    Dense(1, activation='sigmoid')
])

model1.compile(optimizer='adam', loss='binary_crossentropy', metrics=['accuracy'])

es = EarlyStopping(monitor='val_accuracy', patience=5)
hist1 = model1.fit(X_train, y_train, epochs=10, validation_split=0.3, callbacks=[es])

```

---

## 📈 3. 성능 시각화

```python
import matplotlib.pyplot as plt

plt.plot(hist1.history["accuracy"], label="Train acc")
plt.plot(hist1.history["val_accuracy"], label="Validation acc")
plt.legend(); plt.show()

```

---

## 🧱 4. CNN 모델 설계

### ✅ CNN 기본 구조

-   Conv2D → MaxPooling2D × 3
-   Flatten → Dense × 3 → Output

```python
from tensorflow.keras.layers import Conv2D, MaxPooling2D

model2 = Sequential([
    InputLayer(input_shape=(224, 224, 3)),
    Conv2D(32, (3, 3), activation="relu", padding="same"),
    MaxPooling2D((2, 2)),
    Conv2D(64, (3, 3), activation="relu", padding="same"),
    MaxPooling2D((2, 2)),
    Conv2D(64, (3, 3), activation="relu", padding="same"),
    MaxPooling2D((2, 2)),
    Flatten(),
    Dense(256, activation="relu"),
    Dense(128, activation="relu"),
    Dense(64, activation="relu"),
    Dense(1, activation="sigmoid")
])

model2.compile(optimizer="adam", loss="binary_crossentropy", metrics=["accuracy"])
hist2 = model2.fit(X_train, y_train, epochs=2, validation_split=0.3, callbacks=[es])

```

---

## 🖼️ 5. 이미지 증강(Augmentation)

### ✅ 목적

-   데이터 수가 부족할 때 다양한 변형을 통해 데이터 수를 늘린다
-   회전, 이동, 기울기, 확대, 반전 등을 적용한다

```python
from tensorflow.keras.preprocessing.image import ImageDataGenerator

train_gen = ImageDataGenerator(
    rescale=1./255,
    rotation_range=10,
    width_shift_range=0.1,
    height_shift_range=0.1,
    shear_range=0.1,
    zoom_range=0.1,
    horizontal_flip=True,
    fill_mode="nearest"
)

test_gen = ImageDataGenerator(rescale=1./255)

train_generator = train_gen.flow_from_directory(
    "./data/cats_and_dogs_filtered/train", target_size=(224, 224),
    batch_size=20, class_mode="binary"
)

test_generator = test_gen.flow_from_directory(
    "./data/cats_and_dogs_filtered/validation", target_size=(224, 224),
    batch_size=20, class_mode="binary"
)

```

---

## 🧱 6. 증강 데이터 기반 CNN 학습

```python
model3 = Sequential([
    InputLayer(input_shape=(224, 224, 3)),
    Conv2D(32, (3,3), activation="relu", padding="same"),
    MaxPooling2D((2,2)),
    Conv2D(64, (3,3), activation="relu", padding="same"),
    MaxPooling2D((2,2)),
    Conv2D(64, (3,3), activation="relu", padding="same"),
    MaxPooling2D((2,2)),
    Flatten(),
    Dense(256, activation="relu"),
    Dense(128, activation="relu"),
    Dense(64, activation="relu"),
    Dense(1, activation="sigmoid")
])

model3.compile(optimizer="adam", loss="binary_crossentropy", metrics=["accuracy"])
hist3 = model3.fit(train_generator, validation_data=test_generator, epochs=2, callbacks=[es])

```

---

## 🔁 7. 전이학습 (Transfer Learning) - VGG16

### ✅ 특징 추출 방식 (동결)

```python
from keras.applications import VGG16

conv_base = VGG16(weights="imagenet", include_top=False, input_shape=(224, 224, 3))
conv_base.trainable = False  # 전처리기 가중치 동결

model4 = Sequential([
    conv_base,
    Flatten(),
    Dense(256, activation="relu"),
    Dense(128, activation="relu"),
    Dense(64, activation="relu"),
    Dense(1, activation="sigmoid")
])

model4.compile(optimizer="adam", loss="binary_crossentropy", metrics=["accuracy"])
hist4 = model4.fit(X_train, y_train, epochs=2, validation_split=0.3)

```

---

## 🔧 8. 미세조정 (Fine-Tuning)

### ✅ block5_conv3 이후만 학습 가능하도록 설정

```python
conv_base.trainable = True
for layer in conv_base.layers:
    if layer.name == "block5_conv3":
        layer.trainable = True
    else:
        layer.trainable = False

model4.compile(optimizer="adam", loss="binary_crossentropy", metrics=["accuracy"])
hist4 = model4.fit(X_train, y_train, epochs=2, validation_split=0.3)

```

---

## 🧪 9. 과적합 방지 모델 설계

### ✅ BatchNormalization + LeakyReLU + GlobalAveragePooling

```python
from tensorflow.keras.layers import BatchNormalization, Activation, LeakyReLU, GlobalAveragePooling2D

model5 = Sequential([
    InputLayer(input_shape=(224, 224, 3)),
    Conv2D(32, (3,3), padding="same"),
    BatchNormalization(),
    Activation(LeakyReLU()),
    MaxPooling2D((2,2)),

    Conv2D(64, (3,3), padding="same"),
    BatchNormalization(),
    Activation(LeakyReLU()),
    MaxPooling2D((2,2)),

    Conv2D(64, (3,3), padding="same"),
    BatchNormalization(),
    Activation(LeakyReLU()),
    GlobalAveragePooling2D(),

    Dense(256, activation="relu"),
    Dense(128, activation="relu"),
    Dense(64, activation="relu"),
    Dense(1, activation="sigmoid")
])

model5.compile(optimizer="adam", loss="binary_crossentropy", metrics=["accuracy"])
hist5 = model5.fit(X_train, y_train, epochs=10, validation_split=0.3, callbacks=[es])

```

---

## 📊 10. 학습 결과 시각화

```python
plt.plot(hist5.history["accuracy"], label="Train acc")
plt.plot(hist5.history["val_accuracy"], label="Validation acc")
plt.legend(); plt.show()

```
