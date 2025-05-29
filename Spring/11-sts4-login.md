## ✅ 1. `SpringBootBasicApplication.java`

```java

@SpringBootApplication
public class SpringBootBasicApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringBootBasicApplication.class, args);
    }
}

```

### 설명

- `@SpringBootApplication`은 아래 3개 어노테이션을 결합한 강력한 어노테이션:
    - `@Configuration` : 설정 파일 역할
    - `@EnableAutoConfiguration` : 자동 설정 (Spring Boot의 핵심 기능)
    - `@ComponentScan` : 하위 패키지 스캔해서 Bean 등록

---

## ✅ 2. 에러 발생 및 DB 설정

### 🔴 에러 메시지

```

Failed to configure a DataSource: 'url' attribute is not specified and no embedded datasource could be configured.

```

### ✅ 해결: `application.properties`에서 DB 설정 추가

```

# 서버 포트 설정
server.port=8085

# DB 설정
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/DB명
spring.datasource.username=사용자명
spring.datasource.password=비밀번호

# JPA 설정
spring.jpa.hibernate.ddl-auto=update
spring.jpa.database-platform=org.hibernate.dialect.MySQL8Dialect
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true

```

---

## ✅ 3. `MainController.java` 생성

```java

package com.user.basic.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class MainController {

    @GetMapping("/")
    public String index() {
        return "index"; // templates/index.html 호출
    }

    @GetMapping("/home")
    public String home() {
        return "home"; // templates/home.html 호출
    }
}

```

### 설명

- `@GetMapping`: GET 요청을 처리.
- `return "파일명"`은 `resources/templates/파일명.html`을 렌더링한다.

---

## ✅ 4. `templates/index.html` 생성

```html

<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>회원가입 / 로그인</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="bg-light">
    <div class="container d-flex justify-content-center align-items-center min-vh-100">
        <div class="card shadow p-4" style="width: 100%; max-width: 400px;">
            <h2 class="mb-4 text-center">회원가입 / 로그인</h2>
            <form action="login.do" method="POST">
                <div class="mb-3">
                    <label for="email" class="form-label">이메일</label>
                    <input type="text" class="form-control" id="email" name="email">
                </div>
                <div class="mb-3">
                    <label for="password" class="form-label">비밀번호</label>
                    <input type="password" class="form-control" id="pw" name="pw">
                </div>
                <div class="d-grid gap-2">
                    <button type="submit" class="btn btn-primary">로그인</button>
                    <button type="button" class="btn btn-secondary">회원가입</button>
                </div>
            </form>
        </div>
    </div>
</body>
</html>

```

### 설명

- Bootstrap을 활용한 깔끔한 로그인/회원가입 UI 구성.

---

## ✅ 5. `MemberEntity.java` 생성

```java

package com.user.basic.entity;

import jakarta.persistence.*;

@Entity
public class MemberEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, unique = true)
    private String email;

    private String pw;
    private String tel;

    @Column(length = 100)
    private String address;

    // Getter/Setter 생략 가능 (롬복 쓰면 간단해짐)
}

```

### 설명

- `@Entity`: 이 클래스는 DB의 테이블로 변환됨.
- `@Id`: Primary Key 설정
- `@GeneratedValue`: 자동 증가
- `@Column`: 제약조건 설정 가능 (ex: `nullable`, `unique`, `length` 등)

---

## ✅ 6. `MemberRepository.java` 생성

```java

package com.user.basic.repository;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import com.user.basic.entity.MemberEntity;

@Repository
public interface MemberRepository extends JpaRepository<MemberEntity, Long> {
    // JpaRepository를 상속받으면 기본 CRUD 메서드 제공됨
}

```

### 설명

- `JpaRepository<T, ID>`를 상속받으면 `findAll()`, `save()`, `deleteById()` 등의 기본 메서드 사용 가능.
- T → 엔티티 클래스명
- ID → PK 타입 (여기선 Long)

---

## ✅ 7. 다음 목표

> 현재까지는 DB 연결 및 기본 구성만 완료했기 때문에 500 에러가 나는 게 정상
> 

- JPA 응용: 실제 DB에 데이터 `삽입`, `조회`
- `회원가입`, `로그인` 기능 완성
- 이후:
    - **게시판** 기능
    - **파일 업로드** 기능
    - **통합 웹 프로젝트 구성**

---

## 🔁 순서 요약

1. `SpringBootBasicApplication.java`에서 `@SpringBootApplication` 구성
2. DB 설정 (`application.properties`)으로 에러 해결
3. `MainController`에서 index, home 요청 처리
4. `index.html` 생성 (Bootstrap 로그인 UI)
5. `MemberEntity` 생성 → DB 테이블과 매핑
6. `MemberRepository` 생성 → DB 접근 설정
7. 서버 실행 → 콘솔에서 테이블 생성 SQL 확인 → 워크벤치에서 확인