
# 📘 Java - Variables and Data Types

> 📅 Date: 2025-04-18
> 📚 Topic: Java Variables and Data Types

---

## 📌 Summary

- Java는 출력 시 `System.out.print()` 또는 `System.out.println()` 사용
- 입력은 `Scanner` 클래스를 통해 받으며, `next()`와 `nextLine()`의 차이를 숙지해야 함
- 기본 자료형은 논리형, 문자형, 정수형, 실수형으로 나뉘며, 저장 범위가 다름
- 형 변환에는 묵시적(자동) 변환과 명시적(강제) 변환이 있음
- 상수는 `final` 키워드로 선언하며, 코딩 컨벤션을 지켜야 함

---

## 🧠 Syntax Examples

### ✅ 출력하기

```java
System.out.print("줄바꿈 없이 출력");
System.out.println("출력 후 줄바꿈");
```

### ✅ 입력 받기 (Scanner)

> Java에서 사용자 입력을 받을 때는 `Scanner` 클래스를 사용함  
> 사용 전 `import java.util.Scanner;` 필수!

```java
import java.util.Scanner;

Scanner sc = new Scanner(System.in);
String name = sc.next();  // 공백 전까지 입력
System.out.println(name);
```

📌 `sc.next()` : 공백 전까지 입력  
📌 `sc.nextLine()` : 한 줄 전체 입력 (띄어쓰기 포함)

#### 🔢 숫자 입력 예제

```java
System.out.println("사고싶은 아이스크림의 개수 입력 >>");
int num = sc.nextInt();
System.out.println("여기 있음 " + num + "개임");
```

---

## 💡 변수 선언과 자료형

Java는 변수를 선언할 때 자료형을 **반드시 명시**해야 함.

### 🔸 변수 선언과 할당

```java
int age = 20;       // 선언과 동시에 할당
int number;         // 선언만
number = 10;        // 나중에 할당
```

---

## 📘 기본 자료형 정리

| 분류   | 자료형     | 크기       | 예시                         |
|--------|------------|------------|------------------------------|
| 논리형 | `boolean`  | 1 byte     | `boolean result = true;`     |
| 문자형 | `char`     | 2 byte     | `char grade = 'A';`          |
| 정수형 | `byte`     | 1 byte     | `byte b = 10;`               |
|        | `short`    | 2 byte     | `short s = 1000;`            |
|        | `int`      | 4 byte     | `int i = 12345;`             |
|        | `long`     | 8 byte     | `long l = 8_000_000_000L;`   |
| 실수형 | `float`    | 4 byte     | `float f = 78.5f;`           |
|        | `double`   | 8 byte     | `double pi = 3.14;`          |

---

## 🔒 상수 선언 (final)

```java
final int MINUTE = 60;
```

- `final` 키워드 사용 시 값 변경 불가
- 상수명은 보통 대문자 사용 (예: `PI`, `MAX_COUNT`)

---

## 🎨 자바 코딩 컨벤션

- **패키지명**: 소문자 시작 (예: `ex01기초`)  
- **클래스명**: 대문자 시작 (예: `Ex01출력`)  
- **변수명**: 소문자 시작 + 카멜케이스 (예: `wishList`)  
- **예약어 사용 금지**: 예) `int class = 1;` ❌  
- **숫자로 시작 불가**: 예) `int 3num = 3;` ❌  
- **특수문자 제한**: `_`, `$`만 사용 가능

---

## 🔄 형 변환 (Type Casting)

### 🔁 묵시적 형 변환 (자동)

> 작은 크기 → 큰 크기

```java
int a = 3;
double b = a;  // int → double
System.out.println(b);  // 출력: 3.0
```

### 🔁 명시적 형 변환 (강제)

> 큰 크기 → 작은 크기

```java
double c = 73.2;
int d = (int) c;
System.out.println(d);  // 출력: 73
```

---

## ⚠️ 자주 발생하는 에러

| 에러 메시지                        | 설명                             |
|-----------------------------------|----------------------------------|
| `type mismatch`                   | 변수 타입이 일치하지 않음       |
| `cannot convert from int to double` | 형 변환 필요                   |
| `syntax error`                    | 문법 오류 (예: 세미콜론 누락 등) |
