## ✅ 5. 게시글 상세보기, 수정, 삭제, 검색 기능

---

### 📌 상세보기 (게시글 클릭 시 상세 페이지로 이동)

### 📄 index.html

```html
<td><a th:href="@{board/detail/{id}(id=${board.id})}" th:text="${board.title}">제목</a></td>

```

- 각 게시글 제목 클릭 시 `/board/detail/{id}`로 이동하며 해당 게시글의 ID를 넘긴다.

---

### 📄 BoardController.java

```java
@GetMapping("/detail/{id}")
public String detail(@PathVariable Long id, Model model) {
    Optional<BoardEntity> detail = boardService.detailPage(id);
    model.addAttribute("detail", detail.get());
    return "detail";
}

```

- `id`를 통해 게시글 조회 후 `detail.html`에 데이터 전달.

---

### 📄 detail.html

```html
<h3 th:text="${detail.title}"></h3>
<p th:text="${detail.content}"></p>

<img th:if="${detail.imgPath != null}" th:src="@{${detail.imgPath}}" class="img-fluid" />

<div th:if="${session.user != null and session.user.id == detail.writer}">
    <button th:onclick="|location.href='/board/edit/${detail.id}'|">수정</button>
    <button onclick="deleteBoard()">삭제</button>
</div>

```

- 로그인한 사용자가 작성자일 경우에만 "수정/삭제" 버튼 보임.

---

### 📌 게시글 수정

### 📄 BoardController.java

```java
@GetMapping("/edit/{id}")
public String edit(@PathVariable Long id, Model model) {
    Optional<BoardEntity> board = boardService.detailPage(id);
    model.addAttribute("board", board.get());
    return "edit";
}

```

- 수정 폼에 기존 게시글 정보를 채워서 보여줌.

```java
@PostMapping("/update")
public String update(@RequestParam String title, @RequestParam String content,
                     @RequestParam MultipartFile image, @RequestParam String oldImgPath,
                     @RequestParam Long id) {

    BoardEntity entity = boardService.detailPage(id).get();
    String uploadDir = fileUploadconfig.getUploadDir();

    // 새 이미지가 있다면 기존 이미지 삭제 후 새 이미지 저장
    if (!image.isEmpty()) {
        if (oldImgPath != null && !oldImgPath.isEmpty()) {
            Path oldFilePath = Paths.get(uploadDir, oldImgPath.replace("/uploads/", ""));
            Files.deleteIfExists(oldFilePath);
        }
        String newFileName = UUID.randomUUID() + "_" + image.getOriginalFilename();
        image.transferTo(Paths.get(uploadDir, newFileName));
        entity.setImgPath("/uploads/" + newFileName);
    }

    entity.setTitle(title);
    entity.setContent(content);
    boardService.write(entity); // save()를 통해 update 처리
    return "redirect:/board/detail/" + id;
}

```

---

### 📄 edit.html

```html
<form action="/board/update" method="post" enctype="multipart/form-data">
    <input type="hidden" name="id" th:value="${board.id}">
    <input type="hidden" name="oldImgPath" th:value="${board.imgPath}">
    ...
</form>

```

- 기존 이미지 경로와 게시글 ID를 hidden 필드로 함께 전송하여 수정 시 활용.

---

### 📌 게시글 삭제

### 📄 detail.html

```
function deleteBoard() {
    if (confirm("게시글을 삭제하시겠습니까?")) {
        $.ajax({
            url: "/delete/[[${detail.id}]]",
            type: "DELETE",
            success: function () {
                alert("삭제가 완료되었습니다.");
                window.location.href = "/";
            },
            error: function () {
                alert("삭제 실패. 관리자에게 문의하세요.");
            }
        });
    }
}

```

- 삭제 요청은 Ajax로 처리되며 성공 시 메인 페이지로 이동.

---

### 📄 BoardRestController.java

```java
@DeleteMapping("/delete/{id}")
public void deleteBoard(@PathVariable Long id) {
    String imgPath = boardService.detailPage(id).get().getImgPath();
    String uploadDir = fileUploadConfig.getUploadDir();

    if (imgPath != null && !imgPath.isEmpty()) {
        Path filePath = Paths.get(uploadDir, imgPath.replace("/uploads/", ""));
        Files.deleteIfExists(filePath);
    }
    boardService.deleteBoard(id); // DB에서 삭제
}

```

- 이미지가 존재하면 서버에서 파일도 함께 삭제.

---

### 📌 검색 기능

### 📄 index.html (JavaScript로 검색 처리)

```
function searchBoard(event) {
    event.preventDefault();
    const type = $("#searchType").val();
    const keyword = $("#keyword").val();

    $.ajax({
        url: "/search",
        type: "GET",
        data: { type: type, keyword: keyword },
        success: function (data) {
            const tableBody = $("#boardTable").empty();
            if (data.length === 0) {
                tableBody.append(`<tr><td colspan="4" class="text-center">검색 결과가 없습니다.</td></tr>`);
            } else {
                $.each(data, function (index, board) {
                    const row = `<tr>
                        <td>${board.id}</td>
                        <td><a href="/board/detail/${board.id}">${board.title}</a></td>
                        <td>${board.writer}</td>
                        <td>${board.writeDay}</td>
                    </tr>`;
                    tableBody.append(row);
                });
            }
        },
        error: function () {
            alert("검색 실패");
        }
    });
}

```

---

### 📄 BoardRestController.java

```java
@GetMapping("/search")
public List<BoardEntity> search(@RequestParam String type, @RequestParam String keyword) {
    return boardService.searchResult(type, keyword);
}

```

---

### 📄 BoardService.java

```java
public List<BoardEntity> searchResult(String type, String keyword) {
    switch (type) {
        case "title":
            return boardRepository.findByTitleContaining(keyword);
        case "content":
            return boardRepository.searchContent(keyword);
        case "writer":
            // 직접 쿼리 작성 필요
            return boardRepository.findByWriterContaining(keyword);
        default:
            return List.of();
    }
}

```

- 작성자 검색은 `BoardRepository`에 별도 메서드 추가 필요:

```java
List<BoardEntity> findByWriterContaining(String keyword);

```

---

## ✅ 최종 흐름 요약

```
[상세보기]
index.html → BoardController.detail(id) → boardService.detailPage() → detail.html

[수정]
edit.html → BoardController.edit(id) → 수정폼 표시
→ POST /update → 이미지 변경 처리 → DB update

[삭제]
detail.html → Ajax(DELETE) 요청 → BoardRestController → 이미지 및 DB 삭제

[검색]
index.html 검색 form → Ajax 요청 → BoardRestController → boardService → 결과 반환

```