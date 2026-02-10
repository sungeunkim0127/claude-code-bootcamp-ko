# 섹션 7: 스킬 시스템 (레슨 33-42)

## 개요
스킬(Skill) 시스템을 마스터하여 재사용 가능하고 공유 가능한 전문 지식을 만들어 보세요. 스킬은 Claude Code의 커스터마이징과 자동화를 위한 가장 강력한 기능 중 하나입니다.

---

## 레슨 33: 스킬이란 무엇인가?

### 학습 목표
- 재사용 가능한 전문 지식으로서의 스킬 이해하기
- 스킬 범위(엔터프라이즈, 개인, 프로젝트, 플러그인) 학습하기
- 내장 스킬 탐색하기
- 스킬을 만들어야 할 시점 파악하기

### 내용

#### 스킬이란 무엇인가?

**스킬(Skill)**은 전문화된 지식과 워크플로를 통해 Claude의 기능을 확장하는 재사용 가능한 전문 지식 패키지입니다.

**스킬을 다음과 같이 생각해 보세요**:
- 특정 작업을 위한 전문 어시스턴트
- 일반적인 워크플로를 위한 재사용 가능한 템플릿
- 코드화된 모범 사례
- 공유 가능한 팀 지식

#### 스킬의 작동 방식

```
User: /code-review

Claude:
  [code-review 스킬 로드]
  [스킬 지침 따르기]
  [스킬 전용 도구 사용]
  [스킬에 정의된 출력 생성]
```

**내부 동작 원리**:
1. 스킬의 SKILL.md가 컨텍스트에 로드됩니다
2. Claude가 스킬 지침을 따릅니다
3. 도구가 스킬에서 허용된 목록으로 제한됩니다
4. 출력이 스킬 요구사항에 맞게 포맷됩니다

#### 스킬 구성 요소

**SKILL.md 파일**:
```markdown
---
name: skill-name
description: What this skill does
commands:
  - command-name
triggers:
  - pattern: "trigger phrase"
    command: command-name
model: sonnet
autoInvoke: false
tools:
  - Read
  - Write
---

# Skill Instructions

[Claude를 위한 상세 지침]
```

**주요 구성 요소**:
- **프론트매터(Frontmatter)** (YAML): 설정 정보
- **지침(Instructions)**: Claude가 수행해야 할 내용
- **예제(Examples)**: 스킬 사용 방법
- **보조 파일(Supporting Files)**: 템플릿, 예시

#### 스킬 범위

**1. 엔터프라이즈 스킬(Enterprise Skills)** (`/enterprise/skills/`)
- 조직에서 관리
- 모든 팀원이 사용 가능
- 표준 강제 적용
- 예시: 보안 리뷰, 배포 체크리스트

**2. 개인 스킬(Personal Skills)** (`~/.claude/skills/`)
- 개인용 스킬
- 모든 프로젝트에서 사용 가능
- 예시: 개인 코드 스타일, 자주 하는 작업

**3. 프로젝트 스킬(Project Skills)** (`.claude/skills/`)
- 특정 프로젝트에 한정
- git을 통해 공유
- 예시: 프로젝트별 워크플로

**4. 플러그인 스킬(Plugin Skills)** (플러그인을 통해 제공)
- 플러그인과 함께 번들로 제공
- 마켓플레이스에서 설치
- 예시: 커뮤니티 기여 스킬

**우선순위**: 엔터프라이즈 → 개인 → 프로젝트 → 플러그인

#### 내장 스킬 vs 사용자 정의 스킬

**내장 스킬(Built-in Skills)** (사용 가능한 경우):
- Claude Code에 사전 설치됨
- 일반적인 워크플로 지원
- Anthropic에서 유지 관리
- 예시: 기본 git, 테스트, 문서화

**사용자 정의 스킬(Custom Skills)**:
- 사용자 또는 팀에서 직접 생성
- 사용자의 요구에 맞게 특화
- 다른 사람과 공유 가능
- 예시: 회사 전용 워크플로

#### 스킬을 만들어야 할 때

**스킬을 만들어야 하는 경우**:
- 동일한 작업을 반복적으로 수행할 때
- 작업에 명확하고 재사용 가능한 단계가 있을 때
- 다른 사람도 해당 워크플로에서 이점을 얻을 수 있을 때
- 일관된 결과를 원할 때
- 작업에 특정 도메인 지식이 필요할 때

**예시**:
- 회사 표준에 따른 코드 리뷰
- 패턴을 따르는 새 컴포넌트 생성
- 배포 체크리스트
- 데이터베이스 마이그레이션 워크플로
- API 엔드포인트 생성

**스킬을 만들지 않아도 되는 경우**:
- 일회성 작업인 경우
- 단계가 매번 크게 다른 경우
- 훅(Hook)으로 처리하는 것이 더 나은 경우 (자동 동작)
- 너무 단순한 경우 (Claude에게 직접 물어보면 되는 수준)

#### 스킬 검색

**사용 가능한 스킬 목록 보기**:
```bash
claude skills list
# 또는 세션 내에서:
/skills
```

**스킬 정보 확인**:
```bash
claude skills info code-review
```

**스킬 검색**:
```bash
claude skills search "review"
```

#### 스킬 호출

**1. 명령어 구문**:
```
/skill-name
/skill-name arg1 arg2
```

**2. 자동 호출(Auto-Invocation)**:
```
User: "review this code"
→ /code-review 스킬이 자동으로 트리거됨
```

**3. 스킬 도구(Skill Tool)**:
```
Use Skill tool with skill name
```

#### 스킬의 장점

**일관성**:
- 매번 동일한 프로세스 수행
- 표준화된 출력
- 오류 감소

**효율성**:
- 지침을 반복하지 않아도 됨
- 더 빠른 실행
- 여러 프롬프트 대신 하나의 명령어

**지식 공유**:
- 모범 사례 코드화
- 신규 팀원 온보딩
- 전문 지식 공유

**품질**:
- 포괄적인 체크리스트
- 단계 누락 방지
- 검증된 워크플로

### 연습

**과제**: 스킬 탐색 및 이해하기

**준비**:
```bash
# 스킬이 있는지 확인
ls ~/.claude/skills/

# 없다면 디렉터리 생성
mkdir -p ~/.claude/skills/
```

**작업**:

1. **개념 이해**:
```
업무에서 반복적으로 수행하는 3가지 작업을 생각해 보세요.
각각에 대해 다음을 답해 보세요:
- 어떤 단계들이 있나요?
- 이것을 스킬로 만들 수 있나요?
- 스킬이 무엇을 하게 될까요?
```

