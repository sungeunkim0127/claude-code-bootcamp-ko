# 연습 8: 훅 시스템 기초

**소요 시간**: 1시간
**사전 조건**: 레슨 43-47

## 목표
로깅, 포매팅, 알림 훅을 포함하여 Claude의 동작에 자동으로 응답하는 훅을 생성하고 구성합니다.

## 설정

```bash
mkdir -p ~/claude-exercises/ex8/src ~/claude-exercises/ex8/tests ~/claude-exercises/ex8/logs
cd ~/claude-exercises/ex8
git init
npm init -y > /dev/null 2>&1
npm install --save-dev prettier > /dev/null 2>&1

cat > src/app.js << 'EOF'
const http = require('http')
function handleRequest(req, res) {
    const data = {message:"hello world",status:"ok"}
    res.writeHead(200,{'Content-Type':'application/json'})
    res.end(JSON.stringify(data))
}
const server = http.createServer(handleRequest)
server.listen(3000)
EOF

cat > src/utils.js << 'EOF'
function add(a,b){return a+b}
function multiply(a,b){return a*b}
function divide(a,b){if(b===0)throw new Error('Division by zero');return a/b}
module.exports={add,multiply,divide}
EOF

cat > tests/utils.test.js << 'EOF'
const {add,multiply,divide} = require('../src/utils')
test('add',()=>{expect(add(2,3)).toBe(5)})
test('multiply',()=>{expect(multiply(3,4)).toBe(12)})
test('divide by zero',()=>{expect(()=>divide(1,0)).toThrow()})
EOF

cat > .prettierrc << 'EOF'
{ "semi": false, "singleQuote": true, "tabWidth": 2, "trailingComma": "es5" }
EOF

mkdir -p .claude
claude
```

## 과제

### 과제 1: 파일 쓰기를 로깅하는 PreToolUse 훅 생성 (10분)

Claude가 파일을 쓰거나 편집할 때마다 로깅하는 훅을 만듭니다.

`.claude/settings.json`에 다음 구성을 추가합니다:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write|Edit",
        "command": "echo \"[$(date '+%Y-%m-%d %H:%M:%S')] PRE: $TOOL_NAME on $CLAUDE_FILE_PATH\" >> logs/tool-activity.log"
      }
    ]
  }
}
```

Claude에게 요청하여 테스트합니다: "src/utils.js에 subtract 함수를 추가하고 내보내줘"

**예상 결과**:
- 편집 전에 훅이 실행됨
- `logs/tool-activity.log`에 로그 항목이 기록됨
- 로깅 후 편집이 정상적으로 진행됨

**확인**: 로그 파일을 확인합니다:
```bash
cat logs/tool-activity.log
```
`src/utils.js`에 대한 Write 또는 Edit 도구 작업을 보여주는 타임스탬프가 포함된 항목이 있어야 합니다.

---

### 과제 2: 코드를 자동 포매팅하는 PostToolUse 훅 생성 (12분)

JavaScript 파일이 작성되거나 편집된 후 Prettier를 실행하는 PostToolUse 훅을 추가합니다.

`.claude/settings.json`을 업데이트하여 PostToolUse 섹션을 추가합니다:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write|Edit",
        "command": "echo \"[$(date '+%Y-%m-%d %H:%M:%S')] PRE: $TOOL_NAME on $CLAUDE_FILE_PATH\" >> logs/tool-activity.log"
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Write|Edit:.*\\.js$",
        "command": "npx prettier --write \"$CLAUDE_FILE_PATH\" 2>/dev/null"
      }
    ]
  }
}
```

Claude에게 요청하여 테스트합니다: "src/app.js를 다시 작성하여 /health 엔드포인트를 추가해줘. 포매팅은 신경 쓰지 마."

**예상 결과**:
- Claude가 파일을 작성함 (일관되지 않은 포매팅일 수 있음)
- 쓰기 후 Prettier가 자동으로 실행됨
- 결과 파일이 깔끔하게 포매팅됨

**확인**: `src/app.js`를 열어 올바른 포매팅(일관된 따옴표, `.prettierrc` 설정에 맞는 세미콜론, 적절한 들여쓰기)을 확인합니다.

---

### 과제 3: 알림 훅 생성 (10분)

세션 이벤트를 로그 파일에 기록하는 Notification 훅을 추가합니다.

