# 🧪 정규 표현식 (Regular Expressions) 실습 정리

---

## ✅ 1. 필요한 라이브러리 설치

정규 표현식을 사용하려면 Python의 내장 모듈인 `re` 모듈을 사용합니다.

> re는 Python에 기본 포함되어 있으므로 별도의 설치는 필요 없습니다.
> 

```python

import re

```

---

## ✅ 2. 문자열을 정규표현식으로 **토큰화**

### 예시 텍스트:

```python

text = """100 Jone       PROF
          101 James      STUD
          102 Max   STUD"""

```

### ✅ 최소 공백 2개 이상을 기준으로 분리

```python

tokens = re.split("\s+", text)
print(tokens)

```

🔎 **설명**:

- `\s+` : 하나 이상의 공백 문자
- 결과:

```python

['100', 'Jone', 'PROF', '101', 'James', 'STUD', '102', 'Max', 'STUD']

```

---

## ✅ 3. 숫자 추출 (ID 등)

```python

print(re.findall("[0-9]+", text))
print(re.findall("\d+", text))  # 동일한 의미

```

🔎 **설명**:

- `[0-9]+` 또는 `\d+` : 연속된 숫자 1개 이상
- 결과: `['100', '101', '102']`

---

## ✅ 4. 이름 추출 (대문자로 시작, 소문자 따름)

```python

print(re.findall("[A-Z][a-z]+", text))

```

🔎 **설명**:

- `[A-Z]` : 대문자 하나
- `[a-z]+` : 소문자 1개 이상
- 결과: `['Jone', 'James', 'Max']`

---

## ✅ 5. 전화번호 추출

### 예시 문자열:

```python

str1 = """홍길동 062-334-3434
          성춘향 02-455-3432
          이순신 070-3534-5344
          돌쇠   010-3232-3533
          마당쇠 019-334-4544
          김유신 34-32342-434533"""

```

### ✅ 일반 전화번호 패턴 추출

```python

print(re.findall("\d{2,3}-\d{3,4}-\d{4}", str1))

```

🔎 **설명**:

- `\d{2,3}` : 앞자리가 2~3자리 숫자
- `\d{3,4}` : 중간이 3~4자리 숫자
- `\d{4}` : 마지막은 4자리
- 결과:

```python

['062-334-3434', '02-455-3432', '070-3534-5344', '010-3232-3533', '019-334-4544']

```

### ✅ 핸드폰 번호만 추출 (010, 011 등)

```python

print(re.findall("01[016789]-\d{4}-\d{4}", str1))

```

🔎 **설명**:

- `01[016789]` : 010, 011, 016, 017, 018, 019 중 하나
- 결과: `['010-3232-3533']`

---

## ✅ 6. 사이트 주소 추출

### 예시 문자열:

```python

str2 = """사이트 주소
          (1) https://example.com
          (2) http://www.naver.com, https://m.naver.co.kr
          (3) www.daum.co.kr"""

```

### ✅ 정규식:

```python

print(re.findall("https?://[^\s,]+|www\.[^\s,]+", str2))

```

🔎 **설명**:

- `https?://` : `http://` 또는 `https://` (s는 0~1회)
- `|` : 또는
- `www\.` : www.로 시작
- `[^\s,]+` : 공백과 쉼표 제외한 문자들
- 결과:

```python

['https://example.com', 'http://www.naver.com', 'https://m.naver.co.kr', 'www.daum.co.kr']

```

---

## ✅ 7. 이메일 주소 추출

### 예시 문자열:

```python

text_email = """이메일 주소
            - aaaa@gmail.com
            - bk234@hanmail.net
            - q14ds@naver.co.kr
            - a_ddd@nK_3.N234.co.kr
            - a@1.2.2
            - 1@1.2
"""

```

### ✅ 정규식:

```python

print(re.findall("[a-zA-Z0-9][a-zA-Z0-9_-]*@[a-zA-Z0-9][a-zA-Z0-9._-]*\.[a-zA-Z]{2,}+", text_email))

```

🔎 **설명**:

- `@` 앞: `[a-zA-Z0-9][a-zA-Z0-9_-]*` : 첫 문자는 영문/숫자, 이후에는 `_`, 도 허용
- `@` 뒤 도메인: `[a-zA-Z0-9._-]*`
- 마지막 `.도메인`: `\.[a-zA-Z]{2,}` : 예) `.com`, `.co.kr` 등
- 결과:

```python

['aaaa@gmail.com', 'bk234@hanmail.net', 'q14ds@naver.co.kr', 'a_ddd@nK_3.N234.co.kr']

```

---

## 📌 정리

| 목적 | 정규표현식 예시 |
| --- | --- |
| 숫자 추출 | `\d+` 또는 `[0-9]+` |
| 이름 추출 | `[A-Z][a-z]+` |
| 전화번호 추출 | `\d{2,3}-\d{3,4}-\d{4}` |
| 휴대폰 번호만 | `01[016789]-\d{4}-\d{4}` |
| URL 추출 | `https?://[^\s,]+ |
| 이메일 추출 | `[a-zA-Z0-9][a-zA-Z0-9_-]*@` |