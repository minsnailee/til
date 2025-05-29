# ✅ Spring Boot 로그인/회원가입 기능 구현 정리

---

## ✅ 8. `register.html` 회원가입 페이지 생성

### 📁 `templates/register.html`

```html

<!-- 생략된 상단 포함 -->

<form action="register.do" method="post">
    <input type="text" name="email" placeholder="example@example.com" required>
    <input type="password" name="pw" placeholder="비밀번호 입력" required>
    <input type="tel" name="tel" placeholder="010-1234-5678" required>
    <input type="text" name="address" placeholder="주소 입력" required>
    <button type="submit" class="btn btn-primary">회원가입</button>
</form>

```

### 설명

- `POST` 방식으로 `register.do`에 데이터 전송
- `이메일`, `비밀번호`, `전화번호`, `주소` 입력받음 

---

## ✅ 9. `MainController.java`에 회원가입 페이지 연결

```java

@GetMapping("/register")
public String register() {
    return "register"; // templates/register.html
}

```

---

## ✅ 10. `MemberEntity.java`에 롬복 추가

```java

@Getter
@Setter
@AllArgsConstructor
@NoArgsConstructor
@Entity
public class MemberEntity {
    // 엔티티 필드 생략
}

```

### 설명

- `@Getter`, `@Setter`: Lombok을 통해 자동 생성
- `@AllArgsConstructor`, `@NoArgsConstructor`: 전체 생성자/기본 생성자 자동 생성

✅ **주의**: Lombok 적용 후에는 **프로젝트를 껐다가 다시 실행**해야 적용됨

→ STS에 Lombok 연동도 잘 확인 (실행파일 다시 더블클릭해서 설치경로 재지정)

---

## ✅ 11. `MemberController.java` - 회원가입 기능 작성

```java

@PostMapping("/register.do")
public String register(@RequestParam String email,
                       @RequestParam String pw,
                       @RequestParam String tel,
                       @RequestParam String address) {
    MemberEntity entity = new MemberEntity();
    entity.setEmail(email);
    entity.setPw(pw);
    entity.setTel(tel);
    entity.setAddress(address);

    memberRepository.save(entity); // insert into DB

    return "redirect:/"; // 첫 화면(index)으로 리다이렉트
}

```

---

## ✅ 12. `MemberRepository.java`에 로그인용 추상 메서드 추가

```java

MemberEntity findByEmailAndPw(String email, String pw);

```

### 설명

- Spring Data JPA는 **메서드 이름만 보고 자동으로 쿼리를 생성**
- 위 메서드는 다음 SQL과 동일한 동작을 함:

```sql

SELECT * FROM member_entity WHERE email = ? AND pw = ?

```

---

## ✅ 13. `MemberController.java` - 로그인 기능 구현

```java

@PostMapping("login.do")
public String login(@RequestParam String email,
                    @RequestParam String pw,
                    HttpSession session) {

    MemberEntity member = memberRepository.findByEmailAndPw(email, pw);

    if (member != null) {
        session.setAttribute("member", member); // 로그인 정보 세션에 저장
        return "loginSuccess"; // 로그인 성공 페이지로 이동
    } else {
        return "redirect:/"; // 실패 시 다시 로그인 화면으로
    }
}

```

---

## ✅ 14. `loginSuccess.html` 로그인 성공 페이지 생성

```html

<!DOCTYPE html>
<html lang="ko" xmlns:th="http://www.thymeleaf.org">
<!-- 생략된 head -->
<p class="fs-4"><strong th:text="${session.member.email}"></strong> 님, 환영합니다 😊</p>

```

### 설명

- `Thymeleaf` 문법 사용
- `${session.member.email}`: 세션에 담긴 사용자 정보 표시

---

## ✅ 15. 새 프로젝트 시작 시 변경 필수 사항

| 항목 | 설명 |
| --- | --- |
| 포트번호 | `application.properties`에서 `server.port=xxxx` 설정 |
| DB 정보 | `spring.datasource.url`, `username`, `password` 설정 |
| JPA 설정 | `ddl-auto`, `MySQLDialect` 꼭 설정 |
| Controller 매핑 | `@GetMapping`, `@PostMapping` 확인 |
| Repository 메서드 | 필요한 메서드 (ex. `findByEmailAndPw`) 미리 정의할 것 |
| HTML 이름 & 경로 확인 | templates 폴더 내 `.html` 파일명과 컨트롤러 반환명 일치시킬 것 |
| Lombok 설정 | 적용 안 되면 STS에서 다시 연동 설정 |

---

## 🔁 실습 전체 흐름 요약

| 단계 | 기능 | 설명 |
| --- | --- | --- |
| 1 | DB 설정 | `.properties`에서 DB 접속 정보 설정 |
| 2 | 엔티티 생성 | `@Entity`, `@Id`, `@Column` 사용 |
| 3 | 레포지토리 생성 | `JpaRepository` 상속 |
| 4 | 컨트롤러 생성 | GET/POST 매핑 |
| 5 | 로그인 페이지 구현 | `index.html` |
| 6 | 회원가입 페이지 구현 | `register.html` |
| 7 | 로그인 기능 구현 | `findByEmailAndPw`, `session` 사용 |
| 8 | 성공 페이지 구현 | `loginSuccess.html`, thymeleaf 적용 |

---

## 📌 자주 묻는 질문

> Q. 이미 있는 테이블 사용 가능할까?
> 
> 
> 🟢 가능. `ddl-auto=none` 또는 `validate`로 설정하고, 엔티티 이름과 필드를 테이블에 맞춰주면 됨.
> 

> Q. SQL문 직접 작성하고 싶다면?
> 
> 
> 🟢 `@Query` 어노테이션을 사용해서 JPQL 또는 네이티브 SQL 작성 가능.
>