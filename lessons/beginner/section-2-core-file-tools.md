# 섹션 2: 핵심 파일 도구 (레슨 6-12)

## 개요
Claude가 코드베이스와 상호작용하는 데 사용하는 필수 도구들을 마스터하세요. 이 도구들은 Claude가 수행하는 모든 작업의 기반입니다.

---

## 레슨 6: Read 도구로 파일 읽기

### 학습 목표
- Claude가 언제 Read 도구를 사용하는지 이해하기
- 특정 파일을 참조하는 방법 배우기
- 파일 참조를 위한 @-멘션 사용하기
- 여러 파일을 효율적으로 읽기

### 내용

#### Read 도구

**Read 도구**는 Claude가 코드를 이해하는 주요 방법입니다. 항상 사용됩니다.

**Read 도구의 기능**:
- 파일 내용 읽기
- 줄 번호와 함께 반환
- 다양한 파일 유형 처리 (코드, 마크다운, JSON 등)
- 특정 줄 범위 읽기 가능
- 파일을 시각적으로 표시 (이미지, PDF)

#### Claude가 Read를 사용하는 경우

**자동 사용**:
```
사용자: "app.js가 무엇을 하는지 설명해줘"
Claude: [app.js에 Read 사용] → 코드를 설명
```

**탐색 중**:
```
사용자: "인증 모듈의 버그를 수정해줘"
Claude:
  [Glob을 사용하여 인증 파일 찾기]
  [각 파일에 Read 사용]
  [버그 식별]
```

**편집 전**:
```
사용자: "설정을 업데이트해줘"
Claude:
  [Read를 사용하여 현재 설정 확인]
  [정보에 기반한 변경 수행]
```

#### 특정 줄 범위 읽기

**전체 파일** (기본값):
```
사용자: "src/app.js 읽어줘"
Claude: [전체 파일 읽기]
```

**특정 줄**:
```
사용자: "src/app.js의 50-100줄 읽어줘"
Claude: [50-100줄만 읽기]
```

**이것이 중요한 이유**:
- 큰 파일은 토큰을 소비합니다
- 때로는 특정 섹션만 필요합니다
- 더 효율적인 컨텍스트 사용

#### 파일을 위한 @-멘션

**직접 파일 참조**:
```
사용자: "@src/auth.js 설명해줘"
Claude: [auth.js를 읽고 설명]
```

**여러 파일**:
```
사용자: "@src/auth.js와 @src/auth-backup.js를 비교해줘"
Claude: [둘 다 읽고 비교]
```

**IDE에서**: @-멘션은 자동완성을 트리거합니다

#### 여러 파일 읽기

**순차적**:
```
사용자: "app.js를 읽고, 그다음 config.js를 읽고, 그다음 utils.js를 읽고
어떻게 함께 작동하는지 설명해줘"

Claude:
  [app.js 읽기]
  [config.js 읽기]
  [utils.js 읽기]
  [통합된 설명 제공]
```

**병렬** (더 효율적):
```
사용자: "이 파일들을 병렬로 읽어줘:
- app.js
- config.js
- utils.js
- database.js

그다음 아키텍처를 설명해줘"

Claude: [4개 파일 동시에 읽기] → 설명
```

#### Read가 지원하는 파일 유형

**텍스트 파일**:
- 소스 코드 (.js, .py, .rs, .go 등)
- 마크다운 (.md)
- 설정 (.json, .yaml, .toml)
- 문서 (.txt, .rst)

**특별 처리**:
- **이미지**: 시각적으로 표시
- **PDF**: 페이지별로 처리
- **Jupyter 노트북**: 코드 + 출력 표시
- **바이너리 파일**: 오류 (읽기 불가)

#### Read 도구 출력 형식

```
     1  import express from 'express'
     2  import { connectDB } from './db'
     3
     4  const app = express()
     5  const PORT = process.env.PORT || 3000
     6
     7  app.get('/health', (req, res) => {
     8    res.json({ status: 'healthy' })
     9  })
    10
    11  app.listen(PORT, () => {
    12    console.log(`Server running on port ${PORT}`)
    13  })
```

**참고**: 쉬운 참조를 위한 줄 번호

#### 효율적인 읽기 전략

**1. 편집 전 읽기**:
```
나쁜 예: "API 엔드포인트를 /v2/users로 변경해줘"
좋은 예: "api/routes.js를 읽고, users 엔드포인트를 /v2/users로 업데이트해줘"
```

**2. 관련 파일 읽기**:
```
"컨트롤러와 모델을 둘 다 읽어서 전체 흐름을 이해해줘"
```

**3. 큰 파일에는 줄 범위 사용**:
```
"app.js의 임포트 섹션(1-20줄)만 읽어줘"
```

**4. Grep과 결합**:
```
"src/에서 'authentication'을 검색하고, 관련 파일을 읽어줘"
```

### 연습

**과제**: 파일 읽기 기법 마스터하기

**준비**:
```bash
cd ~/claude-practice
cat > sample.js << 'EOF'
// Configuration
const config = {
  port: 3000,
  host: 'localhost',
  database: {
    url: 'mongodb://localhost:27017',
    name: 'myapp'
  }
}

// Helper function
function getConfigValue(key) {
  return config[key]
}

// Export
module.exports = { config, getConfigValue }
EOF

cat > index.js << 'EOF'
const { config } = require('./sample')
const express = require('express')

const app = express()

app.listen(config.port, () => {
  console.log(`Running on ${config.port}`)
})
EOF

claude
```

**과제들**:

1. **기본 읽기**:
```
"sample.js를 읽고 무엇을 하는지 설명해줘"
```

2. **특정 줄**:
```
"sample.js의 1-10줄을 읽어줘"
```

3. **여러 파일**:
```
"sample.js와 index.js를 둘 다 읽고 어떻게 함께 작동하는지 설명해줘"
```

4. **@-멘션 사용**:
```
"@sample.js와 @index.js의 차이점이 뭐야?"
```

5. **효율적 읽기**:
```
"sample.js와 index.js를 병렬로 읽고, 애플리케이션이
무엇을 하는지 알려줘"
```

### 확인사항

다음 단계로 넘어가기 전에 확인하세요:
- [ ] Claude에게 특정 파일을 읽도록 요청할 수 있는가
- [ ] 파일 참조를 위한 @-멘션을 사용할 수 있는가
- [ ] 특정 줄 범위를 읽을 수 있는가
- [ ] 여러 파일을 병렬로 읽을 수 있는가
- [ ] Read 도구 출력 형식을 이해하는가

### 흔한 실수

1. **편집 전에 읽지 않기**:
   - Claude가 잘못된 가정을 할 수 있습니다
   - 항상 현재 상태를 먼저 읽으세요

2. **큰 파일 전체를 읽기**:
   - 토큰을 낭비합니다
   - 대상을 좁혀서 읽으려면 줄 범위를 사용하세요

