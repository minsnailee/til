# 📘 Java - MVC 패턴으로 만든 회원관리 프로그램 정리

---
### 🧱 구조 및 역할

### 📁 1. Model

- **`MemberDTO.java`**
    - 데이터 전송용 객체 (Data Transfer Object)
    - 필드: `id`, `pw`, `name`, `age`, `score`
    - 기능: 생성자 + getter/setter 메서드

### 📁 2. Controller

- **`MemberDAO.java`**
    - 데이터 접근 객체 (Data Access Object)
    - 기능:
        - `join()` : 회원가입
        - `login()` : 로그인
        - `delete()` : 회원탈퇴
        - `selectAll()` : 전체 회원 조회
    - DB 연결 정보는 `getConn()`, 자원 반납은 `getClose()`로 분리

### 📁 3. View

- **`Main.java`**
    - 사용자와 상호작용하는 콘솔 UI
    - Scanner로 입력 받고, `MemberDAO`를 통해 기능 수행
    - 기능 분기:
        - [1] 회원가입
        - [2] 로그인
        - [3] 회원탈퇴
        - [4] 전체회원조회

---

### 🔄 전체 흐름 요약

| 기능 | View (`Main.java`)에서 하는 일 | Controller (`MemberDAO`)에서 하는 일 |
| --- | --- | --- |
| 회원가입 | 사용자 입력 → DTO로 묶어서 DAO에 전달 | DB에 insert 실행 |
| 로그인 | ID, PW 입력 → DTO로 전달 → 이름 반환받기 | DB에서 ID/PW 체크 → 이름 조회 후 반환 |
| 회원탈퇴 | ID, PW 입력 → DTO 전달 | 조건 일치 시 delete 실행 |
| 전체조회 | DAO 호출 → DTO 리스트 받아 출력 | DB에서 모든 회원 SELECT → DTO 리스트로 반환 |

---

### ⚠️ 유의사항

- DB 정보 (`url`, `user`, `password`)는 코드 외부 설정이나 환경변수로 분리하는 것이 안전하다.
- SQL 예외처리, 자원 반납은 반드시 `finally` 블록에서 처리할 것.
- `MemberDTO`는 데이터 이동만 담당하고, 로직은 넣지 않는다.

---

### 📁 `view/Main.java`

```java
package view;

import java.util.ArrayList;
import java.util.Scanner;

import controller.MemberDAO;
import model.MemberDTO;

public class Main {
    public static void main(String[] args) {
        System.out.println("==== 회원관리 프로그램 ====");
        Scanner sc = new Scanner(System.in);

        while (true) {
            System.out.println("[1] 회원가입\t[2] 로그인\t[3]회원탈퇴\t[4] 전체회원조회");
            int choice = sc.nextInt();

            if (choice == 1) {
                System.out.print("ID 입력 : ");
                String id = sc.next();
                System.out.print("PW 입력 : ");
                String pw = sc.next();
                System.out.print("이름 입력 : ");
                String name = sc.next();
                System.out.print("나이 입력 : ");
                int age = sc.nextInt();
                System.out.print("점수 입력 : ");
                int score = sc.nextInt();

                MemberDAO dao = new MemberDAO();
                MemberDTO dto = new MemberDTO(id, pw, name, age, score);
                int row = dao.join(dto);

                if (row > 0) {
                    System.out.println("회원가입 완료");
                } else {
                    System.out.println("회원가입 실패");
                }

            } else if (choice == 2) {
                System.out.print("ID 입력 : ");
                String id = sc.next();
                System.out.print("PW 입력 : ");
                String pw = sc.next();

                MemberDAO dao = new MemberDAO();
                MemberDTO dto = new MemberDTO(id, pw);
                String userName = dao.login(dto);
                if (userName != null) {
                    System.out.println(userName + "님 환영합니다.");
                } else {
                    System.out.println("로그인 실패");
                    System.out.println("아이디나 비밀번호를 다시 확인해보세요.");
                }

            } else if (choice == 3) {
                System.out.print("ID 입력 >>");
                String id = sc.next();
                System.out.print("PW 입력 >>");
                String pw = sc.next();

                MemberDAO dao = new MemberDAO();
                MemberDTO dto = new MemberDTO(id, pw);
                int row = dao.delete(dto);

                if (row > 0) {
                    System.out.println("회원탈퇴 성공");
                } else {
                    System.out.println("회원탈퇴 실패");
                }

            } else if (choice == 4) {
                System.out.println("전체 회원 조회");
                System.out.println("ID \t 이름 \t 나이 \t 점수");

                MemberDAO dao = new MemberDAO();
                ArrayList<MemberDTO> list = dao.selectAll();
                for (MemberDTO d : list) {
                    System.out.println("----------------------------------");
                    System.out.println(d.getId() + " \t " + d.getName() + "\t " + d.getAge() + " \t " + d.getScore());
                }

            } else {
                System.out.println("잘못 입력함. 종료");
                break;
            }
        }
    }
}

```

