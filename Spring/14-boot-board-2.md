## ✅ 1. 로그인 기능: 로그인하면 로그아웃 버튼으로 변경되도록

### 📌 흐름 설명

로그인에 성공하면 `session`에 사용자 정보를 저장하고, 화면에서 세션에 사용자가 존재하는지 여부를 확인해 로그인 또는 로그아웃 버튼을 조건부로 표시하도록 한다.

---

### 📂 login.html → UserController.java

### 📄 login.html

사용자가 아이디와 비밀번호를 입력하고 `login.do`로 POST 요청을 보낸다.

### 📄 UserController.java

```java

@PostMapping("login.do")
public String login(@RequestParam String id, @RequestParam String pw, HttpSession session) {
    UserEntity user = userService.login(id, pw); // 사용자 정보 확인
    if (user != null) {
        session.setAttribute("user", user); // 로그인 성공 시 세션에 저장
        return "redirect:/"; // 메인 페이지로 이동
    } else {
        return "redirect:/login"; // 실패 시 로그인 페이지로 재이동
    }
}

```

세션을 통해 로그인 상태를 유지한다.

---

### 📄 UserService.java

```java

public UserEntity login(String id, String pw) {
    return userRepository.findAllByIdAndPw(id, pw); // DB에서 사용자 조회
}

```

서비스에서는 아이디와 비밀번호로 사용자를 조회하는 로직을 실행한다.

---

### 📄 header.html

```html

<button th:if="${session.user == null}" class="btn btn-light" onclick="location.href='login'">로그인</button>
<button th:if="${session.user != null}" class="btn btn-light" onclick="location.href='logout'">로그아웃</button>

```

Thymeleaf에서 세션에 `user`가 존재하는지에 따라 버튼이 다르게 표시된다.

---

### 📄 로그아웃 기능

```java

@GetMapping("/logout")
public String logout(HttpSession session) {
    session.removeAttribute("user"); // 세션에서 사용자 정보 제거
    return "redirect:/";
}

```

로그아웃 시에는 세션에서 사용자 정보를 제거하고 메인 페이지로 돌아간다.

---

## ✅ 2. 게시판 기능: 글쓰기 및 목록 출력

---

### 📌 글쓰기 (write.html → BoardController.java)

### 📄 write.html

```html

<form action="/board/write" method="post" enctype="multipart/form-data">
	<input type="text" name="title" required>
	<textarea name="content" required></textarea>
	<input type="file" name="image">
	<button type="submit">등록</button>
</form>

```

사용자가 제목, 내용, 이미지를 입력하여 폼을 제출한다.

---

### 📄 BoardController.java

```java

@PostMapping("/write")
public String write(@RequestParam String title, @RequestParam String content,
                    HttpSession session, @RequestParam MultipartFile image) {

    String imgPath = "";
    String uploadDir = fileUploadconfig.getUploadDir();

    if (!image.isEmpty()) {
        String fileName = UUID.randomUUID() + "_" + image.getOriginalFilename();
        String filePath = Paths.get(uploadDir, fileName).toString();
        try {
            image.transferTo(new File(filePath));
        } catch (IOException e) {
            e.printStackTrace();
        }
        imgPath = "/uploads/" + fileName;
    }

    BoardEntity board = new BoardEntity();
    board.setTitle(title);
    board.setContent(content);
    board.setImgPath(imgPath);

    UserEntity user = (UserEntity) session.getAttribute("user");
    board.setWriter(user.getId());

    boardService.write(board); // DB 저장
    return "redirect:/";
}

```

이미지 파일은 서버의 지정된 폴더에 저장되며, 게시글과 함께 작성자의 정보도 저장된다.

---

### 📄 BoardService.java

```java

public BoardEntity write(BoardEntity entity) {
    return boardRepository.save(entity); // DB에 저장
}

```

서비스에서 엔티티를 받아서 저장소에 전달한다.

---

### 📌 글 목록 출력 (index.html → MainController.java)

### 📄 index.html

```html

<tr th:each="board : ${boardList}">
	<td th:text="${board.id}">1</td>
	<td><a href="#" th:text="${board.title}">제목</a></td>
	<td th:text="${board.writer}">작성자</td>
	<td th:text="${board.writeDay}">작성일</td>
</tr>

```

모델에 전달된 게시글 리스트를 반복문으로 출력한다.

```html

<button th:if="${session.user != null}" class="btn btn-success" onclick="location.href='/write'">글쓰기</button>

```

세션에 사용자가 존재할 경우에만 글쓰기 버튼이 보인다.

---

### 📄 MainController.java

```java

@GetMapping("/")
public String index(Model model) {
    ArrayList<BoardEntity> list = boardService.selectAll();
    model.addAttribute("boardList", list);
    return "index";
}

```

서비스에서 게시글 전체 목록을 받아서 화면에 전달한다.

---

## ✅ 3. 이미지 업로드 기능

---

### 📌 파일 저장 설정

### 📄 application.properties

```

file.upload-dir=C:/upload/

```

파일을 저장할 경로를 지정한다.

---

### 📄 FileUploadConfig.java

```java

@Configuration
public class FileUploadConfig {
    @Value("${file.upload-dir}")
    private String uploadDir;
    public String getUploadDir() {
        return uploadDir;
    }
}

```

설정값을 읽어서 컨트롤러에서 사용할 수 있도록 한다.

---

### 📄 WebConfig.java

```java

@Override
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    registry.addResourceHandler("/uploads/**")
            .addResourceLocations("file:C:/upload/");
}

```

브라우저가 업로드된 이미지를 `/uploads/` 경로를 통해 접근할 수 있도록 설정한다.

---

## ✅ 4. 회원가입 기능: 아이디 중복 체크 + 비밀번호 일치 확인

---

### 📌 register.html의 기능 설명

### 아이디 중복 체크

```jsx

$.ajax({
    url: '/user/check-id',
    method: "GET",
    data: { "id": id },
    success: function (response) {
        if (response.exist) {
            $('#idMessage').text('이미 사용중인 아이디입니다.').css('color', 'red');
        } else {
            $('#idMessage').text('사용 가능한 아이디입니다.').css('color', 'blue');
        }
    }
});

```

사용자가 입력한 아이디가 DB에 이미 존재하는지 서버에 요청하여 메시지를 출력한다.

---

### 비밀번호 확인

```jsx

$("#pwConfirm").on("input", () => {
    const pw = $("#pw").val();
    const pwConfirm = $("#pwConfirm").val();
    if (pw === pwConfirm) {
        pwMsg.text("일치합니다").css('color', 'blue');
    } else {
        pwMsg.text("비밀번호가 다릅니다.").css('color', 'red');
    }
});

```

입력된 비밀번호와 확인 비밀번호가 같은지 실시간으로 체크한다.

---

## ✅ 전체 흐름 요약

```

[회원가입]
register.html → UserController.register() → UserService → UserRepository.save()

[로그인]
login.html → UserController.login() → UserService → DB 조회 → 세션 저장

[로그아웃]
→ 세션 삭제

[글쓰기]
write.html → BoardController.write() → 이미지 저장 → DB 저장

[글목록]
index.html → MainController.index() → BoardService → boardList 출력

[이미지 업로드]
multipart/form-data로 업로드 → C:/upload/에 저장 → "/uploads/**" 경로로 브라우저 접근 가능

```