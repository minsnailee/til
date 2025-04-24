
# 📘 Java - Conditional Statements (if, switch)

> 📅 Date: 2025-04-24  
> 📚 Topic: 자바 조건문 – if 문과 switch 문 활용

---

## 📌 요약
자바 조건문은 프로그램의 흐름을 제어할 수 있다.  
if, else if, else 문을 통해 다양한 조건을 분기 처리할 수 있다.  
switch 문은 하나의 값에 대해 여러 경우를 나눌 때 유용하다.  
Java 12 이후부터는 `->` 문법을 사용할 수 있어 switch 문이 더 간결해졌다.

## ✅ 예제: 나이에 따른 분류
```java
import java.util.Scanner;

public class AgeCheck {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.print("나이 입력: ");
        int age = sc.nextInt();

        if (age > 19) {
            System.out.println("성인입니다.");
        } else if (age > 12) {
            System.out.println("청소년입니다.");
        } else {
            System.out.println("어린이입니다.");
        }
    }
}
```

## ✅ 예제: 3과 5의 배수 판별
```java
import java.util.Scanner;

public class MultipleCheck {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.print("정수 입력: ");
        int num = sc.nextInt();

        if (num % 3 == 0 && num % 5 == 0) {
            System.out.println("3과 5의 배수입니다.");
        } else {
            System.out.println("3과 5의 배수가 아닙니다.");
        }
    }
}
```

## ✅ 예제: 에버랜드 입장료 계산기
```java
import java.util.Scanner;

public class EverlandTickets {
    public static void main(String[] args) {
        System.out.println("==== 에버랜드에 오신 것을 환영합니다! ====");
        Scanner sc = new Scanner(System.in);
        System.out.print("나이를 입력하세요: ");
        int age = sc.nextInt();
        System.out.print("인원수를 입력하세요: ");
        int num = sc.nextInt();

        int money;
        if (age < 20) {
            money = 5000 / 2 * num;
        } else {
            money = 5000 * num;
        }
        System.out.println("총 " + money + "원입니다.");
    }
}
```

## ✅ 예제: switch 문으로 계절 판단하기 (Java 12+)
```java
import java.util.Scanner;

public class SeasonSwitch {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.print("월 입력: ");
        int month = sc.nextInt();

        switch (month) {
            case 12, 1, 2 -> System.out.println(month + "월은 겨울입니다.");
            case 3, 4, 5 -> System.out.println(month + "월은 봄입니다.");
            case 6, 7, 8 -> System.out.println(month + "월은 여름입니다.");
            case 9, 10, 11 -> System.out.println(month + "월은 가을입니다.");
            default -> System.out.println("올바르지 않은 월입니다.");
        }
    }
}
```

## ✅ 예제: 자판기 프로그램
```java
import java.util.Scanner;

public class VendingMachine {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.print("금액을 입력하세요: ");
        int money = sc.nextInt();

        System.out.println("========== 메뉴 ==========");
        System.out.println("[1] 후라이드 치킨 (10,000원)");
        System.out.println("[2] 양념 치킨 (11,000원)");
        System.out.println("[3] 간장 치킨 (9,000원)");
        System.out.print("메뉴를 고르세요: ");
        int menu = sc.nextInt();

        int change = 0;

        if (menu == 1) {
            change = money - 10000;
            System.out.println("후라이드치킨을 선택하셨습니다.");
        } else if (menu == 2) {
            change = money - 11000;
            System.out.println("양념치킨을 선택하셨습니다.");
        } else if (menu == 3) {
            change = money - 9000;
            System.out.println("간장치킨을 선택하셨습니다.");
        } else {
            System.out.println("잘못된 입력입니다.");
        }

        System.out.println("잔돈: " + change + "원");
        System.out.println("천원: " + change / 1000 + "개");
        System.out.println("오백원: " + (change % 1000) / 500 + "개");
    }
}
```