3. **병렬이 가능한데 순차적으로 읽기**:
   - 필요 이상으로 느려집니다
   - 독립적인 파일은 병렬 읽기를 요청하세요

### 프로 팁

**줄 참조**:
```
"버그가 auth.js 45줄 근처에 있어 - 그 섹션을 읽어줘"
```

**컨텍스트 구축**:
```
"메인 파일을 읽고, 그다음 임포트 파일을 읽고, 그다음 흐름을 설명해줘"
```

**비교**:
```
"이전 버전(backup.js)과 새 버전(current.js)을 읽고
무엇이 변경되었는지 설명해줘"
```

---

## 레슨 7: Write 도구로 파일 생성

### 학습 목표
- Write와 Edit의 사용 시점 배우기
- 처음부터 새 파일 생성하기
- 파일 경로 처리 이해하기
- 쓰기 전 파일 내용 검토하기

### 내용

#### Write 도구

**Write**는 새 파일을 생성하거나 기존 파일을 완전히 교체합니다.

**Write를 사용하는 경우**:
- 새 파일 생성
- 처음부터 코드 생성
- 전체 파일 내용 교체
- 설정 파일 생성

**Write를 사용하지 않는 경우**:
- 기존 파일 수정 → 대신 **Edit** 사용
- 작은 섹션 변경 → **Edit** 사용
- 설정 업데이트 → 보통 **Edit** 사용

#### Write 도구 구문

**기본 Write**:
```
파일 경로: src/newFile.js
내용: [전체 파일 내용]
```

**동작 과정**:
1. Claude가 권한을 요청합니다
2. 사용자가 승인합니다
3. 파일이 생성/덮어쓰기됩니다
4. Claude가 성공을 확인합니다

#### 새 파일 생성

**예시 흐름**:
```
사용자: "기본 로깅 유틸리티로 src/utils/logger.js 새 파일을 만들어줘"

Claude:
  "로거 유틸리티를 생성하겠습니다..."

  [도구: Write - src/utils/logger.js]

  내용:
  const logger = {
    info: (msg) => console.log(`[INFO] ${msg}`),
    error: (msg) => console.error(`[ERROR] ${msg}`),
    debug: (msg) => console.debug(`[DEBUG] ${msg}`)
  }

  module.exports = logger

  "로거 유틸리티가 생성되었습니다!"
```

#### 파일 경로 처리

**절대 경로**:
```
/Users/username/project/src/app.js
```

**상대 경로** (현재 디렉토리 기준):
```
src/app.js
./src/app.js
```

**디렉토리 생성**:
Claude는 부모 디렉토리가 없으면 자동으로 생성합니다:
```
Write: src/api/v2/controllers/userController.js
→ 필요하면 src/, api/, v2/, controllers/를 생성
```

#### 파일 덮어쓰기

**주의**: Write는 전체 파일을 교체합니다!

```
사용자: "새 config.js를 작성해줘"

Claude: [config.js 생성]

사용자: "다른 내용으로 config.js를 작성해줘"

Claude: [config.js 전체를 교체]
⚠️ 이전 내용이 사라집니다!
```

**모범 사례**: 기존 파일에는 Edit를 사용하세요

#### 쓰기 전 검토

**항상 검토하세요**:
```
Claude가 보여줍니다:
  "src/app.js에 다음을 작성하겠습니다:"

  [전체 내용 표시]

  "이 파일을 생성하려면 승인해주세요"
```

**확인 사항**:
- 파일 경로가 올바른지
- 내용이 원하는 것인지
- 중요한 파일을 실수로 덮어쓰지 않는지

#### Write 도구 vs Edit 도구

| 시나리오 | 도구 | 이유 |
|----------|------|------|
| 새 파일 생성 | Write | 파일이 존재하지 않음 |
| 함수 추가 | Edit | 기존 파일 수정 |
| 변수 변경 | Edit | 작은 변경 |
| 전체 파일 재작성 | Write | 완전한 교체 |
| 보일러플레이트 생성 | Write | 처음부터 생성 |
| 설정 값 업데이트 | Edit | 대상 지정 변경 |

#### 여러 파일 생성

**순차적**:
```
사용자: "Express 앱을 위해 index.js, app.js, config.js를 생성해줘"

Claude:
  [index.js 작성]
  [app.js 작성]
  [config.js 작성]
```

**체계적 접근**:
```
사용자: "다음으로 새 Express API를 설정해줘:
- src/index.js (진입점)
- src/app.js (Express 설정)
- src/routes/users.js (사용자 라우트)
- src/controllers/userController.js
- src/models/User.js
- package.json"

Claude: [모든 파일을 순서대로 생성]
```

#### 파일 내용 모범 사례

**필수 요소 포함**:
- 상단에 임포트/require
- 하단에 적절한 export
- 명확성을 위한 주석
- 에러 처리
- 프로젝트 규칙 따르기

**좋은 Write 예시**:
```javascript
// src/services/emailService.js

const nodemailer = require('nodemailer')
const config = require('../config')

/**
 * Email service for sending transactional emails
 */
class EmailService {
  constructor() {
    this.transporter = nodemailer.createTransport({
      host: config.email.host,
      port: config.email.port,
      auth: {
        user: config.email.user,
        pass: config.email.password
      }
    })
  }

  async sendEmail(to, subject, body) {
    try {
      const result = await this.transporter.sendMail({
        from: config.email.from,
        to,
        subject,
        html: body
      })
      return { success: true, messageId: result.messageId }
    } catch (error) {
      console.error('Email send failed:', error)
      return { success: false, error: error.message }
    }
  }
}

module.exports = new EmailService()
```

### 연습

**과제**: 처음부터 완전한 모듈 생성하기

**준비**:
```bash
cd ~/claude-practice
rm -rf math-utils  # 깨끗한 상태
mkdir math-utils
cd math-utils
claude
```

**과제들**:

1. **메인 모듈 생성**:
```
"다음 함수들이 포함된 src/math.js를 생성해줘:
- add(a, b)
- subtract(a, b)
- multiply(a, b)
- divide(a, b) — 0으로 나누기에 대한 에러 처리 포함

모던 JavaScript와 적절한 export를 사용해줘"
```

2. **테스트 파일 생성**:
```
"Jest를 사용하여 모든 수학 함수에 대한 포괄적인 테스트가 있는
tests/math.test.js를 생성해줘"
```

3. **Package.json 생성**:
```
"이 프로젝트를 위한 package.json을 생성해줘:
- name: math-utils
- version: 1.0.0
- test 스크립트
- jest를 dev 의존성으로"
```

4. **README 생성**:
```
"다음이 포함된 README.md를 생성해줘:
- 프로젝트 설명
- 설치 안내
- 사용 예시
- API 문서"
```

5. **모든 파일 검토**:
```
"생성한 모든 파일을 보여줘"
```

### 확인사항

다음 단계로 넘어가기 전에 확인하세요:
- [ ] Write 도구로 새 파일을 생성할 수 있는가
- [ ] Write와 Edit의 차이를 이해하는가
- [ ] 여러 관련 파일을 생성할 수 있는가
- [ ] 승인 전에 내용을 검토할 수 있는가
- [ ] 적절한 파일 구조를 만들 수 있는가

