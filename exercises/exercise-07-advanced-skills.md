# 연습 7: 고급 스킬 개발

**소요 시간**: 1시간
**사전 조건**: 레슨 36-42

## 목표
다단계 워크플로우, 트리거, 모델 선택, 지원 파일을 포함한 고급 스킬을 생성, 구성, 공유합니다.

## 설정

```bash
mkdir -p ~/claude-exercises/ex7/src ~/claude-exercises/ex7/tests ~/claude-exercises/ex7/.claude/skills
cd ~/claude-exercises/ex7
git init

cat > src/app.js << 'EOF'
const express = require('express')
const app = express()
app.use(express.json())
app.get('/api/health', (req, res) => res.json({ status: 'ok' }))
app.listen(3000)
module.exports = app
EOF

cat > src/utils.js << 'EOF'
function formatDate(date) { return date.toISOString().split('T')[0] }
function slugify(text) { return text.toLowerCase().replace(/\s+/g, '-') }
function capitalize(str) { return str.charAt(0).toUpperCase() + str.slice(1) }
module.exports = { formatDate, slugify, capitalize }
EOF

cat > tests/utils.test.js << 'EOF'
const { formatDate, slugify, capitalize } = require('../src/utils')
test('slugify converts spaces to hyphens', () => {
  expect(slugify('hello world')).toBe('hello-world')
})
EOF

cat > package.json << 'EOF'
{ "name": "ex7", "scripts": { "test": "jest", "lint": "eslint src/" } }
EOF

mkdir -p ~/.claude/skills
claude
```

## 과제

### 과제 1: 다단계 워크플로우 스킬 생성 (15분)

구조화된 API 엔드포인트 생성 워크플로우를 수행하는 스킬을 만듭니다.

`.claude/skills/generate-endpoint/SKILL.md` 파일을 다음 내용으로 생성합니다:

```markdown
---
name: generate-endpoint
description: 라우트, 컨트롤러, 유효성 검사, 테스트를 포함한 완전한 REST API 엔드포인트 생성
commands:
  - generate-endpoint
  - gen-api
tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
---

# REST API 엔드포인트 생성

엔티티 이름과 함께 호출 시 (예: `/generate-endpoint products`):

## 단계 1: 기존 패턴 분석
- Grep 도구를 사용하여 `src/`에서 기존 라우트 패턴 찾기
- 기존 라우트 파일을 읽어 프로젝트의 관례 파악
- 미들웨어 사용법, 오류 처리 스타일, 응답 형식 확인

## 단계 2: 라우트 파일 생성
`src/routes/{entity}.js`를 다음과 같이 생성:
- GET / (전체 목록)
- GET /:id (단일 조회)
- POST / (생성)
- PUT /:id (수정)
- DELETE /:id (삭제)
단계 1에서 찾은 패턴과 동일하게 따릅니다.

## 단계 3: 유효성 검사 생성
`src/validation/{entity}.js`를 다음과 같이 생성:
- 생성 및 수정을 위한 입력 유효성 검사 스키마
- 기존 코드와 동일한 유효성 검사 라이브러리 사용, 없으면 기본 검사 사용

## 단계 4: 테스트 생성
`tests/{entity}.test.js`를 다음과 같이 생성:
- 각 엔드포인트에 대한 테스트
- 성공 및 오류 케이스 포함
- 기존 테스트와 동일한 테스트 패턴 사용

## 단계 5: 라우트 등록
- `src/app.js`에 새 라우트 추가
- 기존 import 및 등록 패턴 따르기

## 단계 6: 요약
생성된 내용 보고:
- 생성된 파일
- 사용 가능한 엔드포인트
- 작성된 테스트
```

**확인**: `/generate-endpoint products`를 실행하고 라우트, 유효성 검사, 테스트 파일이 생성되는지 확인

---

### 과제 2: 고급 프론트매터 추가 (10분)

트리거, 모델 지정, 도구 제한이 포함된 스킬을 생성합니다.

`.claude/skills/quick-review/SKILL.md` 파일을 생성합니다:

```markdown
---
name: quick-review
description: 버그와 보안 이슈에 초점을 맞춘 빠른 코드 리뷰
commands:
  - quick-review
  - qr
triggers:
  - pattern: "review this (file|code|change)"
    command: quick-review
model: sonnet
autoInvoke: false
tools:
  - Read
  - Grep
  - Glob
---

# 빠른 코드 리뷰

지정된 파일 또는 디렉토리에 대해 빠르고 집중적인 코드 리뷰를 수행합니다.

## 리뷰 체크리스트
1. **보안**: SQL 인젝션, XSS, 하드코딩된 시크릿, 안전하지 않은 기본값
2. **버그**: Null 참조, 오프바이원 오류, 처리되지 않은 프로미스, 경쟁 조건
3. **성능**: N+1 쿼리, 불필요한 루프, 누락된 인덱스
4. **모범 사례**: 오류 처리, 입력 유효성 검사, 로깅

## 출력 형식
```text
## Quick Review: {filename}

### 심각한 이슈
- [SECURITY] 설명 (line N)
- [BUG] 설명 (line N)

### 경고
- [PERF] 설명 (line N)
- [STYLE] 설명 (line N)

### 요약
심각 X건, 경고 Y건 발견
```

## 제약 조건
- 읽기 전용: 파일을 수정하지 않음
- 영향이 큰 이슈에만 집중
- 리뷰 항목 20개 이하로 유지
```

Claude에게 다음과 같이 말하여 트리거를 테스트합니다: "review this file src/app.js"

**확인**: 문구에서 스킬이 자동으로 트리거되고, 읽기 전용 도구만 사용하며, 정의된 형식으로 출력되는지 확인

