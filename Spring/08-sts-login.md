# 📚 Spring MVC 로그인 기능 구현 정리


## ✅ 1. 프로젝트 개요

**목표:**

사용자에게 로그인 정보를 입력받아 MySQL DB에서 사용자 존재 여부 확인 후 로그인 처리하고, 세션에 사용자 정보를 저장한다.

**사용 기술:**

- Spring MVC (Controller, Session)
- MyBatis (Mapper 연동)
- JSP (View, JSTL 사용)
- MySQL (DB)
- HttpSession (세션 처리)

---

## ✅ 2. 로그인 기능 흐름 요약

```

사용자 ➝ header.jsp (로그인 form) ➝ MemberController ➝ MemberVO ➝ MemberMapper(interface) ➝ MemberMapper.xml (SQL) ➝ MySQL

```

---

## ✅ 3. 상세 단계별 설명

---

### 🔹 [1단계] 로그인 폼 만들기 (`header.jsp`)

```jsp

<form action="member/login.do" method="post">
    <li><input type="text" name="email" placeholder="Email을 입력하세요"></li>
    <li><input type="password" name="pw" placeholder="PW를 입력하세요"></li>
    <li><input type="submit" value="Login" class="button fit"></li>
</form>

```

- `action="member/login.do"`: 로그인 요청 경로
- `method="post"`: POST 방식으로 데이터 전송
- `name="email"`, `name="pw"`는 VO 필드명과 일치해야 함

---

### 🔹 [2단계] 컨트롤러로 로그인 처리 (`MemberController.java`)

```java

@RequestMapping(value="/member/login.do", method=RequestMethod.POST)
public String login(@RequestParam("email") String email,
                    @RequestParam("pw") String pw,
                    HttpSession session) {

    MemberVO member = new MemberVO();
    member.setEmail(email);
    member.setPw(pw);

    MemberVO vo = mapper.login(member);

    if(vo != null) {
        System.out.println("로그인 성공");
        session.setAttribute("login", vo);
    } else {
        System.out.println("로그인 실패");
    }

    return "redirect:/";
}

```

- `@RequestParam`: 폼의 값을 파라미터로 받음
- `HttpSession`: 로그인한 사용자 정보를 세션에 저장
- `session.setAttribute("login", vo)`: 로그인 성공 시 VO 객체 저장

---

### 🔹 [3단계] VO 객체 사용 (`MemberVO.java`)

```java

@Getter
@Setter
@AllArgsConstructor
@NoArgsConstructor
public class MemberVO {
    private String email;
    private String pw;
    private String tel;
    private String address;
}

```

- 로그인에 필요한 필드: `email`, `pw`
- VO는 회원가입과 로그인 공통 사용

---

### 🔹 [4단계] Mapper 인터페이스 작성 (`MemberMapper.java`)

```java

@Mapper
public interface MemberMapper {
    MemberVO login(MemberVO vo); // 로그인용
}

```

- 반환타입은 `MemberVO` → 로그인 성공 시 사용자 정보 가져옴

---

### 🔹 [5단계] SQL 쿼리 작성 (`MemberMapper.xml`)

```xml

<select id="login" parameterType="com.user.myapp.model.MemberVO"
        resultType="com.user.myapp.model.MemberVO">
    SELECT * FROM MEMBER WHERE EMAIL = #{email} AND PW = #{pw}
</select>

```

- `parameterType`: VO 객체
- `resultType`: 조회된 결과를 VO에 매핑하기 위한 설정 (반드시 필요!)

---

### 🔹 [6단계] 로그인 상태에 따라 메뉴 다르게 출력 (`header.jsp`)

```jsp

<c:choose>
    <c:when test="${empty login}">
        <a href="#menu">로그인</a>
    </c:when>
    <c:otherwise>
        <c:if test="${login.email eq 'admin'}">
            <a href="#">전체 회원 정보</a>
        </c:if>
        <a href="#">로그아웃</a>
    </c:otherwise>
</c:choose>

```

- `${login}`은 세션에 저장된 VO 객체
- 관리자(admin 계정) 로그인 시 추가 메뉴 출력

---

### 🔹 [7단계] 메인(index.jsp)에서 로그인 여부 확인

```jsp

<c:choose>
    <c:when test="${empty login}">
        <h1>로그인 한 세션아이디를 확인해주세요</h1>
    </c:when>
    <c:otherwise>
        <h1>${login.tel} 환영</h1>
    </c:otherwise>
</c:choose>

```

- `${login.tel}`: 로그인한 사용자 정보 출력
- `${login.email}`로 출력 안 될 경우 → DB 컬럼명과 VO 필드명이 불일치했기 때문

---

### 🔹 [8단계] DB 컬럼 수정 (MySQL)

- `ID` → `EMAIL`로 컬럼명 변경
- SQL문에서도 `ID` → `EMAIL`로 수정해야 VO와 일치

---

## ✅ 정리: 로그인 처리 핵심 포인트

| 항목 | 설명 |
| --- | --- |
| `@RequestParam` | 폼 입력값 컨트롤러에서 받기 |
| `HttpSession` | 로그인 정보를 세션에 저장 |
| `MemberVO` | 이메일과 비밀번호 포함한 사용자 정보 VO |
| `MemberMapper` | 로그인 SQL 호출하는 인터페이스 |
| `login.jsp / header.jsp` | JSTL로 로그인 여부 분기 처리 |
| `resultType` | SQL 결과를 VO로 매핑하기 위한 설정 |
| `redirect:/` | 로그인 후 중복 방지용 PRG 패턴 사용 |
| `admin 계정` | 특정 이메일일 때만 관리자 메뉴 보이게 설정 |

---

## 🛠 작업 순서

1. `MemberVO` 재사용
2. `MemberMapper.java`에 로그인 메소드 작성
3. `MemberMapper.xml`에 SQL 작성 (`EMAIL` 기준 수정)
4. `MemberController`에 `login()` 메소드 작성
5. `header.jsp` 로그인 폼 작성 및 JSTL 분기 처리
6. `index.jsp` 세션 상태 확인하여 사용자 인사말 출력
7. DB 컬럼 확인 및 정정 (EMAIL 사용)
8. 전체 기능 테스트

---

## 🔍 자주 하는 실수 정리

| 실수 항목 | 설명 |
| --- | --- |
| VO 필드명과 DB 컬럼명 불일치 | VO: `email`, DB: `ID` → SQL 에러 발생 |
| `resultType` 누락 | select 결과를 VO로 못 매핑 |
| 세션 저장 안 함 | 로그인 후 정보 유지 안 됨 |
| JSTL 조건문 오타 | `${login}` 대신 `${login.email}`처럼 접근해야 정보 표시 가능 |
| SQL 조건문 오타 | `EMAIL = #{email}` 정확히 작성 |