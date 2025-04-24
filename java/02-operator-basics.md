
# 🧮 Java - Operators

> 📅 Date: 2025-04-24
> 📚 Topic: Java Operators (산술, 대입, 증감, 비교, 논리, 삼항 연산자)

---

## 📌 연산자 기초 1

```java
int num1 = 7;
int num2 = 3;

// 몫과 나머지 출력
System.out.println(num1 / num2);  // 출력: 2
System.out.println(num1 % num2);  // 출력: 1

// 실수형 나눗셈
float num3 = 7.0f;
float num4 = (float) 3.0;
System.out.println(num3 / num4);  // 출력: 2.333...
System.out.println(num1 / num4);  // 출력: 2.333...

// 문자열과 숫자 결합
System.out.println(7 + 3 + "7");  // 출력: 107
System.out.println(7 + "3" + 7);  // 출력: 737
System.out.println("7" + 3 + 7);  // 출력: 737

// 710 출력
System.out.println("7" + (3 + 7));  // 출력: 710
```

---

## 📌 연산자 기초 2 - 복합 대입 & 증감 연산자

```java
int num1 = 29;
num1 = num1 - 2;   // 27
num1 -= 2;         // 25
num1 -= 2 + 3 * 4; // 11

int number = 5;
System.out.println(number++);  // 5
System.out.println(number);    // 6
System.out.println(++number);  // 7
System.out.println(number);    // 7
```

---

## 📌 연산자 기초 3 - 비교, 논리, 삼항 연산자

```java
int a = 3;
int b = 10;

System.out.println(a > b);  // false
System.out.println(a < b);  // true
System.out.println(a == b); // false
System.out.println(a != b); // true

// 논리 연산자
System.out.println((1 == 2) && (1 < 2)); // false
System.out.println((1 != 2) && (1 < 2)); // true
System.out.println((1 == 2) || (1 < 2)); // true

// 삼항 연산자
int i = 3;
int j = 5;
System.out.println(i == j ? "같다" : "같지않다");

boolean isRainy = false;
System.out.println(isRainy ? "비가온다" : "비가오지않는다");
```

---

## 📌 연산자 실습

```java
Scanner sc = new Scanner(System.in);

// 1. 456의 백의 자리만 출력
int num = 456;
System.out.println(num / 100 * 100);  // 출력: 400

// 2. 두 정수를 입력받아 사칙연산
System.out.print("첫 번째 정수 입력: ");
int a = sc.nextInt();
System.out.print("두 번째 정수 입력: ");
int b = sc.nextInt();
System.out.println("더하기: " + (a + b));
System.out.println("빼기: " + (a - b));
System.out.println("곱하기: " + (a * b));
System.out.println("나누기: " + (a / b));

// 3. 짝수/홀수 판별
System.out.print("정수를 입력하세요: ");
int c = sc.nextInt();
System.out.println(c % 2 == 0 ? c + "는 짝수" : c + "는 홀수");

// 4. 두 수의 차 출력
System.out.print("첫 번째 정수 입력: ");
int e = sc.nextInt();
System.out.print("두 번째 정수 입력: ");
int f = sc.nextInt();
System.out.println("두 수의 차: " + (e > f ? e - f : f - e));

// 5. 나이 입력 후 성인/미성년 판별
System.out.print("나이를 입력하세요: ");
int d = sc.nextInt();
System.out.println(d >= 20 ? "성인" : "미성년");

// 6. 초를 시간/분/초로 변환
System.out.print("초 입력: ");
int g = sc.nextInt();
int hour = g / 3600;
int minute = (g / 60) % 60;
int second = g % 60;
System.out.println(hour + "시간 " + minute + "분 " + second + "초");
```

---

## ✅ 정리

| 연산자 종류 | 설명 |
|-------------|------|
| 산술 연산자 | +, -, *, /, % |
| 대입 연산자 | =, +=, -= 등 |
| 증감 연산자 | ++, -- |
| 비교 연산자 | >, <, ==, != 등 |
| 논리 연산자 | &&, ||, ! |
| 삼항 연산자 | 조건 ? 참 : 거짓 |

---

📌 다양한 연산자 조합을 통해 조건 제어 및 데이터 처리를 처리할 수 있다.
