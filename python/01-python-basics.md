# 📘 Python 기본 문법 정리

## 1. 변수와 자료형

```python
# 변수 선언
x = 10
y = 3.14
name = "Alice"
is_active = True

# 여러 개 변수 선언
a, b, c = 1, 2, 3
```

| 자료형 | 예시 |
| --- | --- |
| 정수(int) | `x = 5` |
| 실수(float) | `y = 3.14` |
| 문자열(str) | `name = "Python"` |
| 불리언(bool) | `is_active = True` |

---

## 2. 출력 (Print)

### 여러 값

```python
name = "Alice"
age = 25
print(name, age)
```

- 기본적으로 **공백(' ')** 으로 구분해서 출력됨.

### end 옵션

```python
# 출력 끝에 줄바꿈 대신 다른 문자 넣기
print("Hello", end=" ")
print("World")
```

- 기본값은 `end='\n'` (줄바꿈)
- 바꿔주면 줄바꿈 없이 연결 출력 가능

### sep 옵션

```python
# 여러 값을 출력할 때 구분자 변경
print("2025", "04", "27", sep="-")
```

- 기본값은 `sep=' '` (공백)
- 변경하면 출력값 사이에 원하는 문자 삽입

### f-string (Python 3.6+)

```python
name = "Alice"
age = 25
print(f"My name is {name} and I'm {age} years old.")
```

- 가독성이 뛰어남
- 중괄호 `{}` 안에 변수나 표현식 바로 작성 가능

```python
# 표현식도 가능
print(f"2 + 3 = {2 + 3}")
```

### format() 함수

```python
name = "Alice"
age = 25
print("My name is {} and I'm {} years old.".format(name, age))
```

- 인덱스 지정도 가능

```python
print("My name is {0} and I'm {1} years old. {0} likes Python.".format(name, age))
```

- 키워드 인자 사용

```python
print("Name: {name}, Age: {age}".format(name="Alice", age=25))
```

### % 포맷팅 (구 방식)

```python
name = "Alice"
age = 25
print("My name is %s and I'm %d years old." % (name, age))
```

- `%s` : 문자열
- `%d` : 정수
- `%f` : 실수

```python
pi = 3.141592
print("PI is approximately %.2f" % pi)  # 소수점 둘째 자리까지
```

### 이스케이프 문자

```python
print("Hello\nWorld")  # 줄바꿈
print("Hello\tWorld")  # 탭
print("Hello \"World\"")  # 큰따옴표 출력
```

| 이스케이프 문자 | 의미 |
| --- | --- |
| `\n` | 줄바꿈 |
| `\t` | 탭(tab) |
| `\'` | 작은따옴표 |
| `\"` | 큰따옴표 |
| `\\` | 백슬래시 |

### 줄바꿈 없이 여러 줄 출력

```python
print("""여러 줄을
한 번에 출력""")
```

- `""" """` 또는 `''' '''` 사용
- 문자열 안에 줄바꿈 포함 가능

### 출력 스타일 (정렬, 채우기)

```python
# 정렬과 채우기
print("{:<10}".format("left"))    # 왼쪽 정렬
print("{:>10}".format("right"))   # 오른쪽 정렬
print("{:^10}".format("center"))  # 가운데 정렬

# 빈 공간에 문자 채우기
print("{:*^10}".format("hi"))     # *로 채운 가운데 정렬
```

### 숫자 포맷팅

```python
# 자릿수 맞추기
print("{:05d}".format(42))    # 00042

# 소수점 표현
print("{:.2f}".format(3.141592))  # 3.14

# 천 단위 콤마
print("{:,}".format(1000000))     # 1,000,000
```

### 12. 출력 내용을 파일로 보내기

```python
with open('output.txt', 'w') as f:
    print("Hello, file!", file=f)
```

- `file` 매개변수로 파일 객체를 넘기면 콘솔이 아닌 파일에 출력

---

## 3. 입력 (Input)

```python

# 기본 입력 (항상 문자열로 입력됨)
user_input = input("값을 입력하세요: ")
print(f"입력한 값: {user_input}")

# 정수로 변환
num = int(input("숫자를 입력하세요: "))
print(f"2배한 값: {num * 2}")

```

### 여러 개 입력 받기 (split 사용)

```python
# 입력: 10 20
a, b = input("두 수를 입력하세요: ").split()
print(a, b)
```

- `split()`을 쓰면 띄어쓰기를 기준으로 나눠준다.

**정수로 변환:**

```python
a, b = map(int, input("두 정수를 입력하세요: ").split())
print(a + b)
```

- `map(형변환함수, 반복가능한것)` 형태

### 리스트 입력

```python
# 입력: 1 2 3 4 5
lst = list(map(int, input("숫자 여러 개 입력: ").split()))
print(lst)
```

- 한 줄에 여러 값을 리스트로 받기.

### 특정 구분자로 입력 받기

```python
fruits = input("과일을 입력하세요 (콤마로 구분): ").split(",")
print(fruits)
```

- `split(",")`로 쉼표(,)를 기준으로 분리

### 여러 줄 입력 (반복문 사용)

```python
n = int(input("줄 수 입력: "))
lines = []
for _ in range(n):
    line = input()
    lines.append(line)

print(lines)
```

- 몇 줄 입력 받을지 정해진 경우

### sys.stdin으로 빠른 입력 받기

(특히 알고리즘 문제에서 유용)

```python
import sys

data = sys.stdin.readline()
print(data)
```

- `input()`보다 `sys.stdin.readline()`이 **훨씬 빠름**
- 대신 끝에 **'\n' 줄바꿈**이 붙어 있으므로 `.strip()`과 함께 사용하기

```python
data = sys.stdin.readline().strip()
```

---

## 4. 조건문 (if문)

```python

x = 10

if x > 0:
    print("양수입니다.")
elif x == 0:
    print("0입니다.")
else:
    print("음수입니다.")

```

---

## 5. 반복문 (for, while)

```python
# for문
for i in range(5):
    print(i)

# while문
count = 0
while count < 5:
    print(count)
    count += 1
```

---

## 6. 함수

```python
def greet(name):
    return f"Hello, {name}!"

print(greet("Alice"))
```

---

## 7. 리스트, 튜플, 딕셔너리

```python
# 리스트
fruits = ["apple", "banana", "cherry"]

# 튜플 (수정 불가)
point = (10, 20)

# 딕셔너리 (key-value)
person = {"name": "Alice", "age": 25}
```

---

## 8. 주요 연산자

| 종류 | 예시 | 설명 |
| --- | --- | --- |
| 산술 연산자 | `+`, `-`, `*`, `/`, `//`, `%`, `**` | 덧셈, 뺄셈, 곱셈, 나눗셈, 몫, 나머지, 거듭제곱 |
| 비교 연산자 | `==`, `!=`, `>`, `<`, `>=`, `<=` | 비교 결과 True/False 반환 |
| 논리 연산자 | `and`, `or`, `not` | 여러 조건 연결 |
