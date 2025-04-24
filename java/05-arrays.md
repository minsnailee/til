
# 📘 Java - Arrays & 2D Arrays

> 📅 Date: 2025-04-24  
> 📚 Topic: 자바 배열과 이차원 배열 기초, 활용 및 실습 예제

---

## 📌 요약

- **배열 (Array)**: 같은 자료형의 데이터를 하나로 묶어서 관리하는 자료구조  
- **이차원 배열 (2D Array)**: 배열 안에 또 다른 배열이 있는 형태 (행과 열)  
- 배열은 레퍼런스(참조) 타입이기 때문에 주소값을 가진다  
- `new` 키워드를 사용해서 배열을 생성하고, 인덱스로 각 요소에 접근  

---

## ✅ 배열 기초 설명 및 기본 사용법

```java
int[] numbers = new int[3];
System.out.println(numbers); // 주소값 출력
System.out.println("데이터 할당 전 " + numbers[0]);
numbers[0] = 1;
System.out.println("데이터 할당 후 " + numbers[0]);
```

> 배열 생성 시 기본값이 자동 할당됨  
(int → 0, double → 0.0, boolean → false, String → null)

---

## ✅ 배열과 for문을 활용한 홀수 찾기

```java
int[] arr = new int[5];
Random rd = new Random();
int cnt = 0;

for (int i = 0; i < arr.length; i++) {
    arr[i] = rd.nextInt(100) + 1; // 1~100 사이
    if (arr[i] % 2 == 1) {
        System.out.print(arr[i] + " ");
        cnt++;
    }
}
System.out.println("이며, 총 " + cnt + "개 입니다.");
```

---

## ✅ 배열을 활용한 점수 계산 프로그램

```java
int[] arr = new int[5];
Scanner sc = new Scanner(System.in);

for (int i = 0; i < arr.length; i++) {
    System.out.print(i+1 + "번째 입력 >> ");
    arr[i] = sc.nextInt();
}
System.out.println("입력된 점수: " + Arrays.toString(arr));

// 최고점수 찾기
int max = arr[0];
for (int i = 0; i < arr.length; i++) {
    if (arr[i] > max) max = arr[i];
}

// 최저점수 찾기
int min = arr[0];
for (int i = 0; i < arr.length; i++) {
    if (arr[i] < min) min = arr[i];
}

System.out.println("최고 점수: " + max);
System.out.println("최저 점수: " + min);

// 총합과 평균
int sum = 0;
for (int i = 0; i < arr.length; i++) {
    sum += arr[i];
}
double avg = (double)sum / arr.length;
System.out.println("총합: " + sum);
System.out.println("평균: " + avg);
```

---

## ✅ 이차원 배열 기본 구조와 값 할당

```java
int[][] arr = new int[3][3];
int num = 1;

for (int j = 0; j <= 2; j++) {
    for (int i = 0; i <= 2; i++) {
        arr[j][i] = num++;
        System.out.println(arr[j][i]);
    }
    System.out.println(" ");
}
```

---

## ✅ 이차원 배열 실습 - 4x4 배열 채우기

```java
int[][] arr = new int[4][4];
int num = 1;

for (int j = 0; j <= 3; j++) {
    for (int i = 0; i <= 3; i++) {
        arr[j][i] = num++;
        System.out.print(arr[j][i] + " ");
    }
    System.out.println(" ");
}
```
