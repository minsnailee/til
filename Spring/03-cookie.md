**쿠키의 기본 동작**, 즉 쿠키의 **생성**, **유효기간 설정**, **출력**, **삭제** 과정을 직접 구현함으로써 쿠키의 흐름과 특징을 이해

## ✅ 쿠키 생성 및 출력 페이지

```jsp

<%@page import="java.net.URLEncoder"%>
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Cookie</title>
</head>
<body>
	<h1>Cookie 페이지</h1>

	<%
	// 한글이나 공백이 포함된 문자열을 쿠키에 저장하려면 반드시 URL 인코딩을 해야 한다.
	String data = URLEncoder.encode("first cookie", "UTF-8");

	// 1. 쿠키 객체 생성 - key: "first", value: data
	Cookie cookie = new Cookie("first", data);

	// 2. 쿠키 유지 시간 설정 - 60초 동안 유지
	cookie.setMaxAge(60);

	// 3. 클라이언트에 쿠키 전송
	response.addCookie(cookie);
	%>

	<h2>Cookie 가져오기</h2>
	<%
	// 클라이언트에서 전송된 쿠키들 가져오기
	Cookie[] cookies = request.getCookies();

	// 쿠키 배열을 순회하며 쿠키 이름과 값을 출력
	for(Cookie c : cookies) {
		out.print(c.getName() + "<br>" + c.getValue() + "<br>");
	}
	%>

	<a href="ex07RemoveCookie.jsp">쿠키 삭제하기</a>
</body>
</html>

```

### 📌 설명 요약

- 쿠키는 클라이언트에 저장되며, 요청과 응답을 통해 전달된다.
- 한글이나 특수문자가 포함된 값은 반드시 `URLEncoder.encode()`를 사용하여 인코딩해야 에러(500 오류 등)를 방지할 수 있다.
- `Cookie` 객체는 `(key, value)` 형식으로 생성된다.
- `setMaxAge(int seconds)`를 통해 쿠키의 유지 시간을 초 단위로 설정할 수 있다. 여기서는 60초로 설정하였다.
- 쿠키는 `response.addCookie()`를 통해 클라이언트에 전달된다.
- 클라이언트가 다음 요청을 보낼 때 `request.getCookies()`를 통해 쿠키 정보를 서버로 전송한다.

---

## ✅ 쿠키 삭제 페이지 (`ex07RemoveCookie.jsp`)

```jsp

<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>쿠키 삭제</title>
</head>
<body>
<h1>쿠키 삭제하는 페이지</h1>
<%
	// 쿠키를 삭제하려면 동일한 이름으로 쿠키를 다시 생성하고, 유효기간을 0으로 설정한다.
	Cookie cookie = new Cookie("first", "");

	// 쿠키 유효기간을 0초로 설정하여 브라우저가 즉시 삭제하도록 함
	cookie.setMaxAge(0);

	// 클라이언트에 삭제용 쿠키를 전송
	response.addCookie(cookie);
%>
</body>
</html>

```

### 📌 설명 요약

- JSP에서는 쿠키를 직접적으로 삭제하는 메서드는 없다.
- 쿠키 삭제는 "우회 방법"으로 이루어지며, 삭제하고자 하는 쿠키 이름과 동일한 이름으로 빈 값을 넣고 `setMaxAge(0)`으로 설정하여 삭제를 유도한다.
- 클라이언트가 이 응답을 수신하면 브라우저는 해당 쿠키를 삭제한다.

---

## 💡 추가 설명

### 쿠키의 특징

- **저장 위치**: 서버가 아닌 클라이언트(브라우저)에 저장됨.
- **저장 용량 제한**:
    - 사이트당 약 20개 쿠키
    - 쿠키 1개의 크기: 약 4KB (정확히는 브라우저마다 다르지만 일반적으로 300~4096 byte)
- **보안 이슈**: 클라이언트에 저장되므로 조작 가능성이 있고, 민감한 정보 저장에는 적합하지 않다.
- **브라우저 종속성**: 브라우저를 바꾸면 쿠키가 공유되지 않는다.
- **단방향 저장**: 쿠키는 하나의 key-value만 저장하므로 복잡한 구조에는 부적합하다.

### 인코딩 필요성

- 쿠키에 한글, 공백, 특수문자 등을 포함할 경우 인코딩하지 않으면 HTTP 응답에서 오류가 발생할 수 있다.
- `URLEncoder.encode(String, charset)`을 이용해 반드시 UTF-8로 인코딩해야 브라우저가 제대로 쿠키 값을 인식할 수 있다.

---


실무에서 보안이 중요한 정보는 반드시 **세션** 또는 **서버 저장 방식**을 사용하며, 쿠키는 간단한 사용자 설정이나 UI 상태 저장용도로 주로 사용된다.