2. **스킬 구조 탐색**:
```
스킬 템플릿을 살펴보세요:
cat ~/claude-code-bootcamp/templates/SKILL-TEMPLATE.md

다음을 확인해 보세요:
- 설정 정보는 어디에 있나요?
- 지침은 어디에 있나요?
- 예제는 어디에 있나요?
```

3. **예제 스킬 읽기**:
```
코드 리뷰 스킬을 읽어 보세요:
cat ~/claude-code-bootcamp/resources/example-skills/code-review-skill/SKILL.md

다음을 이해해 보세요:
- 무엇을 하나요?
- 어떤 도구를 사용하나요?
- 어떻게 구조화되어 있나요?
```

4. **첫 번째 스킬 계획하기**:
```
다음을 작성해 보세요:
- 스킬 이름: ___________
- 하는 일: ___________
- 따르는 단계: ___________
- 필요한 도구: ___________
```

### 확인사항

다음 단계로 넘어가기 전에 다음을 이해했는지 확인하세요:
- [ ] 스킬이 무엇이고 어떻게 작동하는지
- [ ] 네 가지 스킬 범위
- [ ] 스킬을 만들어야 할 때와 만들지 않아도 될 때
- [ ] 스킬을 호출하는 방법
- [ ] 기본적인 스킬 구조

### 핵심 개념

**스킬 vs 프롬프트(Prompt)**:
- 프롬프트: 일회성 지침
- 스킬: 재사용 가능한 워크플로

**스킬 vs 훅(Hook)**:
- 스킬: 수동으로 호출
- 훅: 자동으로 트리거

**스킬 vs 서브에이전트(Subagent)**:
- 스킬: 지침 + 제한된 도구
- 서브에이전트: 사용자 정의 동작을 가진 완전한 에이전트

---

## 레슨 34: 기존 스킬 사용하기

### 학습 목표
- /command 구문으로 스킬 호출하기
- 스킬에 인수 전달하기
- 자동 호출(Auto-invocation) 이해하기
- 스킬 도구(Skill Tool) 사용하기

### 내용

#### 기본 스킬 호출

**명령어 구문**:
```
/skill-name
```

**예시**:
```
You: /code-review

Claude:
  [code-review 스킬 로드]
  "포괄적인 코드 리뷰를 수행하겠습니다..."
  [리뷰 워크플로 실행]
```

#### 인수 전달하기

**인수와 함께 사용**:
```
/skill-name arg1 arg2 arg3
```

**예시**:
```
You: /code-review src/api/users.js

Claude:
  "src/api/users.js를 리뷰하겠습니다..."
  [지정된 파일 리뷰]
```

**여러 인수**:
```
You: /deploy production --skip-tests

Claude:
  "프로덕션에 배포합니다, 테스트를 건너뜁니다..."
```

#### 자동 호출(Auto-Invocation)

**트리거 패턴(Trigger Patterns)**:
스킬은 패턴을 기반으로 자동 트리거될 수 있습니다:

```yaml
triggers:
  - pattern: "review (this|the) code"
    command: review
```

**예시**:
```
You: "Can you review this code?"

Claude:
  [code-review 스킬 자동 호출]
  "코드 리뷰를 수행하겠습니다..."
```

**장점**:
- 자연어 상호작용
- 정확한 명령어를 기억할 필요 없음
- 매끄러운 경험

**자동 호출 비활성화**:
```yaml
autoInvoke: false
```

#### 스킬 도구(Skill Tool) 사용하기

**명시적 스킬 도구 호출**:
```
"Use the code-review skill on these files"
```

Claude가 수행하는 작업:
1. 스킬 참조를 인식
2. 스킬 로드
3. 워크플로 실행

**정규화된 이름(Fully Qualified Name)으로 호출**:
```
"Use enterprise:code-review skill"
"Use plugin:awesome-plugin:formatter skill"
```

#### 스킬 컨텍스트(Skill Context)

**스킬이 볼 수 있는 것**:
- 현재 대화 기록
- 언급한 파일들
- 현재 작업 디렉터리
- 스킬 지침
- 보조 파일

**스킬이 볼 수 없는 것**:
- 다른 스킬의 내부 정보
- 훅(Hook) 설정 (특별히 지정하지 않은 한)
- 전체 채팅 기록 (현재 세션만)

#### 일반적인 내장 스킬 (사용 가능한 경우)

**Git 스킬**:
```
/commit              # git 커밋 생성
/commit-fix          # 커밋 메시지 수정
/branch              # 브랜치 생성/전환
```

**테스트 스킬**:
```
/test                # 테스트 실행
/test-gen            # 테스트 생성
/coverage            # 커버리지 확인
```

**문서화 스킬**:
```
/doc                 # 문서 생성
/readme              # README 생성/업데이트
/api-doc             # API 문서화
```

**배포 스킬**:
```
/deploy              # 애플리케이션 배포
/rollback            # 배포 롤백
```

#### 스킬 사용 모범 사례

**스킬 사용 시**:

1. **스킬 설명을 먼저 읽으세요**:
```
/skills info code-review
```

2. **필요한 컨텍스트를 제공하세요**:
```
# 나쁜 예
/deploy

# 좋은 예
/deploy production --version v2.1.0
```

3. **출력을 확인하세요**:
- 스킬이 수행한 작업 검토
- 결과 확인
- 필요시 설명 요청

4. **스킬을 조합하세요**:
```
/test
# 테스트가 통과하면:
/deploy staging
```

### 연습

**과제**: 스킬을 효과적으로 사용하기

**준비**:
```bash
cd ~/claude-practice
claude
```

**작업**:

1. **사용 가능한 스킬 목록 보기** (지원되는 경우):
```
/skills
# 또는
"What skills are available?"
```

2. **스킬 정보 확인** (지원되는 경우):
```
/skills info code-review
# 또는
"Tell me about the code-review skill"
```

3. **스킬 호출하기**:
```
# code-review 스킬이 설치되어 있다면:
/code-review

# 그렇지 않다면 시뮬레이션:
"Perform a code review following these steps:
1. Check for security issues
2. Check code quality
3. Check performance
4. Generate report"
```

4. **인수 전달하기**:
```
/code-review src/app.js
# 또는
"Review src/app.js for security and performance"
```

5. **스킬 연결하기**:
```
"First run tests, then if they pass, create a commit"
```

### 확인사항

다음 단계로 넘어가기 전에 다음을 할 수 있는지 확인하세요:
- [ ] /command 구문으로 스킬 호출하기
- [ ] 스킬에 인수 전달하기
- [ ] 자동 호출(Auto-invocation) 이해하기
- [ ] 스킬 정보 확인하기
- [ ] 여러 스킬 연결하기

---

## 레슨 35: 첫 번째 스킬 만들기

### 학습 목표
- SKILL.md 파일 생성하기
- 스킬 지침 작성하기
- 스킬 디렉터리 설정하기
- 스킬 테스트하기

