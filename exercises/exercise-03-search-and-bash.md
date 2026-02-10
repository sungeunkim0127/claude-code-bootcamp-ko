# 연습 3: 검색 도구와 Bash — Glob, Grep, Bash

**소요 시간**: 45분
**사전 조건**: 레슨 9-12

## 목표

파일 탐색, 내용 검색, 명령어 실행, 다중 도구 워크플로우를 마스터합니다.

## 설정

```bash
mkdir -p ~/claude-exercises/ex3/src/routes ~/claude-exercises/ex3/src/models ~/claude-exercises/ex3/tests
cd ~/claude-exercises/ex3

cat > src/app.js << 'EOF'
const express = require('express')
const userRoutes = require('./routes/users')
// TODO: 인증 미들웨어 추가
const app = express()
app.use('/api/users', userRoutes)
// FIXME: 에러 처리 미들웨어 추가
app.listen(3000)
module.exports = app
EOF

cat > src/routes/users.js << 'EOF'
const router = require('express').Router()
const User = require('../models/User')
// TODO: 입력 유효성 검사 추가
router.get('/', async (req, res) => {
  const users = await User.findAll()
  res.json(users)
})
router.post('/', async (req, res) => {
  const user = await User.create(req.body)
  res.json(user)
})
module.exports = router
EOF

cat > src/models/User.js << 'EOF'
class User {
  static async findAll() { return [] }
  static async create(data) { return { id: 1, ...data } }
  static async findById(id) { return { id, name: 'Test' } }
}
module.exports = User
EOF

cat > tests/users.test.js << 'EOF'
const User = require('../src/models/User')
// TODO: 더 포괄적인 테스트 추가
test('findAll returns array', async () => {
  const users = await User.findAll()
  expect(Array.isArray(users)).toBe(true)
})
EOF

cat > package.json << 'EOF'
{ "name": "ex3", "scripts": { "test": "jest", "start": "node src/app.js" } }
EOF

npm init -y > /dev/null 2>&1
claude
```

## 과제

### 과제 1: Glob으로 파일 찾기 (5분)

Claude에게 요청: "이 프로젝트의 모든 JavaScript 파일을 찾아줘"

**확인**: Claude가 Glob 도구를 사용하고, src/와 tests/ 안의 파일을 찾음

Claude에게 요청: "routes 디렉토리에 있는 모든 파일을 찾아줘"

**확인**: src/routes/users.js를 표시함

---

### 과제 2: Grep으로 내용 검색 (10분)

Claude에게 요청: "프로젝트에서 모든 TODO와 FIXME 주석을 찾아줘"

**확인**: TODO 주석 3개와 FIXME 1개를 찾음

Claude에게 요청: "어떤 파일이 User 모델을 import 또는 require하고 있어?"

**확인**: src/routes/users.js를 찾음

Claude에게 요청: "코드베이스에서 'async'가 몇 번 나오는지 알려줘"

**확인**: 파일별 횟수를 반환함

---

### 과제 3: Bash로 명령어 실행 (10분)

Claude에게 요청: "jest를 개발 의존성으로 설치하고 테스트를 실행해줘"

**확인**: jest가 설치되고 테스트 결과가 표시됨

Claude에게 요청: "프로젝트의 package.json 스크립트를 보여줘"

**확인**: 사용 가능한 npm 스크립트가 나열됨

---

### 과제 4: 다중 도구 워크플로우 — 버그 수정 (10분)

Claude에게 요청: "POST /api/users 엔드포인트에 입력 유효성 검사가 없어. 해당 엔드포인트를 찾고, name과 email 필드에 대한 유효성 검사를 추가하고, 테스트도 만들어줘."

**확인**: Claude가 Grep 도구로 라우트를 찾고, Read 도구로 내용을 파악하고, Edit 도구로 유효성 검사를 추가하고, Write 도구로 테스트를 생성하고, Bash 도구로 테스트를 실행함

---

### 과제 5: 다중 도구 워크플로우 — 감사 (10분)

Claude에게 요청: "전체 코드베이스에서 TODO/FIXME 주석을 검색하고, 주변 코드를 읽은 다음, 모든 이슈를 수정하고 주석을 제거해줘"

**확인**: 모든 TODO/FIXME가 해결됨, 주석이 제거됨, 테스트가 여전히 통과함

## 완료 기준

- [ ] Glob 도구를 사용하여 패턴으로 파일을 찾음
- [ ] Grep 도구를 사용하여 파일 내용을 검색함
- [ ] Bash 도구를 사용하여 명령어를 실행함
- [ ] 다중 도구 워크플로우를 완료함 (검색 → 읽기 → 편집)
- [ ] Glob vs Grep vs Bash를 언제 사용하는지 이해함
