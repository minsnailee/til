
# 📘 Java - Loops (while, do-while, for)

> 📅 Date: 2025-04-24  
> 📚 Topic: 자바 반복문 - while, do-while, for 문법 및 활용

---

## 📌 요약
- while 문: 반복 횟수가 명확하지 않을 때 사용. 조건이 참이면 계속 반복.
- do-while 문: 조건과 상관없이 최소 한 번은 실행.
- for 문: 반복 횟수가 명확할 때 적합. 초기화, 조건식, 증감식을 포함.
- 이중 반복문을 이용하면 패턴 출력이나 구구단처럼 반복 속에 반복을 구현 가능.

## ✅ do-while 문 기본 예제
```java
int num = 1;
do {
    System.out.println(num);
    num++;
} while (num < 6);
System.out.println("종료");
```

## ✅ for 문 기초 및 활용
```java
// 96부터 53까지 출력
for (int i = 96; i >= 53; i--) {
    System.out.print(i + " ");
}
```

```java
// 21부터 57까지 홀수만 출력
for (int i = 21; i <= 57; i += 2) {
    System.out.print(i + " ");
}
```

## ✅ 구구단 출력 (2단)
```java
for (int i = 1; i < 10; i++) {
    System.out.println("2 * " + i + " = " + (2 * i));
}
```

## ✅ 단수와 범위 입력 받아 구구단 출력
```java
Scanner sc = new Scanner(System.in);
System.out.print("단 입력 : ");
int dan = sc.nextInt();
System.out.print("범위 입력 : ");
int range = sc.nextInt();

for (int i = 1; i <= range; i++) {
    System.out.println(dan + " * " + i + " = " + (dan * i));
}
```

## ✅ 이중 for 문 - 별 출력
```java
for (int i = 1; i <= 5; i++) {
    for (int j = 1; j <= i; j++) {
        System.out.print("*");
    }
    System.out.println();
}
```

## ✅ while 문 기본 예제
```java
Scanner sc = new Scanner(System.in);
System.out.println("숫자 입력: ");
int num = sc.nextInt();

while (num < 10) {
    System.out.println("숫자 입력: ");
    num = sc.nextInt();
}
System.out.println("종료되었습니다.");
```

## ✅ while 문 - 홀수/짝수 개수 세기
```java
Scanner sc = new Scanner(System.in);
int odd = 0, even = 0;

while (true) {
    System.out.print("숫자 입력: ");
    int num = sc.nextInt();
    
    if (num == -1) {
        System.out.println("종료");
        break;
    } else if (num % 2 == 0) {
        even++;
    } else {
        odd++;
    }

    System.out.println("짝수 개수: " + even);
    System.out.println("홀수 개수: " + odd);
}
```

## ✅ while 문 - 플러스 게임
```java
import java.util.Random;
import java.util.Scanner;

Random rd = new Random();
Scanner sc = new Scanner(System.in);

while (true) {
    int num1 = rd.nextInt(20);
    int num2 = rd.nextInt(20);

    System.out.print(num1 + " + " + num2 + " = ");
    int result = sc.nextInt();

    if (num1 + num2 == result) {
        System.out.println("성공성공");
    } else {
        System.out.println("실패 ㅠ_ㅠ");
        System.out.print("계속하시겠습니까? (Y/N): ");
        String answer = sc.next();

        if (answer.equalsIgnoreCase("N")) {
            System.out.println("종료합니다.");
            break;
        }
    }
}
```