### 내용

#### 스킬 파일 구조

**기본 구조**:
```
~/.claude/skills/my-skill/
├── SKILL.md          # 메인 스킬 파일
├── templates/        # 선택적 템플릿
└── examples/         # 선택적 예제
```

**단일 파일 스킬**:
```
~/.claude/skills/my-skill.md
```

#### SKILL.md 생성하기

**최소 스킬**:
```markdown
---
name: my-skill
description: Brief description
commands:
  - my-command
---

# My Skill

Instructions for Claude to follow.
```

**단계별 안내**:

1. **디렉터리 생성**:
```bash
mkdir -p ~/.claude/skills/hello-world
cd ~/.claude/skills/hello-world
```

2. **SKILL.md 생성**:
```bash
cat > SKILL.md << 'EOF'
---
name: hello-world
description: A simple hello world skill
commands:
  - hello
  - greet
---

# Hello World Skill

When invoked, greet the user enthusiastically.

## Instructions

1. Generate a friendly greeting
2. Include a random fun fact
3. Wish them a great day
EOF
```

3. **스킬 테스트**:
```bash
claude
/hello
```

#### 효과적인 지침 작성하기

**지침 구조화**:

```markdown
# Skill Name

Brief overview of what this skill does.

## Purpose
[Why this skill exists]

## Instructions

### Step 1: [First Step]
[Detailed instructions]

### Step 2: [Second Step]
[Detailed instructions]

## Output Format
[How to format results]

## Examples
[Show examples]
```

**구체적으로 작성하세요**:
```markdown
# 나쁜 예 (모호함)
Check the code for issues.

# 좋은 예 (구체적)
Analyze the code for:
1. Security vulnerabilities (SQL injection, XSS, auth bypasses)
2. Performance issues (N+1 queries, inefficient algorithms)
3. Code quality (complexity >10, duplication, naming)
4. Best practices violations

For each issue found:
- Severity: Critical/High/Medium/Low
- Location: file:line
- Problem: What's wrong
- Fix: How to fix it
```

**번호가 매겨진 단계를 사용하세요**:
```markdown
1. Read all files in src/
2. Identify functions >50 lines
3. For each function:
   a. Calculate complexity
   b. Check for duplication
   c. Suggest refactoring if needed
4. Generate report with:
   - Total functions analyzed
   - Functions needing refactoring
   - Specific recommendations
```

#### 예제: 문서화 스킬

**전체 예제**:
```markdown
---
name: doc-function
description: Generates JSDoc documentation for functions
commands:
  - doc-function
  - document
model: sonnet
tools:
  - Read
  - Edit
---

# Function Documentation Skill

Generates comprehensive JSDoc documentation for JavaScript/TypeScript functions.

## Purpose
Ensure all functions have proper documentation following JSDoc standards.

## Instructions

### Step 1: Identify Function
If file and function name provided, read that file.
If not provided, ask which function to document.

### Step 2: Analyze Function
Analyze the function:
- Parameters and their types
- Return value and type
- What the function does
- Potential errors/exceptions
- Examples of usage

### Step 3: Generate JSDoc
Create JSDoc comment with:
```javascript
/**
 * [Brief description]
 *
 * [Detailed description if complex]
 *
 * @param {type} paramName - Description
 * @param {type} [optionalParam] - Description (optional)
 * @returns {type} Description of return value
 * @throws {ErrorType} When/why it throws
 * @example
 * // Example usage
 * functionName(arg1, arg2)
 */
```

### Step 4: Add Documentation
Use Edit tool to add JSDoc above the function.

### Step 5: Verify
Show the documented function for review.

## Example Usage

```
User: /doc-function src/utils.js calculateTotal

Output: [Adds JSDoc to calculateTotal function]
```

## Notes
- Follow existing project JSDoc style if present
- Include @example for non-trivial functions
- Use TypeScript types if .ts file
```

### 연습

**과제**: 첫 번째 유용한 스킬 만들기

**만들 스킬**: 코드 포맷 검사기

**요구사항**:
```markdown
Name: format-check
Description: Checks if code follows formatting rules
Commands: format-check

검사 항목:
- 일관된 들여쓰기 (2칸 스페이스)
- 후행 공백 없음
- 세미콜론 존재 (JS의 경우)
- 적절한 줄바꿈
- 최대 줄 길이 (80자)

출력: 발견된 포맷 문제 목록
```

**단계**:

1. **디렉터리 생성**:
```bash
mkdir -p ~/.claude/skills/format-check
```

2. **SKILL.md 작성**:
```bash
# 편집기를 사용하거나 Claude에게 요청하세요:
"Create SKILL.md for a format-check skill that:
[위의 요구사항을 붙여넣기]

Follow the structure from the documentation skill example."
```

3. **테스트하기**:
```bash
# 잘못된 포맷의 테스트 파일 생성
cat > ~/claude-practice/messy.js << 'EOF'
function test(){
var x=1;
  return x
}
EOF

claude
/format-check messy.js
```

4. **개선하기**:
```
결과를 바탕으로 스킬을 개선하세요:
- 더 많은 검사 항목 추가
- 출력 형식 개선
- 색상 코딩 추가 (원하는 경우)
```

### 확인사항

다음 단계로 넘어가기 전에 다음을 할 수 있는지 확인하세요:
- [ ] 프론트매터가 포함된 SKILL.md 파일 생성하기
- [ ] 명확하고 구체적인 지침 작성하기
- [ ] 지침을 단계별로 구성하기
- [ ] 스킬 테스트하기
- [ ] 결과에 따라 개선하기

### 흔한 실수

1. **너무 모호함**:
```markdown
# 나쁜 예
Check the code.

# 좋은 예
Check the code for:
1. [구체적인 항목]
2. [구체적인 항목]
```

2. **구조가 없음**:
```markdown
# 나쁜 예
Just a wall of text instructions...

# 좋은 예
## Step 1: ...
## Step 2: ...
```

3. **출력 형식 누락**:
```markdown
# 이 섹션을 추가하세요:
## Output Format
Present results as:
- Summary
- Details
- Recommendations
```

---

## 레슨 36: 고급 스킬 설정

### 학습 목표
- 스킬 프론트매터(Frontmatter) 옵션 설정하기
- 스킬의 도구 접근 제어하기
- 모델 선택 설정하기
- 트리거와 자동 호출 사용하기

### 내용

#### 프론트매터 심층 분석

YAML 프론트매터는 스킬의 동작 방식을 제어합니다:

```yaml
---
name: code-review
description: Comprehensive code review with security focus
commands:
  - review
  - code-review
  - cr
triggers:
  - pattern: "review (this|the|my) code"
    command: review
  - pattern: "check.*for (bugs|issues|problems)"
    command: review
model: sonnet
autoInvoke: true
tools:
  - Read
  - Glob
  - Grep
  - Bash
---
```

#### 도구 제한(Tool Restrictions)

스킬이 사용할 수 있는 도구를 제한하면 안전성과 집중도가 향상됩니다:

**읽기 전용 스킬** (리뷰 작업에 안전):
```yaml
tools:
  - Read
  - Glob
  - Grep
```

**전체 접근 스킬** (생성/수정 작업용):
```yaml
tools:
  - Read
  - Write
  - Edit
  - Glob
  - Grep
  - Bash
```

**도구를 제한하는 이유**:
- 리뷰 중 실수로 파일이 수정되는 것을 방지
- 스킬이 의도된 범위 내에서 작동하도록 보장
- 다른 사람과 공유할 때 더 안전
- 의도하지 않은 부작용의 위험 감소

#### 모델 선택

스킬의 복잡도에 맞는 모델을 선택하세요:

```yaml
# 빠르고 간단한 작업
model: haiku

# 범용 (기본값)
model: sonnet

# 복잡한 추론
model: opus
```

**가이드라인**:
| 작업 유형 | 권장 모델 |
|-----------|-----------|
| 포맷팅, 린팅 | Haiku |
| 코드 리뷰, 생성 | Sonnet |
| 아키텍처 분석, 복잡한 리팩토링 | Opus |

#### 트리거 패턴(Trigger Patterns)

자연어를 기반으로 스킬을 자동 호출합니다:

```yaml
triggers:
  - pattern: "review (this|the) (code|PR|pull request)"
    command: review
  - pattern: "create.*component"
    command: create-component
  - pattern: "generate.*test"
    command: gen-test
```

**패턴 작성 팁**:
- `(a|b)`를 사용하여 대안을 지정
- `.*`를 사용하여 유연한 매칭
- 오발동(false trigger)을 방지하기 위해 패턴을 구체적으로 유지
- 여러 표현으로 패턴을 테스트

#### 복수 명령어

하나의 스킬이 여러 명령어에 응답할 수 있습니다:

```yaml
commands:
  - review          # 전체 리뷰
  - quick-review    # 빠른 리뷰
  - security-check  # 보안 전용
```

지침에서 어떤 명령어가 사용되었는지 확인할 수 있습니다:
```markdown
## Instructions

If invoked as `/review`: Perform full comprehensive review.
If invoked as `/quick-review`: Check only critical issues.
If invoked as `/security-check`: Focus exclusively on security.
```

### 연습

**과제**: 고급 설정이 적용된 스킬 만들기

**`~/.claude/skills/smart-review/SKILL.md` 생성**:
```markdown
---
name: smart-review
description: Context-aware code review
commands:
  - smart-review
  - sr
triggers:
  - pattern: "review (this|the|my) (code|changes|file)"
    command: smart-review
model: sonnet
autoInvoke: false
tools:
  - Read
  - Glob
  - Grep
---

# Smart Review

Context-aware review that adapts to the type of code being reviewed.

## Instructions

1. Read the specified file(s)
2. Detect the language and framework
3. Apply language-specific checks:
   - JavaScript/TypeScript: async/await patterns, null checks, type safety
   - Python: type hints, exception handling, PEP 8
   - Rust: ownership, error handling, unsafe usage
4. Check for security issues appropriate to the language
5. Output findings grouped by severity
```

**테스트하기**:
```bash
claude
/smart-review src/app.js
```

### 확인사항

다음 단계로 넘어가기 전에 다음을 할 수 있는지 확인하세요:
- [ ] 완전한 프론트매터 설정 작성하기
- [ ] 적절하게 도구 접근 제한하기
- [ ] 스킬에 적합한 모델 선택하기
- [ ] 트리거 패턴 설정하기
- [ ] 복수 명령어 스킬 만들기

---

## 레슨 37: 스킬 템플릿과 보조 파일

### 학습 목표
- 스킬 내에서 템플릿 사용하기
- 보조 파일과 예제 포함하기
- 외부 리소스 참조하기
- 멀티 파일 스킬 패키지 구축하기

### 내용

#### 스킬 디렉터리 구조

복잡한 스킬은 여러 파일을 사용합니다:

```
~/.claude/skills/component-generator/
├── SKILL.md              # 메인 지침
├── templates/
│   ├── component.tsx     # 컴포넌트 템플릿
│   ├── component.test.tsx # 테스트 템플릿
│   └── component.css     # 스타일 템플릿
├── examples/
│   ├── simple.tsx        # 간단한 컴포넌트 예제
│   └── complex.tsx       # 복잡한 컴포넌트 예제
└── config/
    └── defaults.json     # 기본 설정
```

#### 지침에서 템플릿 사용하기

Claude가 따를 수 있도록 템플릿을 참조합니다:

```markdown
# Component Generator Skill

## Instructions

### Step 1: Determine Component Type
Ask the user or infer:
- Functional component (default)
- Form component
- List component
- Layout component

### Step 2: Generate Component
Use the template from `templates/component.tsx` as the base.
Replace placeholders:
- `{{COMPONENT_NAME}}` with the provided name
- `{{PROPS_INTERFACE}}` with generated props
- `{{COMPONENT_BODY}}` with appropriate JSX

### Step 3: Generate Test
Use `templates/component.test.tsx` as the base.
Create tests for:
- Rendering without errors
- Props are applied correctly
- User interactions work
- Edge cases

### Step 4: Generate Styles
Use `templates/component.css` as the base.
Follow the project's CSS methodology (BEM, CSS modules, etc.)
```

#### 템플릿 파일

**`templates/component.tsx`**:
```tsx
import React from 'react'
import styles from './{{COMPONENT_NAME}}.module.css'

interface {{COMPONENT_NAME}}Props {
  {{PROPS_INTERFACE}}
}

export function {{COMPONENT_NAME}}({ {{DESTRUCTURED_PROPS}} }: {{COMPONENT_NAME}}Props) {
  return (
    <div className={styles.container}>
      {{COMPONENT_BODY}}
    </div>
  )
}
```

**`templates/component.test.tsx`**:
```tsx
import { render, screen } from '@testing-library/react'
import { {{COMPONENT_NAME}} } from './{{COMPONENT_NAME}}'

describe('{{COMPONENT_NAME}}', () => {
  it('renders without crashing', () => {
    render(<{{COMPONENT_NAME}} {{DEFAULT_PROPS}} />)
  })

  {{ADDITIONAL_TESTS}}
})
```

#### 참조용 예제

Claude가 기대하는 출력을 이해할 수 있도록 예제를 포함합니다:

