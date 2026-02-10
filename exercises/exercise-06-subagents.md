# 연습 6: 서브에이전트 활용하기

**소요 시간**: 45분
**사전 조건**: 레슨 26-32

## 목표
코드베이스 분석, 복잡한 작업, 병렬 실행을 위해 전문화된 서브에이전트에 작업을 위임하는 방법을 학습합니다.

## 설정

```bash
mkdir -p ~/claude-exercises/ex6/src/auth ~/claude-exercises/ex6/src/api ~/claude-exercises/ex6/src/db ~/claude-exercises/ex6/tests
cd ~/claude-exercises/ex6

cat > src/auth/login.js << 'EOF'
const jwt = require('jsonwebtoken')
const bcrypt = require('bcrypt')
const { findUserByEmail } = require('../db/users')

async function login(email, password) {
  const user = await findUserByEmail(email)
  if (!user) throw new Error('User not found')
  const valid = await bcrypt.compare(password, user.passwordHash)
  if (!valid) throw new Error('Invalid password')
  return jwt.sign({ userId: user.id, role: user.role }, process.env.JWT_SECRET)
}

module.exports = { login }
EOF

cat > src/auth/middleware.js << 'EOF'
const jwt = require('jsonwebtoken')
// TODO: 토큰 갱신 로직 추가
function authMiddleware(req, res, next) {
  const token = req.headers.authorization?.split(' ')[1]
  if (!token) return res.status(401).json({ error: 'No token' })
  try {
    req.user = jwt.verify(token, process.env.JWT_SECRET)
    next()
  } catch (err) {
    res.status(403).json({ error: 'Invalid token' })
  }
}
module.exports = { authMiddleware }
EOF

cat > src/api/users.js << 'EOF'
const router = require('express').Router()
const { findAllUsers, createUser, findUserById } = require('../db/users')
const { authMiddleware } = require('../auth/middleware')
// FIXME: 속도 제한 추가
router.get('/', authMiddleware, async (req, res) => {
  const users = await findAllUsers()
  res.json(users)
})
router.get('/:id', authMiddleware, async (req, res) => {
  const user = await findUserById(req.params.id)
  if (!user) return res.status(404).json({ error: 'Not found' })
  res.json(user)
})
router.post('/', async (req, res) => {
  const user = await createUser(req.body)
  res.status(201).json(user)
})
module.exports = router
EOF

cat > src/api/posts.js << 'EOF'
const router = require('express').Router()
const { authMiddleware } = require('../auth/middleware')
// TODO: 페이지네이션 추가
router.get('/', async (req, res) => { res.json([]) })
router.post('/', authMiddleware, async (req, res) => { res.status(201).json({}) })
module.exports = router
EOF

cat > src/db/users.js << 'EOF'
const users = []
async function findAllUsers() { return users }
async function createUser(data) { const u = { id: users.length + 1, ...data }; users.push(u); return u }
async function findUserById(id) { return users.find(u => u.id === Number(id)) }
async function findUserByEmail(email) { return users.find(u => u.email === email) }
module.exports = { findAllUsers, createUser, findUserById, findUserByEmail }
EOF

cat > src/app.js << 'EOF'
const express = require('express')
const userRoutes = require('./api/users')
const postRoutes = require('./api/posts')
const app = express()
app.use(express.json())
app.use('/api/users', userRoutes)
app.use('/api/posts', postRoutes)
app.listen(3000, () => console.log('Server running on port 3000'))
module.exports = app
EOF

cat > tests/auth.test.js << 'EOF'
const { login } = require('../src/auth/login')
test('login throws on missing user', async () => {
  await expect(login('nobody@test.com', 'pass')).rejects.toThrow('User not found')
})
EOF

cat > package.json << 'EOF'
{ "name": "ex6", "scripts": { "test": "jest", "start": "node src/app.js" } }
EOF

claude
```

## 과제

### 과제 1: Explore 에이전트 실행하기 (8분)

Claude에게 Explore 서브에이전트를 사용하여 프로젝트 아키텍처를 분석하도록 요청합니다:

```
"Explore 에이전트를 'very thorough' 모드로 사용하여 이 프로젝트를 분석해줘. 다음을 파악해줘:
- 진입점과 요청 흐름
- 인증 구현 방식
- 데이터베이스 레이어 설계
- 보안 우려 사항"
```

**예상 결과**:
- 읽기 전용 도구(Glob, Grep, Read 도구)로 Explore 에이전트가 실행됨
- 아키텍처에 대한 포괄적인 분석이 반환됨
- 보안 이슈가 식별됨 (예: 속도 제한 미비, 토큰 갱신 없음)

**확인**: 분석이 네 가지 항목을 모두 다루고 최소 두 개의 TODO/FIXME 항목을 식별했는지 확인

---

