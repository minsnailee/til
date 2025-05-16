# JSP에서 Scope를 다루는 실습

스코프란 JSP에서 데이터를 저장할 때, 어디까지 사용할 수 있는가(범위)를 지정하는 개념이다. 

각 스코프는 생명 주기와 접근 가능한 범위가 다르며, 실습에서는 `page`, `request`, `application` 스코프를 다루었다.

---

## ✅ Scope(스코프)란?

> 데이터를 저장할 때, 그 데이터가 어디까지 유지되고 공유될 수 있는지를 결정하는 범위
> 

---

## 📌 1. Page Scope

```jsp

<%
	pageContext.setAttribute("data1", "hello");
	String data1 = (String) pageContext.getAttribute("data1");
%>

```

### ✔ 설명

- **`pageContext`** 객체는 JSP의 내장 객체로, **현재 JSP 페이지 내에서만** 데이터를 유지한다.
- 이 스코프는 하나의 JSP 파일 안에서만 접근 가능하며, **다른 페이지로 이동 시 데이터는 사라진다.**
- `<a href="...">` 태그를 이용해 페이지 이동하면 새 요청으로 간주되므로 이전 pageScope의 데이터는 사용할 수 없다.

### 🧠 특징 요약

| 구분 | 설명 |
| --- | --- |
| 저장 객체 | `pageContext` |
| 유지 범위 | 현재 JSP 파일 내부 |
| 유지 시간 | JSP 실행 종료 시까지 |
| 사용 예 | 페이지 내부에서만 필요한 임시 데이터 |

---

## 📌 2. Request Scope

```jsp

<%
	request.setAttribute("data2", "hello2");
	String data2 = (String) request.getAttribute("data2");
%>

```

### ✔ 설명

- `request` 객체에 데이터를 저장하면, **요청(request)과 응답(response)이 끝날 때까지** 데이터를 유지할 수 있다.
- **포워드(forward)** 를 사용하면 동일한 요청이기 때문에 다음 JSP에서도 데이터를 사용할 수 있다.
- 그러나 **a 태그는 단순한 페이지 이동(Link)이므로 새로운 요청**으로 간주되어 데이터가 사라진다.

```jsp

<jsp:forward page="ex10RequestScope2.jsp"></jsp:forward>

```

- `<jsp:forward>`는 **서버 내부에서 요청을 전달**하므로, `request` 스코프 유지됨.

### 🧠 특징 요약

| 구분 | 설명 |
| --- | --- |
| 저장 객체 | `request` |
| 유지 범위 | 하나의 요청 내 |
| 유지 시간 | 요청 처리 완료 시까지 (`service()` 종료까지) |
| 사용 예 | form 처리, 데이터 전달, 서버 내 forward 등 |

---

## 📌 3. Application Scope

```jsp

<%
	application.setAttribute("data3", "hello3");
%>

```

### ✔ 설명

- **`application` 객체**는 모든 사용자(브라우저)와 모든 요청에 대해 공통으로 접근할 수 있는 전역 저장소이다.
- 웹 애플리케이션이 종료되거나 서버가 재시작되기 전까지 데이터가 유지된다.
- 사용자 수와 관계없이 공유되므로 **공용 데이터 저장소**로 활용된다.

```jsp

<%
	String data3 = (String) application.getAttribute("data3");
%>

```

- 다른 JSP에서도 `application.getAttribute()`로 접근 가능하다.

### 🧠 특징 요약

| 구분 | 설명 |
| --- | --- |
| 저장 객체 | `application` |
| 유지 범위 | 전체 웹 애플리케이션 |
| 유지 시간 | 서버 종료 시까지 |
| 사용 예 | 공용 설정 값, 방문자 수, 글로벌 공지 등 |

---

## ✅ 정리 비교표

| Scope 종류 | 저장 객체 | 유지 범위 | 유지 시간 | 사용 예시 |
| --- | --- | --- | --- | --- |
| page | `pageContext` | 현재 JSP 페이지 | JSP 실행 종료 시 | 단순 출력, 페이지 내 임시 데이터 |
| request | `request` | 요청 내 (forward 포함) | 요청 처리 완료 시 | form 처리, 데이터 전달 |
| session | `session` | 브라우저 단위 | 브라우저 종료 or 명시적 삭제 시 | 로그인 정보, 사용자 데이터 |
| application | `application` | 전체 애플리케이션 | 서버 종료 or 명시적 삭제 시 | 공용 설정, 방문자 수 집계 등 |

---

## ✅ 정리

이번 실습을 통해 JSP에서 데이터를 저장할 수 있는 다양한 스코프(Page, Request, Application)를 직접 사용하며, 각 스코프의 범위와 생명 주기 차이를 이해할 수 있다.

특히, `forward` 사용 시 Request Scope가 유지되는 것과 `a 태그`로 이동 시 Request Scope가 새로 시작되는 것을 구분하는 것이 중요하다.

필요한 경우에는 데이터 저장 범위에 따라 알맞은 스코프를 선택하여 JSP 애플리케이션의 효율적인 데이터 흐름을 구성해야 한다.