## ✅ JavaScript DOM 실습 정리

---

### 1. ✅ **`<p>` 요소 스타일 적용**

### 📄 코드:

```jsx

let pTag = document.getElementsByTagName('p');
pTag[0].style.backgroundColor = 'red';
pTag[0].style.fontSize = '30px';
pTag[0].style.color = '#fff';

```

### 📌 설명:

- 첫 번째 `<p>` 태그에 직접 스타일을 적용
    - 배경: 빨강
    - 글자색: 흰색
    - 폰트 크기: 30px

---

### 2. ✅ **버튼 클릭 시 `<span>` 스타일 토글**

### 📄 코드:

```jsx

let btn = document.getElementsByTagName('button')[0];
let span = document.getElementsByTagName('span')[0];

let toggle = false;
btn.addEventListener('click', () => {
    if (toggle) {
        span.style.color = 'black';
        span.style.fontSize = '16px';
        span.style.fontWeight = 'normal';
    } else {
        span.style.color = 'red';
        span.style.fontSize = '24px';
        span.style.fontWeight = 'bold';
    }
    toggle = !toggle;
});

```

### 📌 설명:

- 버튼 클릭 시 `<span>` 스타일을 빨강/검정으로 토글
- 글자 크기와 굵기도 함께 변경

---

### 3. ✅ **`<div>` 박스 스타일 제어**

### 🟠 `.radius` 버튼 – 둥근 테두리

```jsx

let radiusBtn = document.querySelector('.radius');
let divs = document.querySelectorAll('div');

radiusBtn.addEventListener('click', () => {
    divs.forEach(div => {
        div.style.borderRadius = (div.style.borderRadius === '50px') ? '0' : '50px';
    });
});

```

### 🟢 `.move` 버튼 – 이동/복귀

```jsx

let moveBtn = document.querySelector('.move');
let [redBox, blueBox, greenBox, grayBox] = [
    document.getElementById('redBox'),
    document.getElementById('blueBox'),
    document.getElementById('greenBox'),
    document.getElementById('grayBox')
];

moveBtn.addEventListener('click', () => {
    redBox.style.marginLeft = '0';
    blueBox.style.marginLeft = '50px';
    greenBox.style.marginLeft = '100px';
    grayBox.style.marginLeft = '150px';
});

moveBtn.addEventListener('dblclick', () => {
    [redBox, blueBox, greenBox, grayBox].forEach(box => {
        box.style.marginLeft = '0';
    });
});

```

### 📌 설명:

- `.radius`: 둥근 테두리 토글
- `.move`: 클릭 시 이동 → 더블클릭 시 원위치

---

### 4. ✅ **이벤트 객체 확인**

### 📄 코드:

```jsx

let btn_event = document.getElementById('btn_event');

btn_event.onclick = (event) => {
    console.log(event);                // 전체 이벤트 객체
    console.log(event.target);        // 클릭된 요소
    console.log(event.target.innerText); // 버튼 텍스트
};

```

### 📌 설명:

- 이벤트 객체를 통해 클릭된 요소 및 정보 확인

---

### 5. ✅ **하나의 핸들러로 여러 이벤트 처리**

### 📄 코드:

```jsx

let myBtn = document.getElementById('myBtn');

const handler = (event) => {
    switch (event.type) {
        case "click":
            console.log('클릭');
            break;
        case "mouseover":
            event.target.style.backgroundColor = 'red';
            break;
        case "mouseout":
            event.target.style.backgroundColor = 'yellow';
            break;
    }
};

myBtn.addEventListener('click', handler);
myBtn.addEventListener('mouseover', handler);
myBtn.addEventListener('mouseout', handler);

```

### 📌 설명:

- 한 함수로 클릭, 마우스오버, 마우스아웃을 모두 처리
- 효율적이고 유지보수가 쉬움

---

### 6. ✅ **DOM 노드/속성 추가 및 제거 실습**

### 📄 코드:

```jsx

// 1. li 요소 생성
let li = document.createElement('li');

// 2. img 요소 생성
let img = document.createElement('img');

// 3. img에 src 속성 만들고 값 부여
let src = document.createAttribute('src');
src.value = "img주소";
img.setAttributeNode(src);

// 4. 텍스트 노드 생성
let text = document.createTextNode('간장게장');

// 5. li에 텍스트와 이미지 추가
li.appendChild(text);
li.appendChild(img);

// 6. ul에 li 추가
let ul = document.getElementById('ulTag');
ul.appendChild(li);

// 7. li 리스트에서 네 번째 li 삭제
let li_list = document.getElementsByTagName('li');
li_list[3].remove();

```

### 📌 설명:

- DOM 요소를 **동적으로 생성, 속성 부여, 삽입, 삭제**하는 실습
- 순서대로 요소 만들기 → 속성 설정 → 부모에 추가 → 삭제

---

## 📚 참고 요약

| 기능 | 내용 |
| --- | --- |
| `.style` | JS에서 CSS 직접 조작 |
| `addEventListener` | 이벤트 동적 등록 |
| `event 객체` | 이벤트 발생 시 정보 제공 |
| `createElement / appendChild` | 요소 동적 생성 및 추가 |
| `remove()` | 요소 삭제 |