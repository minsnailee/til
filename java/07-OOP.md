# 📘 Java OOP 기초 정리

📅 Date: 2025-04-25

📚 Topic: Java 객체지향 프로그래밍(Object-Oriented Programming, OOP)

---

## 🔸 객체지향 프로그래밍 (OOP) 이란?

> 객체(Object)를 중심으로 프로그램을 구성하는 방식
> 
> 
> 데이터(필드)와 기능(메서드)을 하나로 묶어 현실 세계를 모델링함
> 

---

## 🧱 클래스와 객체

| 구분 | 설명 |
| --- | --- |
| 클래스 | 객체를 만들기 위한 **설계도** |
| 객체 | 클래스를 바탕으로 생성된 **메모리 상의 실체** |
| 필드 | 객체가 가지는 **데이터(속성)** |
| 메서드 | 객체가 수행할 수 있는 **기능(행동)** |

예시:

```java
class Dog {
    String name;
    void bark() {
        System.out.println("멍멍!");
    }
}

```

---

## 🔑 OOP의 4대 특성

| 특성 | 설명 |
| --- | --- |
| 캡슐화 | 데이터 + 메서드를 하나로 묶고, 외부에 구현 세부를 감춤 |
| 상속 | 기존 클래스의 속성과 기능을 자식 클래스가 물려받음 |
| 추상화 | 핵심 정보만 추출, 복잡한 내부는 숨김 |
| 다형성 | 동일한 이름의 메서드가 다양한 방식으로 동작함 (`오버로딩`, `오버라이딩`) |

---

## 🐶 실습: `Puppy` 클래스

### 📄 `Puppy.java`

```java
public class Puppy {
    // 1. 필드
    String name;
    String type;
    int age;
    String color;

    // 2. 기본 생성자 (자동으로 제공되지만, 명시해도 됨)
    public Puppy() {}

    // 3. 매개변수 생성자
    public Puppy(String name, int age, String color, String type) {
        this.name = name;
        this.age = age;
        this.color = color;
        this.type = type;
    }

    // 4. 메서드
    public void eat() {
        System.out.println(name + "가 밥을 먹어요!");
    }

    public void walk() {
        System.out.println(name + "가 산책을 해요~");
    }
}

```

---

### 📄 `Main.java`

```java
public class Main {
    public static void main(String[] args) {
        // 기본 생성자 사용
        Puppy p1 = new Puppy();
        p1.name = "퍼피";
        p1.age = 1;
        p1.color = "흰색";
        p1.type = "말티푸";

        // 매개변수 생성자 사용
        Puppy p2 = new Puppy("블루", 1, "흰색", "비숑");

        // 출력
        System.out.println("p1 정보: " + p1.name + ", " + p1.age + ", " + p1.color + ", " + p1.type);
        System.out.println("p2 정보: " + p2.name + ", " + p2.age + ", " + p2.color + ", " + p2.type);

        // 메서드 호출
        p1.eat();
        p2.walk();
    }
}

```

---

## 🧠 생성자(Constructor) 정리

| 종류 | 설명 |
| --- | --- |
| 기본 생성자 | 매개변수 없음. 자바가 자동 생성 (직접 생성자 만들면 없어짐!) |
| 매개변수 생성자 | 필드를 초기화하기 위해 사용자가 정의한 생성자 |
| this 키워드 | 현재 객체 자신을 가리킴 (필드와 매개변수 이름이 같을 때 구분용) |

예시:

```java
public Puppy(String name) {
    this.name = name;
}

```

> ✅ 주의: 매개변수 생성자를 만들면 기본 생성자는 자동으로 없어짐 → 꼭 필요하면 직접 만들어야 함
> 

---

## 💻 자바 클래스 설계 순서

| 단계 | 설명 |
| --- | --- |
| 1️⃣ | 만들 대상 정하기 (예: 강아지, 학생, 책 등) |
| 2️⃣ | 클래스 만들기 (`Puppy`) |
| 3️⃣ | 필드 작성 (예: 이름, 나이, 품종 등) |
| 4️⃣ | 생성자 만들기 (기본 생성자 + 매개변수 생성자) |
| 5️⃣ | 메서드 작성 (예: eat(), walk()) |
| 6️⃣ | `Main`에서 객체 생성 및 활용 |

---

## 🧠 메모리 구조: 스택 vs 힙

| 항목 | 스택(Stack) | 힙(Heap) |
| --- | --- | --- |
| 저장 | 지역 변수, 매개변수 | `new`로 생성된 객체 |
| 소멸 시점 | 메서드 종료 시 자동 소멸 | 가비지 컬렉터가 제거 |
| 속도 | 빠름 | 상대적으로 느림 |
| 위치 | 메서드 실행 시 생성 | `new` 사용 시 생성 |

예시 코드:

```java
Dog d = new Dog();
d.name = "멍멍이";

```

- `d`: 스택에 저장 (참조 변수)
- `Dog` 객체: 힙에 생성
- `d`는 힙의 `Dog` 객체 주소를 참조

---

## 🧹 가비지 컬렉션(GC)

- **힙에 있는 객체**는 사용이 끝났다고 자동 삭제되지 않음
- **더 이상 참조하지 않는 객체**는 자바의 **가비지 컬렉터(GC)** 가 알아서 정리함

---

## ✅ 핵심 요약

| 개념 | 요약 설명 |
| --- | --- |
| 클래스 | 객체의 설계도 |
| 객체 | 클래스를 바탕으로 생성된 실체 |
| 생성자 | 객체 생성 시 자동 호출되는 특수 메서드 |
| this | 현재 객체 자신을 가리키는 키워드 |
| 캡슐화 | 데이터와 메서드를 묶고, 외부 접근을 제한 |
| 메모리 | 스택(변수 저장), 힙(객체 저장) |