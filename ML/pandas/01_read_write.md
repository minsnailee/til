# 📘 pandas - read, write

---

### 🏷️ 1. 파일 불러오기 (Reading Data)

### ✅ CSV 파일

```python
import pandas as pd

df = pd.read_csv('sample.csv')
df.head()
```

> 옵션 예시:
> 

```python
pd.read_csv('sample.csv', encoding='utf-8')
pd.read_csv('sample.csv', header=None)
pd.read_csv('sample.csv', names=['col1', 'col2'])
```

---

### ✅ Excel 파일

```python
df = pd.read_excel('sample.xlsx', sheet_name='Sheet1')
```

---

### ✅ JSON 파일

```python
df = pd.read_json('sample.json')
```

---

### ✅ 웹에서 불러오기

```python
url = 'sample.csv'
df = pd.read_csv(url)
```

---

### 🏷️ 2. 파일 저장하기 (Writing Data)

### ✅ CSV로 저장

```python
df.to_csv('output.csv', index=False)
```

### ✅ Excel로 저장

```python
df.to_excel('output.xlsx', index=False, sheet_name='MySheet')
```

### ✅ JSON으로 저장

```python
df.to_json('output.json')
```

---

### 🏷️ 3. 옵션 요약 표 (정리용)

| 함수 | 설명 | 주요 옵션 |
| --- | --- | --- |
| `read_csv()` | CSV 파일 읽기 | `encoding`, `header`, `names`, `sep` |
| `read_excel()` | 엑셀 읽기 | `sheet_name`, `skiprows`, `usecols` |
| `read_json()` | JSON 읽기 | `orient`, `lines` |
| `to_csv()` | CSV 저장 | `index`, `encoding` |
| `to_excel()` | 엑셀 저장 | `sheet_name`, `index` |
| `to_json()` | JSON 저장 | `orient`, `indent` |

---

### 🧪 4. 실습 예제

간단한 DataFrame을 만들어서 다양한 형식으로 저장하고 다시 불러오는 예제를 넣는 것도 좋아요.

```python
df = pd.DataFrame({
    '이름': ['홍길동', '김영희'],
    '나이': [25, 30]
})
df.to_csv('test.csv', index=False)

df2 = pd.read_csv('test.csv')
print(df2)
```

---

### 🧼 파일 경로 문제 해결 팁

```python
import os

os.getcwd()  # 현재 작업 디렉토리 확인
```

---

## ✅ 정리

- `read_csv()` / `to_csv()`는 가장 기본적이고 자주 사용됨
- `Excel`, `JSON` 등 다양한 파일 포맷도 유사한 패턴
- 경로와 인코딩 문제에 유의