## ✅ 세션(Session) 개념 정리

### 📌 세션이란?

- 사용자가 **웹 브라우저를 닫을 때까지 유지되는 정보 저장 기술**이다.
- 쿠키와 달리 클라이언트가 아닌 **서버에서 정보를 저장**한다.
- 서버는 사용자에게 고유한 `Session ID`를 부여하여 클라이언트를 식별한다.
- JSP에서는 `session` 내장 객체를 통해 별도의 생성 없이 바로 사용 가능하다.

### ✅ 세션의 특징

| 구분 | 내용 |
| --- | --- |
| **저장 위치** | 서버 |
| **보안성** | 쿠키보다 높음 (클라이언트에 노출되지 않음) |
| **용량 제한** | 없음 (Object 타입으로 다양한 값 저장 가능) |
| **유지 시간** | 브라우저 종료 또는 명시적 삭제 전까지 |
| **단점** | 서버 자원을 사용하므로 많은 사용자 접속 시 부하 발생 가능 |

---

## ✅ 세션 생성 페이지

```jsp

<%
	// 세션에 데이터 저장
	session.setAttribute("name", "hello world");
	session.setAttribute("age", 20);
%>

```

- `setAttribute(String key, Object value)` 메서드로 데이터를 저장한다.
- 모든 Java 객체(Object 타입)를 저장할 수 있기 때문에, 기본형은 Wrapper 클래스(Integer 등)로 변환되어 저장됨.
- 문자열에 대해 `URLEncoder` 인코딩은 필요 없다. 서버에 저장되므로 인코딩 문제 없음.

```jsp

<%
	String name = (String) session.getAttribute("name");
	Integer age = (Integer) session.getAttribute("age");
%>
<%= name %>
<%= age %>

```

- `getAttribute(String key)`로 값 조회 시 다운캐스팅 필요 (Object → 원하는 타입으로 변환).
- 출력문 `<%= %>`을 사용하여 값을 페이지에 표시함.

---

## ✅ 세션 확인 페이지 (`ex08GetSession.jsp`)

```jsp

<%
	String name = (String) session.getAttribute("name");
	Integer age = (Integer) session.getAttribute("age");
%>

<%= name %>
<%= age %>

```

- 이미 저장된 세션 정보가 있는지 확인하고 출력하는 페이지.
- `session` 객체는 JSP의 내장 객체로 언제든지 접근 가능하다.

---

## ✅ 세션 삭제 페이지 (`ex08RemoveSession.jsp`)

```jsp

<%
	// 개별 세션 속성 삭제
	session.removeAttribute("name");
	session.removeAttribute("age");

	// 전체 세션 무효화 (모든 속성 제거 및 세션 ID 폐기)
	session.invalidate();
%>

```

- `removeAttribute(String key)`는 해당 key의 세션 속성만 제거한다.
- `invalidate()`는 세션 자체를 무효화하고, 모든 세션 속성과 세션 ID를 삭제한다.
- `invalidate()` 이후에는 더 이상 기존 세션을 사용할 수 없다. 이후 접근 시 새로운 세션이 생성된다.

---

## ✅ 추가 설명: 업캐스팅과 다운캐스팅

- `session.getAttribute()`는 리턴 타입이 `Object`이기 때문에 **형변환**이 필요하다.
- **업캐스팅**: 자식 타입 → 부모 타입 (자동)
- **다운캐스팅**: 부모 타입 → 자식 타입 (명시적 형변환 필요)
- 예:
    
    ```java

    String name = (String) session.getAttribute("name");  // 다운캐스팅 필요
    
    ```
    

---

## ✅ 결론 요약

| 기능 | 메서드 |
| --- | --- |
| 데이터 저장 | `session.setAttribute("key", value)` |
| 데이터 조회 | `session.getAttribute("key")` |
| 특정 값 삭제 | `session.removeAttribute("key")` |
| 전체 세션 삭제 | `session.invalidate()` |

세션은 사용자 인증, 로그인 상태 유지 등에서 매우 자주 사용되는 서버 측 저장 기술이며, **보안성이 높고 다양한 데이터를 저장할 수 있지만**, 서버 부하를 고려해야 한다. 실습을 통해 세션의 생성부터 삭제까지의 흐름을 이해하는 것이 중요하다.