`.claude/settings.json`을 업데이트하여 Notification 섹션을 추가합니다:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write|Edit",
        "command": "echo \"[$(date '+%Y-%m-%d %H:%M:%S')] PRE: $TOOL_NAME on $CLAUDE_FILE_PATH\" >> logs/tool-activity.log"
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Write|Edit:.*\\.js$",
        "command": "npx prettier --write \"$CLAUDE_FILE_PATH\" 2>/dev/null"
      }
    ],
    "Notification": [
      {
        "matcher": ".*",
        "command": "echo \"[$(date '+%Y-%m-%d %H:%M:%S')] NOTIFY: $NOTIFICATION_MESSAGE\" >> logs/notifications.log"
      }
    ]
  }
}
```

알림을 생성하는 더 긴 작업을 실행하여 테스트합니다:

```
"src/calculator.js에 add, subtract, multiply, divide 함수를 새로 만들고, tests/calculator.test.js에 포괄적인 테스트를 작성한 다음, 테스트를 실행해줘"
```

**예상 결과**:
- 관련 세션 이벤트에서 Notification 훅이 실행됨
- 메시지가 `logs/notifications.log`에 기록됨

**확인**:
```bash
cat logs/notifications.log
```
세션의 알림 항목이 포함되어 있어야 합니다.

---

### 과제 4: 설정 파일에서 훅 구성 (12분)

서로 다른 범위에서 훅을 구성하고 우선순위를 이해하는 연습을 합니다.

1. 먼저 현재 프로젝트 수준 구성을 확인합니다:
   ```bash
   cat .claude/settings.json
   ```

2. 전역으로 적용되는 사용자 수준 훅을 생성합니다. Claude에게 요청:
   ```
   "~/.claude/settings.json 파일을 읽어줘. 모든 Bash 명령을 타임스탬프와 함께 ~/claude-bash-audit.log에 기록하는 PostToolUse 훅을 추가해줘. 기존 설정은 유지해줘."
   ```

3. 범위 분리를 확인합니다. Claude에게 요청:
   ```
   "현재 활성화된 훅은 무엇이야? 프로젝트 수준(.claude/settings.json)과 사용자 수준(~/.claude/settings.json) 구성을 모두 확인해줘"
   ```

4. 두 범위가 동시에 작동하는지 테스트합니다:
   ```
   "src/utils.js에 modulo 함수를 추가한 다음, npm test로 테스트를 실행해줘"
   ```

**예상 결과**:
- 프로젝트 수준 훅이 실행됨 (로깅, 포매팅)
- 사용자 수준 훅도 실행됨 (bash 감사)
- 각각의 위치에 두 로그가 모두 기록됨

**확인**: 두 로그 파일을 확인합니다:
```bash
cat logs/tool-activity.log
cat ~/claude-bash-audit.log
```
마지막 작업에서 두 파일 모두 새 항목이 있어야 합니다.

---

### 과제 5: 훅이 올바르게 작동하는지 테스트 (10분)

구성된 모든 훅에 대해 체계적인 테스트를 수행합니다:

1. **PreToolUse 로깅 테스트**:
   Claude에게 요청: "src/helpers.js 파일을 새로 만들고 debounce 함수를 작성해줘"
   확인: `tail -1 logs/tool-activity.log`에 Write 이벤트가 표시되어야 함

2. **PostToolUse 포매팅 테스트**:
   Claude에게 요청: "src/helpers.js에 다음 정확한 코드를 추가해줘: `function throttle(fn,delay){let timer;return function(...args){if(!timer){timer=setTimeout(()=>{timer=null},delay);fn.apply(this,args)}}}`"
   확인: `cat src/helpers.js`에 올바르게 포매팅된 코드가 표시되어야 함 (한 줄이 아님)

3. **훅 오류 처리 테스트**:
   Claude에게 요청: "src/script.py에 hello world 함수가 있는 Python 파일을 작성해줘"
   확인: `.py` 파일은 Prettier 훅을 트리거하지 않아야 함 (매처가 `.*\.js$`임)

4. **여러 훅 동시 실행 테스트**:
   Claude에게 요청: "src/utils.js에 'abs' 함수를 추가해줘"
   확인: `logs/tool-activity.log` (PreToolUse)와 포매팅된 출력 (PostToolUse) 모두 있어야 함

**확인**: 네 가지 테스트 모두 예상 결과를 생성합니다. 로그에 여러 항목이 있고 JavaScript 파일이 포매팅되어 있어야 합니다.

---

### 과제 6: 차단 PreToolUse 훅 생성 (6분)

보호된 파일에 대한 쓰기를 차단하는 훅을 만듭니다.

`.claude/settings.json`의 PreToolUse 섹션에 추가합니다:

```json
{
  "matcher": "Write|Edit:.*\\.env.*",
  "command": "echo 'BLOCKED: Cannot modify .env files via Claude' && exit 1"
}
```

Claude에게 요청하여 테스트합니다: "DATABASE_URL=postgres://localhost/mydb가 포함된 .env 파일을 생성해줘"

**예상 결과**:
- PreToolUse 훅이 실행되어 `.env` 파일 패턴을 감지
- 훅이 코드 1로 종료되어 쓰기를 차단
- Claude가 차단 메시지를 수신

**확인**: `.env` 파일이 생성되지 않았는지 확인합니다:
```bash
ls -la .env 2>/dev/null || echo "No .env file - hook blocked successfully"
```

---

## 완료 기준

- [ ] 파일 작업을 로깅하는 PreToolUse 훅을 생성함
- [ ] Prettier로 JavaScript를 자동 포매팅하는 PostToolUse 훅을 생성함
- [ ] 세션 이벤트를 로깅하는 Notification 훅을 생성함
- [ ] 프로젝트와 사용자 범위 모두에서 훅을 구성함
- [ ] 체계적인 테스트로 모든 훅이 올바르게 실행되는지 확인함
- [ ] 민감한 파일에 대한 쓰기를 방지하는 차단 훅을 생성함