### 흔한 실수

1. **Edit 대신 Write 사용**:
   - 전체 파일을 덮어씁니다
   - 기존 내용이 사라집니다
   - 수정에는 Edit를 사용하세요!

2. **내용 검토 안 하기**:
   - Claude가 작성하는 것을 항상 확인하세요
   - 파일 경로가 올바른지 확인하세요

3. **디렉토리 생성 누락**:
   - Claude는 자동으로 디렉토리를 생성합니다
   - 하지만 경로가 의도한 것인지 확인하세요

### 프로 팁

**템플릿 생성**:
```
"우리 표준 템플릿에 따라 새 React 컴포넌트를 생성해줘"
```

**보일러플레이트 생성**:
```
"컨트롤러, 서비스, 테스트가 포함된 새 Express 라우트를 설정해줘"
```

**설정 파일**:
```
"팀의 린트 규칙으로 .eslintrc.json을 생성해줘"
```

---

## 레슨 8: Edit 도구로 파일 편집

### 학습 목표
- 정확한 문자열 교체 이해하기
- Edit 도구 모범 사례 배우기
- 여러 줄 편집 처리하기
- 이름 변경을 위한 replace_all 사용하기

### 내용

#### Edit 도구

**Edit**는 정확한 문자열 교체를 사용하여 기존 파일에 정밀한 변경을 합니다.

**작동 방식**:
1. Claude가 파일을 읽습니다 (아직 읽지 않았다면)
2. 교체할 정확한 문자열을 찾습니다
3. 새 문자열로 교체합니다
4. diff를 보여줍니다
5. 승인 시 변경을 적용합니다

#### 정확한 문자열 매칭

**중요**: Edit는 정확한 문자열 매칭을 사용합니다!

**이것은 작동합니다**:
```javascript
이전 문자열 (파일에서):
function greet(name) {
  return `Hello, ${name}!`
}

새 문자열:
function greet(name) {
  return `Hello, ${name}! Welcome!`
}
```

**이것은 실패합니다**:
```javascript
이전 문자열 (공백 불일치):
function greet(name){
  return `Hello, ${name}!`
}

❌ 찾을 수 없습니다! 공백이 일치하지 않습니다.
```

#### Edit 도구 구문

**기본 Edit**:
```
파일: src/app.js
이전 문자열: const PORT = 3000
새 문자열: const PORT = process.env.PORT || 3000
```

**여러 줄 Edit**:
```
파일: src/app.js
이전 문자열:
app.get('/users', (req, res) => {
  res.json({ users: [] })
})

새 문자열:
app.get('/users', async (req, res) => {
  const users = await User.find()
  res.json({ users })
})
```

#### Diff 이해하기

**Claude가 보여줍니다**:
```diff
  const express = require('express')
  const app = express()

- const PORT = 3000
+ const PORT = process.env.PORT || 3000

  app.listen(PORT, () => {
    console.log(`Server on ${PORT}`)
  })
```

**범례**:
- ` ` (공백): 변경되지 않은 컨텍스트
- `-` (빨간색): 제거됨
- `+` (초록색): 추가됨

#### Replace All

**단일 교체** (기본값):
```
이전: const port = 3000
새로: const port = 8080
→ 첫 번째 발생만 교체
```

**전체 교체**:
```
이전: oldVariableName
새로: newVariableName
Replace all: true
→ 모든 발생을 교체
```

**replace_all을 사용하는 경우**:
- 변수 이름 변경
- 함수 이름 업데이트
- 반복되는 문자열 변경
- 전역 찾기-바꾸기

#### 여러 줄 편집

**복잡한 변경**:
```javascript
이전 문자열:
app.get('/users/:id', (req, res) => {
  const user = users.find(u => u.id === req.params.id)
  res.json(user)
})

새 문자열:
app.get('/users/:id', async (req, res) => {
  try {
    const user = await User.findById(req.params.id)
    if (!user) {
      return res.status(404).json({ error: 'User not found' })
    }
    res.json(user)
  } catch (error) {
    res.status(500).json({ error: error.message })
  }
})
```

#### Edit 실패 처리

**Edit가 실패하는 일반적인 이유**:

1. **문자열을 찾을 수 없음**:
   ```
   오류: "old_string not found in file"
   ```
   - 이전 문자열의 오타
   - 마지막 읽기 이후 파일이 변경됨
   - 공백 불일치

2. **고유하지 않음**:
   ```
   오류: "old_string appears multiple times"
   ```
   - replace_all: true를 사용해야 함
   - 또는 고유하게 만들기 위해 더 많은 컨텍스트 제공

**복구**:
```
1. 파일을 다시 읽기
2. Read 출력에서 정확한 문자열 복사
3. 다시 편집 시도
4. 계속 실패하면 Write 사용 고려
```

#### Edit 모범 사례

**1. 충분한 컨텍스트 제공**:
```
나쁜 예 (고유하지 않을 수 있음):
이전: return true

좋은 예 (고유함):
이전:
function isValid(email) {
  return true
}
```

**2. 공백을 정확히 일치시키기**:
```
파일의 정확한 들여쓰기를 사용하세요
```

**3. 고유성을 위해 주변 코드 포함**:
```
이전:
  const result = calculate()
  return result

다음만 쓰지 마세요:
  return result
```

**4. 편집 전 읽기**:
```
정확한 서식을 확인하기 위해 항상 먼저 파일을 읽으세요
```

#### 편집 체이닝

**여러 변경**:
```
사용자: "app.js를 다음과 같이 업데이트해줘:
1. 포트를 8080으로 변경
2. 에러 처리 추가
3. 응답 형식 업데이트"

Claude:
  [편집 1: 포트 변경]
  [편집 2: 에러 처리 추가]
  [편집 3: 응답 업데이트]
```

**순차적 편집**:
각 편집은 이전 편집 위에 구축됩니다

#### Edit vs Write 결정

**Edit를 사용하는 경우**:
- 작은 섹션 변경
- 특정 함수 업데이트
- 설정 값 수정
- 새 임포트 추가
- 주석 업데이트

**Write를 사용하는 경우**:
- 파일이 완전히 재구성됨
- Edit가 계속 실패함 (공백 문제)
- 편집보다 재작성이 더 쉬움
- 처음부터 생성

### 연습

**과제**: 파일 편집 마스터하기

**준비**:
```bash
cd ~/claude-practice
cat > calculator.js << 'EOF'
function add(a, b) {
  return a + b
}

function subtract(a, b) {
  return a - b
}

const result = add(5, 3)
console.log(result)
EOF

claude
```

**과제들**:

1. **간단한 편집**:
```
"calculator.js를 읽고, add 함수가 문자열 변환을 처리하도록
변경해줘 (parseFloat)"
```

