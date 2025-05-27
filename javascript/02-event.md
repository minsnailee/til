## ✅ JavaScript에서 이벤트 연결하는 3가지 방법

### 1. **인라인 방식**

- HTML 태그 내부에서 직접 JavaScript 함수를 호출하는 방식

```html

<button onclick="show()">클릭</button>

```

```

const show = () => {
    alert('클릭이벤트 발생');
}

```

---

### 2. **프로퍼티 방식 (Property 방식)**

- JavaScript 코드에서 DOM 요소의 이벤트 속성을 직접 설정

```

const p_event = document.getElementById("p_event");

const show2 = () => {
    console.log('프로퍼티 이벤트 발생');
}

// 기존 이벤트를 덮어씌움
p_event.onclick = show2;
p_event.onclick = () => {
    console.log('새로운 프로퍼티 이벤트 발생');
}

```

> ⚠️ 같은 이벤트 타입에 대해 여러 개 등록 불가능. 마지막으로 설정한 핸들러만 적용됨.
> 

---

### 3. **addEventListener 방식**

- 하나의 요소에 동일한 이벤트 타입을 **여러 개 등록 가능**

```

const p_event1 = document.getElementById('p_event1');

p_event1.addEventListener('mouseover', () => {
    console.log('마우스 올렸다.');
});
p_event1.addEventListener('mouseout', () => {
    console.log('마우스 뗐다.');
});
p_event1.addEventListener('click', () => {
    console.log('클릭');
});
p_event1.addEventListener('click', () => {
    console.log('add event');
});

```

---

## ✅ 예제: 버튼 클릭 시 이미지 변경

### 목표:

버튼을 클릭하면 이미지가 두 개의 src 값 사이에서 **토글**됨

```

let imgID = document.getElementById("imgID");
let btnID = document.getElementById("btnID");

let src1 = "이미지 주소1";
let src2 = "이미지 주소2";

btnID.addEventListener('click', () => {
    if(imgID.src === src1){
        imgID.src = src2;
    } else {
        imgID.src = src1;
    }
});

```

> imgID.src는 전체 URL이기 때문에 === 비교는 실제 값으로 비교해야 정확함 (개선 여지 있음).
> 

---

## ✅ 예제: input 입력 값을 실시간으로 p 태그에 반영

### 목표:

사용자가 입력창에 입력할 때마다 해당 값을 `<p>` 태그에 표시

```

let inp = document.getElementById("inputTxt");
let cont = document.getElementById('container');

inp.addEventListener('keyup', () => {
    cont.innerText = inp.value;
});

```

---

## 🔁 요약

| 방식 | 특징 | 중복 등록 |
| --- | --- | --- |
| 인라인 방식 | HTML 태그에 직접 JS 함수 호출 | ❌ |
| 프로퍼티 방식 | `element.onclick = ...` | ❌ (마지막 설정만 적용됨) |
| addEventListener | `element.addEventListener(...)` | ✅ (여러 이벤트 가능) |