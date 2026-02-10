# 연습 9: 고급 훅 & 매처

**소요 시간**: 1시간
**사전 조건**: 레슨 48-50

## 목표
특정 도구 매처, 정규식 패턴, 훅 체이닝, 범위별 구성, 완전한 프로젝트 훅 시스템을 사용하여 정교한 훅 구성을 구축합니다.

## 설정

```bash
mkdir -p ~/claude-exercises/ex9/src/api ~/claude-exercises/ex9/src/models ~/claude-exercises/ex9/src/config ~/claude-exercises/ex9/tests ~/claude-exercises/ex9/logs ~/claude-exercises/ex9/scripts
cd ~/claude-exercises/ex9
git init
npm init -y > /dev/null 2>&1
npm install --save-dev prettier eslint > /dev/null 2>&1

cat > src/api/users.js << 'EOF'
const express = require('express')
const router = express.Router()
router.get('/', (req, res) => res.json([]))
router.post('/', (req, res) => res.status(201).json(req.body))
module.exports = router
EOF

cat > src/api/products.js << 'EOF'
const express = require('express')
const router = express.Router()
router.get('/', (req, res) => res.json([]))
router.post('/', (req, res) => res.status(201).json(req.body))
module.exports = router
EOF

cat > src/models/user.js << 'EOF'
class User {
  constructor(name, email) { this.name = name; this.email = email }
  validate() { return this.name && this.email }
}
module.exports = User
EOF

cat > src/config/database.js << 'EOF'
module.exports = {
  host: 'localhost',
  port: 5432,
  database: 'myapp',
  pool: { min: 2, max: 10 }
}
EOF

cat > tests/user.test.js << 'EOF'
const User = require('../src/models/user')
test('validates user', () => {
  const u = new User('Alice', 'alice@test.com')
  expect(u.validate()).toBe(true)
})
test('rejects incomplete user', () => {
  const u = new User('', '')
  expect(u.validate()).toBe(false)
})
EOF

cat > scripts/validate-json.sh << 'EOF'
#!/bin/bash
python3 -c "import json,sys; json.load(open(sys.argv[1]))" "$1" 2>/dev/null
if [ $? -ne 0 ]; then
  echo "Invalid JSON in $1"
  exit 1
fi
echo "JSON valid: $1"
EOF
chmod +x scripts/validate-json.sh

cat > .prettierrc << 'EOF'
{ "semi": false, "singleQuote": true, "tabWidth": 2 }
EOF

mkdir -p .claude
claude
```

## 과제

### 과제 1: 특정 도구 매처로 훅 생성 (10분)

파일 패턴이 아닌 도구 이름으로 특정 도구를 대상으로 하는 훅을 만듭니다.

`.claude/settings.json`에 추가합니다:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "command": "echo \"[$(date '+%H:%M:%S')] BASH: $CLAUDE_BASH_COMMAND\" >> logs/bash-commands.log"
      },
      {
        "matcher": "Write",
        "command": "echo \"[$(date '+%H:%M:%S')] WRITE: $CLAUDE_FILE_PATH\" >> logs/file-writes.log"
      },
      {
        "matcher": "Edit",
        "command": "echo \"[$(date '+%H:%M:%S')] EDIT: $CLAUDE_FILE_PATH\" >> logs/file-edits.log"
      }
    ]
  }
}
```

각 매처를 개별적으로 테스트합니다:

1. Claude에게 요청: "Run `ls -la src/`" -- `bash-commands.log`에 기록되어야 함
2. Claude에게 요청: "src/helpers.js 파일을 새로 만들고 isEmpty 함수를 작성해줘" -- `file-writes.log`에 기록되어야 함
3. Claude에게 요청: "src/helpers.js에 trim 함수를 추가해줘" -- `file-edits.log`에 기록되어야 함

**확인**: 각 로그 파일을 개별적으로 확인합니다:
```bash
cat logs/bash-commands.log
cat logs/file-writes.log
cat logs/file-edits.log
```
각각 해당 작업 유형만 포함해야 합니다.

---

### 과제 2: 매처에서 정규식 패턴 사용 (12분)

정규식을 사용하여 특정 파일 유형과 경로를 대상으로 하는 매처를 구축합니다.

`.claude/settings.json`에 다음 PostToolUse 훅을 추가하여 업데이트합니다:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "command": "echo \"[$(date '+%H:%M:%S')] BASH: $CLAUDE_BASH_COMMAND\" >> logs/bash-commands.log"
      },
      {
        "matcher": "Write",
        "command": "echo \"[$(date '+%H:%M:%S')] WRITE: $CLAUDE_FILE_PATH\" >> logs/file-writes.log"
      },
      {
        "matcher": "Edit",
        "command": "echo \"[$(date '+%H:%M:%S')] EDIT: $CLAUDE_FILE_PATH\" >> logs/file-edits.log"
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Write|Edit:.*\\.test\\.js$",
        "command": "echo 'Test file modified -- consider running npm test' >> logs/test-changes.log"
      },
      {
        "matcher": "Write|Edit:src/api/.*\\.js$",
        "command": "echo \"[$(date '+%H:%M:%S')] API route changed: $CLAUDE_FILE_PATH\" >> logs/api-changes.log"
      },
      {
        "matcher": "Write|Edit:src/config/.*",
        "command": "echo \"WARNING: Config file modified: $CLAUDE_FILE_PATH\" >> logs/config-audit.log"
      },
      {
        "matcher": "Write|Edit:.*\\.(js|jsx|ts|tsx)$",
        "command": "npx prettier --write \"$CLAUDE_FILE_PATH\" 2>/dev/null"
      }
    ]
  }
}
```

