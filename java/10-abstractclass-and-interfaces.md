# 📘 Java Abstract Classes & Interfaces

---

- 추상 클래스(`abstract class`)와 인터페이스(`interface`)의 개념과 차이 이해
- 추상 메서드를 상속받는 자식 클래스에서의 **강제 구현**
- **다형성**, **업캐스팅/다운캐스팅**, **인터페이스 다중 구현** 실습
- 실제 월급계산 프로그램을 통한 설계 기반 실습

---

## ✅ 월급 계산 프로그램 실습

### 구조

```
월급계산프로그램/
├── Employee.java          ← 추상 클래스
├── RegularEmployee.java   ← 정규직 (추상 클래스 상속 + 인터페이스 구현)
├── TempEmployee.java      ← 계약직
├── PartTimeEmployee.java  ← 아르바이트
├── Temp.java              ← 인터페이스
├── Temp2.java             ← 인터페이스 2 (다중 구현 테스트용)
└── Main.java              ← 실행 및 테스트
```

---

## 🛠️ 코드 작성 순서

---

설계 → 상속 → 인터페이스 구현 → 실행

### ✅ 1단계. 공통 기능 정의하기 (추상 클래스)

🔹 `Employee.java`

- **공통 필드**(`empno`, `name`, `pay`) 작성
- **공통 메서드**인 `print()` 작성
- **월급 계산은 직원마다 다르므로** → `getMoneyPay()`를 **추상 메서드로 선언**
- 추상 메서드가 있으므로 클래스도 `abstract`로 선언
- 공통 필드 초기화를 위한 **생성자** 작성

```java
public abstract class Employee {
    String empno;
    String name;
    int pay;

    public Employee(String empno, String name, int pay) {
        this.empno = empno;
        this.name = name;
        this.pay = pay;
    }

    public abstract int getMoneyPay();

    public String print() {
        return empno + " : " + name + " : " + pay;
    }
}
```

---

### ✅ 2단계. 인터페이스 만들기 (공통 동작 정의)

🔹 `Temp.java`, `Temp2.java`

- `Temp` 인터페이스는 `temp()` 메서드 강제 구현 요구
- 실습용으로 `Temp2`도 만들어두기 (다중 구현 확인)

```java
public interface Temp {
    public abstract void temp();
}

public interface Temp2 {
    // 구현 내용 없음, 다중 인터페이스 실습용
}
```

---

### ✅ 3단계. 정규직 클래스 작성 (상속 + 인터페이스 구현)

🔹 `RegularEmployee.java`

- `Employee`를 상속받고, `Temp`, `Temp2`를 구현
- 보너스(bonus) 필드를 추가
- `getMoneyPay()`와 `temp()` 메서드 반드시 구현

```java
public class RegularEmployee extends Employee implements Temp, Temp2 {
    int bonus;

    public RegularEmployee(String empno, String name, int pay, int bonus) {
        super(empno, name, pay);
        this.bonus = bonus;
    }

    @Override
    public int getMoneyPay() {
        return (pay + bonus) / 12;
    }

    @Override
    public String print() {
        return empno + " : " + name + " : " + pay;
    }

    @Override
    public void temp() {
        // 인터페이스 구현
    }
}
```

---

### ✅ 4단계. 계약직, 아르바이트 클래스 작성

🔹 `TempEmployee.java`

- 연봉(pay)을 12로 나눠 월급 계산

🔹 `PartTimeEmployee.java`

- 일당(pay) × 근무일수(workDay)

> 이 두 클래스는 인터페이스를 구현하지 않고, Employee만 상속받습니다.
> 

```java
public class TempEmployee extends Employee {
    public TempEmployee(String empno, String name, int pay) {
        super(empno, name, pay);
    }

    @Override
    public int getMoneyPay() {
        return pay / 12;
    }

    @Override
    public String print() {
        return empno + " : " + name + " : " + pay;
    }
}
```

```java
public class PartTimeEmployee extends Employee {
    int workDay;

    public PartTimeEmployee(String empno, String name, int pay, int workDay) {
        super(empno, name, pay);
        this.workDay = workDay;
    }

    @Override
    public int getMoneyPay() {
        return pay * workDay;
    }

    @Override
    public String print() {
        return empno + " : " + name + " : " + pay;
    }
}
```

---

### ✅ 5단계. `Main.java`에서 테스트 실행

