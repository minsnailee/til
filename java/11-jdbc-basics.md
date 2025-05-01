# 📘 Java -  JDBC

---

### 📁 `Ex01Join.java` - 회원가입 기능

```java

package jdbc기초;

import java.sql.*;
import java.util.Scanner;

public class Ex01Join {
    public static void main(String[] args) {
        // 사용자 입력 받기
        Scanner sc = new Scanner(System.in);
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

        Connection conn = null;
        PreparedStatement psmt = null;

        try {
            // 1. 드라이버 로딩
            Class.forName("oracle.jdbc.driver.OracleDriver");

            // 2. DB 연결
            String url = "jdbc:oracle:thin:@localhost:1521:xe"; // 예시 URL
            String user = "your_username";
            String password = "your_password";
            conn = DriverManager.getConnection(url, user, password);

            if (conn != null) {
                System.out.println("연결 성공");
            }

            // 3. SQL 작성 및 전송
            String sql = "INSERT INTO NLPMEMBER VALUES (?, ?, ?, ?, ?)";
            psmt = conn.prepareStatement(sql);

            // 4. 데이터 바인딩
            psmt.setString(1, id);
            psmt.setString(2, pw);
            psmt.setString(3, name);
            psmt.setInt(4, age);
            psmt.setInt(5, score);

            // 5. SQL 실행 및 결과 처리
            int row = psmt.executeUpdate();
            if (row > 0) {
                System.out.println("회원가입 완료");
            } else {
                System.out.println("회원가입 실패");
            }

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // 6. 자원 반납
            try {
                if (psmt != null) psmt.close();
                if (conn != null) conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}

```

---

### 📁 `Ex02Login.java` - 로그인 기능

```java

package jdbc기초;

import java.sql.*;
import java.util.Scanner;

public class Ex02Login {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.print("ID 입력 : ");
        String id = sc.next();
        System.out.print("PW 입력 : ");
        String pw = sc.next();

        Connection conn = null;
        PreparedStatement psmt = null;
        ResultSet rs = null;

        try {
            // 1. 드라이버 로딩
            Class.forName("oracle.jdbc.driver.OracleDriver");

            // 2. DB 연결
            String url = "jdbc:oracle:thin:@localhost:1521:xe";
            String user = "your_username";
            String password = "your_password";
            conn = DriverManager.getConnection(url, user, password);

            if (conn != null) {
                System.out.println("연결 성공");
            }

            // 3. SQL 작성 및 전송
            String sql = "SELECT * FROM NLPMEMBER WHERE id = ? AND pw = ?";
            psmt = conn.prepareStatement(sql);
            psmt.setString(1, id);
            psmt.setString(2, pw);

            // 4. SQL 실행 및 결과 처리
            rs = psmt.executeQuery();
            if (rs.next()) {
                String userName = rs.getString("name");
                System.out.println(userName + "님 환영합니다.");
            } else {
                System.out.println("--- 로그인 실패 ---");
                System.out.println("ID나 PW를 다시 입력하세요");
            }

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // 5. 자원 반납
            try {
                if (rs != null) rs.close();
                if (psmt != null) psmt.close();
                if (conn != null) conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}

```

---

### 📁 `Ex03delete.java` - 회원 탈퇴 기능

```java

package jdbc기초;

import java.sql.*;
import java.util.Scanner;

public class Ex03Delete {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.print("ID 입력 >> ");
        String id = sc.next();
        System.out.print("PW 입력 >> ");
        String pw = sc.next();

        Connection conn = null;
        PreparedStatement psmt = null;

        try {
            // 1. 드라이버 로딩
            Class.forName("oracle.jdbc.driver.OracleDriver");

            // 2. DB 연결
            String url = "jdbc:oracle:thin:@localhost:1521:xe";
            String user = "your_username";
            String password = "your_password";
            conn = DriverManager.getConnection(url, user, password);

            if (conn != null) {
                System.out.println("연결 성공");
            }

            // 3. SQL 작성 및 전송
            String sql = "DELETE FROM NLPMEMBER WHERE id = ? AND pw = ?";
            psmt = conn.prepareStatement(sql);
            psmt.setString(1, id);
            psmt.setString(2, pw);

            // 4. SQL 실행 및 결과 처리
            int row = psmt.executeUpdate();
            if (row > 0) {
                System.out.println("회원탈퇴 성공");
            } else {
                System.out.println("회원탈퇴 실패");
            }

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // 5. 자원 반납
            try {
                if (psmt != null) psmt.close();
                if (conn != null) conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}

```

---

### 📁 `Ex04selectAll.java` - 전체 회원 조회 기능