정규식 매처를 테스트합니다:

1. Claude에게 요청: "tests/user.test.js에 이메일 형식을 확인하는 새 테스트를 추가해줘"
   확인: `logs/test-changes.log`에 항목이 있어야 함

2. Claude에게 요청: "src/api/users.js에 PATCH 엔드포인트를 추가해줘"
   확인: `logs/api-changes.log`에 항목이 있어야 함

3. Claude에게 요청: "src/config/database.js에 연결 타임아웃 설정을 추가해줘"
   확인: `logs/config-audit.log`에 WARNING 항목이 있어야 함

4. Claude에게 요청: "Product 클래스가 있는 src/models/product.js를 만들어줘"
   확인: 파일이 포매팅되어야 하지만, api-changes나 config-audit 로그에는 항목이 없어야 함

**확인**: 각 정규식이 의도한 대상만 매칭하는지 확인합니다:
```bash
echo "--- Test changes ---" && cat logs/test-changes.log
echo "--- API changes ---" && cat logs/api-changes.log
echo "--- Config audit ---" && cat logs/config-audit.log
```

---

### 과제 3: 여러 훅 체이닝 (10분)

유효성 검사 파이프라인으로 함께 작동하는 훅 시퀀스를 만듭니다.

JSON 파일용 체인을 형성하는 다음 PreToolUse 훅을 추가합니다:

```json
{
  "matcher": "Write:.*\\.json$",
  "command": "echo \"JSON write detected: $CLAUDE_FILE_PATH\" >> logs/json-pipeline.log"
},
{
  "matcher": "Write:package\\.json$",
  "command": "echo 'AUDIT: package.json modification attempted' >> logs/json-pipeline.log && echo 'Checking for prohibited packages...' >> logs/json-pipeline.log"
}
```

PostToolUse 훅도 추가합니다:

```json
{
  "matcher": "Write:.*\\.json$",
  "command": "bash scripts/validate-json.sh \"$CLAUDE_FILE_PATH\" >> logs/json-pipeline.log 2>&1"
}
```

Claude에게 요청하여 체인을 테스트합니다: "package.json에 'tsc'를 실행하는 'build' 스크립트를 추가해줘"

**예상 결과**:
- PreToolUse: 두 JSON 훅 모두 실행됨 (일반 JSON + package.json 전용)
- 쓰기가 진행됨
- PostToolUse: 결과에 대해 JSON 유효성 검사가 실행됨

**확인**:
```bash
cat logs/json-pipeline.log
```
로그에 전체 파이프라인이 표시되어야 합니다: 감지, 감사, 유효성 검사 항목이 순서대로.

---

### 과제 4: 다른 범위에서 훅 구성 (12분)

사용자 범위와 프로젝트 범위에서 훅을 설정하여 우선순위와 계층화를 이해합니다.

1. **프로젝트 수준 훅** (`.claude/settings.json`에 이미 구성됨):
   이 프로젝트에만 적용됩니다.

2. **사용자 수준 훅**: Claude에게 전역 훅 추가를 요청합니다:
   ```
   "~/.claude/settings.json을 읽고 매처 '.*'로 PostToolUse 훅을 추가해줘. ~/claude-global-activity.log에 '[timestamp] GLOBAL: tool=$TOOL_NAME file=$CLAUDE_FILE_PATH' 형식으로 추가해줘. 기존 설정은 유지해줘."
   ```

3. 두 범위가 모두 실행되는지 테스트합니다. Claude에게 요청:
   ```
   "src/models/user.js에 findById 정적 메서드를 추가해줘"
   ```