```markdown
## Examples

### Simple Component
See `examples/simple.tsx` for a basic button component.

### Complex Component
See `examples/complex.tsx` for a data table with sorting,
filtering, and pagination.

When generating, match the style and patterns shown in
these examples.
```

#### 설정 파일

기본값과 설정을 저장합니다:

**`config/defaults.json`**:
```json
{
  "cssMethodology": "modules",
  "testFramework": "react-testing-library",
  "stateManagement": "hooks",
  "defaultExport": false,
  "includeStorybook": true
}
```

지침에서 참조:
```markdown
Read `config/defaults.json` for project preferences.
Apply these settings unless the user specifies otherwise.
```

### 연습

**과제**: 멀티 파일 스킬 만들기

**API 엔드포인트 생성기(API endpoint generator)** 스킬을 만들어 보세요:

1. **디렉터리 생성**:
```bash
mkdir -p ~/.claude/skills/api-generator/{templates,examples}
```

2. **템플릿 생성** (`templates/route.js`):
```javascript
const express = require('express')
const router = express.Router()

// GET /{{RESOURCE}}
router.get('/', async (req, res) => {
  // TODO: Implement list
})

// GET /{{RESOURCE}}/:id
router.get('/:id', async (req, res) => {
  // TODO: Implement get by ID
})

// POST /{{RESOURCE}}
router.post('/', async (req, res) => {
  // TODO: Implement create
})

module.exports = router
```

3. **SKILL.md 생성**: 템플릿을 참조하는 지침 포함

4. **테스트**: `/api-generator users`

### 확인사항

다음 단계로 넘어가기 전에 다음을 할 수 있는지 확인하세요:
- [ ] 멀티 파일 스킬 디렉터리 생성하기
- [ ] 플레이스홀더가 포함된 템플릿 파일 작성하기
- [ ] 스킬 지침에서 템플릿 참조하기
- [ ] 가이드용 예제 파일 포함하기
- [ ] 기본값을 위한 설정 파일 사용하기

---

## 레슨 38: 스킬 테스트와 디버깅

### 학습 목표
- 체계적으로 스킬 테스트하기
- 일반적인 스킬 문제 디버깅하기
- 스킬 지침 반복 개선하기
- 스킬 출력 품질 검증하기

### 내용

#### 스킬 테스트하기

**수동 테스트 체크리스트**:

1. **기본 호출**:
```bash
claude
/your-skill
# 로드되나요? Claude가 스킬을 인식하나요?
```

2. **인수와 함께**:
```bash
/your-skill src/app.js
# 인수를 올바르게 사용하나요?
```

3. **엣지 케이스**:
```bash
/your-skill                    # 인수 없음
/your-skill nonexistent.js     # 잘못된 파일
/your-skill src/ tests/        # 여러 인수
```

4. **출력 품질**:
- 출력이 올바르게 포맷되었나요?
- 지침을 따르고 있나요?
- 유용하고 실행 가능한 내용인가요?

#### 일반적인 스킬 문제

**문제: 스킬을 찾을 수 없음**:
```
/my-skill
→ "Unknown command: my-skill"
```
**해결**: 파일 위치와 이름 철자를 확인하세요:
```bash
ls ~/.claude/skills/
# SKILL.md가 올바른 위치에 있는지 확인
```

**문제: Claude가 지침을 무시함**:
```
스킬 지침은 "테이블 형식으로 포맷"이라고 하는데
Claude가 일반 텍스트로 출력함
```
**해결**: 지침을 더 명시적으로 만드세요:
```markdown
# 나쁜 예
Format the output nicely.

# 좋은 예
Format the output as a markdown table with these columns:
| File | Issue | Severity | Line |
```

**문제: 잘못된 도구 사용**:
```
리뷰 스킬이 파일을 편집하려고 함
```
**해결**: 프론트매터에서 도구 접근을 제한하세요:
```yaml
tools:
  - Read
  - Glob
  - Grep
# Write, Edit, Bash 제외
```

**문제: 일관되지 않은 출력**:
```
때로는 목록을 출력하고, 때로는 문단을 출력함
```
**해결**: 명시적인 출력 형식 섹션을 추가하세요:
```markdown
## Output Format

Always output in this exact format:

### Summary
[1-2 sentence overview]

### Findings
1. **[Severity]**: [Issue description]
   - File: [path:line]
   - Fix: [suggested fix]

### Score
[X/10] with brief justification
```

#### 반복적 개선

**테스트 루프**:
```
1. 스킬 작성 → 2. 테스트 → 3. 문제 기록
                                  ↓
6. 재테스트 ← 5. 개선 ← 4. 수정 방법 파악
```

**테스트 로그 유지**:
```markdown
## Testing Notes

### v1 (2024-01-15)
- 문제: Python의 보안 취약점을 놓침
- 수정: Python 전용 보안 검사 추가

### v2 (2024-01-16)
- 문제: 빠른 리뷰에 출력이 너무 장황함
- 수정: 요약 섹션 추가, 세부사항은 확장 가능하게 변경

### v3 (2024-01-17)
- JS/Python에 잘 작동함
- TODO: Go와 Rust 지원 추가
```

#### 스킬 품질 체크리스트

스킬을 공유하기 전에 확인하세요:
- [ ] 인수 없이 작동하는지
- [ ] 유효한 인수로 작동하는지
- [ ] 잘못된 입력을 우아하게 처리하는지
- [ ] 출력이 일관되게 포맷되는지
- [ ] 지침이 명확하고 구체적인지
- [ ] 도구 제한이 적절한지
- [ ] 모델 선택이 최적인지
- [ ] 예제가 포함되어 있는지

### 연습

**과제**: 스킬 디버깅 및 개선

**의도적으로 결함이 있는 스킬로 시작하세요**:

`~/.claude/skills/bad-review/SKILL.md`를 생성하세요:
```markdown
---
name: bad-review
description: review code
commands:
  - bad-review
tools:
  - Read
  - Write
  - Edit
  - Bash
---

# Review

Check the code for problems. Tell the user what you found.
```

**작업**:

1. **결함 있는 스킬 테스트**:
```bash
claude
/bad-review src/app.js
```

2. **문제 식별**:
   - 너무 많은 도구 (Write/Edit는 리뷰에 불필요)
   - 모호한 지침
   - 출력 형식 미지정
   - 구체적인 검사 항목 미정의

3. **각 문제 수정**:
   - 읽기 전용 도구로 제한
   - 구체적인 리뷰 기준 추가
   - 출력 형식 정의
   - 예제 추가

4. **이름 변경 및 재테스트**:
   - `~/.claude/skills/good-review/`로 이동
   - 같은 파일로 다시 테스트
   - 출력 품질 비교

### 확인사항