```java

package jdbc기초;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class Ex04selectAll {

	public static void main(String[] args) {

		System.out.println("전체 회원 조회");
		System.out.println("ID \t 이름 \t 나이 \t 점수");

		PreparedStatement psmt = null;
		Connection conn = null;
		ResultSet rs = null;

		try {

			Class.forName("oracle.jdbc.driver.OracleDriver");

			String url = "jdbc:oracle:thin:@localhost:1521:xe";
            String user = "your_username";
            String password = "your_password";
			conn = DriverManager.getConnection(url, user, password);

			String sql = "SELECT * FROM NLPMEMBER";

			psmt = conn.prepareStatement(sql);
			
			rs = psmt.executeQuery();

			while (true) {
				if(rs.next()==true) {
					String id = rs.getString("id");
					String name = rs.getString("name");
					int age = rs.getInt("age");
					int score = rs.getInt("score");
					
					System.out.println("----------------------------------");
	                System.out.println(id + " \t " + name + "\t " + age + " \t " + score);
				}else {
					break;
				}
			}
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			try {
				if (rs != null) {
					rs.close();
				}
				if (psmt != null) {
					psmt.close();
				}
				if (conn != null) {
					conn.close();
				}
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
	}

}


```


## 📘 개념 & 문법 요약

| 분류 | 개념/문법 | 설명 |
| --- | --- | --- |
| 예외 처리 | `try-catch` | 오류가 발생할 수 있는 코드를 감싸서 안전하게 실행함. 예외 발생 시 `catch`에서 처리 |
| 자원 정리 | `finally` | `try-catch` 종료 후 항상 실행됨. DB 연결, Statement, ResultSet 등을 닫을 때 사용 |
| DB 연결 | `Class.forName(...)` | JDBC 드라이버를 메모리에 로딩함 (DB 종류에 따라 문자열 다름, 예: Oracle, MySQL 등) |
| DB 연결 | `Connection` | 자바와 DB를 연결해주는 객체. 이 객체를 통해 SQL을 보냄 |
| SQL 실행 | `PreparedStatement` | SQL을 미리 준비하고, ? 자리에 값을 넣어 실행함. SQL Injection 방지에 효과적 |
| SQL 바인딩 | `setString`, `setInt` 등 | SQL의 `?` 자리에 실제 값을 채워주는 메서드 (예: `setString(1, "abc")`) |
| SELECT 결과 처리 | `ResultSet` | SELECT 쿼리 실행 결과를 담는 객체. 표 형태이며, `rs.next()`로 행을 이동하며 값 꺼냄 |
| SQL 실행 | `executeQuery()` | SELECT 구문 실행 시 사용. 결과는 `ResultSet`으로 받음 |
| SQL 실행 | `executeUpdate()` | INSERT, UPDATE, DELETE 실행 시 사용. 영향을 받은 행의 수(int)를 반환 |
| 안전한 자원 해제 | `rs.close()`, `pstmt.close()`, `conn.close()` | DB 자원은 사용 후 꼭 닫아줘야 함. 보통 `finally`에서 처리 |

---

## 🔒properties 파일로 분리

### 📌 properties 파일을 사용하는 이유

1. **보안**: 비밀번호를 소스코드에 직접 작성하지 않아도 됨.
2. **유지보수**: DB 설정이 바뀌어도 코드 수정 없이 properties 파일만 수정하면 됨.
3. **환경분리**: 개발/운영 환경에서 설정만 바꿔서 쉽게 전환 가능.

아래는 앞서 사용했던 `Ex01join` JDBC 예제를 **`.properties` 파일로 DB 정보를 분리해서 작성한 버전**

### 📌 공통 준비

- `resources/db.properties`
- `DBUtil.java` (DB 연결을 도와주는 도우미 클래스)
- 

### 📄 1. `resources/db.properties` 파일

```
# DB 연결 설정
url=jdbc:oracle:thin:@localhost:1521:xe
user=myuser
password=mypassword
```

🔸 `resources` 폴더는 프로젝트 루트 아래에 두고, 실제 실행 시 `src/main/resources` 또는 `bin/resources` 경로가 될 수 있어요.

---

### 📁 2. `DBUtil.java` – DB 연결 도우미 클래스

