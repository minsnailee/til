# 📘 DOM

---

## ✅ 1. **DOM 요소 접근 방법**

### 💡 다양한 방식으로 HTML 요소에 접근할 수 있다:

| 방법 | 설명 | 반환 형태 |
| --- | --- | --- |
| `getElementById("id")` | 특정 ID로 요소 1개를 선택 | 단일 요소 |
| `getElementsByClassName("class")` | 특정 클래스명으로 여러 요소 선택 | HTMLCollection (유사 배열) |
| `getElementsByName("name")` | name 속성값으로 요소들을 선택 | NodeList (유사 배열) |
| `querySelector("선택자")` | CSS 선택자 방식으로 첫 번째 요소 선택 | 단일 요소 |
| `querySelectorAll("선택자")` | CSS 선택자 방식으로 모든 요소 선택 | NodeList (유사 배열) |

```jsx

let title = document.getElementById("tit");
console.log(title.innerText); // 텍스트 출력

```

---

## ✅ 2. **innerText vs innerHTML**

| 속성 | 설명 | 사용 목적 |
| --- | --- | --- |
| `innerText` | 순수한 텍스트만 읽거나 쓴다 | 화면에 보여지는 텍스트 조작 |
| `innerHTML` | HTML 태그 포함한 문자열을 조작 | 요소를 추가하거나 태그 변경 |

```jsx

title.innerText = "<a>DOM 공부</a>"; // 텍스트로 출력
title.innerHTML = "<a>DOM 공부</a>"; // 실제 링크로 삽입

```

---

## ✅ 3. **버튼 클릭 시 숫자 증가/감소**

```html

<p id="num">0</p>
<button onclick="increate()">증가</button>
<button onclick="decrease()">감소</button>

```

```jsx

const num = document.getElementById("num");

const increate = () => {
    let n = parseInt(num.innerText);
    num.innerText = n + 1;
}

const decrease = () => {
    let n = parseInt(num.innerText);
    if (n > 0) {
        num.innerText = n - 1;
    }
}

```

📌 `parseInt`로 숫자 변환 → 증가 또는 감소 후 다시 출력

---

## ✅ 4. **버튼 클릭 시 태그 동적 생성 (누적)**

```html

<button onclick="mkH1()">h1 태그 생성</button>
<button onclick="mkA()">a 태그 생성</button>
<button onclick="mkUl()">ul 태그 생성</button>
<div id="divTag"></div>

```

```jsx

let divTag = document.getElementById("divTag");

const mkH1 = () => {
    divTag.innerHTML += "<h1>DOM 활용하기!</h1>";
}

const mkA = () => {
    divTag.innerHTML += '<a href="#">Google로 이동!</a><br>';
}

const mkUl = () => {
    divTag.innerHTML += `
        <ul>
            <li>HTML</li>
            <li>CSS</li>
            <li>JS</li>
        </ul>`;
}

```

📌 `innerHTML +=` 로 기존 내용에 누적 추가

---

## ✅ 5. **input 입력값을 alert 또는 화면에 출력**

```html

<input type="text" id="inputTag">
<button onclick="inputAlert()">클릭</button>

<input type="text" id="inputTag2">
<button onclick="inputH1()">h1출력</button>

```

```jsx

let inputTag = document.getElementById("inputTag");
let inputTag2 = document.getElementById("inputTag2");

const inputAlert = () => {
    alert(inputTag.value); // 경고창으로 출력
}

const inputH1 = () => {
    document.write(`<h1>${inputTag2.value}</h1>`); // 새 페이지에 출력
}

```

📌 `.value`로 input 값 읽기 가능

📌 `document.write()`는 기존 페이지를 덮으므로 신중히 사용

---

## ✅ 6. **클래스, name을 이용한 유사배열 처리**

```jsx

let dom_p = document.getElementsByClassName("dom_p");
console.log(dom_p[0].innerText); // 첫 번째 요소 접근

let input_name = document.getElementsByName("prac");
for (let i = 0; i < input_name.length; i++) {
    console.log(input_name[i].value);
}

```

📌 `getElementsByClassName`, `getElementsByName`는 모두 유사 배열이므로 반복문 사용 필요

---

# 📌 핵심 요점

- DOM은 자바스크립트로 HTML 요소를 조작하는 기술
- `.innerText`, `.innerHTML`, `.value`로 텍스트와 태그를 읽고 쓸 수 있다
- 버튼 클릭과 같은 **이벤트 처리**는 함수와 연결되어야 한다 (`onclick`)
- 요소 선택은 `id`, `class`, `name`, CSS 선택자 등 다양하게 가능하다
- 반복문과 조건문을 통해 다양한 동적 기능을 구현할 수 있다