---

### 과제 3: 템플릿과 지원 파일이 있는 스킬 생성 (10분)

코드 생성을 위해 지원 템플릿 파일을 사용하는 스킬을 만듭니다.

```bash
mkdir -p .claude/skills/component-generator/templates
```

`.claude/skills/component-generator/SKILL.md`를 생성합니다:

```markdown
---
name: component-generator
description: 템플릿을 사용하여 테스트와 스타일이 포함된 React 컴포넌트 생성
commands:
  - gen-component
tools:
  - Read
  - Write
  - Glob
---

# 컴포넌트 생성기

이 스킬의 디렉토리에 있는 템플릿을 사용하여 React 컴포넌트를 생성합니다.

## 단계
1. `templates/component.template`에서 템플릿 읽기
2. `{{ComponentName}}`을 제공된 이름(PascalCase)으로 교체
3. `{{componentName}}`을 camelCase 버전으로 교체
4. `src/components/{ComponentName}/{ComponentName}.jsx`에 컴포넌트 파일 생성
5. `templates/test.template`에서 테스트 템플릿 읽기 및 적용
6. `src/components/{ComponentName}/{ComponentName}.test.jsx`에 테스트 생성
7. 생성된 파일 보고
```

`.claude/skills/component-generator/templates/component.template`을 생성합니다:

```text
import React from 'react'

function {{ComponentName}}({ children, className = '' }) {
  return (
    <div className={`{{componentName}} ${className}`}>
      {children}
    </div>
  )
}

export default {{ComponentName}}
```

`.claude/skills/component-generator/templates/test.template`을 생성합니다:

```text
import { render, screen } from '@testing-library/react'
import {{ComponentName}} from './{{ComponentName}}'

describe('{{ComponentName}}', () => {
  it('renders children', () => {
    render(<{{ComponentName}}>Hello</{{ComponentName}}>)
    expect(screen.getByText('Hello')).toBeInTheDocument()
  })

  it('applies custom className', () => {
    const { container } = render(<{{ComponentName}} className="custom" />)
    expect(container.firstChild).toHaveClass('custom')
  })
})
```

**확인**: `/gen-component UserProfile`을 실행하고 컴포넌트와 테스트 파일이 올바른 치환으로 생성되는지 확인

---

### 과제 4: 스킬 테스트 및 디버깅 (10분)

생성한 스킬을 체계적으로 테스트합니다:

1. 엣지 케이스로 엔드포인트 생성기 테스트:
   ```
   /generate-endpoint order-items
   ```
   하이픈이 포함된 이름이 올바르게 처리되는지 확인합니다.

2. 알려진 이슈가 있는 파일에 대해 빠른 리뷰 스킬 테스트:
   ```bash
   cat > src/risky.js << 'EOF'
   const password = "admin123"
   function query(input) { return `SELECT * FROM users WHERE name = '${input}'` }
   async function fetchData() { fetch('/api').then(r => r.json()) }
   EOF
   ```
   Claude에게 요청: "review this file src/risky.js"

3. 비정상적인 입력으로 컴포넌트 생성기 테스트:
   ```
   /gen-component my-widget
   ```
   이름이 PascalCase(MyWidget)로 변환되는지 확인합니다.

**확인**: 세 스킬 모두 오류 없이 엣지 케이스를 올바르게 처리하는지 확인

---

### 과제 5: 프로젝트 수준에서 스킬 공유 (10분)

git을 통해 팀 공유를 위한 스킬을 설정합니다:

1. 스킬이 `.claude/skills/`(프로젝트 수준, `~/.claude/skills/`가 아님)에 있는지 확인:
   ```bash
   ls -la .claude/skills/
   ```

2. 스킬을 버전 관리에 추가:
   ```
   ".claude/skills/에 있는 모든 스킬을 스테이징하고 각 스킬이 무엇을 하는지 설명하는 메시지와 함께 커밋해줘"
   ```

3. Claude에게 스킬 인덱스 생성을 요청:
   ```
   ".claude/skills/에 있는 모든 SKILL.md 파일을 읽고 각 스킬의 이름, 명령어, 설명을 나열하는 요약 표를 만들어줘"
   ```

4. 팀원이 스킬을 사용할 수 있는지 다음을 확인:
   - 모든 SKILL.md 파일에 완전한 프론트매터가 있는지
   - 템플릿이 스킬과 함께 포함되어 있는지
   - 절대 경로나 머신 특정 참조가 없는지

**확인**: `git log`에 커밋이 표시되고, 모든 스킬이 저장소에서 올바르게 추적되는지 확인

---

### 과제 6: 스킬 상호 작용 및 체이닝 (5분)

여러 스킬을 함께 사용하는 것을 테스트합니다:

```
"먼저 /generate-endpoint comments를 사용하고, 그런 다음 생성된 라우트 파일에 /quick-review를 사용해줘"
```

**예상 결과**:
- 첫 번째 스킬이 엔드포인트 파일을 생성
- 두 번째 스킬이 생성된 코드를 리뷰
- 생성 과정의 이슈가 리뷰에서 발견됨

**확인**: 리뷰 출력이 생성된 파일을 참조하고 관련 피드백을 제공하는지 확인

---

## 완료 기준

- [ ] 5단계 이상의 다단계 워크플로우 스킬을 생성함
- [ ] 고급 프론트매터를 사용함: 트리거, 모델, 도구 제한
- [ ] 외부 템플릿 파일이 있는 스킬을 구축함
- [ ] 엣지 케이스와 비정상적인 입력으로 스킬을 테스트함
- [ ] git을 통해 프로젝트 수준에서 스킬을 공유함
- [ ] 두 개의 스킬을 성공적으로 체이닝함