2. **여러 줄 편집**:
```
"숫자가 아닌 입력에 대한 에러 처리를 포함하도록
subtract 함수를 업데이트해줘"
```

3. **새 함수 추가**:
```
"subtract 뒤에 multiply와 divide 함수를 추가해줘"
```

4. **전체 교체**:
```
"파일 전체에서 'result'를 'sum'으로 이름 변경해줘"
```

5. **복잡한 편집**:
```
"모든 함수를 Calculator 클래스로 감싸고 export 해줘"
```

### 확인사항

다음 단계로 넘어가기 전에 확인하세요:
- [ ] Edit를 사용하여 특정 섹션을 수정할 수 있는가
- [ ] 정확한 문자열 매칭을 이해하는가
- [ ] diff를 올바르게 읽을 수 있는가
- [ ] 이름 변경을 위해 replace_all을 사용할 수 있는가
- [ ] 편집 실패를 우아하게 처리할 수 있는가

### 흔한 실수

1. **공백 불일치**:
   - Read 출력에서 정확한 문자열을 복사하세요
   - 탭과 스페이스에 주의하세요

2. **충분히 고유하지 않음**:
   - 더 많은 컨텍스트를 포함하세요
   - 또는 replace_all을 사용하세요

3. **읽지 않고 편집하기**:
   - 항상 현재 상태를 확인하세요
   - 정확한 서식을 검증하세요

### 프로 팁

**점진적 변경**:
```
한 번에 하나의 논리적 변경을 하세요
검토하고 확인하기 더 쉽습니다
```

**Diff 활용**:
```
승인하기 전에 항상 diff를 검토하세요
의도하지 않은 변경을 잡으세요
```

**Edit가 계속 실패할 때**:
```
Write를 사용하여 전체 함수/섹션을 교체하세요
때로는 공백과 싸우는 것보다 더 쉽습니다
```

---

## 레슨 9: Glob 도구로 파일 찾기

### 학습 목표
- Glob 도구와 glob 패턴 이해하기
- 이름, 확장자, 경로로 파일 찾기
- 유연한 검색을 위한 와일드카드 사용하기
- 효율적 탐색을 위해 Glob과 Read 결합하기

### 내용

#### Glob 도구

**Glob 도구**는 파일 경로에 대해 패턴을 매칭하여 파일을 찾습니다. 프로젝트에서 파일을 위치시켜야 할 때 Claude가 주로 사용하는 도구입니다.

**Glob 도구의 기능**:
- 패턴과 일치하는 파일 찾기
- 수정 시간 순으로 정렬된 파일 경로 반환
- 모든 코드베이스 크기에서 작동
- 빠름 — 수동으로 디렉토리를 읽는 것보다 훨씬 빠름

**Claude가 Glob을 사용하는 경우**:
```
사용자: "src/에서 모든 JavaScript 파일을 찾아줘"
Claude: [패턴 "src/**/*.js"로 Glob 사용]
```

#### Glob 패턴 구문

**기본 와일드카드**:

| 패턴 | 매칭 | 예시 |
|------|------|------|
| `*` | 한 세그먼트의 모든 문자 | `*.js` → `app.js`, `index.js` |
| `**` | 임의의 수의 디렉토리 | `**/*.js` → `src/app.js`, `src/lib/utils.js` |
| `?` | 단일 문자 | `app.?s` → `app.js`, `app.ts` |
| `{a,b}` | a 또는 b | `*.{js,ts}` → `app.js`, `app.ts` |

**자주 사용하는 패턴**:

```
# 모든 JavaScript 파일
**/*.js

# src/ 디렉토리의 모든 파일
src/**/*

# 모든 테스트 파일
**/*.test.js
**/*.spec.ts

# 모든 설정 파일
**/config.*
**/.*.json

# TypeScript와 JavaScript
**/*.{js,ts,jsx,tsx}

# 특정 디렉토리
src/components/**/*.tsx
tests/**/*.test.js
```

#### 실전 예시

**소스 파일 찾기**:
```
사용자: "프로젝트의 모든 TypeScript 파일을 찾아줘"
Claude: [Glob 패턴: **/*.ts]
→ src/index.ts
→ src/app.ts
→ src/routes/users.ts
→ src/models/User.ts
```

**설정 파일 찾기**:
```
사용자: "설정 파일은 어디에 있어?"
Claude: [Glob 패턴: **/{config,*.config}.*]
→ webpack.config.js
→ jest.config.ts
→ src/config.json
```

**테스트 찾기**:
```
사용자: "모든 테스트 파일을 찾아줘"
Claude: [Glob 패턴: **/*.{test,spec}.{js,ts,jsx,tsx}]
→ tests/auth.test.js
→ tests/users.spec.ts
→ src/__tests__/app.test.tsx
```

#### Glob과 Read 결합

**탐색 패턴**:
```
사용자: "모든 라우트 파일을 찾고 API 엔드포인트를 보여줘"

Claude:
  [Glob: src/routes/**/*.js]
  → 발견: users.js, auth.js, products.js
  [Read: src/routes/users.js]
  [Read: src/routes/auth.js]
  [Read: src/routes/products.js]
  → "다음은 모든 API 엔드포인트입니다..."
```

**버그 조사**:
```
사용자: "데이터베이스 모듈을 임포트하는 모든 파일을 찾아줘"

Claude:
  [Glob: src/**/*.js]
  [Grep: 해당 파일에서 "import.*database"]
  [Read: 일치하는 파일]
  → "다음 파일들이 데이터베이스 모듈을 임포트합니다..."
```

#### Glob vs 다른 검색 방법

| 방법 | 가장 적합한 용도 |
|------|-----------------|
| **Glob** | 이름/경로 패턴으로 파일 찾기 |
| **Grep** | 내용으로 파일 찾기 |
| **Bash `ls`** | 디렉토리 내용 나열 |
| **Bash `find`** | 복잡한 조건 (크기, 날짜) |

**경험 법칙**: 파일 이름을 알면? Glob을 사용하세요. 안에 무엇이 있는지 알면? Grep을 사용하세요.

### 연습

**과제**: Glob으로 파일 검색 마스터하기

**준비**:
```bash
cd ~/claude-practice
mkdir -p myproject/src/components myproject/src/utils myproject/tests myproject/config
touch myproject/src/index.js myproject/src/app.ts
touch myproject/src/components/Header.tsx myproject/src/components/Footer.tsx
touch myproject/src/utils/helpers.js myproject/src/utils/format.ts
touch myproject/tests/app.test.js myproject/tests/helpers.test.js
touch myproject/config/dev.json myproject/config/prod.json
touch myproject/package.json myproject/tsconfig.json
cd myproject
claude
```

**과제들**:

1. **확장자로 찾기**:
```
"이 프로젝트의 모든 TypeScript 파일을 찾아줘"
```

2. **특정 디렉토리에서 찾기**:
```
"components 디렉토리의 모든 파일을 찾아줘"
```

3. **이름 패턴으로 찾기**:
```
"모든 테스트 파일을 찾아줘"
```