다음 단계로 넘어가기 전에 다음을 할 수 있는지 확인하세요:
- [ ] 체계적으로 스킬 테스트하기
- [ ] 일반적인 스킬 문제 진단하기
- [ ] 반복적으로 스킬 지침 개선하기
- [ ] 출력 품질과 일관성 검증하기
- [ ] 테스트 체크리스트 사용하기

---

## 레슨 39: 팀과 스킬 공유하기

### 학습 목표
- 프로젝트 저장소를 통해 스킬 공유하기
- 팀 스킬 표준 설정하기
- 스킬 버전 관리하기
- 스킬 문서 작성하기

### 내용

#### 프로젝트 레벨 스킬

팀과 스킬을 공유하는 가장 일반적인 방법입니다:

```
your-project/
├── .claude/
│   └── skills/
│       ├── code-review/
│       │   └── SKILL.md
│       ├── deploy/
│       │   └── SKILL.md
│       └── component-gen/
│           ├── SKILL.md
│           └── templates/
├── src/
└── package.json
```

**장점**:
- 스킬이 프로젝트와 함께 버전 관리됨
- 모든 팀원이 동일한 스킬을 사용
- 스킬이 코드베이스와 함께 발전
- 신규 팀원이 자동으로 스킬을 받음

#### 프로젝트에 스킬 추가하기

**단계 1: 스킬 디렉터리 생성**:
```bash
mkdir -p .claude/skills/team-review
```

**단계 2: 스킬 작성**:
```bash
# 팀 표준에 맞는 SKILL.md 생성
```

**단계 3: 커밋 및 푸시**:
```bash
git add .claude/skills/
git commit -m "Add team code review skill"
git push
```

**단계 4: 팀에서 사용**:
```bash
# 레포를 풀(pull)하는 모든 팀원이 스킬을 사용할 수 있음
claude
/team-review
```

#### 스킬 문서화

공유되는 모든 스킬에는 다음이 포함되어야 합니다:

```markdown
---
name: team-review
description: Code review following [Company] standards
commands:
  - team-review
  - tr
---

# Team Code Review

## Purpose
Ensures all code follows our team's quality standards before merge.

## Usage
\`\`\`
/team-review                    # 스테이지된 변경사항 리뷰
/team-review src/feature.js     # 특정 파일 리뷰
/team-review --security         # 보안 중심 리뷰
\`\`\`

## What It Checks
1. **Security**: OWASP Top 10, auth patterns
2. **Performance**: N+1 queries, memory leaks
3. **Style**: ESLint compliance, naming conventions
4. **Testing**: Coverage thresholds, edge cases

## Output Format
Produces a structured report with:
- Summary score (1-10)
- Critical issues (must fix)
- Warnings (should fix)
- Suggestions (nice to have)

## Examples
[Link to example review output]

## Maintainer
@alice - ping in #engineering for issues
```

#### 버전 관리

**스킬의 시맨틱 버전 관리(Semantic Versioning)**:
```yaml
---
name: team-review
version: 2.1.0
# Major.Minor.Patch
# Major: 출력/동작의 호환성이 깨지는 변경
# Minor: 새로운 검사 항목 또는 기능
# Patch: 버그 수정, 문구 개선
---
```

**변경 이력(Changelog)** (SKILL.md 또는 별도 파일에):
```markdown
## Changelog

### v2.1.0
- Added Python type hint checking
- Improved output formatting

### v2.0.0
- Breaking: Changed output format to structured JSON
- Added severity scoring

### v1.0.0
- Initial release
```

#### 팀을 위한 스킬 표준

팀을 위한 **스킬 스타일 가이드**를 만드세요:

```markdown
# Skill Development Guidelines

## Naming
- Use kebab-case: `code-review`, `deploy-staging`
- Be descriptive: `api-endpoint-generator` not `gen`

## Structure
- Always include Purpose section
- Always include Usage examples
- Always include Output Format
- Include at least one worked example

## Tool Access
- Review skills: Read-only (Read, Glob, Grep)
- Generation skills: Read + Write
- Workflow skills: Full access (with justification)

## Testing
- Test with at least 3 different inputs
- Test edge cases (no args, bad args)
- Document expected outputs
```

### 연습

**과제**: 공유 가능한 팀 스킬 만들기

1. **프로젝트 스킬 생성**:
```bash
cd ~/claude-practice
mkdir -p .claude/skills/pr-description
```

2. **SKILL.md 작성**: 팀 형식에 맞는 PR 설명을 생성하는 스킬

3. **문서 추가**: 사용법, 예제, 출력 형식 포함

4. **테스트**: 최소 3가지 다른 시나리오로 테스트

5. **커밋**: 버전 관리에 스킬 커밋

### 확인사항

다음 단계로 넘어가기 전에 다음을 할 수 있는지 확인하세요:
- [ ] 프로젝트 레벨 스킬 생성하기 (.claude/skills/)
- [ ] 스킬 문서 작성하기
- [ ] 스킬 버전 관리하기
- [ ] git을 통해 스킬 공유하기
- [ ] 팀 스킬 표준 수립하기

---

## 레슨 40: 멀티 스텝 워크플로 스킬

### 학습 목표
- 여러 단계를 수행하는 스킬 구축하기
- 지침에 조건부 로직 구현하기
- 대화형 스킬 만들기
- 작업을 연결하기

### 내용

#### 멀티 스텝 스킬 설계

복잡한 스킬은 일련의 작업을 순차적으로 수행합니다:

```markdown
# Deploy Skill

## Instructions

### Step 1: Pre-flight Checks
1. Read package.json for version
2. Run `npm test` — STOP if tests fail
3. Run `npm run lint` — STOP if lint fails
4. Check git status — STOP if uncommitted changes

### Step 2: Build
1. Run `npm run build`
2. Verify build output exists in dist/

### Step 3: Version Bump
1. Ask user: major, minor, or patch?
2. Update version in package.json
3. Create git tag

### Step 4: Deploy
1. Run deployment command
2. Verify deployment succeeded
3. Report results
```

#### 조건부 로직(Conditional Logic)

명확한 if/then 언어를 사용하세요:

```markdown
## Instructions

### Step 1: Detect Environment
Read the project files to determine:
- If `package.json` exists → Node.js project
- If `requirements.txt` exists → Python project
- If `Cargo.toml` exists → Rust project
- If none match → Ask the user

### Step 2: Run Appropriate Checks
**For Node.js**:
1. Run `npm test`
2. Run `npm run lint`

**For Python**:
1. Run `pytest`
2. Run `flake8`

**For Rust**:
1. Run `cargo test`
2. Run `cargo clippy`
```

#### 중지 조건(Stop Conditions)

Claude에게 언제 멈추어야 하는지 알려주세요:

```markdown
## Critical Rules

**STOP and report** if any of these occur:
- Tests fail → Report which tests failed
- Lint errors > 10 → List the top 10 errors
- Security vulnerabilities found → List all critical ones
- Build fails → Show the error output

**Do NOT continue** to the next step until the current step passes.
```

#### 대화형 단계(Interactive Steps)

스킬은 사용자에게 입력을 요청할 수 있습니다:

```markdown
### Step 3: User Decision Point

Present the user with these options:
1. **Deploy to staging** — Safer, test first
2. **Deploy to production** — Direct deploy
3. **Cancel** — Abort deployment

Wait for user response before continuing.
```

#### 예제: 전체 워크플로 스킬

```markdown
---
name: feature-complete
description: Complete feature workflow (test, review, commit, PR)
commands:
  - feature-complete
  - fc
model: sonnet
tools:
  - Read
  - Write
  - Edit
  - Glob
  - Grep
  - Bash
---

# Feature Complete Workflow

Runs the full workflow to finalize a feature.

## Instructions

### Step 1: Verify Changes
1. Run `git status` to see all changes
2. Run `git diff` to review changes
3. Summarize what was changed and why

### Step 2: Run Tests
1. Run the project's test suite
2. If tests fail:
   - Show failing tests
   - Attempt to fix obvious issues
   - Re-run tests
   - If still failing, STOP and report

### Step 3: Code Review
1. Read all changed files
2. Check for:
   - Security issues
   - Missing error handling
   - Code style violations
3. Fix any issues found
4. Re-run tests after fixes

### Step 4: Create Commit
1. Stage all relevant files (exclude .env, node_modules)
2. Write a descriptive commit message
3. Create the commit

### Step 5: Create PR (if requested)
Ask user: "Create a pull request? (y/n)"
If yes:
1. Push branch to remote
2. Create PR with summary of all changes
3. Report PR URL

## Output
At each step, report:
- What was done
- Any issues found and fixed
- Current status (pass/fail)
```

### 연습

**과제**: 멀티 스텝 배포 스킬 구축

`~/.claude/skills/safe-deploy/SKILL.md`를 생성하세요. 다음을 수행해야 합니다:

1. **사전 점검(Pre-flight)**: 테스트, 린트, git 상태 확인
2. **빌드(Build)**: 프로젝트 컴파일
3. **검증(Validate)**: 빌드 출력 확인
4. **확인(Confirm)**: 사용자에게 진행 여부 확인
5. **배포(Deploy)**: 배포 명령 실행
6. **검증(Verify)**: 배포 상태 확인
7. **보고(Report)**: 수행된 작업 요약

간단한 Node.js 프로젝트로 테스트해 보세요.

### 확인사항

다음 단계로 넘어가기 전에 다음을 할 수 있는지 확인하세요:
- [ ] 멀티 스텝 스킬 워크플로 설계하기
- [ ] 조건부 로직 구현하기
- [ ] 안전을 위한 중지 조건 추가하기
- [ ] 대화형 결정 포인트 만들기
- [ ] 작업을 연결하기

---

## 레슨 41: 스킬 구성과 재사용

### 학습 목표
- 작은 스킬들로 스킬 구성하기
- 스킬 라이브러리 만들기
- 공통 패턴 재사용하기
- 개인 스킬 컬렉션 구축하기

### 내용

#### 스킬 구성(Composing Skills)

큰 워크플로는 다른 스킬을 참조할 수 있습니다:

```markdown
# Release Workflow

## Instructions

### Step 1: Quality Gate
Perform the checks from the `/code-review` skill on all
changed files. If any critical issues are found, STOP.

### Step 2: Test Suite
Run the full test suite. Apply the same standards as
the `/test-coverage` skill (minimum 80% coverage).

### Step 3: Create Release
Follow the commit standards from our project's CLAUDE.md.
```

#### 스킬 라이브러리 구축

카테고리별로 스킬을 정리하세요:

```
~/.claude/skills/
├── review/
│   ├── code-review/SKILL.md
│   ├── security-review/SKILL.md
│   └── perf-review/SKILL.md
├── generate/
│   ├── component/SKILL.md
│   ├── api-endpoint/SKILL.md
│   └── test-suite/SKILL.md
├── workflow/
│   ├── feature-complete/SKILL.md
│   ├── deploy/SKILL.md
│   └── release/SKILL.md
└── utility/
    ├── format-check/SKILL.md
    ├── dependency-audit/SKILL.md
    └── cleanup/SKILL.md
```

#### 공통 스킬 패턴

**분석기 패턴(Analyzer Pattern)**:
```markdown
1. Discover files (Glob)
2. Read and analyze (Read, Grep)
3. Generate report (structured output)
```
용도: 코드 리뷰, 감사, 문서 점검

**생성기 패턴(Generator Pattern)**:
```markdown
1. Gather requirements (from args or asking)
2. Read existing patterns (Read)
3. Generate code (Write)
4. Validate (Bash: run tests)
```
용도: 컴포넌트 생성, API 스캐폴딩, 테스트 생성

**워크플로 패턴(Workflow Pattern)**:
```markdown
1. Pre-checks (validate state)
2. Execute steps (sequential operations)
3. Verify (run tests/checks)
4. Report (summarize results)
```
용도: 배포, 릴리스, 마이그레이션

#### 재사용 가능한 지침 블록

공통 지침 스니펫을 만드세요:

**에러 처리 블록**:
```markdown
## Error Handling
If any step fails:
1. Log the error with context
2. Attempt recovery if possible
3. If recovery fails, STOP and report:
   - Which step failed
   - The error message
   - Suggested manual fix
```

**출력 형식 블록**:
```markdown
## Output Format
Present results as:

### Summary
- Total items checked: [N]
- Issues found: [N]
- Auto-fixed: [N]

### Details
| # | Severity | File | Issue | Status |
|---|----------|------|-------|--------|
| 1 | Critical | path | desc  | Fixed  |
```

### 연습

**과제**: 개인 스킬 라이브러리 구축

다양한 패턴에서 최소 3개의 스킬을 만드세요:

1. **분석기 스킬**: 코드 복잡도 검사기
   - 50줄 이상인 함수 찾기
   - 순환 복잡도(cyclomatic complexity) 보고
   - 리팩토링 제안

2. **생성기 스킬**: 테스트 파일 생성기
   - 소스 파일 읽기
   - 포괄적인 테스트 파일 생성
   - 엣지 케이스 포함

3. **워크플로 스킬**: 아침 스탠드업 준비
   - 어제의 커밋 표시
   - 열려 있는 PR 목록
   - 현재 브랜치 상태 표시
   - 스탠드업 보고서 형식으로 포맷

### 확인사항

