# 📘 Java - ArrayList

📅 Date: 2025-04-29

📚 Topic: ArrayList 활용

---

## ✅ 실습 코드

### 📁 `Music.java`

```java
package mp3PlayList;

public class Music {
	private String title;
	private String singer;
	private String genre;
	private int playTime;

	public String getTitle() {
		return title;
	}
	public void setTitle(String title) {
		this.title = title;
	}
	public String getSinger() {
		return singer;
	}
	public void setSinger(String singer) {
		this.singer = singer;
	}
	public String getGenre() {
		return genre;
	}
	public void setGenre(String genre) {
		this.genre = genre;
	}
	public int getPlayTime() {
		return playTime;
	}
	public void setPlayTime(int playTime) {
		this.playTime = playTime;
	}

	public Music(String title, String singer, String genre, int playTime) {
		this.title = title;
		this.singer = singer;
		this.genre = genre;
		this.playTime = playTime;
	}
}
```

---

### 📁 `Main.java`

```java
package mp3PlayList;

import java.util.ArrayList;
import java.util.Scanner;

public class Main {

	public static void main(String[] args) {
		ArrayList<Music> list = new ArrayList<Music>();
		list.add(new Music("Love Dive", "아이브", "댄스", 180));

		System.out.println(list.get(0).getTitle());
		Scanner sc = new Scanner(System.in);

		while (true) {
			System.out.println("=== 언어지능의 MP3 PlayList ===");
			System.out.println("[1] 노래추가\t\t[2] 노래정보보기\t[3] 노래삭제\t\n[4] 노래전체삭제\t[5] 전체노래정보\t[6] 종료");
			int input = sc.nextInt();

			if (input == 1) {
				System.out.print("제목 입력 : ");
				String titInput = sc.next();
				System.out.print("가수 입력 : ");
				String singerInput = sc.next();
				System.out.print("장르 입력 : ");
				String genreInput = sc.next();
				System.out.print("재생시간 입력 : ");
				int playTimeInput = sc.nextInt();

				list.add(new Music(titInput, singerInput, genreInput, playTimeInput));
				System.out.println("노래 추가 완료");
			} else if (input == 2) {
				sc.nextLine();
				System.out.print("제목 입력 : ");
				String titInfo = sc.nextLine();
				boolean check = false;

				for (Music m : list) {
					if (m.getTitle().equals(titInfo)) {
						System.out.println("제목 : " + m.getTitle() + "\t가수  \t: " + m.getSinger() + "\n장르 : "
								+ m.getGenre() + "\t\t재생시간 : " + m.getPlayTime());
						System.out.println();
						check = true;
					}
				}
				if (!check) {
					System.out.println("해당하는 노래가 존재하지 않습니다.");
				}
			} else if (input == 3) {
				System.out.print("삭제할 노래 : ");
				int index = sc.nextInt();
				list.remove(index);
				System.out.println("해당 노래 삭제됨");
			} else if (input == 4) {
				list.clear();
				System.out.println("노래 전체 삭제됨");
			} else if (input == 5) {
				System.out.println("==== 전체 노래 정보 ====");
				for (int i = 0; i < list.size(); i++) {
					System.out.println("제목 : " + list.get(i).getTitle() + "\t가수  \t: " + list.get(i).getSinger() + "\n장르 : "
							+ list.get(i).getGenre() + "\t\t재생시간 : " + list.get(i).getPlayTime());
				}
			} else {
				System.out.println("프로그램을 종료");
				break;
			}
		}
	}
}
```

## ✅ 프로그램 구성요소

### 1. `Music` 클래스 (노래 정보를 담는 틀)

- **하나의 노래**에 대한 정보를 담고 있다.
- 정보: `제목`, `가수`, `장르`, `재생시간` (초 단위)

```java
public class Music {
	private String title;
	private String singer;
	private String genre;
	private int playTime;

	public Music(String title, String singer, String genre, int playTime) {
		this.title = title;
		this.singer = singer;
		this.genre = genre;
		this.playTime = playTime;
	}

	// getter/setter는 필드 값 읽고 쓰는 메서드들 (필요할 때 사용)
}

```

---

### 2. `Main` 클래스 (기능을 실행하는 부분)

- 사용자가 선택할 수 있는 메뉴와 기능을 포함한 `main()` 메서드
- `Scanner`로 사용자 입력을 받고, `ArrayList<Music>`에 노래를 추가/삭제/조회

---

## ✅ 기능 정리

### 📌 1. `ArrayList<Music>` 만들기

```java
ArrayList<Music> list = new ArrayList<>();
```