4. **설정 파일 찾기**:
```
"모든 JSON 설정 파일을 찾아줘"
```

5. **결합 검색**:
```
"src/의 모든 TypeScript 파일을 찾아서 각각 읽어줘"
```

### 확인사항

다음 단계로 넘어가기 전에 확인하세요:
- [ ] 기본 glob 패턴을 이해하는가 (`*`, `**`, `?`, `{}`)
- [ ] 확장자로 파일을 찾을 수 있는가
- [ ] 특정 디렉토리에서 검색할 수 있는가
- [ ] Glob과 Read를 결합할 수 있는가
- [ ] Glob과 Grep 중 선택할 수 있는가

### 흔한 실수

1. **`**`가 필요한데 `*`를 사용**:
   - `*.js`는 루트 레벨 JS 파일만 매칭합니다
   - `**/*.js`는 모든 깊이의 JS 파일을 매칭합니다

2. **파일 확장자를 잊음**:
   - `src/**/*`는 코드가 아닌 파일을 포함한 모든 것을 매칭합니다
   - 구체적으로 하세요: `src/**/*.{js,ts}`

3. **읽기 전에 Glob을 사용하지 않음**:
   - 파일 경로를 추측하지 마세요
   - 먼저 Glob으로 찾고, 그다음 결과를 Read하세요

### 프로 팁

**탐색 워크플로**:
```
"먼저 모든 라우트 파일을 찾고, 각각 읽은 다음
API 구조를 요약해줘"
```

**다중 패턴 검색**:
```
"모든 .js와 .ts 파일을 찾되 node_modules와 dist는 제외해줘"
```

**컨텍스트와 결합**:
```
"최근에 수정된 모든 파일을 찾아줘 (glob은 수정 시간순으로 정렬됨)"
```

---

## 레슨 10: Grep 도구로 내용 검색

### 학습 목표
- Grep을 사용하여 파일 내부 검색하기
- 검색을 위한 정규식 패턴 이해하기
- 출력 모드로 결과 필터링하기
- Grep을 다른 도구와 결합하기

### 내용

#### Grep 도구

**Grep 도구**는 파일 내용에서 패턴을 검색합니다. Glob이 이름으로 파일을 찾는 반면, Grep은 파일 *안에* 무엇이 있는지로 파일을 찾습니다.

**Grep 도구의 기능**:
- 정규식 패턴을 사용하여 파일 내용 검색
- 파일 유형이나 glob 패턴으로 필터링
- 일치하는 파일, 줄 또는 개수 반환
- 속도를 위해 ripgrep 기반으로 구축

**Claude가 Grep을 사용하는 경우**:
```
사용자: "사용자 모델이 어디에 정의되어 있는지 찾아줘"
Claude: ["class User" 또는 "const User" 패턴으로 Grep 사용]
```

#### Grep 출력 모드

**1. `files_with_matches`** (기본값) — 파일 경로만:
```
사용자: "인증을 언급하는 파일이 어디야?"
Claude: [Grep: "authentication"]
→ src/auth.js
→ src/middleware/auth.js
→ docs/security.md
```

**2. `content`** — 컨텍스트와 함께 일치하는 줄:
```
사용자: "PORT가 어디에 정의되어 있는지 보여줘"
Claude: [Grep: "PORT.*=", output_mode: "content"]
→ src/config.js:5: const PORT = process.env.PORT || 3000
→ src/app.js:12:   app.listen(PORT, () => {
```

**3. `count`** — 파일당 일치 개수:
```
사용자: "코드베이스에 TODO 주석이 몇 개야?"
Claude: [Grep: "TODO", output_mode: "count"]
→ src/auth.js: 3
→ src/app.js: 1
→ src/utils.js: 5
```

#### 정규식 패턴

**일반적인 검색 패턴**:

```
# 리터럴 문자열
"authentication"

# 함수 정의
"function\\s+\\w+"

# 임포트 문
"import.*from"

# 변수 할당
"const PORT\\s*="

# 클래스 정의
"class\\s+User"

# TODO/FIXME 주석
"(TODO|FIXME|HACK)"

# 이메일 패턴
"[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+"
```

#### 결과 필터링

**파일 유형별**:
```
사용자: "JavaScript 파일에서만 'fetch'를 검색해줘"
Claude: [Grep: "fetch", type: "js"]
```

**Glob 패턴별**:
```
사용자: "설정 파일에서만 'database'를 검색해줘"
Claude: [Grep: "database", glob: "**/config.*"]
```

**컨텍스트 줄 포함**:
```
사용자: "데이터베이스 연결 주변의 에러 처리를 보여줘"
Claude: [Grep: "database.*connect", -C: 5]
→ 각 일치 전후 5줄을 표시
```

#### 실전 검색 패턴

**사용하지 않는 코드 찾기**:
```
사용자: "모든 내보낸 함수를 찾고, 그중 어디에서도
임포트되지 않는 것을 찾아줘"

Claude:
  [Grep: "export (function|const|class)" → 내보내기 목록]
  [각 내보내기에 대해: 임포트 참조를 Grep]
  → "다음 내보내기는 사용되지 않는 것 같습니다..."
```

**보안 감사**:
```
사용자: "잠재적 보안 문제를 검색해줘"

Claude:
  [Grep: "eval\\("]
  [Grep: "innerHTML"]
  [Grep: "password.*=.*['\"]"]
  → "잠재적 보안 우려 사항을 발견했습니다..."
```

**의존성 사용**:
```
사용자: "lodash 라이브러리가 코드에서 어떻게 사용되고 있어?"

Claude:
  [Grep: "from 'lodash'" 또는 "require.*lodash"]
  [일치하는 파일 Read]
  → "lodash는 다음 파일에서 ... 용도로 사용됩니다"
```

#### Grep vs Glob 결정

```
질문: "User 모델이 어디에 있어?"
→ 먼저 Glob 시도: **/*User*.{js,ts}
→ 찾지 못하면 Grep 시도: "class User" 또는 "model.*User"

질문: "인증을 처리하는 파일은?"
→ Grep: "auth|authenticate|login"

질문: "모든 테스트 파일 찾아줘"
→ Glob: **/*.test.{js,ts}
```

### 연습

**과제**: 내용 검색 마스터하기

**준비**:
```bash
cd ~/claude-practice
mkdir -p search-demo/src

cat > search-demo/src/app.js << 'EOF'
const express = require('express')
const { connectDB } = require('./database')

// TODO: Add rate limiting
const app = express()
const PORT = process.env.PORT || 3000

app.get('/users', async (req, res) => {
  const users = await getUsers()
  res.json(users)
})

// FIXME: Add proper error handling
app.listen(PORT, () => {
  console.log(`Server on ${PORT}`)
})
EOF

cat > search-demo/src/database.js << 'EOF'
const mongoose = require('mongoose')

// TODO: Move connection string to env
const DB_URL = 'mongodb://localhost:27017/myapp'

async function connectDB() {
  await mongoose.connect(DB_URL)
  console.log('Database connected')
}

module.exports = { connectDB }
EOF

cat > search-demo/src/auth.js << 'EOF'
const jwt = require('jsonwebtoken')
const SECRET = 'my-secret-key'

// TODO: Use environment variable for secret
function authenticate(token) {
  return jwt.verify(token, SECRET)
}

function generateToken(user) {
  return jwt.sign({ id: user.id }, SECRET)
}

module.exports = { authenticate, generateToken }
EOF

cd search-demo
claude
```

