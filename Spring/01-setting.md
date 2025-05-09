**이클립스(Eclipse)와 톰캣(Tomcat) 환경 셋팅 및 서블릿(Servlet) 기본 흐름**

---

## ✅ 1. **Eclipse 작업 공간 변경**

- 기존 프로젝트가 남아있을 경우 새로운 워크스페이스를 설정.
- `File > Switch Workspace > Other` 클릭 → 새 경로 지정.

---

## ✅ 2. **Dynamic Web Project 생성**

- `File > New > Dynamic Web Project` 선택.
- 프로젝트 이름 입력 후 `Finish`.

---

## ✅ 3. **Tomcat 9 다운로드 및 설정**

- [Tomcat 9 다운로드 (ZIP)](https://tomcat.apache.org/download-90.cgi) → 압축 해제.
- Eclipse에서 `Servers` 탭 → `New Server` 선택.
- Tomcat 9 선택 후 경로에 압축 푼 Tomcat 폴더 지정 → `Finish`.

---

## ✅ 4. **서버 시작 오류 해결 (포트 변경)**

- 기본 포트 `8080`이 이미 사용 중일 경우 에러 발생.
- `Servers` 탭 → 서버 우클릭 → `Open` → Port를 `8081`로 변경.
- 다시 서버 시작 → 상태가 `Started`로 바뀜.

---

## ✅ 5. **Servlet 생성 및 설정**

- `Java Resources > src/main/java` 우클릭 → `New > Servlet` 선택.
- 클래스 이름 및 패키지 정보 입력 후 `Next`.
- URL Mapping 확인 (연습용은 기본값 사용 가능).
- 생성할 메서드(`doGet`, `doPost`) 선택 후 `Finish`.

---

## ✅ 6. **라이브러리 설정 (서버 런타임 추가)**

- 에러 발생 시:
    - 프로젝트 우클릭 → `Properties > Java Build Path > Libraries > Add Library`.
    - `Server Runtime` 선택 → 설치된 Tomcat 추가.
    - `Apply and Close` 후 서버 다시 시작.

---

## ✅ 7. **Run on Server 실행 오류 해결**

- Run on Server에서 서버 선택 항목이 안 뜨는 경우:
    - 프로젝트 우클릭 → `Properties > Project Facets`.
    - `Dynamic Web Module`, `Java`, `JavaScript` 등 설정 확인 후 저장.
    - 서버 리스타트.

---

## ✅ 8. **Tomcat server.xml 설정**

- `server.xml` 파일의 `<Connector>` 태그에서 포트 및 인코딩 설정 가능:

```xml

<ConnectorconnectionTimeout="20000"
  maxParameterCount="1000"
  port="8081"
  protocol="HTTP/1.1"
  redirectPort="8443"
  URIEncoding="EUC-kr"/>

```

---

## ✅ 9. **웹 기본 개념 복습**

- **HTTP**: 클라이언트와 서버 간의 통신 프로토콜.
- **URL**: 자원의 위치.
- **Client / Server 구조**: 브라우저(클라이언트) → 서버 요청 → 서버 → 응답.
- **Request (요청)**: GET, POST 등.
- **Response (응답)**: HTML, JSON 등 포맷으로 반환.
- **Static Web Page**: 고정된 내용의 페이지 (HTML 등).
- **Dynamic Web Page**: 사용자 요청, 시간 등에 따라 내용이 바뀌는 페이지 (JSP, Servlet 등).
- **Servlet**: 자바로 동적인 웹페이지를 만드는 서버 사이드 컴포넌트.

---

## 🔚 요약

- 이 수업은 Spring Boot 기반 개발을 위한 **환경 셋업 중심 수업**이었으며, Eclipse, Tomcat 설정 및 서블릿을 활용한 웹 페이지 구동 흐름을 실습함.
- 주요 개념과 도구들의 연동 방식, 오류 해결 방법까지 익힘으로써 **Spring 기반 웹 프로젝트의 기초 준비 완료**.