- `Music` 객체들을 저장하는 리스트
- 한 곡씩 `list.add(new Music(...))` 으로 넣는다.

---

### 📌 2. 메뉴 기능 설명

### [1] 노래추가

- 사용자에게 제목, 가수, 장르, 재생시간을 입력받고 새 `Music` 객체를 리스트에 추가

```java

list.add(new Music(제목, 가수, 장르, 재생시간));
```

---

### [2] 노래정보보기

- 사용자에게 `제목`을 입력받고, 그 제목과 일치하는 노래가 리스트에 있는지 확인
- 있다면 노래 정보 출력

```java
for (Music m : list) {
	if (m.getTitle().equals(입력한제목)) {
		// 정보 출력
	}
}
```

---

### [3] 노래삭제

- 사용자에게 **몇 번째(index)** 노래를 지울지 번호를 입력받고 삭제

```java

int index = sc.nextInt();
list.remove(index); // 예: 0이면 첫 번째 노래 삭제
```

<aside>
📌

**동일한 제목의 노래가 여러 개 있을 때 목록을 보여주고, 그 중 하나를 선택해서 삭제하는 방식**

(사용자 입장에서 가장 직관적)

---

1. 제목 입력 → 일치하는 노래 전부 화면에 번호와 함께 보여줌
2. 사용자에게 **몇 번째 곡을 삭제할지 선택**하게 함
3. 선택된 노래만 삭제

---

## 🔧 제목 중복 선택 삭제

```java

else if (input == 3) {
    sc.nextLine(); // 버퍼 비우기
    System.out.print("삭제할 노래 제목 입력 : ");
    String delTitle = sc.nextLine();

    // 일치하는 노래들을 담기 위한 임시 리스트
    ArrayList<Music> matchList = new ArrayList<>();
    ArrayList<Integer> matchIndices = new ArrayList<>();

    // 일치하는 노래 찾기
    for (int i = 0; i < list.size(); i++) {
        if (list.get(i).getTitle().equals(delTitle)) {
            matchList.add(list.get(i));
            matchIndices.add(i);
        }
    }

    // 결과 확인
    if (matchList.isEmpty()) {
        System.out.println("해당 제목의 노래가 없습니다.");
    } else {
        System.out.println("찾은 노래 목록:");
        for (int i = 0; i < matchList.size(); i++) {
            Music m = matchList.get(i);
            System.out.println("[" + i + "] 제목: " + m.getTitle() + ", 가수: " + m.getSinger());
        }

        System.out.print("삭제할 노래 번호 선택: ");
        int delIndex = sc.nextInt();

        // 실제 리스트에서 삭제
        int actualIndex = matchIndices.get(delIndex);
        list.remove(actualIndex);
        System.out.println("노래가 삭제되었습니다.");
    }
}
```

---

## 🧠 동작

1. 사용자가 `"Love Dive"`를 삭제하려고 하면
2. `Love Dive`인 노래를 전부 찾아서 번호 붙여 출력:
    
    ```
    [0] 제목: Love Dive, 가수: 아이브
    [1] 제목: Love Dive, 가수: 뉴진스
    ```
    
3. 사용자는 0 또는 1을 입력해 원하는 곡만 삭제

## 예외 처리

- 사용자가 선택한 번호가 **유효한 범위인지 확인**
- 잘못된 입력이면 삭제하지 않고 **오류 메시지 출력**
- `try-catch` 사용해서 숫자 외 입력도 방지

```java

else if (input == 3) {
    sc.nextLine(); // 버퍼 비우기
    System.out.print("삭제할 노래 제목 입력 : ");
    String delTitle = sc.nextLine();

    ArrayList<Music> matchList = new ArrayList<>();
    ArrayList<Integer> matchIndices = new ArrayList<>();

    for (int i = 0; i < list.size(); i++) {
        if (list.get(i).getTitle().equals(delTitle)) {
            matchList.add(list.get(i));
            matchIndices.add(i);
        }
    }

    if (matchList.isEmpty()) {
        System.out.println("해당 제목의 노래가 없습니다.");
    } else {
        System.out.println("찾은 노래 목록:");
        for (int i = 0; i < matchList.size(); i++) {
            Music m = matchList.get(i);
            System.out.println("[" + i + "] 제목: " + m.getTitle() + ", 가수: " + m.getSinger());
        }

        System.out.print("삭제할 노래 번호 선택: ");
        try {
            int delIndex = sc.nextInt();

            if (delIndex < 0 || delIndex >= matchList.size()) {
                System.out.println("잘못된 번호입니다. 삭제를 취소합니다.");
            } else {
                int actualIndex = matchIndices.get(delIndex);
                list.remove(actualIndex);
                System.out.println("노래가 삭제되었습니다.");
            }
        } catch (Exception e) {
            System.out.println("숫자로 입력해주세요. 삭제를 취소합니다.");
            sc.nextLine(); // 잘못된 입력 버퍼 비우기
        }
    }
}
```