**과제들**:

1. **기본 검색**:
```
"'TODO' 주석이 포함된 모든 파일을 찾아줘"
```

2. **내용 검색**:
```
"모든 TODO와 FIXME 주석을 주변 코드와 함께 보여줘"
```

3. **패턴 검색**:
```
"프로젝트 전체에서 모든 require() 문을 찾아줘"
```

4. **필터링된 검색**:
```
"JavaScript 파일에서만 'connect'를 검색해줘"
```

5. **보안 검색**:
```
"코드에서 하드코딩된 시크릿이나 자격 증명을 찾아줘"
```

### 확인사항

다음 단계로 넘어가기 전에 확인하세요:
- [ ] 파일에서 텍스트 패턴을 검색할 수 있는가
- [ ] 다른 출력 모드를 사용할 수 있는가 (files, content, count)
- [ ] 파일 유형이나 glob으로 검색을 필터링할 수 있는가
- [ ] 컨텍스트 줄을 사용하여 주변 코드를 볼 수 있는가
- [ ] 조사를 위해 Grep과 Read를 결합할 수 있는가

### 흔한 실수

1. **너무 넓은 검색**:
   - `"a"`는 거의 모든 것에 매칭됩니다
   - 구체적으로 하세요: `"function"` 대신 `"async function"`

2. **파일 유형 필터를 사용하지 않음**:
   - `node_modules`를 포함한 모든 파일을 검색
   - `type: "js"` 또는 `glob: "src/**"`를 사용하세요

3. **Grep과 Glob을 혼동**:
   - Glob = 파일 이름/경로
   - Grep = 파일 내용

### 프로 팁

**대소문자 구분 없는 검색**:
```
-i 플래그와 함께 Grep: "error"가 "Error", "ERROR" 등을 매칭
```

**여러 줄 검색**:
```
여러 줄에 걸친 패턴을 매칭하려면 multiline: true를 사용하세요
```

**전체 그림을 위한 결합**:
```
"'database'를 Grep하여 모든 관련 파일을 찾고,
각각 읽어서 전체 데이터베이스 계층을 이해해줘"
```

---

## 레슨 11: Bash 도구로 명령 실행

### 학습 목표
- Claude가 Bash를 언제 사용하는지 이해하기
- 안전한 명령 실행 배우기
- 빌드, 테스트, 스크립트 실행하기
- 명령에 대한 권한 모델 이해하기

### 내용

#### Bash 도구

**Bash 도구**는 Claude가 터미널 명령을 실행하게 해줍니다. Claude가 빌드를 실행하고, 패키지를 설치하고, 테스트를 실행하고, 시스템 작업을 수행하는 방법입니다.

**Bash가 할 수 있는 것**:
- npm/yarn/pnpm 명령 실행
- 테스트 실행
- 프로젝트 빌드
- 의존성 설치
- 스크립트 실행
- Git 작업
- Docker 명령
- 모든 터미널 명령

**Claude가 Bash를 사용하는 경우**:
```
사용자: "express를 설치하고 테스트를 실행해줘"
Claude:
  [Bash: npm install express]
  [Bash: npm test]
```

#### 권한 모델

Claude는 명령을 실행하기 전에 권한을 요청합니다. 특히 시스템을 변경하는 작업에서 그렇습니다.

**자동 허용** (일반적이고 안전한 작업):
- `ls`, `pwd`, `echo`
- `git status`, `git diff`, `git log`
- 읽기 작업

**권한 필요** (파일/시스템 변경):
- `npm install`
- `git commit`, `git push`
- `rm`, `mkdir`
- 스크립트 실행
- 데이터베이스 명령

**설정 가능**:
```
# 모든 npm 명령 허용
Permission: npm *

# 테스트 허용
Permission: npm test, jest, pytest
```

#### 안전한 명령 실행

**Claude의 안전 규칙**:
1. 권한 없이 파괴적 명령을 실행하지 않음
2. 중요한 디렉토리에 `rm -rf`를 피함
3. 비밀을 유출하는 명령을 실행하지 않음
4. 되돌릴 수 없는 작업에 대해 경고
5. Bash보다 전용 도구를 우선 사용

**예시 — 안전한 접근**:
```
사용자: "오래된 빌드 디렉토리를 삭제해줘"

Claude:
  "build/ 디렉토리를 제거하겠습니다. 모든 컴파일된
  파일이 삭제됩니다. 진행할까요?"

  [Bash: rm -rf build/]  ← 먼저 권한을 요청
```

#### 일반적인 Bash 워크플로

**패키지 관리**:
```
사용자: "이 프로젝트를 설정해줘"
Claude:
  [Bash: npm install]
  → "의존성이 설치되었습니다. 3개의 취약점을 발견했습니다..."
  [Bash: npm audit fix]
  → "2개의 취약점을 수정했습니다"
```

**테스트 실행**:
```
사용자: "테스트를 실행해줘"
Claude:
  [Bash: npm test]
  → "테스트: 12개 통과, 2개 실패"
  → "실패한 테스트를 살펴보겠습니다..."
```

**빌드 및 검증**:
```
사용자: "프로젝트를 빌드하고 작동하는지 확인해줘"
Claude:
  [Bash: npm run build]
  → "빌드 성공"
  [Bash: npm start]
  → "서버가 포트 3000에서 실행 중"
```

**Git 작업**:
```
사용자: "무엇이 변경되었는지 보여줘"
Claude:
  [Bash: git status]
  [Bash: git diff]
  → "다음이 변경되었습니다..."
```

#### Bash 도구 제한사항

**주의할 점**:
- **작업 디렉토리가 호출 간에 초기화됨** (절대 경로 또는 `&&` 체이닝 사용)
- **대화형 명령은 작동하지 않음** (`vim`, `nano`, 대화형 프롬프트)
- **타임아웃**: 기본적으로 2분 후 타임아웃
- **출력 잘림**: 매우 긴 출력은 잘릴 수 있음

**명령 체이닝**:
```bash
# 좋음 — 체이닝이 있는 단일 Bash 호출
npm install && npm run build && npm test

# 이것도 작동 — 순차적 명령
npm install; npm run build; npm test
```

**오래 걸리는 명령**:
```
사용자: "전체 테스트 스위트를 실행해줘 (~5분 소요)"
Claude:
  [확장된 타임아웃으로 Bash: npm run test:full]
  → 더 많은 시간을 허용하기 위해 timeout 매개변수 사용
```

