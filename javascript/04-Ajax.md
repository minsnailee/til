# 영화 정보 가져오기

## ✅ 실습 목적

사용자로부터 입력받은 날짜에 대해, KOBIS OpenAPI를 통해 **일일 박스오피스 영화 목록을 비동기 방식으로 요청**하고, HTML 테이블에 데이터를 출력하는 기능을 구현하는 것이다.

---

## ✅ Ajax 개념 정리

### 📌 Ajax란?

**Ajax**는 *Asynchronous JavaScript and XML*의 약어로, 전체 페이지를 새로 고치지 않고도 **서버와 비동기적으로 데이터를 주고받을 수 있는 웹 기술**이다.

Ajax를 활용하면 웹 페이지의 일부분만을 갱신할 수 있어 사용자 경험(UX)을 향상시킬 수 있다.

---

### 📌 jQuery `$.ajax()` 함수 구조

```jsx

$.ajax({
    url: "요청할 주소",
    type: "get" 또는 "post",
    dataType: "json", // 응답 데이터 형식
    data: {}, // 서버로 전송할 데이터 (선택사항)
    success: function(response) {
        // 요청 성공 시 실행할 코드
    },
    error: function(error) {
        // 요청 실패 시 실행할 코드
    }
});

```

---

## ✅ 개선된 코드 및 상세 설명

### 📁 HTML 코드 (`index.html`)

```html

<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>영화 순위 조회</title>
    <script src="https://code.jquery.com/jquery-3.7.1.min.js"></script>
</head>
<body>
    <div id="result-area">
        <table border="1">
            <thead>
                <tr>
                    <th>순위</th>
                    <th>영화명</th>
                    <th>개봉일</th>
                </tr>
            </thead>
            <tbody id="tbody">
            </tbody>
        </table>
    </div>
    <button id="req-btn">데이터 요청</button>
    <script src="ex27Ajax.js"></script>
</body>
</html>

```

---

### 📁 JavaScript 코드 (`ex27Ajax.js`)

```jsx

$("#req-btn").on('click', () => {
    let date = prompt('개봉일자를 입력하세요. (예: 20240528)', 'YYYYMMDD');
    
    if (!date || !/^\d{8}$/.test(date)) {
        alert('날짜 형식이 올바르지 않습니다.');
        return;
    }

    // * 부분은 발급받은 키값
    let movieURL = `https://kobis.or.kr/kobisopenapi/webservice/rest/boxoffice/searchDailyBoxOfficeList.json?key=************&targetDt=${date}`;

    // 기존 테이블 내용 초기화
    $("#tbody").empty();

    $.ajax({
        url: movieURL,
        type: 'get',
        dataType: 'json',
        success: (result) => {
            let movieList = result.boxOfficeResult.dailyBoxOfficeList;

            if (movieList.length === 0) {
                $("#tbody").append(`<tr><td colspan="3">데이터가 없습니다.</td></tr>`);
                return;
            }

            movieList.forEach(movie => {
                let { rank, movieNm, openDt } = movie;

                $("#tbody").append(`
                    <tr>
                        <td>${rank}</td>
                        <td>${movieNm}</td>
                        <td>${openDt}</td>
                    </tr>
                `);
            });
        },
        error: () => {
            alert('데이터 요청에 실패했습니다.');
        }
    });
});


```

---

## ✅ 코드 개선사항 요약

| 항목 | 개선 전 | 개선 후 |
| --- | --- | --- |
| 반복문 | `for(let i = 0; i <= length)` → 범위 초과 오류 발생 가능 | `forEach`로 안전하게 반복 |
| 유효성 검사 | 없음 | 날짜 형식 (`YYYYMMDD`) 검증 추가 |
| 중복 출력 | 이전 데이터 유지 | `$("#tbody").empty()`로 초기화 |
| 에러 피드백 | 콘솔 출력 | 사용자에게 `alert`로 오류 전달 |
| 빈 데이터 처리 | 없음 | "데이터가 없습니다" 행 추가 |

---

## ✅ 결론

이 실습은 Ajax와 jQuery를 통해 비동기 방식의 데이터 요청을 실전처럼 다뤄보는 매우 유익한 예제다.

특히 API 응답 결과를 **동적으로 DOM에 반영하는 과정**은 프론트엔드 개발의 핵심 개념을 직접 경험할 수 있게 해준다.

필요 시, 이 코드를 바탕으로 `fetch()`나 `async/await`, 또는 `React`, `Vue`와 같은 프레임워크 기반으로도 확장 가능하다.