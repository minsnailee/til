### ✅ 1. `ex02datasend.html`

**HTML 폼을 통해 데이터 전송하는 방식 실습**

### ✔ GET 방식

```html

<form action="send" method="get">
	data : <input type="text" name="data">
	<input type="submit" value="get으로 데이터 전송">
</form>

```

- URL에 데이터가 포함됨 (보안에 취약)
- 길이 제한 있음
- 검색이나 즐겨찾기에 유리

### ✔ POST 방식

```html

<form action="send" method="post">
	data : <input type="text" name="data">
	<input type="submit" value="post로 데이터 전송">
</form>

```

- 데이터가 본문(body)에 포함됨 (보안에 상대적으로 강함)
- 데이터 양의 제한이 없음
- 로그인, 회원가입 등에 적합

---

### ✅ 2. `ex03jsp.jsp`

**JSP 문법 기본 구조와 요소 학습**

### ✔ JSP 구성요소 설명

```jsp

<% int num = 10; %>  // 스크립틀릿
<%= num %>           // 표현식 (값 출력)
<%! int num2 = 10; %> // 선언문 (전역 변수)

```

### ✔ 조건문 사용 예시

```jsp

<% if(num % 2 == 0) { %>
  <h1>짝수입니다.</h1>
<% } %>

```

- 스크립틀릿 내에 Java 코드 작성 가능
- HTML과 Java 혼합 가능하지만, 유지보수 어려움

---

### ✅ 3. `ex03jsp2.jsp`

**오류 처리 및 include 사용 실습**

### ✔ 예외 처리 테스트

```

<% int result = 2 / 1; %>  // 0으로 나누면 에러 발생

```

### ✔ 오류 페이지 지정

```

<%@page errorPage="ex04errorpage.jsp"%>

```

### ✔ 다른 JSP 포함

```

<%@include file="ex05footer.jsp"%>

```

---

### ✅ 4. `ex04errorpage.jsp`

**에러 발생 시 보여줄 페이지**

```html

<h3>점검중</h3>
<img src="..."> <!-- 점검 이미지 출력 -->

```

- 실제 오류 발생 시 사용자에게 친절한 메시지를 제공

---

### ✅ 5. `ex05footer.jsp`

**공통 하단 정보 포함 페이지**

```html

<p>기관명 : -</p>
<p>전화번호 : -</p>
<p>주소 : -</p>

```

- 공통 정보는 include 지시자로 다른 페이지에서 재사용

---

### ✅ 6. `ex06taglib.jsp`

**JSTL 및 EL(Expression Language) 실습**

### ✔ JSTL 사용을 위한 taglib 선언

```jsp

<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>

```

### ✔ 반복문

```jsp

<c:forEach begin="1" end="3" step="1">
	<h1>Hello world2</h1>
</c:forEach>

```

### ✔ 조건문 (if)

```jsp

<c:if test="${num1 % 2 == 0}">
	<h1>짝수입니다.</h1>
</c:if>

```

### ✔ choose-when-otherwise (switch-case 유사)

```jsp

<c:choose>
  <c:when test="${num1 % 2 == 1}">
    <h1>홀수입니다.</h1>
  </c:when>
  <c:otherwise>
    <h1>숫자가 아닙니다.</h1>
  </c:otherwise>
</c:choose>

```

### ✔ EL(Expression Language)

- `${}` 안에서 JSP 변수 값을 간단하게 출력 가능
- JSP 코드를 간결하게 만듦

---

### ✅ 기타 정리

- **mavenrepository**: 외부 라이브러리 다운로드 사이트
- **JSTL 1.2 버전**을 다운로드 받아 `WEB-INF/lib` 폴더에 추가
- 앞으로는 직접 라이브러리 복사 대신 Maven 또는 Gradle 사용 예정

---

### 💡 총정리 요약

| 개념 | 설명 |
| --- | --- |
| **GET/POST 전송** | HTML 폼을 통해 서버로 데이터 전송 실습 |
| **JSP 구성 요소** | 지시자, 선언문, 스크립틀릿, 표현식 사용법 |
| **예외 처리** | 에러 페이지 연결 및 include 지시자 사용 |
| **JSTL 활용** | 반복문, 조건문, choose 등 템플릿 방식의 표현 |
| **EL(Expression Language)** | JSP 변수 출력 및 JSTL과 함께 사용 |