#### Bash를 사용하지 말아야 할 때

Claude는 많은 작업에서 Bash보다 전용 도구를 선호합니다:

| 작업 | 사용하지 마세요 | 대신 사용하세요 |
|------|----------------|----------------|
| 파일 읽기 | `cat file.js` | **Read** 도구 |
| 내용 검색 | `grep -r "pattern"` | **Grep** 도구 |
| 파일 찾기 | `find . -name "*.js"` | **Glob** 도구 |
| 파일 편집 | `sed -i 's/old/new/'` | **Edit** 도구 |
| 파일 생성 | `echo "content" > file` | **Write** 도구 |

**Bash의 용도**: 시스템 명령, 패키지 관리자, 빌드, 테스트, git, 스크립트.

### 연습

**과제**: 명령 실행 연습하기

**준비**:
```bash
cd ~/claude-practice
mkdir bash-demo && cd bash-demo
npm init -y
cat > index.js << 'EOF'
console.log("Hello from bash-demo!")
EOF
cat > math.js << 'EOF'
function add(a, b) { return a + b }
function subtract(a, b) { return a - b }
module.exports = { add, subtract }
EOF
claude
```

**과제들**:

1. **의존성 설치**:
```
"jest를 dev 의존성으로 설치해줘"
```

2. **테스트 생성 및 실행**:
```
"math.js의 테스트 파일을 만들고 테스트를 실행해줘"
```

3. **Git 상태 확인**:
```
"git 저장소를 초기화하고 상태를 보여줘"
```

4. **스크립트 실행**:
```
"package.json에 'start' 스크립트를 추가하고 실행해줘"
```

5. **명령 체이닝**:
```
"의존성 설치하고, 테스트 실행하고, git 상태를 보여줘"
```

### 확인사항

다음 단계로 넘어가기 전에 확인하세요:
- [ ] Claude가 언제 Bash를 사용하고 다른 도구를 사용하는지 이해하는가
- [ ] 패키지 관리 명령을 실행할 수 있는가
- [ ] 테스트를 실행하고 결과를 해석할 수 있는가
- [ ] 명령을 함께 체이닝할 수 있는가
- [ ] 어떤 작업에 권한이 필요한지 아는가

### 흔한 실수

1. **파일 작업에 Bash 사용**:
   - Claude는 전용 Read/Write/Edit/Glob/Grep 도구가 있습니다
   - Bash는 파일 조작이 아닌 시스템 명령에 사용해야 합니다

2. **명령 체이닝을 잊음**:
   - 작업 디렉토리가 Bash 호출 간에 유지되지 않습니다
   - 의존적인 명령에는 `&&`를 사용하세요

3. **타임아웃 없이 오래 걸리는 명령 실행**:
   - 기본 타임아웃은 2분입니다
   - 오래 걸리는 빌드/테스트에는 확장된 타임아웃을 요청하세요

### 프로 팁

**백그라운드 실행**:
```
"다른 작업을 하는 동안 전체 테스트 스위트를
백그라운드에서 실행해줘"
```

**병렬 명령**:
```
"린트와 테스트를 병렬로 실행해줘"
→ Claude는 여러 Bash 호출을 동시에 할 수 있습니다
```

**스크립트 발견**:
```
"package.json에서 사용 가능한 스크립트를 보여주고
각각 무엇을 하는지 설명해줘"
```

---

## 레슨 12: 복잡한 작업을 위한 도구 조합

### 학습 목표
- 다중 도구 워크플로 이해하기
- 효율적인 검색-읽기-편집 파이프라인 구축하기
- 버그 수정과 기능 추가를 위한 도구 오케스트레이션
- 다단계 작업을 위한 효과적인 프롬프팅 개발하기

### 내용

#### 왜 도구를 조합할까?

실제 작업은 단일 도구를 거의 사용하지 않습니다. 일반적인 워크플로는 여러 도구가 함께 작동합니다:

```
버그 수정 워크플로:
  Grep  → 버그가 있을 수 있는 곳 찾기
  Read  → 코드 이해하기
  Edit  → 버그 수정하기
  Bash  → 테스트를 실행하여 검증

기능 추가:
  Glob  → 관련 파일 찾기
  Read  → 기존 패턴 이해하기
  Write → 새 파일 생성
  Edit  → 임포트와 참조 업데이트
  Bash  → 빌드 및 테스트
```

#### 검색 → 읽기 → 실행 패턴

가장 일반적인 다중 도구 패턴:

**1단계: 검색** (Glob 또는 Grep)
```
관련 파일 찾기
```

**2단계: 읽기** (Read 도구)
```
코드 이해하기
```

**3단계: 실행** (Edit, Write 또는 Bash)
```
변경하거나 명령 실행하기
```

**예시 — 버그 찾기 및 수정하기**:
```
사용자: "사용자들이 로그인 엔드포인트에서 500 에러가 발생한다고 합니다"

Claude:
  [Grep: src/에서 "login"]
  → 발견: src/routes/auth.js, src/controllers/authController.js

  [Read: src/routes/auth.js]
  → POST /login 라우트가 여기에 정의됨

  [Read: src/controllers/authController.js]
  → 발견: 사용자 조회에 null 체크 누락

  [Edit: src/controllers/authController.js]
  → null 체크 추가: if (!user) return res.status(401)...

  [Bash: npm test]
  → 모든 테스트 통과

  "수정했습니다! 로그인 컨트롤러가 데이터베이스 쿼리에서
  null 사용자 결과를 확인하지 않고 있었습니다."
```

#### 일반적인 다중 도구 워크플로

**1. 코드 탐색**:
```
사용자: "인증 시스템을 설명해줘"

Claude:
  [Glob: **/*auth*.*]         → 인증 관련 파일 찾기
  [Read: 각 파일]              → 코드 이해하기
  → 포괄적인 설명 제공
```

**2. 리팩토링**:
```
사용자: "모든 곳에서 UserService를 UserManager로 이름 변경해줘"

Claude:
  [Grep: "UserService"]       → 모든 참조 찾기
  [Read: 각 파일]              → 컨텍스트 이해하기
  [Edit: 각 파일]              → 참조 교체 (replace_all)
  [Bash: npm test]             → 아무것도 깨지지 않았는지 확인
```

**3. 기능 추가**:
```
사용자: "API에 /health 엔드포인트를 추가해줘"

Claude:
  [Glob: src/routes/**/*.js]  → 라우트 파일 찾기
  [Read: 기존 라우트]          → 패턴 이해하기
  [Edit: src/routes/index.js]  → health 라우트 추가
  [Write: tests/health.test.js] → 테스트 생성
  [Bash: npm test]              → 테스트 실행
```

**4. 의존성 업데이트**:
```
사용자: "lodash를 업데이트하고 호환성 문제를 수정해줘"

Claude:
  [Bash: npm outdated]        → 현재 버전 확인
  [Grep: src/에서 "lodash"]   → 모든 사용처 찾기
  [Read: 각 파일]              → 어떻게 사용되는지 이해
  [Bash: npm install lodash@latest] → 업데이트
  [Bash: npm test]             → 문제 확인
  [Edit: 필요한 파일들]        → 호환성 문제 수정
```

