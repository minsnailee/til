# 📘 Java - 클래스, 캡슐화, 생성자, 객체 배열, ArrayList

📅 Date: 2025-04-28

📚 Topic: 클래스, 캡슐화, 생성자, 객체 배열, ArrayList

---

## 💡 주요 개념 정리

- 클래스와 캡슐화: `private` 필드 + `getter/setter` 사용
- 생성자: 객체 초기화의 편리함
- 객체 배열: 고정된 크기의 참조형 배열
- ArrayList: 유동적인 객체 저장 구조
- for-each 문 활용

---

## 📁 실습 코드 구조

### 1. `객체배열/Pokemon.java`

```java
package 객체배열;

public class Pokemon {
	private String name;
	private String type;
	private int hp;
	private int atk;
	private String skill;

	public Pokemon(String name, String type, int hp, int atk, String skill) {
		this.name = name;
		this.type = type;
		this.hp = hp;
		this.atk = atk;
		this.skill = skill;
	}

	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getType() {
		return type;
	}
	public void setType(String type) {
		this.type = type;
	}
	public int getHp() {
		return hp;
	}
	public void setHp(int hp) {
		this.hp = hp;
	}
	public int getAtk() {
		return atk;
	}
	public void setAtk(int atk) {
		this.atk = atk;
	}
	public String getSkill() {
		return skill;
	}
	public void setSkill(String skill) {
		this.skill = skill;
	}
}

```

---

### 2. `객체배열/Main.java`

```java
package 객체배열;

public class Main {
	public static void main(String[] args) {
		Pokemon[] bag = new Pokemon[3];

		Pokemon pika = new Pokemon("피카츄", "전기", 1000, 120, "백만볼트");
		Pokemon lizard = new Pokemon("리자드", "불", 1000, 90, "불대문자");
		Pokemon aboke = new Pokemon("아보크", "독", 1000, 80, "몸통박치기");

		bag[0] = pika;
		bag[1] = lizard;
		bag[2] = aboke;

		// for-each 문으로 출력
		for (Pokemon p : bag) {
			System.out.println(p.getName() + "\t" + p.getType() + "\t" + p.getHp() + "\t" + p.getAtk() + "\t" + p.getSkill());
		}
	}
}

```

---

### 3. `student/Student.java`

```java
package student;

public class Student {
	private String name;
	private int age;

	public Student(String name, int age) {
		this.name = name;
		this.age = age;
	}

	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		this.age = age;
	}
}

```

---

### 4. `student/Main.java`

```java
package student;

import java.util.ArrayList;

public class Main {
	public static void main(String[] args) {
		Student s1 = new Student("홍길동", 20);

		ArrayList<String> list = new ArrayList<>();
		list.add("홍길동");
		list.add("홍길순");
		list.add("홍길원");

		list.add(1, "홍길남");
		list.remove(1);
		System.out.println("list의 크기 : " + list.size());
		list.clear();

		ArrayList<Student> studentList = new ArrayList<>();
		studentList.add(s1);
		studentList.add(new Student("홍길치", 30));
		studentList.add(new Student("가나다", 15));
		studentList.add(new Student("라마바", 25));

		for (Student s : studentList) {
			System.out.println(s.getName());
		}
	}
}

```

---

## ✅ 요약

- 클래스는 상태(필드)와 동작(메서드)을 하나로 묶는다.
- `private` 키워드는 외부 직접 접근을 막고, `getter/setter`로 간접 접근을 제공한다.
- 객체 배열은 고정 크기, `ArrayList`는 동적 크기로 관리된다.
- `for-each` 문은 배열 및 리스트 순회에 가장 직관적이다.