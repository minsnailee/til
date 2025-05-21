# ✅ STS4 + Spring Boot 환경 설정 가이드

---

## 1. STS4 설치

1. 구글에서 `STS4` 검색 후 공식 사이트에서 다운로드
    
    → 설치 파일 실행 후 설치 진행
    

---

## 2. JDK 17 설치

- **Spring Boot는 JDK 17 이상부터 권장됨**
- JDK 설치 후 환경변수 설정:
    - `JAVA_HOME` 설정
    - `Path`에 `%JAVA_HOME%\bin` 추가

---

## 3. STS 환경 설정

- **워크스페이스 경로에 한글 절대 금지!**
    - 예: `C:\Users\사용자이름\workspace` ❌
    - 예: `C:\workspace\springboot` ✅

---

## 4. 새 프로젝트 생성

1. `File` → `New` → `Spring Starter Project` 클릭
    
    ※ start.spring.io 사이트에 접속해서 설정할 수도 있지만 실습에서는 STS에서 직접 생성
    
2. 설정 예시:
    - **Type**: Maven
    - **Java Version**: 17
    - **Packaging**: Jar
    - **Language**: Java
    - **Name / Group / Artifact / Version**: 필요에 따라 수정
3. Dependencies(필수 라이브러리) 추가:
    - ✅ Spring Boot DevTools
    - ✅ Lombok
    - ✅ Spring Data JPA
    - ✅ MySQL Driver
    - ✅ Thymeleaf
    - ✅ Spring Web

> ⚠️ 추후 라이브러리 추가 시:
> 
> 
> `프로젝트 우클릭 → Add Starters` 로 설치 가능
> 

---

## 5. 기본 프로젝트 구조

- `src/main/java` : Java 코드 작성
- `src/main/resources` : 설정 파일 및 정적 리소스(html, css 등)

---

## 6. HTML 파일 생성 기능 추가

STS 기본 상태에선 HTML 자동완성이 없을 수 있음 → 마켓플레이스에서 추가

1. 메뉴 → `Help` → `Eclipse Marketplace`
2. "web" 검색 → 관련 HTML/CSS/JS 도구 설치

---

## 7. pom.xml 에러 해결

- 처음에 `pom.xml`에서 라이브러리 다운로드 에러 발생 가능
    
    → **Maven 강제 다운로드**
    

```bash

프로젝트 우클릭 → Maven → Update Project (Alt + F5)

```

---

## 8. 인코딩 설정

1. `Window` → `Preferences`
2. 검색창에 `encoding` 입력
3. 아래 항목 모두 `UTF-8`로 변경:
    - Text file encoding
    - Java properties file
    - Java class file

---

## 9. application.properties 설정

```

spring.application.name=SpringBootBasic

# 포트 번호 변경
server.port=8085

```

> 📌 .properties 형식은 key = value,
> 
> 
> `.yaml` 형식은 `key: value` (들여쓰기 중요)
> 

---

## 10. 프로젝트 구성

- `com.user.basic` → 패키지 생성
- `SpringBootBasicApplication.java`
    
    → Spring Boot의 시작점(Main 클래스)
    

---

## 11. Thymeleaf 테스트용 HTML 파일 생성

- `src/main/resources/templates/home.html` 파일 생성
- 예시:

```html

<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>홈페이지</title>
</head>
<body>
    <h1>Spring Boot 시작!</h1>
</body>
</html>

```

---

# ✅ 정리

| 항목 | 설정 내용 |
| --- | --- |
| IDE | STS4 |
| JDK | 17 이상 |
| Build Tool | Maven |
| Encoding | UTF-8 |
| 주요 설정 파일 | application.properties |
| 주요 라이브러리 | DevTools, Lombok, JPA, MySQL, Web, Thymeleaf |
| 주의사항 | 경로에 한글 ❌, pom.xml 강제 업데이트 필요 |