#### 다중 도구 작업을 위한 프롬프팅

**목표를 구체적으로 명시**:
```
나쁜 예:  "코드를 수정해줘"
좋은 예: "500 에러를 일으키는 인증 버그를 찾아서
      수정하고 테스트를 실행하여 검증해줘"
```

**컨텍스트 제공**:
```
나쁜 예:  "기능을 추가해줘"
좋은 예: "다른 라우트와 같은 패턴을 따라서
      { status: 'ok', uptime: process.uptime() }를 반환하는
      GET /health 엔드포인트를 추가해줘"
```

**검증 요청**:
```
"변경 후 테스트를 실행하고 변경한 내용의
git diff를 보여줘"
```

#### 효율성 팁

**1. Claude가 먼저 탐색하게 하기**:
```
"프로젝트 구조를 탐색하고, API에서 데이터베이스까지
데이터가 어떻게 흐르는지 알려줘"
```

**2. 병렬 작업 요청**:
```
"이 5개 파일을 병렬로 읽고 아키텍처를
요약해줘"
```

**3. 관련 변경을 일괄 처리**:
```
"모든 API 엔드포인트를 한 번에 새 응답 형식을
사용하도록 업데이트해줘"
```

**4. 먼저 계획 요청**:
```
"변경하기 전에 인증 모듈 리팩토링 계획을
알려줘"
```

### 연습

**과제**: 다중 도구 워크플로 완성하기

**준비**:
```bash
cd ~/claude-practice
mkdir -p multi-tool-demo/src multi-tool-demo/tests

cat > multi-tool-demo/src/app.js << 'EOF'
const express = require('express')
const app = express()

app.get('/users', (req, res) => {
  res.json([
    { id: 1, name: 'Alice' },
    { id: 2, name: 'Bob' }
  ])
})

app.get('/users/:id', (req, res) => {
  const users = [
    { id: 1, name: 'Alice' },
    { id: 2, name: 'Bob' }
  ]
  const user = users.find(u => u.id === parseInt(req.params.id))
  res.json(user)
})

app.listen(3000)
EOF

cat > multi-tool-demo/package.json << 'EOF'
{
  "name": "multi-tool-demo",
  "version": "1.0.0",
  "scripts": {
    "start": "node src/app.js",
    "test": "jest"
  },
  "dependencies": {
    "express": "^4.18.0"
  },
  "devDependencies": {
    "jest": "^29.0.0"
  }
}
EOF

cd multi-tool-demo
claude
```

**과제들** (각각 여러 도구를 사용):

1. **버그 수정 워크플로**:
```
"GET /users/:id 엔드포인트가 사용자를 찾지 못하면 충돌합니다.
버그를 찾아서 수정하고 테스트를 추가해줘."
```

2. **기능 추가**:
```
"POST /users 엔드포인트를 추가해줘. 기존 엔드포인트와 같은
패턴을 따르세요. 유효성 검사, 에러 처리, 테스트를 추가해줘."
```

3. **리팩토링**:
```
"사용자 데이터가 하드코딩되어 있습니다. 별도의 데이터 모듈로
추출하고, 라우트가 그것을 사용하도록 업데이트하고,
모든 것이 여전히 작동하는지 확인해줘."
```

4. **코드 리뷰**:
```
"src/의 모든 코드를 보안 문제, 누락된 에러 처리,
모범 사례 위반에 대해 검토해줘.
발견한 모든 것을 수정해줘."
```

5. **문서화**:
```
"프로젝트를 탐색하고, API 문서, 설정 안내,
예시 사용법이 포함된 포괄적인 README.md를 생성해줘."
```

### 확인사항

다음 단계로 넘어가기 전에 확인하세요:
- [ ] 검색 → 읽기 → 실행 패턴을 설명할 수 있는가
- [ ] 다단계 워크플로를 위해 Claude에 프롬프트할 수 있는가
- [ ] 다양한 하위 작업을 위한 도구 선택을 이해하는가
- [ ] 변경 후 검증을 요청할 수 있는가
- [ ] 효율적인 도구 사용을 안내하는 프롬프트를 작성할 수 있는가

### 흔한 실수

1. **너무 모호함**:
   - Claude가 올바른 도구를 선택하려면 명확한 목표가 필요합니다
   - 무엇을 찾고, 변경하고, 검증할지 명시하세요

2. **검증을 요청하지 않음**:
   - 변경 후 항상 Claude에게 테스트하도록 요청하세요
   - "테스트를 실행해줘"는 대부분의 워크플로에 포함되어야 합니다

3. **컨텍스트를 제공하지 않음**:
   - "API를 수정해줘" → Claude가 어떤 API인지, 무엇이 잘못되었는지 모릅니다
   - "POST /users의 500 에러를 수정해줘" → 명확하고 실행 가능합니다

### 프로 팁

**다단계 프롬프트 템플릿**:
```
"다음을 해주세요:
1. [검색/찾기]: 관련 코드를 찾아줘
2. [이해]: 분석해줘
3. [변경]: 수정해줘
4. [검증]: 작동하는지 테스트해줘
5. [보고]: 무엇이 변경되었는지 보여줘"
```

**계획 요청**:
```
"시작하기 전에, 이 변경에 대한 계획을 설명해줘.
그다음 승인하면 진행해줘."
```

**반복적 워크플로**:
```
"단계별로 작업합시다.
먼저, 결제 시스템과 관련된 모든 파일을 찾아줘."
[결과 검토]
"좋아. 이제 메인 결제 컨트롤러를 읽어줘."
[코드 검토]
"이제 45줄의 이중 청구 버그를 수정해줘."
```

---

## 섹션 2 요약

이제 Claude Code의 핵심 파일 도구를 마스터했습니다:

| 도구 | 목적 | 주요 사용법 |
|------|------|------------|
| **Read** | 파일 내용 보기 | 코드 이해, 변경 사항 검토 |
| **Write** | 파일 생성/교체 | 새 파일, 보일러플레이트, 전체 재작성 |
| **Edit** | 기존 파일 수정 | 대상 지정 변경, 버그 수정, 업데이트 |
| **Glob** | 패턴으로 파일 찾기 | 파일 발견, 프로젝트 탐색 |
| **Grep** | 파일 내용 검색 | 코드 검색, 패턴 찾기, 감사 |
| **Bash** | 터미널 명령 실행 | 빌드, 테스트, 패키지, git |
| **조합** | 다중 도구 워크플로 | 실제 작업은 여러 도구를 사용 |

### 다음 단계

**섹션 3: Git 통합**에서는 Claude가 Git과 함께 커밋, 브랜치, 풀 리퀘스트, 코드 리뷰를 수행하는 방법을 배웁니다 — 이 파일 도구들을 기반으로 전체 개발 워크플로를 관리합니다.