```java

import java.io.FileInputStream;
import java.io.IOException;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.util.Properties;

public class DBUtil {

    public static Connection getConnection() {
        Connection conn = null;

        try {
            // 1. DB 드라이버 로딩 (Oracle JDBC)
            Class.forName("oracle.jdbc.driver.OracleDriver");

            // 2. properties 파일 불러오기
            Properties prop = new Properties();
            FileInputStream fis = new FileInputStream("resources/db.properties");
            prop.load(fis);

            // 3. 설정 값 읽기
            String url = prop.getProperty("url");
            String user = prop.getProperty("user");
            String password = prop.getProperty("password");

            // 4. 연결 시도
            conn = DriverManager.getConnection(url, user, password);
            System.out.println("DB 연결 성공");

        } catch (ClassNotFoundException | IOException | SQLException e) {
            e.printStackTrace();
        }

        return conn;
    }
}

```

🧠 **추가 설명**:

- `Class.forName(...)`: Oracle 드라이버를 메모리에 로드
- `Properties`: 키=값 형태로 구성된 파일을 불러올 수 있는 자바 내장 클래스
- `getProperty(...)`: 설정파일에서 특정 키의 값을 꺼냄

---

### 🧾 3. `Ex01join.java`

```java

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.util.Scanner;

public class Ex01join {

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.print("ID 입력 : ");
        String id = sc.nextLine();
        System.out.print("PW 입력 : ");
        String pw = sc.nextLine();

        Connection conn = null;
        PreparedStatement pstmt = null;

        try {
            // DBUtil에서 Connection 가져오기
            conn = DBUtil.getConnection();

            // SQL 준비
            String sql = "INSERT INTO member (id, pw) VALUES (?, ?)";

            // PreparedStatement 생성 및 값 설정
            pstmt = conn.prepareStatement(sql);
            pstmt.setString(1, id);
            pstmt.setString(2, pw);

            int result = pstmt.executeUpdate();

            if (result > 0) {
                System.out.println("회원가입 성공!");
            } else {
                System.out.println("회원가입 실패!");
            }

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try { if (pstmt != null) pstmt.close(); } catch (Exception e) {}
            try { if (conn != null) conn.close(); } catch (Exception e) {}
        }

        sc.close();
    }
}

```

---

### 🔐 4. `Ex02login.java`

```java

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.util.Scanner;

public class Ex02login {

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.print("ID 입력 : ");
        String id = sc.nextLine();
        System.out.print("PW 입력 : ");
        String pw = sc.nextLine();

        Connection conn = null;
        PreparedStatement pstmt = null;
        ResultSet rs = null;

        try {
            conn = DBUtil.getConnection();

            String sql = "SELECT * FROM member WHERE id = ? AND pw = ?";
            pstmt = conn.prepareStatement(sql);
            pstmt.setString(1, id);
            pstmt.setString(2, pw);

            rs = pstmt.executeQuery();

            if (rs.next()) {
                System.out.println("로그인 성공!");
                // rs.getString("id"); 등으로 사용자 정보 가져올 수 있음
            } else {
                System.out.println("로그인 실패!");
            }

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try { if (rs != null) rs.close(); } catch (Exception e) {}
            try { if (pstmt != null) pstmt.close(); } catch (Exception e) {}
            try { if (conn != null) conn.close(); } catch (Exception e) {}
        }

        sc.close();
    }
}

```

---

### 🗑 5. `Ex03delete.java`

```java

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.util.Scanner;

public class Ex03delete {

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.print("삭제할 ID 입력 : ");
        String id = sc.nextLine();
        System.out.print("PW 확인 : ");
        String pw = sc.nextLine();

        Connection conn = null;
        PreparedStatement pstmt = null;

        try {
            conn = DBUtil.getConnection();

            String sql = "DELETE FROM member WHERE id = ? AND pw = ?";
            pstmt = conn.prepareStatement(sql);
            pstmt.setString(1, id);
            pstmt.setString(2, pw);

            int result = pstmt.executeUpdate();

            if (result > 0) {
                System.out.println("회원 삭제 성공!");
            } else {
                System.out.println("삭제 실패 (ID/PW 불일치)!");
            }

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try { if (pstmt != null) pstmt.close(); } catch (Exception e) {}
            try { if (conn != null) conn.close(); } catch (Exception e) {}
        }

        sc.close();
    }
}

```

---

### ✅ 정리

| 구성 요소 | 역할 |
| --- | --- |
| `db.properties` | DB 접속 정보 저장 (보안과 유지보수를 위해 분리) |
| `DBUtil.java` | 접속 정보를 불러와서 DB 연결을 도와주는 클래스 |
| `Ex01join.java` | 사용자 입력을 받아 INSERT 하는 실제 실행 코드 |

---

### 🔒 보안을 위해

- `.properties` 파일은 `.gitignore`에 넣어서 깃허브에 업로드하지 않도록 함
- 실무에서는 비밀번호 암호화(`SHA-256`, `bcrypt`)도 필요