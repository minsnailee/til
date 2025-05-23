# 📚 Spring MVC 회원가입 기능 구현 정리

Spring MVC + MyBatis + JSP + MySQL 기반 회원가입 구현 실습 내용

## ✅ 1. 프로젝트 개요

**목표**: 사용자로부터 회원정보를 입력받아, MySQL DB에 회원정보를 저장하는 기능 구현

**사용 기술**:

- Spring MVC (Controller, ViewResolver 등)
- MyBatis (ORM, SQL 분리)
- JSP (View)
- MySQL (DB)
- Maven (의존성 관리)
- HikariCP (DB 커넥션 풀)

---

## ✅ 2. 회원가입 흐름 요약

```

사용자 ➝ header.jsp(회원가입 form) ➝ MemberController ➝ MemberVO ➝ MemberMapper(interface) ➝ MemberMapper.xml(SQL) ➝ MySQL

```

---

## ✅ 3. 상세 단계별 설명

---

### 🔹 [1단계] JSP 회원가입 폼 만들기 (`header.jsp`)

### 📄 파일: `header.jsp`

```jsp

<form action="member/join.do" method="post">
    <li><input type="text" name="email" placeholder="Email을 입력하세요"></li>
    <li><input type="password" name="pw" placeholder="PW를 입력하세요"></li>
    <li><input type="text" name="tel" placeholder="전화번호를 입력하세요"></li>
    <li><input type="text" name="address" placeholder="집주소를 입력하세요"></li>
    <li><input type="submit" value="JoinUs" class="button fit"></li>
</form>

```

- `form`의 `action="member/join.do"`: 요청을 `MemberController`의 `/member/join.do`로 보냄
- `method="post"`: POST 방식으로 데이터 전송
- `input`의 `name` 속성은 VO와 일치해야 함

---

### 🔹 [2단계] 컨트롤러로 요청 받기 (`MemberController.java`)

### 📄 파일: `MemberController.java`

```java

@Autowired
MemberMapper mapper; // Mapper 인터페이스 주입

@RequestMapping(value="/member/join.do", method = RequestMethod.POST)
public String join(@RequestParam("email") String email,
                   @RequestParam("pw") String pw,
                   @RequestParam("tel") String tel,
                   @RequestParam("address") String address) {

    // 1. 콘솔 확인 (테스트용)
    System.out.println("email : " + email);
    System.out.println("pw : " + pw);
    System.out.println("tel : " + tel);
    System.out.println("address : " + address);

    // 2. VO 객체 생성 (파라미터 묶기)
    MemberVO member = new MemberVO(email, pw, tel, address);

    // 3. Mapper 호출 (DB 연동)
    int cnt = mapper.join(member);

    if(cnt > 0) {
        return "index"; // 성공 시 메인 페이지로 이동
    } else {
        return ""; // 실패 시 에러페이지 (혹은 예외처리)
    }
}

```

redirect:/로 바꿔준다.

```java

    if(cnt > 0) {
        return "redirect:/"; // 성공 시 메인으로 리디렉션 (GET)
    } else {
        return "error"; // 실패 시 에러 페이지 보여주기
    }

```

### ✅ `return "index";` 와 `return "redirect:/";`의 차이

### 🔹 `return "index";`

- 이건 **JSP 페이지를 "forward" 방식으로 이동**시키는 것.
- 실제로는 **/WEB-INF/views/index.jsp**를 직접 보여주는 것.
- 클라이언트(URL)는 그대로 있고 서버 내에서 view를 렌더링함.

📌 예:

```java

return "index";

```

결과:

- 브라우저 주소: 그대로 `/member/join.do`
- 보여지는 화면: `/WEB-INF/views/index.jsp`

### 🔹 `return "redirect:/";`

- 이건 **클라이언트에게 새로 "요청을 보내라"고 지시**하는 것.
- 즉, **브라우저가 다시 `/` 경로로 GET 요청**을 보내도록 만듦.
- 결과적으로 **MainController의 "/" mapping이 실행**됨.

📌 예:

```java

return "redirect:/";

```

결과:

- 브라우저 주소: `/`로 바뀜
- MainController에서 index.jsp로 이동됨

---

### ✅ 왜 `redirect:/`가 더 좋은가?

### 🔹 사용자가 회원가입 POST 후 새로고침하면?

- `return "index";`로 하면 `POST` 요청을 다시 보냄 (데이터 재전송 발생)
- 사용자가 실수로 **F5 누르면 같은 회원가입이 또 시도됨**
    
    → **중복 가입 문제**
    

📌 이를 **PRG 패턴**(Post-Redirect-Get)이라고 부릅니다.

> ✅ PRG 패턴 =
> 
> 1. POST로 데이터 전송
> 2. 서버는 처리 후 `redirect:`로 GET 페이지로 이동시킴
> 3. 새로고침해도 POST가 다시 안 일어남

---

### ✅ 정리: 언제 `redirect:`를 써야 할까?

| 상황 | 사용 예 | 설명 |
| --- | --- | --- |
| POST 후 페이지 이동 | `redirect:/` | 데이터 중복 방지 (PRG 패턴) |
| 단순 JSP 보기 | `return "index"` | 그냥 보여주기만 할 때 (GET 요청) |
| 다른 컨트롤러 메서드 실행 유도 | `redirect:/some.do` | 특정 URL로 브라우저 리디렉션 |

---


이렇게 해야:

- 회원가입이 성공했을 때 **POST → GET 흐름이 되고**
- 브라우저 주소도 `/`로 깔끔하게 보이고
- 새로고침해도 중복가입 안 일어남


---

- `@RequestParam`: form에서 입력받은 값 가져오기
- `MemberVO` 생성 후 `mapper.join()` 호출로 DB 저장

---

### 🔹 [3단계] VO 객체 만들기 (`MemberVO.java`)

### 📄 파일: `MemberVO.java`

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

- lombok을 사용해 getter/setter 자동 생성
- `@AllArgsConstructor` + `@NoArgsConstructor`: 생성자 자동생성

---

### 🔹 [4단계] Mapper 인터페이스 만들기 (`MemberMapper.java`)

### 📄 파일: `MemberMapper.java`

```java

@Mapper
public interface MemberMapper {
    int join(MemberVO vo); // MemberMapper.xml과 연결될 메소드
}

```

- `@Mapper`: MyBatis가 이 인터페이스를 Mapper로 인식
- 메서드명은 `MemberMapper.xml`의 `<insert id="join">`과 일치해야 함

---

### 🔹 [5단계] SQL 작성 (`MemberMapper.xml`)

### 📄 파일: `MemberMapper.xml`

```xml

<mapper namespace="com.user.myapp.mapper.MemberMapper">
    <insert id="join" parameterType="com.user.myapp.model.MemberVO">
        INSERT INTO MEMBER VALUES(#{email}, #{pw}, #{tel}, #{address})
    </insert>
</mapper>

```

- `namespace`: 인터페이스와 동일해야 연결됨
- `parameterType`: VO 클래스 경로
- `#{}` 안의 이름은 VO 필드명과 일치해야 함

---

### 🔹 [6단계] DB 설정 (`root-context.xml`)

### 📄 주요 설정

```xml

<!-- HikariCP DB 설정 -->
<bean id="hikariConfig" class="com.zaxxer.hikari.HikariConfig">
    <property name="driverClassName" value="com.mysql.cj.jdbc.Driver" />
    <property name="jdbcUrl" value="jdbc:mysql://localhost:~" />
    <property name="username" value="-" />
    <property name="password" value="-" />
</bean>

<bean id="dataSource" class="com.zaxxer.hikari.HikariDataSource" destroy-method="close">
    <constructor-arg ref="hikariConfig" />
</bean>

<!-- SqlSessionFactoryBean -->
<bean class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource" />
</bean>

<!-- Mapper Scan -->
<mybatis-spring:scan base-package="com.user.myapp.mapper" />

```

---

### 🔹 [7단계] View Resolver 설정 (`servlet-context.xml`)

```xml

<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/views/" />
    <property name="suffix" value=".jsp" />
</bean>

```

- Controller에서 return `"index"` → 실제 경로 `/WEB-INF/views/index.jsp`로 이동

---

### 🔹 [8단계] 라우팅 설정 (`web.xml`)

```xml

<servlet>
    <servlet-name>appServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/spring/appServlet/servlet-context.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
</servlet>

<servlet-mapping>
    <servlet-name>appServlet</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>

```

- Spring DispatcherServlet 설정
- 모든 요청이 Controller로 매핑됨

---

### 🔹 [9단계] pom.xml 설정

MyBatis, Spring, MySQL, HikariCP, JSTL 등 필요한 라이브러리 모두 등록되어 있음.

특히 확인할 것:

```xml

<dependency>
    <groupId>com.zaxxer</groupId>
    <artifactId>HikariCP</artifactId>
    <version>4.0.3</version>
</dependency>

```

---

## ✅ 전체 흐름 요약 다이어그램

```

1. header.jsp (회원가입 form 입력)
          ↓
2. MemberController.join()
   - @RequestParam으로 입력값 받음
   - MemberVO 객체 생성
   - mapper.join(member) 호출
          ↓
3. MemberMapper (interface)
   - join(MemberVO vo) 선언
          ↓
4. MemberMapper.xml
   - <insert id="join"> SQL 실행
          ↓
5. MySQL DB에 INSERT

```

---

## 🛠 추천 작업 순서 (실습 시 참고)

1. ✅ `MemberVO` 생성 (데이터 담을 객체)
2. ✅ `MemberMapper.java` 인터페이스 생성
3. ✅ `MemberMapper.xml` SQL 파일 생성
4. ✅ `header.jsp`에서 form 작성
5. ✅ `MemberController`에서 join 메소드 작성
6. ✅ DB 연결 (`root-context.xml`)
7. ✅ 뷰 리졸버, 라우팅 설정 확인 (`web.xml`, `servlet-context.xml`)
8. ✅ 실행 및 테스트

---

## ✅ 확인 방법

### 회원가입 입력 후 콘솔에 출력

```java

System.out.println("email : " + email);
// 실제로 콘솔에 email, pw, tel, address가 잘 찍히면 form -> Controller 연결 확인 완료

```

---

## 🔍 실습 시 자주 하는 실수

| 실수 항목 | 설명 |
| --- | --- |
| `form action` 경로 오타 | `/member/join.do` 정확히 맞춰야 Controller에서 받음 |
| VO 필드와 input name 불일치 | `name="email"` ↔ `MemberVO.email` 일치 필요 |
| mapper.xml의 `namespace` 불일치 | `namespace="com.user.myapp.mapper.MemberMapper"` 정확 |