- 각 클래스별로 객체를 생성
- `print()`와 `getMoneyPay()` 호출
- 익명 클래스 방식으로 인터페이스도 실습

```java
public class Main {
    public static void main(String[] args) {
        RegularEmployee r = new RegularEmployee("001", "홍길동", 6000, 1200);
        TempEmployee t = new TempEmployee("002", "홍길순", 4800);
        PartTimeEmployee p = new PartTimeEmployee("003", "홍길남", 400, 10);

        System.out.println(r.print() + " 월급: " + r.getMoneyPay());
        System.out.println(t.print() + " 월급: " + t.getMoneyPay());
        System.out.println(p.print() + " 월급: " + p.getMoneyPay());

        // 인터페이스 익명 클래스 구현
        Temp temp = new Temp() {
            @Override
            public void temp() {
                System.out.println("익명 클래스에서 temp() 구현");
            }
        };
        temp.temp();
    }
}
```

---

## 📌 정리 - 추천 작성 순서 요약

| 순서 | 파일명 | 작성 내용 요약 |
| --- | --- | --- |
| 1️⃣ | `Employee.java` | 추상 클래스 생성, 공통 필드와 추상 메서드 작성 |
| 2️⃣ | `Temp.java` `Temp2.java` | 인터페이스 정의 (공통 동작 강제) |
| 3️⃣ | `RegularEmployee.java` | 상속 + 인터페이스 다중 구현 |
| 4️⃣ | `TempEmployee.java` | 계약직 클래스 정의 |
| 5️⃣ | `PartTimeEmployee.java` | 아르바이트 클래스 정의 |
| 6️⃣ | `Main.java` | 객체 생성, 테스트 실행, 인터페이스 테스트 |

---

## 🧩 `Employee.java` (추상 클래스)

```java
public abstract class Employee {
	String empno;  // 사번
	String name;   // 이름
	int pay;       // 연봉 또는 일당

	// ▶ 추상 메서드: 메서드의 틀만 정의하고 기능(내용)은 없음.
	// 자식 클래스에서 반드시 "오버라이딩" 하여 구현해야 함.
	public abstract int getMoneyPay();

	// ▶ 일반 메서드: 자식 클래스에서 그대로 쓸 수도 있고, 오버라이딩해도 됨.
	public String print() {
		return empno + " : " + name + " : "+ pay;
	}

	// ▶ 생성자: 자식 클래스에서 super()로 호출됨.
	public Employee(String empno, String name, int pay) {
		this.empno = empno;
		this.name = name;
		this.pay = pay;
	}
}
```

### 📌 설명

- `abstract class`는 **불완전한 클래스**, 객체 생성 불가
- `getMoneyPay()`는 **추상 메서드** → 자식 클래스에서 반드시 오버라이딩
- 공통 필드: `empno`, `name`, `pay`
- 공통 메서드: `print()`는 자식 클래스에서 그대로 사용할 수도 있음

---

## 🧩 `RegularEmployee.java` (정규직 + 인터페이스 구현)

```java
public class RegularEmployee extends Employee implements Temp, Temp2 {
	String empno;
	String name;
	int pay;
	int bonus;

	public RegularEmployee(String empno, String name, int pay, int bonus) {
		// 부모(Employee)의 생성자 호출 → 공통 필드 초기화
		super(empno, name, pay);
		this.bonus = bonus; // 정규직만 가지는 보너스 필드
	}

	@Override
	public int getMoneyPay() {
		// 연봉 + 보너스를 12개월로 나눈 값
		return (pay + bonus) / 12;
	}

	@Override
	public String print() {
		return empno + " : " + name + " : " + pay;
	}

	@Override
	public void temp() {
		// 인터페이스 Temp에서 요구하는 메서드를 반드시 구현해야 함.
		// 기능은 없지만 인터페이스 규칙상 반드시 존재해야 함.
	}
}
```

### 📌 개념 정리

- `implements Temp, Temp2` → **인터페이스 다중 구현**
- `Employee` 추상 클래스 상속 → `getMoneyPay()` 반드시 구현
- `Temp` 인터페이스 → `temp()` 메서드도 강제 구현됨

---

## 🧩 `TempEmployee.java` (계약직)

