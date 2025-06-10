# 🚢 FastAPI + Spring Boot 연동 기반 타이타닉 생존 예측 실습

## 🧭 1. 전체 흐름 요약

1. **사용자**가 `titanic.html`에서 정보를 입력
2. **Spring Boot**가 입력 데이터를 FastAPI 서버로 전송
3. **FastAPI**가 머신러닝 모델로 생존 예측 수행 후 결과 반환
4. **Spring Boot**가 결과를 `result.html`에 표시

---

## ⚙️ 2. FastAPI (`app.py`)

```python
from fastapi import FastAPI, Form
from pydantic import BaseModel
import pandas as pd
import joblib

app = FastAPI()

# 🔹 머신러닝 모델 로드
load_model = joblib.load("./knn_model2.pkl")

# 🔹 Pydantic 데이터 모델
class TitanicData(BaseModel):
    Pclass: int
    Sex: str
    Age: float
    SSA: int
    PCA: int
    Fare: float

# 🔹 기본 라우트 (테스트용)
@app.get("/")
def root():
    return "Hello World"

# 🔹 POST 테스트용 (문자열 받기)
@app.post("/post")
async def post(data: str):
    return {"data": data}

# 🔹 폼데이터 받기 테스트
@app.post("/form")
async def form(id: str = Form(...), pw: str = Form(...)):
    return {"id": id, "pw": pw}

# 🔹 핵심: 예측 API
@app.post("/data")
async def data(data: TitanicData):
    df = pd.DataFrame({
        'Pclass': [data.Pclass],
        'Sex': [data.Sex],
        'Age': [data.Age],
        'Siblings/Spouses Aboard': [data.SSA],
        'Parents/Children Aboard': [data.PCA],
        'Fare': [data.Fare],
    })

    # 예측 수행
    predict = load_model.predict(df)
    proba = load_model.predict_proba(df)

    predict_result = "생존하셨습니다." if predict[0] == 1 else "사망하셨습니다."
    percent = round(proba[0][1 if predict[0] == 1 else 0] * 100, 1)

    return {"predict": predict_result, "percent": percent}

```

---

## 🌐 3. Spring Boot 컨트롤러

### `MainController.java`: 메인 페이지 이동

```java
@Controller
public class MainController {

    @GetMapping("/titanic")
    public String titanic() {
        return "titanic";
    }
}

```

- 사용자가 타이타닉 예측 페이지에 진입하는 역할

---

### `FastController.java`: 폼데이터 수신 및 FastAPI 호출

```java
@Controller
public class FastController {

    @PostMapping("/analyze")
    public String analyze(@RequestParam Map<String, String> data, Model model) {

        Map<String, Object> jsonData = new HashMap<>();
        jsonData.put("Pclass", Integer.parseInt(data.get("Pclass")));
        jsonData.put("Sex", data.get("Sex")); // 성별 0(남), 1(여)
        jsonData.put("Age", Float.parseFloat(data.get("Age")));
        jsonData.put("SSA", Integer.parseInt(data.get("SSA")));
        jsonData.put("PCA", Integer.parseInt(data.get("PCA")));
        jsonData.put("Fare", Float.parseFloat(data.get("Fare")));

        try {
            WebClient webClient = WebClient.builder()
                .baseUrl("http://127.0.0.1:8000/data")
                .defaultHeader(HttpHeaders.CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE)
                .build();

            Map<String, Object> response = webClient.post()
                .bodyValue(jsonData)
                .retrieve()
                .bodyToMono(Map.class)
                .block(); // 동기 방식

            model.addAttribute("name", data.get("name"));
            model.addAttribute("result", response);

            return "result";

        } catch (Exception e) {
            model.addAttribute("error", "FastAPI와 통신 실패: " + e.getMessage());
            return "error";
        }
    }
}

```

- `@RequestParam Map<String, String>`으로 폼 데이터 수신
- FastAPI에 JSON POST 요청 → 예측 결과 받아 Thymeleaf 모델에 저장

---

## 🖼️ 4. HTML Template

### `titanic.html`: 입력 폼 페이지

```html
<form action="/analyze" method="post" class="p-4 border rounded bg-light">

    <input type="text" name="name" class="form-control" required>

    <input type="number" name="Pclass" class="form-control" required>

    <select name="Sex" class="form-select" required>
        <option value="0">남자</option>
        <option value="1">여자</option>
    </select>

    <input type="number" name="Age" class="form-control" required>
    <input type="number" name="SSA" class="form-control" required>
    <input type="number" name="PCA" class="form-control" required>
    <input type="number" name="Fare" step="0.01" class="form-control" required>

    <button type="submit" class="btn btn-primary">분석 결과 보기</button>
</form>

```

- 사용자의 입력을 받아 `/analyze`로 POST 전송

---

### `result.html`: 결과 출력

```html
<h2 th:text="${name} + ' 님의 생존 예측 결과'"></h2>

<div class="alert alert-success">
    <p><strong>결과:</strong> <span th:text="${result.predict}"></span></p>
    <p><strong>정확도:</strong> <span th:text="${result.percent}"></span>%</p>
</div>

```

- FastAPI에서 받은 예측 결과와 확률을 표시

---

## 🛠️ 5. Spring 설정 (`application.properties`)

```
server.port=8087

```

- Spring Boot 서버 포트 지정

---
## ✅ 핵심 요약

| 구성 요소 | 설명 |
| --- | --- |
| FastAPI | 모델 예측 API 제공 (`/data`) |
| Spring Controller | 사용자 입력 수집 & FastAPI 호출 |
| HTML (Thymeleaf) | 입력 폼 (`titanic.html`), 결과 출력 (`result.html`) |
| 머신러닝 모델 | `knn_model2.pkl`로 저장된 sklearn 모델 |
| 통신 방식 | `WebClient` → JSON POST 방식 |