---

### 📁 `model/MemberDTO.java`

```java
package model;

public class MemberDTO {
    private String id;
    private String pw;
    private String name;
    private int age;
    private int score;

    public MemberDTO(String id, String pw, String name, int age, int score) {
        this.id = id;
        this.pw = pw;
        this.name = name;
        this.age = age;
        this.score = score;
    }

    public MemberDTO(String id, String pw) {
        this.id = id;
        this.pw = pw;
    }

    public String getId() {
        return id;
    }
    public void setId(String id) {
        this.id = id;
    }

    public String getPw() {
        return pw;
    }
    public void setPw(String pw) {
        this.pw = pw;
    }

    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }
    public void setAge(int age) {
        this.age = age;
    }

    public int getScore() {
        return score;
    }
    public void setScore(int score) {
        this.score = score;
    }
}

```

---

### 📁 `controller/MemberDAO.java`

```java
package controller;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;

import model.MemberDTO;

public class MemberDAO {
    PreparedStatement psmt = null;
    Connection conn = null;
    ResultSet rs = null;

    private void getClose() {
        try {
            if (rs != null) rs.close();
            if (psmt != null) psmt.close();
            if (conn != null) conn.close();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    private void getConn() {
        try {
            Class.forName("oracle.jdbc.driver.OracleDriver");
            String url = "jdbc:oracle:thin:@[DB_HOST]:[PORT]:[SID]";
            String user = "[DB_USER]";
            String password = "[DB_PASSWORD]";
            conn = DriverManager.getConnection(url, user, password);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public String login(MemberDTO dto) {
        String userName = null;
        try {
            getConn();
            String sql = "SELECT * FROM NLPMEMBER WHERE id = ? AND pw = ?";
            psmt = conn.prepareStatement(sql);
            psmt.setString(1, dto.getId());
            psmt.setString(2, dto.getPw());
            rs = psmt.executeQuery();
            if (rs.next()) {
                userName = rs.getString("name");
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            getClose();
        }
        return userName;
    }

    public int join(MemberDTO dto) {
        int row = 0;
        try {
            getConn();
            String sql = "INSERT INTO NLPMEMBER VALUES(?,?,?,?,?)";
            psmt = conn.prepareStatement(sql);
            psmt.setString(1, dto.getId());
            psmt.setString(2, dto.getPw());
            psmt.setString(3, dto.getName());
            psmt.setInt(4, dto.getAge());
            psmt.setInt(5, dto.getScore());
            row = psmt.executeUpdate();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            getClose();
        }
        return row;
    }

    public int delete(MemberDTO dto) {
        int row = 0;
        try {
            getConn();
            String sql = "DELETE FROM NLPMEMBER WHERE id = ? AND pw = ?";
            psmt = conn.prepareStatement(sql);
            psmt.setString(1, dto.getId());
            psmt.setString(2, dto.getPw());
            row = psmt.executeUpdate();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            getClose();
        }
        return row;
    }

    public ArrayList<MemberDTO> selectAll() {
        ArrayList<MemberDTO> list = new ArrayList<>();
        try {
            getConn();
            String sql = "SELECT * FROM NLPMEMBER";
            psmt = conn.prepareStatement(sql);
            rs = psmt.executeQuery();
            while (rs.next()) {
                String id = rs.getString("id");
                String name = rs.getString("name");
                int age = rs.getInt("age");
                int score = rs.getInt("score");
                MemberDTO dto = new MemberDTO(id, " ", name, age, score);
                list.add(dto);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            getClose();
        }
        return list;
    }
}

```