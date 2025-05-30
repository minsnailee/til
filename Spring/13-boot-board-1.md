# 📌 Spring Boot 회원가입 + 로그인 + 아이디 중복 체크 정리

## ✅ 전체 개발 흐름

### 1. 📁 프로젝트 기본 구조 설정

- SpringBootBoard 프로젝트 생성
- `src/main/java` 아래에 패키지 구조 생성
    
    예: `com.user.board.controller`, `repository`, `entity`, `service`
    

---

### 2. 🧭 메인 페이지 및 이동 컨트롤러 설정 (MainController)

```java

@Controller
public class MainController {
    @GetMapping("/") // 메인 페이지
    public String index() {
        return "index";
    }

    @GetMapping("/login") // 로그인 페이지 이동
    public String login() {
        return "login";
    }

    @GetMapping("/register") // 회원가입 페이지 이동
    public String register() {
        return "register";
    }
}

```

---

### 3. 👤 회원가입 처리 (UserController)

```java

@Controller
public class UserController {
    @Autowired
    private UserService userService;

    @PostMapping("/register.do")
    public String register(@RequestParam String id,
                           @RequestParam String pw,
                           @RequestParam String name,
                           @RequestParam int age) {

        UserEntity entity = new UserEntity();
        entity.setId(id);
        entity.setPw(pw);
        entity.setName(name);
        entity.setAge(age);

        String result = userService.register(entity);

        if(result.equals("success")) {
            return "redirect:/login";
        } else {
            return "redirect:/register";
        }
    }
}

```

---

### 4. 🧱 Entity 정의 (UserEntity)

```java

@Entity
@Data
@Table(name="user")
public class UserEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long idx; // 객체 타입 사용 권장(Long)

    @Column(nullable = false, unique = true)
    private String id;

    private String pw;
    private String name;
    private Integer age;
}

```

> 📝 Long, Integer 등의 객체 타입을 사용하는 이유는 null 값을 처리할 수 있기 때문이다. 기본형은 null을 허용하지 않는다.
> 

---

### 5. 📂 데이터베이스 연결 인터페이스 (UserRepository)

```java

@Repository
public interface UserRepository extends JpaRepository<UserEntity, Long> {
    boolean existsById(String id); // 아이디 중복 체크
}

```

> JpaRepository<T, ID>에서 T는 엔티티 클래스, ID는 PK 타입이다.
> 

---

### 6. ⚙ 서비스 클래스 정의 (UserService)

```java

@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    // 회원가입 기능
    public String register(UserEntity entity) {
        UserEntity e = userRepository.save(entity);
        return (e != null) ? "success" : "fail";
    }

    // 아이디 중복 체크 기능
    public boolean check(String id) {
        return userRepository.existsById(id);
    }
}

```

> 서비스는 컨트롤러와 레포지토리 사이의 중간층 역할. 비즈니스 로직을 처리한다.
> 

---

### 7. 🌐 REST 컨트롤러 (UserRestController - AJAX용)

```java

@RestController
public class UserRestController {

    @Autowired
    private UserService userService;

    @GetMapping("/user/check-id")
    public HashMap<String, Boolean> checkId(@RequestParam String id) {
        boolean exist = userService.check(id);

        HashMap<String, Boolean> res = new HashMap<>();
        res.put("exist", exist);

        return res;
    }
}

```

> @RestController는 JSON 형태로 데이터를 반환한다.
> 

---

### 8. 🖥️ 화면 파일 (register.html – 아이디 중복 & 비밀번호 확인 포함)

```html

<!-- 생략된 부분 있음, 핵심 기능 위주 -->
<script>
    // 아이디 중복 체크
    function checkId(){
        const id = $("#id").val();
        if(id){
            $.ajax({
                url: '/user/check-id',
                method: "GET",
                data: {"id" : id},
                success: function(response){
                    if(response.exist){
                        $("#idMessage").text("이미 사용 중인 아이디입니다.").css("color", "red");
                    } else {
                        $("#idMessage").text("사용 가능한 아이디입니다!").css("color", "green");
                    }
                },
                error: function(){
                    console.log("실패");
                }
            });
        }
    }

    // 비밀번호 확인
    $("#pwConfirm").on("input", () => {
        const pw = $("#pw").val();
        const pwConfirm = $("#pwConfirm").val();
        const pwMsg = $("#pwMessage");

        if(pw === pwConfirm){
            pwMsg.text("일치합니다").css({'color':"blue"});
        } else {
            pwMsg.text("비밀번호가 다릅니다.").css({'color':"red"});
        }
    });
</script>

```

---

## ⚠️ 잘못된 부분 / 헷갈릴 수 있는 설명 정리

| 위치 | 기존 설명 | 올바른 설명 |
| --- | --- | --- |
| `UserEntity` | `private long idx` → 기본형 | `Long`처럼 객체형을 사용해야 null 처리가 가능하고 제너릭에도 쓸 수 있음 |
| `UserRepository` | `void existsById` | `boolean existsById`가 맞음, 반환이 있으니까 |
| `Service가 없으면 db 연결이 한번밖에 안된다` | ❌ 애매한 표현 | 서비스는 여러 비즈니스 로직을 조합하거나 유지보수 용이성을 위해 사용됨 |
| `@Autowired UserRepository` 주석 | `DB 연결 -- repository 객체 생성 X` | `X`가 아니라 **객체 생성은 Spring이 대신 해주는 것** |
| 제너릭 설명 | `기본형 불가` | ✔️ 제너릭은 참조형(예: Boolean, Integer)만 사용 가능. `boolean`, `int`는 불가 |

---

## ✅ 한눈에 보는 기능 흐름도

```

[회원가입 화면]
   ↓ (입력 후 버튼 클릭)
[UserController]
   ↓
[UserService.register()]
   ↓
[UserRepository.save()] → DB 저장

[아이디 중복 체크 (AJAX)]
   ↓
[UserRestController]
   ↓
[UserService.check()]
   ↓
[UserRepository.existsById()] → true/false 반환

```