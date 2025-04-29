# 📘 Java 상속(Inheritance) 실습 정리

📅 **2025-04-29**

Java의 상속 개념과 다형성을 두 가지 실습(`한식당 예제`, `닌텐도 게임 예제`)를 통해 실습하고 정리

---

## ✅ 실습 1: `restaurant` - 한식당 예제

### 📁 클래스 구조

```
restaurant/
├── Parent.java
├── Child.java
└── Main.java
```

---

### 🛠 코드 작성 순서

1. **`Parent` 클래스 작성**
    
    → 공통 기능인 `makeKimchi()`, `makeJang()` 메서드를 구현
    
    → `restaurant/Parent.java`
    
2. **`Child` 클래스 작성**
    
    → `extends Parent`로 상속
    
    → `makeKimchi()` 오버라이딩, `makeJeyuk()` 추가
    
    → `restaurant/Child.java`
    
3. **`Main` 클래스 작성 및 테스트**
    
    → `Parent`, `Child` 객체 생성 및 메서드 호출 테스트
    
    → 업캐스팅과 다운캐스팅 동작 확인
    
    → `restaurant/Main.java`
    

---

### 1. Parent.java

```java
package restaurant;

public class Parent {
	public void makeKimchi() {
		System.out.println("김치찌개를 만든다");
	}

	public void makeJang() {
		System.out.println("된장찌개를 끓인다");
	}
}
```

- 기본 한식당 클래스
- 두 가지 요리 기능: `makeKimchi()`, `makeJang()`

---

### 2. Child.java (상속 + 오버라이딩)

```java
package restaurant;

public class Child extends Parent {
	public void makeJeyuk() {
		System.out.println("제육볶음을 볶는다");
	}

	@Override
	public void makeKimchi() {
		System.out.println("돼지고기를 듬뿍 넣은 김치찌개를 끓인다");
	}
}
```

- `extends Parent` 를 통해 상속
- 부모의 `makeKimchi()` 메서드를 오버라이딩 (재정의)
- `makeJeyuk()`은 자식만의 고유 메서드

---

### 3. Main.java (업캐스팅/다운캐스팅 실습)

```java
package restaurant;

public class Main {
	public static void main(String[] args) {
		Parent p = new Parent();
		p.makeJang();     // 된장찌개를 끓인다
		p.makeKimchi();   // 김치찌개를 만든다

		Child c = new Child();
		c.makeJang();     // 상속받은 된장찌개
		c.makeKimchi();   // 재정의된 김치찌개
		c.makeJeyuk();    // 제육볶음

		System.out.println("=== 업캐스팅 ===");
		Parent p2 = new Child(); // 업캐스팅 (자동)
		p2.makeJang();           // 부모 메서드
		p2.makeKimchi();         // 오버라이딩된 자식 메서드 (다형성)

		System.out.println("=== 다운캐스팅 ===");
		Child c2 = (Child) p2;   // 다운캐스팅 (강제)
		c2.makeJeyuk();          // 자식만의 고유 메서드 사용 가능
	}
}
```

---

### 🔍 주요 개념 정리

| 개념 | 설명 |
| --- | --- |
| **상속** | `Child extends Parent` : 부모의 메서드를 자식이 그대로 사용 가능 |
| **오버라이딩** | `makeKimchi()`를 자식 클래스에서 재정의 (`@Override` 사용) |
| **업캐스팅** | `Parent p = new Child();` 자식 → 부모 타입으로 자동 형변환 |
| **다운캐스팅** | `Child c = (Child) p2;` 부모 → 자식 타입으로 강제 형변환 |
| **다형성** | 업캐스팅된 객체에서 자식의 오버라이딩 메서드가 실행되는 성질 |

---

## ✅ 실습 2: `닌텐도게임` - 다형성 응용 예제

### 📁 클래스 구조

```
닌텐도게임/
├── GameChip.java
├── Pokemon.java
├── Zelda.java
├── Mario.java
└── Main.java
```

---

### 🛠 코드 작성 순서

1. **`GameChip` 부모 클래스 작성**
    
    → 공통 메서드 `gameStart()` 정의
    
    → `닌텐도게임/GameChip.java`
    
2. **자식 클래스들 작성 (`Pokemon`, `Zelda`, `Mario`)**
    
    → 각각 `extends GameChip`
    
    → `gameStart()` 오버라이딩
    
    → 각각 `닌텐도게임/Pokemon.java`, `Zelda.java`, `Mario.java`
    
3. **`Main` 클래스 작성 및 다형성 테스트**
    
    → 부모 타입인 `GameChip` 배열 또는 변수로 자식 객체 관리
    
    → `insert()` 메서드로 다형성 실습
    
    → `닌텐도게임/Main.java`
    

---

### 1. GameChip.java (공통 규격 클래스)

```java
package 닌텐도게임;

public class GameChip {
	public void gameStart() {
		System.out.println("게임 시작");
	}
}
```

- 닌텐도에 들어가는 모든 게임칩의 **부모 클래스**
- `gameStart()`는 공통 인터페이스로 정의됨

---

### 2. 자식 게임칩 클래스들 (오버라이딩 활용)

### Pokemon.java

```java
package 닌텐도게임;

public class Pokemon extends GameChip {
	@Override
	public void gameStart() {
		System.out.println("포켓몬 게임 시작");
	}
}
```

### Zelda.java

```java
package 닌텐도게임;

public class Zelda extends GameChip {
	@Override
	public void gameStart() {
		System.out.println("젤다 게임 시작");
	}
}
```

### Mario.java

```java
package 닌텐도게임;

public class Mario extends GameChip {
	@Override
	public void gameStart() {
		System.out.println("마리오 게임 시작");
	}
}
```

---

### 3. Main.java (다형성을 통한 게임 실행)

```java
package 닌텐도게임;

public class Main {
	public static void main(String[] args) {
		GameChip p = new Pokemon();
		GameChip z = new Zelda();
		GameChip m = new Mario();

		insert(p); // 포켓몬 게임 시작
		insert(z); // 젤다 게임 시작
		insert(m); // 마리오 게임 시작
	}

	private static void insert(GameChip g) {
		g.gameStart();
	}
}
```

---

### 🎯 다형성의 핵심

- `insert(GameChip g)` 메서드 하나로 어떤 게임이든 실행 가능
- 각각의 `gameStart()` 메서드는 자식 클래스에서 오버라이딩된 버전이 호출됨
- 동일한 형태의 호출 → 서로 다른 실행 결과

---

## 🧠 정리

| 항목 | 내용 |
| --- | --- |
| 상속 | 코드 재사용 및 기능 확장 가능 |
| 오버라이딩 | 부모 메서드를 자식이 재정의하여 다르게 동작시킴 |
| 업캐스팅 | 자식 → 부모로 자동 형변환 (다형성 활용 가능) |
| 다운캐스팅 | 부모 → 자식으로 강제 형변환 (주의 필요) |
| 다형성 | 부모 타입으로 여러 자식 객체를 통합하여 처리 가능 |

---