### 과제 2: 범용 서브에이전트로 복잡한 작업 수행 (10분)

다단계 작업을 범용 서브에이전트에 위임합니다:

```
"서브에이전트를 실행하여 다음을 수행해줘:
1. 코드베이스의 모든 TODO와 FIXME 주석 찾기
2. 각 이슈를 적절하게 수정 (속도 제한, 페이지네이션, 토큰 갱신 추가)
3. 추가된 내용을 설명하는 관련 주석 추가
4. 모든 변경 사항 요약 보고"
```

**예상 결과**:
- 서브에이전트가 명확한 다단계 지시를 받음
- 여러 파일에 걸쳐 독립적으로 작업
- 변경 사항 요약을 보고함

**확인**: `grep -r "TODO\|FIXME" src/` 실행 -- 모든 원본 TODO/FIXME 주석이 해결되어야 함

---

### 과제 3: 병렬 서브에이전트 실행 (8분)

Claude에게 코드베이스의 서로 다른 부분을 동시에 분석하도록 요청합니다:

```
"병렬로 세 개의 Explore 에이전트를 실행해줘:
1. src/auth/의 인증 모듈 분석
2. src/api/의 API 라우트 분석
3. src/db/의 데이터베이스 레이어 분석
그런 다음 결과를 하나의 아키텍처 요약으로 통합해줘."
```

**예상 결과**:
- 세 개의 서브에이전트가 동시에 실행됨
- 각각 할당된 모듈에 집중
- 결과가 통합된 요약으로 결합됨

**확인**: 출력에 각 모듈에 대한 개별 분석과 통합 요약이 포함되어 있는지 확인

---

### 과제 4: 백그라운드 작업과 진행 상황 확인 (8분)

더 오래 실행되는 백그라운드 작업을 시작합니다:

```
"백그라운드 서브에이전트를 시작하여 데이터베이스 레이어를 리팩토링해줘:
- 모든 db 함수에 입력 유효성 검사 추가
- 모든 내보내기 함수에 JSDoc 주석 추가
- 모든 것을 다시 내보내는 새 db/index.js 생성
완료되면 알려줘."
```

작업이 진행되는 동안 Claude에게 물어봅니다: "백그라운드 에이전트가 지금 무엇을 하고 있어?"

**예상 결과**:
- 백그라운드 작업이 독립적으로 실행됨
- 진행 상황 업데이트를 확인할 수 있음
- 메인 대화를 차단하지 않고 작업이 완료됨

**확인**: `src/db/users.js`에 JSDoc 주석과 유효성 검사가 있고, `src/db/index.js`가 존재하는지 확인

---

### 과제 5: 작업 관리 및 분해 (6분)

Claude의 작업 관리 기능을 사용하여 기능을 계획하고 실행합니다:

```
"다음 기능을 하위 작업으로 분해한 다음 각각을 실행해줘:
사용자 역할 시스템 추가:
- 역할 정의 파일
- 역할을 검사하는 미들웨어
- 관리자 전용 보호 라우트
- 역할 검사 테스트"
```

**예상 결과**:
- Claude가 하위 작업으로 구조화된 계획을 생성
- 각 하위 작업이 순서대로 실행됨
- 작업 간 의존성이 존중됨

**확인**: 역할 시스템이 전체적으로 작동하는지 확인: 역할 정의가 존재하고, 미들웨어가 역할을 검사하고, 라우트가 보호됨

---

### 과제 6: 적절한 서브에이전트 선택하기 (5분)

Claude에게 다음 요청을 처리하도록 하고 어떤 서브에이전트 유형이 선택되는지 관찰합니다:

1. "이 프로젝트에서 인증 흐름이 어떻게 작동해?" (Explore를 사용해야 함)
2. "모든 API 라우트에 오류 처리를 추가하고 테스트를 작성해줘" (범용 서브에이전트를 사용해야 함)
3. "npm test를 실행하고 결과를 보여줘" (Bash 또는 직접 도구를 사용해야 함)

**예상 결과**:
- 서로 다른 작업에 대해 서로 다른 서브에이전트 유형이 선택됨
- Claude가 각 유형이 선택된 이유를 설명

**확인**: 각 요청이 작업 요구 사항에 맞는 적절한 서브에이전트 유형을 사용하는지 확인

---

## 완료 기준

- [ ] 코드베이스 분석을 위한 Explore 에이전트를 성공적으로 실행함
- [ ] 복잡한 다단계 작업을 범용 서브에이전트에 위임함
- [ ] 병렬 서브에이전트를 실행하고 결합된 결과를 받음
- [ ] 백그라운드 작업을 사용하고 진행 상황을 확인함
- [ ] 작업 관리를 사용하여 기능을 하위 작업으로 분해함
- [ ] Explore, 범용, Bash 서브에이전트를 언제 사용해야 하는지 이해함