4. 범위 계층화를 확인합니다:
   ```bash
   # 프로젝트 수준 로그에 항목이 있어야 함
   cat logs/file-edits.log

   # 사용자 수준 로그에도 항목이 있어야 함
   cat ~/claude-global-activity.log
   ```

5. 프로젝트 훅이 다른 디렉토리에 영향을 주지 않는지 테스트합니다:
   ```bash
   mkdir -p ~/claude-exercises/ex9-other
   ```
   `~/claude-exercises/ex9-other`에서 Claude를 열고 파일을 생성하도록 요청합니다. ex9의 프로젝트 수준 훅이 실행되지 않아야 합니다.

**확인**: 프로젝트 훅은 프로젝트에 격리되고, 사용자 훅은 모든 프로젝트에 전역적으로 적용됩니다.

---

### 과제 5: 프로젝트를 위한 완전한 훅 시스템 구축 (16분)

모든 것을 결합하여 프로젝트를 위한 프로덕션 품질의 훅 구성을 만듭니다.

Claude에게 포괄적인 훅 시스템을 설계하고 구현하도록 요청합니다:

```
"이 Node.js 프로젝트를 위해 다음 요구 사항으로 완전한 .claude/settings.json 훅 구성을 만들어줘:

PreToolUse 훅:
1. 모든 도구 사용을 타임스탬프와 함께 logs/tool-activity.log에 기록
2. *.env* 또는 *secret* 또는 *credential*과 매칭되는 파일에 대한 Write 또는 Edit 차단
3. 설정 파일이 수정될 때 경고 (하지만 허용)

PostToolUse 훅:
4. Write 또는 Edit 후 .js/.jsx/.ts/.tsx 파일을 prettier로 자동 포매팅
5. API 라우트 변경 (src/api/*)을 logs/api-audit.log에 기록
6. 테스트 파일 변경을 logs/test-audit.log에 기록
7. .json 파일이 작성된 후 JSON 유효성 검사 실행
8. 모든 Bash 명령과 종료 컨텍스트를 logs/bash-audit.log에 기록

Notification 훅:
9. 모든 알림을 타임스탬프와 함께 logs/session.log에 추가"
```

Claude가 구성을 생성한 후 다음 테스트 시퀀스를 실행하여 확인합니다:

1. "src/api/orders.js에 GET과 POST 엔드포인트가 있는 새 API 라우트 파일을 만들어줘"
2. "tests/orders.test.js에 기본 테스트가 있는 테스트 파일을 추가해줘"
3. "API_KEY=test123이 포함된 .env.local 파일을 생성해봐"
4. "npm test를 실행해줘"
5. "package.json에 'start' 스크립트를 추가해줘"

**확인**:
```bash
echo "=== Tool Activity ===" && wc -l logs/tool-activity.log
echo "=== API Audit ===" && cat logs/api-audit.log
echo "=== Test Audit ===" && cat logs/test-audit.log
echo "=== Bash Audit ===" && cat logs/bash-audit.log
echo "=== Session ===" && cat logs/session.log
echo "=== Blocked? ===" && ls .env.local 2>/dev/null || echo ".env.local blocked successfully"
```

모든 로그에 관련 항목이 있어야 하고, `.env.local` 파일은 차단되었어야 하며, 모든 `.js` 파일이 올바르게 포매팅되어 있어야 합니다.

---

### 과제 6: 훅 시스템 검토 및 문서화 (5분)

Claude에게 최종 구성을 감사하도록 요청합니다:

```
".claude/settings.json을 읽고 모든 활성 훅의 요약을 작성해줘:
- 각 훅의 이벤트 유형, 매처 패턴, 용도를 나열해줘
- 잠재적 이슈를 식별해줘 (겹치는 매처, 성능 우려)
- 개선 사항이나 누락된 훅을 제안해줘"
```

**예상 결과**:
- 모든 훅의 명확한 요약 표
- 겹치는 매처 식별
- 성능 고려 사항 언급 (예: 모든 편집 시 prettier 실행)
- 개선을 위한 제안

**확인**: 요약이 구성의 모든 훅을 정확하게 설명하고 실행 가능한 피드백을 제공하는지 확인

---

## 완료 기준

- [ ] 도구별 매처(Bash, Write, Edit)로 훅을 생성함
- [ ] 정규식 패턴을 사용하여 특정 파일 유형과 경로를 대상으로 함
- [ ] 여러 훅을 유효성 검사 파이프라인으로 체이닝함
- [ ] 프로젝트와 사용자 범위 모두에서 훅을 구성함
- [ ] PreToolUse, PostToolUse, Notification을 포함하는 8개 이상의 훅으로 포괄적인 훅 시스템을 구축함
- [ ] 차단 훅이 민감한 파일 수정을 방지하는지 확인함
