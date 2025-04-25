# Java - Method Basics

📅 Date: 2025-04-25

📚 Topic: 자바 메서드 정의, 호출, 약수/완전수 판단 등 다양한 실습

---

## 📌 요약

- 메서드는 하나의 기능 단위를 캡슐화하여 코드의 재사용성과 가독성을 높이는 자바의 기본 구성요소
- `return` 키워드를 통해 값을 반환하거나, `void` 타입으로 단순 동작을 수행할 수도 있음
- 실습을 통해 사용자 입력, 조건 판단, 반복문 등을 함께 사용하는 연습이 중요

---

## ✅ 메서드 기본 구조 및 설명

```java

[접근제한자] static [리턴타입] 메서드명(매개변수) {
    // 로직
    return 반환값;
}

```

| 요소 | 설명 |
| --- | --- |
| 접근제한자 | `public`, `private` |
| static | 정적 메서드. 객체 없이 호출 가능 |
| 리턴타입 | 결과값의 자료형. 없으면 `void` |
| 매개변수 | 메서드 호출 시 전달받는 값 |

---

## ✅ 메서드 정의와 호출

```java

public class Ex01메서드기초 {

    public static void main(String[] args) {
        System.out.println("sum 결과: " + sum(1, 2));
        sumPrint(4, 4);
        System.out.println("이름: " + getName());
        todayWeather();
    }

    // 두 수를 더해 반환
    public static int sum(int num1, int num2) {
        return num1 + num2;
    }

    // 두 수를 더해서 출력만 수행
    public static void sumPrint(int num1, int num2) {
        int result = num1 + num2;
        System.out.println("두 수의 합은 " + result + " 입니다.");
    }

    // 문자열 리턴
    public static String getName() {
        return "깔깔";
    }

    // 리턴도 매개변수도 없는 메서드
    public static void todayWeather() {
        System.out.println("오늘 광주의 날씨는 맑음입니다.");
    }
}

```

---

## ✅ 큰 수 판단 메서드

```java

import java.util.Scanner;

public class Ex02큰수찾기 {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.print("첫 번째 정수 입력: ");
        int num1 = sc.nextInt();
        System.out.print("두 번째 정수 입력: ");
        int num2 = sc.nextInt();

        int result = bigNum(num1, num2);
        System.out.println("입력받은 두 수 중 더 큰 수는 " + result + " 입니다.");

        sc.close(); // Scanner 닫기
    }

    // 두 수 중 큰 수 반환
    public static int bigNum(int num1, int num2) {
        return (num1 > num2) ? num1 : num2;
    }
}

```

---

## ✅ 약수/완전수 판단 메서드

```java

import java.util.Scanner;

public class Ex03약수찾기 {

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.print("정수 입력: ");
        int num = sc.nextInt();

        System.out.println("약수의 총합: " + getSum(num));
        System.out.println("입력된 수가 완전수인가요? " + isPerfect(num));

        sc.close();
    }

    // 약수 총합 구하기 (자기 자신 제외)
    public static int getSum(int num) {
        int sum = 0;
        for (int i = 1; i < num; i++) {
            if (num % i == 0) {
                sum += i;
            }
        }
        return sum;
    }

    // 완전수 판단
    public static boolean isPerfect(int num) {
        return getSum(num) == num;
    }

    // 약수 여부 판단
    public static boolean isDivisor(int num1, int num2) {
        return num1 % num2 == 0;
    }
}

```

---

## ⚠️ 자주 발생하는 메서드 관련 에러 정리

| 에러 메시지 | 원인 | 해결 방법 |
| --- | --- | --- |
| `This method must return a result of type int` | `return`이 없거나 누락됨 | 모든 경로에서 `return` 작성 |
| `missing return statement` | 조건 분기에 따라 `return`이 누락됨 | 모든 if/else 구문에 `return` 포함 |
| `cannot make a static reference to the non-static method` | static 메서드에서 non-static 메서드 호출 | 객체 생성 후 호출 or 메서드를 static으로 변경 |
| `method [메서드명] in class [클래스명] cannot be applied to given types` | 인자 타입 또는 개수 불일치 | 메서드 정의와 호출 인자 확인 |
| `variable might not have been initialized` | 지역 변수 초기화 없이 사용 | 변수 선언 후 초기값 할당 |
| `non-static variable cannot be referenced from a static context` | static 메서드에서 non-static 변수 사용 | 객체 생성 후 접근 or 변수 static으로 변경 |

---