```java
public class TempEmployee extends Employee {
	String empno;
	String name;
	int pay;

	public TempEmployee(String empno, String name, int pay) {
		super(empno, name, pay);
	}

	@Override
	public int getMoneyPay() {
		// 계약직은 연봉을 12개월로 나눠서 계산
		return pay / 12;
	}

	@Override
	public String print() {
		return empno + " : " + name + " : " + pay;
	}
}
```

- 단순한 계약직 클래스
- 보너스 없음, pay를 12개월로 나눠 월급 계산

---

## 🧩 `PartTimeEmployee.java` (아르바이트)

```java
public class PartTimeEmployee extends Employee {
	String empno;
	String name;
	int pay;
	int workDay;  // 일한 일수

	public PartTimeEmployee(String empno, String name, int pay, int workDay) {
		super(empno, name, pay);
		this.workDay = workDay;
	}

	@Override
	public int getMoneyPay() {
		// 일당(pay) × 근무일수
		return pay * workDay;
	}

	@Override
	public String print() {
		return empno + " : " + name + " : " + pay;
	}
}
```

- **pay × 일한 일수**로 월급 계산

---

## 🧩 `Temp.java` (인터페이스)

```java
public interface Temp {
	// 인터페이스 안에서는 상수만 필드로 선언 가능
	public static final int num = 0;

	// 추상 메서드: 내용 없이 메서드 틀만 제공
	public abstract void temp();

	// Java 8 이후 지원: 일반 메서드도 가능하지만 static 또는 default 붙어야 함
	static void temp2() {
		System.out.println("정적 메서드");
	}
}
```

---

## 🧩 `Temp2.java` (두 번째 인터페이스)

```java
public interface Temp2 {
	// 실습 목적으로 만든 인터페이스 (Temp와 함께 다중 구현 실습)
}
```

---

## 🧩 `Main.java` (실행 테스트)

```java
public class Main {
	public static void main(String[] args) {
		// 각각의 직원 타입으로 객체 생성
		RegularEmployee r1 = new RegularEmployee("250908", "홍길동", 6000, 0);
		TempEmployee t1 = new TempEmployee("250908", "홍길남", 6000);
		PartTimeEmployee p1 = new PartTimeEmployee("250908", "홍길순", 400, 4);

		// 각 객체의 정보 출력
		System.out.println(r1.print());  // 사번 : 이름 : 연봉
		System.out.println(t1.print());
		System.out.println(p1.print());

		// 인터페이스는 객체 생성 불가 → 익명 클래스(Anonymous Inner Class) 사용
		Temp t = new Temp() {
			public void temp() {
				// 인터페이스의 메서드를 직접 구현
				System.out.println("익명 객체의 temp 구현");
			}
		};
		t.temp();  // "익명 객체의 temp 구현" 출력
	}
}

```

- 인터페이스는 `new Temp()`처럼 직접 객체 생성이 불가능
- 대신 익명 클래스를 사용해 1회성 구현 가능 (→ **람다식**으로도 가능)
- `System.out.println(...)`으로 결과 출력 확인

---

## 📌 정리

| 구분 | 추상 클래스 (`abstract`) | 인터페이스 (`interface`) |
| --- | --- | --- |
| 객체 생성 | ❌ 직접 생성 불가 | ❌ 직접 생성 불가 (익명 내부 클래스로 사용 가능) |
| 필드 선언 | 일반 변수 사용 가능 | 상수 (`public static final`)만 가능 |
| 메서드 | 일반 + 추상 메서드 가능 | 추상 메서드만 가능 (Java 8 이후 static, default 추가 가능) |
| 상속/구현 개수 | 단일 상속만 가능 (`extends`) | 다중 구현 가능 (`implements`) |
| 사용 목적 | 공통 기능 설계 (일부 구현 포함) | 구현 강제 + 설계 명세 |
| 대표적 키워드 | `abstract class`, `abstract method` | `interface`, `implements` |

---

## 🧠 정리

- **추상 클래스**는 공통된 필드와 일부 기능을 정의할 수 있는 부모 설계도이며, 단일 상속만 가능하다.
- **인터페이스**는 기능(메서드)만 정의하며, 다중 구현이 가능하고 모든 메서드는 반드시 구현해야 한다.
- `implements`는 인터페이스의 메서드를 자식 클래스에서 **강제적으로 오버라이딩**하게 한다.
- 익명 클래스는 **일회성 구현**이 필요할 때 유용하며, 인터페이스나 추상 클래스의 임시 구현에 적합하다.