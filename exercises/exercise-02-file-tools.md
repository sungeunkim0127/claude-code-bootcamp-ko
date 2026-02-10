# 연습 2: 파일 도구 — Read, Write, Edit

**소요 시간**: 45분
**사전 조건**: 레슨 6-8

## 목표

Claude Code를 통해 파일 읽기, 생성, 편집을 실습합니다.

## 설정

```bash
mkdir ~/claude-exercises/ex2
cd ~/claude-exercises/ex2

cat > server.js << 'EOF'
const http = require('http')
const PORT = 3000

const server = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' })
  res.end('Hello World')
})

server.listen(PORT, () => {
  console.log(`Running on ${PORT}`)
})
EOF

cat > config.json << 'EOF'
{
  "port": 3000,
  "host": "localhost",
  "debug": false
}
EOF

claude
```

## 과제

### 과제 1: 읽기와 설명 (5분)

Claude에게 요청: "server.js와 config.json을 읽고 이 프로젝트가 무엇을 하는지 설명해줘"

**확인**: Claude가 두 파일을 모두 읽고 정확한 설명을 제공함

---

### 과제 2: 새 파일 생성 (10분)

Claude에게 요청: "utils.js 파일을 만들어줘. `formatResponse(data)` 함수는 데이터를 `{ success: true, data }`로 감싸고, `logRequest(method, url)` 함수는 타임스탬프와 함께 로그를 출력해야 해"

**확인**: utils.js가 두 함수와 올바른 exports를 포함하여 존재함

---

### 과제 3: 기존 파일 편집 (10분)

Claude에게 요청: "server.js를 수정해서 하드코딩된 3000 대신 config.json의 포트를 사용하고, utils.js의 formatResponse 함수를 사용하도록 해줘"

**확인**: server.js가 config.json과 utils.js에서 import함

---

### 과제 4: 여러 줄 편집 (10분)

Claude에게 요청: "server.js에 /health 엔드포인트를 추가해줘. formatResponse를 사용해서 `{ status: 'ok', uptime: process.uptime() }`를 반환해야 해"

**확인**: 새 라우트가 존재하고 formatResponse를 사용함

---

### 과제 5: 전체 치환으로 이름 변경 (5분)

Claude에게 요청: "server.js에서 변수 `server`를 모든 곳에서 `httpServer`로 이름을 바꿔줘"

**확인**: 모든 참조가 업데이트됨, 남은 `server` 변수 없음

---

### 과제 6: 변경 사항 검토 (5분)

Claude에게 요청: "세 파일 모두 읽고 지금 어떻게 함께 동작하는지 설명해줘"

**확인**: Claude가 업데이트된 프로젝트의 정확한 요약을 제공함

## 완료 기준

- [ ] 파일을 읽고 설명을 받음
- [ ] Write 도구로 새 파일을 생성함
- [ ] Edit 도구로 기존 파일을 편집함
- [ ] 여러 줄 편집을 수행함
- [ ] replace_all을 사용하여 이름을 변경함
- [ ] Read vs Write vs Edit 도구 선택 기준을 이해함