다음 단계로 넘어가기 전에 다음을 할 수 있는지 확인하세요:
- [ ] 다른 스킬로부터 스킬 구성하기
- [ ] 스킬 라이브러리 정리하기
- [ ] 공통 스킬 패턴 인식하기
- [ ] 재사용 가능한 지침 블록 만들기
- [ ] 여러 카테고리에 걸쳐 스킬 구축하기

---

## 레슨 42: 스킬 성능과 모범 사례

### 학습 목표
- 스킬 성능 최적화하기
- 프로덕션 스킬의 모범 사례 따르기
- 엣지 케이스를 우아하게 처리하기
- 유지보수 가능한 스킬 지침 만들기

### 내용

#### 성능 최적화

**도구 호출 최소화**:
```markdown
# 나쁜 예: 여러 번의 작은 읽기
1. Read file1.js
2. Read file2.js
3. Read file3.js
4. Read file4.js

# 좋은 예: 병렬 읽기
1. Read all files in src/ in parallel:
   - file1.js, file2.js, file3.js, file4.js
```

**대상 지정 검색 사용**:
```markdown
# 나쁜 예: 모든 것을 읽기
1. Read every file in the project

# 좋은 예: 검색 후 읽기
1. Grep for "TODO" in src/
2. Read only the files with matches
```

**적절한 모델 선택**:
```markdown
# 간단하고 빠른 작업에는 Haiku 사용
model: haiku  # Formatting, simple checks

# 균형 잡힌 작업에는 Sonnet 사용
model: sonnet  # Reviews, generation

# 필요한 경우에만 Opus 사용
model: opus  # Complex architecture analysis
```

#### 모범 사례

**1. 명확하고 구체적인 지침**:
```markdown
# 나쁜 예
Review the code and find problems.

# 좋은 예
Review the code for these specific issues:
1. SQL injection: Look for string concatenation in queries
2. XSS: Look for innerHTML or dangerouslySetInnerHTML
3. Auth bypass: Look for missing authentication checks
4. Secrets: Look for hardcoded passwords or API keys

For each issue found, provide:
- Severity (Critical/High/Medium/Low)
- Exact file and line number
- Code snippet showing the problem
- Recommended fix with code example
```

**2. 우아한 에러 처리**:
```markdown
## Error Handling

If a file cannot be read:
→ Skip it and note in the report

If no files match the search:
→ Report "No matching files found" and suggest
  checking the path

If tests fail to run:
→ Report the error and suggest checking dependencies
```

**3. 일관된 출력**:
```markdown
## Output Format

ALWAYS use this exact format:

## [Skill Name] Report

**Date**: [current date]
**Scope**: [files analyzed]

### Summary
[1-2 sentences]

### Findings
[Numbered list]

### Recommendations
[Prioritized action items]
```

**4. 버전 관리 및 문서화**:
```yaml
---
name: my-skill
version: 1.2.0
description: Clear description of what this skill does
author: your-name
---
```

#### 엣지 케이스 처리

**입력 없음**:
```markdown
If no file or directory is specified:
→ Use the current working directory
→ Analyze all relevant files (*.js, *.ts, *.py)
```

**대규모 프로젝트**:
```markdown
If more than 50 files match:
→ Focus on files modified in the last 30 days
→ Prioritize files in src/ over tests/
→ Report that a subset was analyzed
```

**결과 없음**:
```markdown
If no issues are found:
→ Report "No issues found"
→ Confirm what was checked
→ Suggest areas for manual review
```

#### 스킬 유지보수

**스킬을 업데이트해야 할 때**:
- 팀 표준이 변경될 때
- 새로운 검사 패턴이 필요할 때
- 사용자가 오탐/미탐을 보고할 때
- 프레임워크 버전이 업데이트될 때
- 새로운 도구를 사용할 수 있게 될 때

**지원 중단(Deprecation)**:
```markdown
---
name: old-review
deprecated: true
replacement: new-review
---

이 스킬은 지원이 중단되었습니다. `/new-review`를 대신 사용하세요.
```

### 연습

**과제**: 스킬 최적화 및 다듬기

지금까지 만든 스킬 중 하나를 선택하여 다음을 수행하세요:

1. **벤치마크**: 몇 번의 도구 호출을 하나요? 줄일 수 있나요?
2. **엣지 케이스**: 빈 파일, 누락된 파일, 큰 파일로 테스트
3. **출력 품질**: 출력이 일관되게 포맷되고 있나요?
4. **문서화**: 사용법이 명확한가요? 예제가 포함되어 있나요?
5. **에러 처리**: 문제가 발생하면 어떻게 되나요?

**최적화 체크리스트**:
- [ ] 가능한 곳에서 병렬 읽기
- [ ] 광범위한 읽기 전 대상 지정 검색
- [ ] 적절한 모델 선택
- [ ] 명확한 에러 메시지
- [ ] 일관된 출력 형식
- [ ] 버전 번호와 변경 이력
- [ ] 사용법 문서

### 확인사항

다음 단계로 넘어가기 전에 다음을 할 수 있는지 확인하세요:
- [ ] 스킬 성능 최적화하기
- [ ] 엣지 케이스를 우아하게 처리하기
- [ ] 프로덕션 수준의 스킬 지침 작성하기
- [ ] 스킬 유지보수 및 버전 관리하기
- [ ] 스킬 모범 사례 체크리스트 따르기

---

## 섹션 7 요약

스킬(Skill) 시스템을 마스터했습니다:

| 레슨 | 주제 | 핵심 요점 |
|------|------|-----------|
| 33 | 스킬이란 무엇인가? | 재사용 가능한 전문 지식 패키지 |
| 34 | 기존 스킬 사용하기 | /command 구문과 자동 호출 |
| 35 | 첫 번째 스킬 만들기 | SKILL.md 구조와 지침 |
| 36 | 고급 설정 | 프론트매터, 도구, 모델, 트리거 |
| 37 | 템플릿과 보조 파일 | 템플릿을 사용한 멀티 파일 스킬 |
| 38 | 테스트와 디버깅 | 체계적인 테스트와 반복 개선 |
| 39 | 스킬 공유하기 | 프로젝트 레벨 스킬과 팀 표준 |
| 40 | 멀티 스텝 워크플로 | 복잡한 순차적 작업 |
| 41 | 구성과 재사용 | 스킬 라이브러리와 패턴 |
| 42 | 성능과 모범 사례 | 프로덕션 수준의 스킬 |

### 다음 단계

**섹션 8: 훅 시스템(Hooks System)**에서는 훅(Hook)을 사용하여 도구 호출, 파일 저장, 커밋 등의 이벤트에 응답하여 자동으로 실행되는 동작을 자동화하는 방법을 배우게 됩니다.
