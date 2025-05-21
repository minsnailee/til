# 📚 Spring MVC 로그아웃 & 전체 회원 조회 기능 구현 정리

---

## ✅ 1. 프로젝트 개요 (확장)

**기능 목표:**

- **로그아웃 기능**: 로그인 세션 삭제
- **전체 회원 조회**: `admin` 계정 로그인 시 모든 회원 정보를 조회하고 화면에 출력

**사용 기술:**

- Spring MVC (`@RequestMapping`, `HttpSession`, `Model`)
- MyBatis (`Mapper Interface`, `Mapper XML`)
- JSP (`JSTL`, `c:forEach`, `c:if`)
- MySQL
- 컬렉션: `ArrayList`

---

## ✅ 2. 로그아웃 기능 구현 흐름

```

사용자 ➝ header.jsp (로그아웃 버튼 클릭) ➝ MemberController ➝ 세션 초기화 ➝ redirect:/ (홈 이동)

```

---

### 🔹 [1단계] 로그아웃 컨트롤러 구현 (`MemberController.java`)

```java

// 로그아웃 기능 (DB 접근 필요 없음)
@RequestMapping(value = "/logout", method = RequestMethod.GET)
public String logout(HttpSession session) {
    session.removeAttribute("login"); // 세션에서 login 정보 삭제
    return "redirect:/"; // 홈으로 리디렉션
}

```

- `session.removeAttribute("login")`: 로그인 정보 제거
- PRG 패턴 적용: "redirect:/"

---

### 🔹 [2단계] 로그아웃 링크 추가 (`header.jsp`)

```

<a href="logout">로그아웃</a>

```

- 로그인 상태일 때만 보이도록 JSTL로 조건 처리 (이전 정리 참고)

---

## ✅ 3. 전체 회원 조회 기능 구현 흐름

```
pgsql
복사편집
admin 로그인 ➝ header.jsp (전체 회원정보 클릭) ➝ MemberController ➝ MemberMapper ➝ MemberMapper.xml ➝ MySQL ➝ select.jsp 출력

```

---

### 🔹 [1단계] Mapper 인터페이스 수정 (`MemberMapper.java`)

```java

// 전체 회원 조회 기능
ArrayList<MemberVO> searchAllMember();

```

- `ArrayList<MemberVO>`: 여러 명의 회원 정보를 반환하므로 List 형태 사용
- `<MemberVO>`: 컬렉션 내부 타입 지정 (제네릭스 사용)

---

### 🔹 [2단계] SQL 작성 (`MemberMapper.xml`)

```xml

<select id="searchAllMember" resultType="com.user.myapp.model.MemberVO">
    SELECT * FROM MEMBER
</select>

```

- `resultType`은 반드시 VO 클래스 경로로 지정

---

### 🔹 [3단계] 컨트롤러 작성 (`MemberController.java`)

```java

// admin 계정의 전체 회원 정보 조회
@RequestMapping(value="/select", method=RequestMethod.GET)
public String select(Model model) {
    // DB 조회
    ArrayList<MemberVO> list = mapper.searchAllMember();

    // 결과를 model에 저장해서 view에 전달
    model.addAttribute("list", list);

    return "select"; // select.jsp로 이동
}

```

- `Model`: Spring에서 가장 가벼운 request scope용 객체
- `model.addAttribute("list", list)`: JSP에서 `${list}`로 접근 가능

---

### 🔹 [4단계] 전체회원정보 링크 수정 (`header.jsp`)

```

<c:if test="${login.email eq 'admin'}">
    <a href="select">전체 회원 정보</a>
</c:if>

```

- admin 계정만 조회 가능
- `herf` 오타 → `href`로 반드시 수정

---

### 🔹 [5단계] 회원정보 출력 (`select.jsp`)

```

<!-- JSTL forEach를 통해 회원 목록 반복 출력 -->
<c:forEach items="${list}" var="member">
    <tr>
        <td>${member.email}</td>
        <td>${member.tel}</td>
        <td>${member.address}</td>
    </tr>
</c:forEach>

```

- `${list}`: 컨트롤러에서 전달받은 회원 목록
- `${member.xxx}`: 각 회원 객체의 필드 (var="member"에 따라 이름 지정)

---

## ✅ 정리: 기능별 핵심 포인트 요약

| 기능 | 설명 |
| --- | --- |
| 로그아웃 | `session.removeAttribute("login")`으로 세션 초기화 |
| 전체 회원 조회 | admin 계정만 접근 허용, `ArrayList<MemberVO>` 사용 |
| Model 객체 | request 범위 데이터 전달에 최적화 |
| Mapper 연동 | interface와 xml에서 동일한 id 사용 필수 |
| JSTL 반복문 | `<c:forEach items="list" var="member">`로 사용 |
| SQL 쿼리 | `SELECT * FROM MEMBER`로 전체 회원 정보 조회 |

---

## 🛠 작업 순서 요약

1. `MemberMapper.java`: `searchAllMember()` 메서드 추가
2. `MemberMapper.xml`: SQL `<select>` 문 추가
3. `MemberController.java`: `select()` 메서드 추가, `Model` 사용
4. `header.jsp`: 관리자만 전체 회원 정보 메뉴 보이도록 처리
5. `select.jsp`: 회원 정보 출력 테이블 구성
6. `logout()` 메서드 추가, 링크 연결
7. 전체 테스트

---

## 🔍 자주 하는 실수 정리

| 실수 항목 | 설명 |
| --- | --- |
| `herf` 오타 | `href`로 정확히 입력해야 링크 작동 |
| Mapper 인터페이스와 XML 불일치 | id 이름 다르면 동작 안 함 |
| resultType 누락 | MyBatis가 VO로 매핑 못 함 |
| 세션 삭제 안 함 | 로그아웃 시 정보 유지되어 오류 |
| 컬렉션 제네릭 타입 미지정 | `<ArrayList>` → `<ArrayList<MemberVO>>` 필수 |