</aside>

---

### [4] 전체삭제

```java

list.clear();
```

- 리스트 안에 있는 모든 노래를 삭제

---

### [5] 전체노래정보

- 리스트에 있는 모든 노래의 정보를 하나씩 출력

```java

for (int i = 0; i < list.size(); i++) {
	System.out.println(list.get(i).getTitle() + ...);
}
```

---

### [6] 종료

- `break;` 로 반복문 종료해서 프로그램 종료

---

## 🔄 실행 흐름 요약

```java

while(true) {
  // 메뉴출력 -> 사용자입력 -> if문으로 기능 실행
}

```

---

## 🧠 핵심 개념

- `ArrayList<Music>`: 노래들을 저장하는 그릇
- `.add()`: 노래 추가
- `.get(i)`: i번째 노래 정보 얻기
- `.remove(i)`: i번째 노래 삭제
- `.clear()`: 전체 삭제
- `.equals()`: 문자열 비교 (제목 같은지 확인)

---

## `중복 제거`, `정렬`, `검색` 기능 추가

---

## 📌 전체 코드에 넣을 기능 요약

| 번호 | 기능 | 설명 |
| --- | --- | --- |
| 7 | 중복 제거 | 같은 제목/가수의 노래는 하나만 남김 |
| 8 | 정렬 기능 | 제목 순으로 정렬 |
| 9 | 검색 기능 | 키워드를 포함한 제목 검색 |

---

## 🧾 메뉴 항목에 추가

```java

System.out.println("[1] 노래추가\t[2] 노래정보보기\t[3] 노래삭제");
System.out.println("[4] 전체삭제\t[5] 전체정보\t[6] 종료\t[7] 중복제거\t[8] 정렬\t[9] 검색");
```

---

## 🧩 1. [7] 중복 제거 기능

```java

else if (input == 7) {
    for (int i = 0; i < list.size(); i++) {
        Music m1 = list.get(i);
        for (int j = i + 1; j < list.size(); j++) {
            Music m2 = list.get(j);
            if (m1.getTitle().equals(m2.getTitle()) && m1.getSinger().equals(m2.getSinger())) {
                list.remove(j);
                j--; // 삭제했으므로 인덱스 조정
            }
        }
    }
    System.out.println("중복 노래 제거 완료!");
}
```

---

## 🧩 2. [8] 제목순 정렬 기능

```java

else if (input == 8) {
    list.sort((m1, m2) -> m1.getTitle().compareTo(m2.getTitle()));
    System.out.println("제목 기준으로 정렬 완료!");
}
```

> 🔍 오름차순 (가나다순) 정렬
> 

---

## 🧩 3. [9] 키워드 포함 검색

```java

else if (input == 9) {
    sc.nextLine(); // 개행 제거
    System.out.print("검색할 제목 키워드 입력: ");
    String keyword = sc.nextLine();

    boolean found = false;
    for (Music m : list) {
        if (m.getTitle().contains(keyword)) {
            System.out.println("제목: " + m.getTitle() + ", 가수: " + m.getSinger());
            found = true;
        }
    }

    if (!found) {
        System.out.println("해당 키워드를 포함한 노래가 없습니다.");
    }
}
```

---

## ✅ 전체 코드

### 📁 `Music.java`

```java
package mp3PlayList;

public class Music {
    private String title;
    private String singer;
    private String genre;
    private int playTime;

    public Music(String title, String singer, String genre, int playTime) {
        this.title = title;
        this.singer = singer;
        this.genre = genre;
        this.playTime = playTime;
    }

    public String getTitle() { return title; }
    public String getSinger() { return singer; }
    public String getGenre() { return genre; }
    public int getPlayTime() { return playTime; }

    public void setTitle(String title) { this.title = title; }
    public void setSinger(String singer) { this.singer = singer; }
    public void setGenre(String genre) { this.genre = genre; }
    public void setPlayTime(int playTime) { this.playTime = playTime; }
}
```

---

### 📁 `Main.java`

