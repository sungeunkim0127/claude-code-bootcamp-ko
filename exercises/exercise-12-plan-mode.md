# 연습 12: 플랜 모드 마스터하기
**소요 시간**: 45분
**사전 조건**: 레슨 71-74

## 목표
플랜 모드를 사용하여 복잡한 기능을 실행 전에 검토 가능한 단계로 분해하고, 난이도 수준별 계획 전략을 비교합니다.

## 설정
```bash
mkdir -p ~/plan-mode-lab/src ~/plan-mode-lab/tests
cd ~/plan-mode-lab
git init
cat > src/users.js << 'EOF'
const users = [];

function addUser(name, email) {
  users.push({ name, email, createdAt: new Date() });
}

function findUser(email) {
  return users.find(u => u.email === email);
}

module.exports = { addUser, findUser, users };
EOF
cat > src/server.js << 'EOF'
const http = require("http");
const { addUser, findUser } = require("./users");

const server = http.createServer((req, res) => {
  if (req.method === "POST" && req.url === "/users") {
    let body = "";
    req.on("data", chunk => body += chunk);
    req.on("end", () => {
      const { name, email } = JSON.parse(body);
      addUser(name, email);
      res.writeHead(201);
      res.end(JSON.stringify({ status: "created" }));
    });
  } else if (req.method === "GET" && req.url.startsWith("/users/")) {
    const email = decodeURIComponent(req.url.split("/")[2]);
    const user = findUser(email);
    res.writeHead(user ? 200 : 404);
    res.end(JSON.stringify(user || { error: "not found" }));
  }
});

server.listen(3000);
module.exports = server;
EOF
npm init -y
git add -A && git commit -m "Initial user service"
```

## 과제

### 과제 1: 복잡한 기능을 위해 플랜 모드 진입 (10분)
Claude에게 사용자 서비스에 인증을 추가하는 계획을 세우되 (실행하지 말고) 요청하세요. `--plan` 플래그 또는 shift+tab을 사용하여 플랜 모드에 진입합니다.

```bash
cd ~/plan-mode-lab
claude --plan "Add JWT-based authentication to this user service. Include signup with password hashing, login that returns a token, and middleware that protects the GET /users/:email endpoint."
```

Claude가 생성한 계획을 검토하세요. 다음이 포함되어야 합니다:
- 생성하거나 수정할 파일 목록.
- 작업 순서 (의존성 먼저 설치, 그 다음 유틸리티, 그 다음 통합).
- 실제 코드 변경 없음 -- 구조화된 개요만.

**확인**: `git status`가 변경 사항이 없음을 확인하세요. 계획은 제안일 뿐, 실행이 아닙니다.

---

### 과제 2: 계획 검토 및 수정 (10분)
생성된 계획을 읽고 승인 전에 수정을 요청하세요:

1. 계획이 비밀번호를 평문으로 저장하는 경우, Claude에게 bcrypt 해싱을 포함하도록 계획을 수정하도록 요청하세요.
2. 계획에 입력 유효성 검사가 없는 경우, Claude에게 이메일 형식과 비밀번호 길이에 대한 유효성 검사 단계를 추가하도록 요청하세요.
3. 계획에 테스트가 포함되지 않은 경우, Claude에게 테스트 단계를 추가하도록 요청하세요.

플랜 모드에서 후속 프롬프트를 사용하여 반복하세요:
```
"Revise the plan: add input validation for email and password, and include a testing step using Node's built-in assert module."
```

**확인**: 수정된 계획이 명시적으로 bcrypt (또는 argon2), 입력 유효성 검사 규칙, 테스트 파일을 언급하는지 확인하세요.

---

### 과제 3: 계획 승인 및 실행 (10분)
플랜 모드에서 실행 모드로 전환하고 계획을 승인하세요. Claude가 이제 각 단계를 구현합니다.

실행을 관찰하고 다음을 기록하세요:
- Claude가 계획된 작업 순서를 따르는가?
- 계획된 모든 파일이 생성되었는가?
- 구현이 개요와 일치하는가?

```bash
git diff --stat
```

**확인**: `git diff --stat`를 실행하여 변경/생성된 파일이 계획과 일치하는지 확인하세요. `node -e "require('./src/server.js')"`를 실행하여 서버가 구문 오류 없이 시작되는지 확인하세요 (그런 다음 프로세스를 종료하세요).

---

### 과제 4: 플랜 모드 유무에 따른 결과 비교 (10분)
저장소를 초기화하고 플랜 모드 없이 동일한 작업을 시도하여 비교하세요:

```bash
cd ~/plan-mode-lab
git checkout -- .
git clean -fd
```

이제 계획 없이 동일한 요청을 직접 실행하세요:
```bash
claude --print "Add JWT-based authentication to this user service. Include signup with password hashing, login that returns a token, and middleware that protects the GET /users/:email endpoint."
```

두 가지 접근 방식을 비교하세요:
- 직접 접근 방식이 계획에서 포착한 단계를 놓쳤는가?
- 파일 구조가 잘 정리되었는가?
- 계획이 방지했을 수 있는 오류가 있었는가?

**확인**: `~/plan-mode-lab/comparison.txt`에 관찰한 최소 두 가지 차이점을 문서화하는 짧은 비교 노트를 작성하세요.

---

### 과제 5: 아키텍처 계획을 위한 확장 사고 사용 (5분)
확장 사고를 사용하여 더 복잡한 아키텍처 변경을 계획하세요:

```bash
cd ~/plan-mode-lab
git checkout -- .
git clean -fd
claude --plan "Refactor this application from a monolithic single-file server into a layered architecture with separate route handlers, a service layer, a data access layer, and middleware. Include error handling and request logging."
```

확장 사고가 계획에 어떤 영향을 미치는지 관찰하세요:
- 계획이 트레이드오프에 대한 더 깊은 고려를 보여주는가?
- 엣지 케이스와 오류 시나리오가 다루어졌는가?
- 레이어 간 의존성 그래프가 명확하게 정의되었는가?

**확인**: 계획에 최소 네 개의 구별되는 레이어 (라우트, 서비스, 데이터 접근, 미들웨어)가 포함되고 레이어 간 의존성 방향이 명시되어 있는지 확인하세요.

## 완료 기준
- [ ] 플랜 모드가 파일 수정 없이 구조화된 개요를 생성함
- [ ] 승인 전에 유효성 검사, 해싱, 테스트를 포함하도록 계획이 수정됨
- [ ] 실행이 계획된 단계를 따르고 작동하는 코드를 생성함
- [ ] 비교 노트가 계획된 실행과 비계획 실행 간의 구체적 차이를 문서화함
- [ ] 확장 사고가 명확한 레이어 경계를 가진 계층형 아키텍처 계획을 생성함