```java
package mp3PlayList;

import java.util.ArrayList;
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        ArrayList<Music> list = new ArrayList<>();
        list.add(new Music("Love Dive", "아이브", "댄스", 180));

        Scanner sc = new Scanner(System.in);

        while (true) {
            System.out.println("=== MP3 PlayList ===");
            System.out.println("[1] 노래추가\t[2] 노래정보보기\t[3] 노래삭제");
            System.out.println("[4] 전체삭제\t[5] 전체정보\t[6] 종료\t[7] 중복제거\t[8] 정렬\t[9] 검색");
            int input = sc.nextInt();

            if (input == 1) {
                System.out.print("제목 입력: ");
                String title = sc.next();
                System.out.print("가수 입력: ");
                String singer = sc.next();
                System.out.print("장르 입력: ");
                String genre = sc.next();
                System.out.print("재생시간(초): ");
                int time = sc.nextInt();
                list.add(new Music(title, singer, genre, time));
                System.out.println("노래 추가 완료");

            } else if (input == 2) {
                sc.nextLine();
                System.out.print("찾을 제목 입력: ");
                String findTitle = sc.nextLine();
                boolean found = false;
                for (Music m : list) {
                    if (m.getTitle().equals(findTitle)) {
                        System.out.println("제목: " + m.getTitle() + " / 가수: " + m.getSinger());
                        System.out.println("장르: " + m.getGenre() + " / 재생시간: " + m.getPlayTime() + "초");
                        found = true;
                    }
                }
                if (!found) System.out.println("해당 노래가 없습니다.");

            } else if (input == 3) {
                sc.nextLine();
                System.out.print("삭제할 노래 제목 입력: ");
                String delTitle = sc.nextLine();
                ArrayList<Music> matchList = new ArrayList<>();
                ArrayList<Integer> matchIndices = new ArrayList<>();

                for (int i = 0; i < list.size(); i++) {
                    if (list.get(i).getTitle().equals(delTitle)) {
                        matchList.add(list.get(i));
                        matchIndices.add(i);
                    }
                }

                if (matchList.isEmpty()) {
                    System.out.println("해당 제목의 노래가 없습니다.");
                } else {
                    System.out.println("찾은 노래 목록:");
                    for (int i = 0; i < matchList.size(); i++) {
                        Music m = matchList.get(i);
                        System.out.println("[" + i + "] 제목: " + m.getTitle() + ", 가수: " + m.getSinger());
                    }

                    System.out.print("삭제할 번호 선택: ");
                    try {
                        int delIndex = sc.nextInt();
                        if (delIndex < 0 || delIndex >= matchList.size()) {
                            System.out.println("잘못된 번호입니다. 삭제 취소.");
                        } else {
                            int realIndex = matchIndices.get(delIndex);
                            list.remove(realIndex);
                            System.out.println("노래 삭제 완료.");
                        }
                    } catch (Exception e) {
                        System.out.println("숫자만 입력해주세요. 삭제 취소.");
                        sc.nextLine();
                    }
                }

            } else if (input == 4) {
                list.clear();
                System.out.println("전체 노래 삭제 완료");

            } else if (input == 5) {
                System.out.println("==== 전체 노래 목록 ====");
                for (Music m : list) {
                    System.out.println("제목: " + m.getTitle() + " / 가수: " + m.getSinger());
                    System.out.println("장르: " + m.getGenre() + " / 재생시간: " + m.getPlayTime() + "초");
                    System.out.println("---------------------------");
                }

            } else if (input == 6) {
                System.out.println("프로그램 종료");
                break;

            } else if (input == 7) {
                for (int i = 0; i < list.size(); i++) {
                    Music m1 = list.get(i);
                    for (int j = i + 1; j < list.size(); j++) {
                        Music m2 = list.get(j);
                        if (m1.getTitle().equals(m2.getTitle()) &&
                            m1.getSinger().equals(m2.getSinger())) {
                            list.remove(j);
                            j--;
                        }
                    }
                }
                System.out.println("중복 노래 제거 완료");

            } else if (input == 8) {
                list.sort((m1, m2) -> m1.getTitle().compareTo(m2.getTitle()));
                System.out.println("제목 오름차순 정렬 완료");

            } else if (input == 9) {
                sc.nextLine();
                System.out.print("검색할 키워드 입력: ");
                String keyword = sc.nextLine();
                boolean found = false;
                for (Music m : list) {
                    if (m.getTitle().contains(keyword)) {
                        System.out.println("제목: " + m.getTitle() + " / 가수: " + m.getSinger());
                        found = true;
                    }
                }
                if (!found) System.out.println("검색 결과 없음");
            }
        }
    }
}
```