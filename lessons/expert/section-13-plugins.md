# 섹션 13: 플러그인 (레슨 81-86)

## 개요
플러그인 시스템을 통해 Claude Code를 확장하는 방법을 배웁니다. 스킬, 서브에이전트, 훅, MCP 서버를 재사용 가능하고 공유 가능한 단위로 패키징하는 플러그인을 구축, 배포, 관리합니다.

---

## 레슨 81: 플러그인 아키텍처

### 학습 목표
- 네 가지 핵심 플러그인 구성 요소를 이해합니다: 스킬, 서브에이전트, 훅, MCP 서버
- 표준 플러그인 디렉토리 구조를 배웁니다
- plugin.json 매니페스트 형식을 마스터합니다
- 플러그인이 Claude Code 기능을 확장하는 방법을 설명합니다

### 내용

#### 플러그인이란 무엇인가?

플러그인은 새로운 기능으로 Claude Code를 확장하는 자체 완결형 패키지입니다. 스킬, 훅, 서브에이전트, MCP 서버를 개별적으로 구성하는 대신, 플러그인은 이들을 메타데이터, 문서, 버전 관리와 함께 묶어서 단일 단위로 설치, 업데이트, 공유할 수 있게 합니다.

#### 네 가지 플러그인 구성 요소

| 구성 요소 | 용도 | 예시 |
|-----------|------|------|
| **스킬** | 특정 작업을 수행하는 슬래시 명령 | `/deploy`, `/lint-fix`, `/db-migrate` |
| **서브에이전트** | 도메인별 작업을 위한 전문 에이전트 | 보안 감사자, API 디자이너 |
| **훅** | Claude 동작에 의해 트리거되는 이벤트 기반 자동화 | 파일 저장 시 자동 포맷, 오류 시 알림 |
| **MCP 서버** | Model Context Protocol을 통한 외부 도구 통합 | 데이터베이스 접근, CI/CD 제어, 모니터링 |

#### 플러그인 디렉토리 구조

모든 플러그인은 표준 레이아웃을 따릅니다:

```
my-plugin/
├── skills/
│   ├── deploy.md
│   └── lint-fix.md
├── subagents/
│   └── security-auditor.md
├── hooks/
│   └── hooks.json
├── mcp/
│   └── mcp-config.json
├── README.md
└── plugin.json
```

각 디렉토리는 선택 사항입니다. 플러그인은 스킬만 제공하거나, 훅만 제공하거나, 네 가지 구성 요소의 어떤 조합이든 제공할 수 있습니다.

#### plugin.json 매니페스트

매니페스트 파일은 플러그인을 설명하고 내용을 선언합니다:

```json
{
  "name": "devops-toolkit",
  "version": "1.2.0",
  "description": "DevOps automation skills, hooks, and integrations for Claude Code",
  "author": "Jane Smith",
  "license": "MIT",
  "repository": "https://github.com/janesmith/devops-toolkit",
  "keywords": ["devops", "deploy", "ci-cd", "docker"],
  "claude_code_version": ">=1.0.0",
  "components": {
    "skills": [
      "skills/deploy.md",
      "skills/lint-fix.md"
    ],
    "subagents": [
      "subagents/security-auditor.md"
    ],
    "hooks": "hooks/hooks.json",
    "mcp": "mcp/mcp-config.json"
  },
  "config": {
    "deploy_target": {
      "type": "string",
      "description": "Default deployment target",
      "default": "staging"
    }
  },
  "dependencies": {
    "docker": ">=20.0.0",
    "node": ">=18.0.0"
  }
}
```

#### 주요 매니페스트 필드

| 필드 | 필수 | 설명 |
|------|------|------|
| `name` | 예 | 고유 플러그인 식별자 (소문자, 하이픈) |
| `version` | 예 | Semver 버전 문자열 |
| `description` | 예 | 플러그인의 한 줄 요약 |
| `components` | 예 | 구성 요소 유형에서 파일 경로로의 맵 |
| `author` | 아니오 | 플러그인 작성자 이름 또는 조직 |
| `claude_code_version` | 아니오 | 최소 호환 Claude Code 버전 |
| `config` | 아니오 | 기본값이 있는 사용자 구성 가능 설정 |
| `dependencies` | 아니오 | 외부 도구 요구 사항 |

#### 플러그인이 Claude Code를 확장하는 방법

플러그인이 설치되면 Claude Code는:

1. 스킬 파일을 활성 스킬 디렉토리에 복사합니다
2. 에이전트 시스템에서 사용할 서브에이전트 정의를 등록합니다
3. 훅 구성을 프로젝트 또는 사용자 설정에 병합합니다
4. MCP 서버 항목을 MCP 구성에 추가합니다

모든 구성 요소는 수동으로 구성한 것처럼 즉시 사용 가능해집니다.

### 연습

**과제**: 플러그인을 검토하고 스케치하기

1. 자주 반복하는 워크플로우를 선택합니다 (예: 데이터베이스 마이그레이션, 테스트 스캐폴딩)
2. 필요한 구성 요소를 식별합니다 (스킬, 훅, 서브에이전트, MCP 서버)
3. 디렉토리 구조를 종이에 또는 스크래치 파일에 작성합니다
4. 모든 필수 필드가 포함된 `plugin.json` 매니페스트를 초안합니다

### 확인사항
- [ ] 네 가지 플러그인 구성 요소 유형을 모두 명명할 수 있음
- [ ] 디렉토리 구조가 표준 레이아웃을 따름
- [ ] plugin.json에 name, version, description, components가 포함됨
- [ ] 각 구성 요소가 Claude Code에 통합되는 방법을 이해함

---

## 레슨 82: 플러그인 만들기

### 학습 목표
- 표준 구조를 사용하여 처음부터 플러그인을 만듭니다
- 플러그인을 위한 스킬, 훅, 서브에이전트를 구축합니다
- 필요할 때 MCP 서버 구성을 추가합니다
- 배포 전에 플러그인을 로컬에서 테스트합니다

### 내용

#### 1단계: 플러그인 범위 설계

파일을 작성하기 전에 플러그인이 할 일을 정의합니다:

```markdown
Plugin: test-helper
Purpose: Streamline test creation and execution
Components needed:
  - Skill: /generate-test (creates test files from source)
  - Skill: /run-tests (executes tests with coverage)
  - Hook: auto-run tests when test files change
  - Subagent: test-reviewer (analyzes test quality)
```

범위를 집중적으로 유지하세요. 한 가지를 잘하는 플러그인이 많은 것을 대충 하는 플러그인보다 더 유용합니다.

#### 2단계: 디렉토리 구조 생성

```bash
mkdir -p test-helper/{skills,subagents,hooks}
touch test-helper/plugin.json
touch test-helper/README.md
```

#### 3단계: 스킬 구축

`skills/generate-test.md`를 생성합니다:

```markdown
# Generate Test

Create a comprehensive test file for a given source file.

## Usage
/generate-test <source-file>

## Instructions
1. Read the source file provided as an argument
2. Identify all exported functions, classes, and methods
3. Generate test cases covering:
   - Happy path for each function
   - Edge cases (null, undefined, empty values)
   - Error conditions
4. Use the project's existing test framework (detect from package.json)
5. Place the test file alongside the source with `.test` suffix
6. Run the tests to verify they pass

## Output
- Created test file path
- Number of test cases generated
- Test execution results
```

`skills/run-tests.md`를 생성합니다:

```markdown
# Run Tests

Execute tests with coverage reporting and failure analysis.

## Usage
/run-tests [path] [--coverage] [--watch]

## Instructions
1. Detect the test framework from project configuration
2. Run tests for the specified path, or all tests if none given
3. If --coverage flag is present, include coverage reporting
4. On failure, analyze the failing tests and suggest fixes
5. Summarize results with pass/fail counts

## Output Format
- Total tests: X
- Passed: X
- Failed: X
- Coverage: X% (if requested)
- Failure analysis (if any failures)
```

#### 4단계: 훅 구축

`hooks/hooks.json`을 생성합니다:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "EditFile|CreateFile",
        "pattern": "\\.(test|spec)\\.(ts|js|tsx|jsx)$",
        "command": "npm test -- --bail --findRelatedTests $MATCHED_FILE",
        "description": "Auto-run related tests when test files are modified"
      }
    ]
  }
}
```

#### 5단계: 서브에이전트 구축

`subagents/test-reviewer.md`를 생성합니다:

```markdown
# Test Reviewer

You are a test quality reviewer. When invoked, analyze test files for:

## Review Criteria
1. **Coverage completeness** - Are all code paths tested?
2. **Assertion quality** - Are assertions specific and meaningful?
3. **Test isolation** - Do tests depend on each other?
4. **Naming clarity** - Do test names describe the expected behavior?
5. **Edge cases** - Are boundary conditions covered?

## Output Format
Provide a quality score (1-10) and specific recommendations for improvement.
Prioritize recommendations by impact.
```

#### 6단계: 매니페스트 작성

`plugin.json`을 생성합니다:

```json
{
  "name": "test-helper",
  "version": "1.0.0",
  "description": "Streamline test creation, execution, and quality review",
  "author": "Your Name",
  "license": "MIT",
  "keywords": ["testing", "test-generation", "coverage"],
  "claude_code_version": ">=1.0.0",
  "components": {
    "skills": [
      "skills/generate-test.md",
      "skills/run-tests.md"
    ],
    "subagents": [
      "subagents/test-reviewer.md"
    ],
    "hooks": "hooks/hooks.json"
  },
  "dependencies": {
    "node": ">=18.0.0"
  }
}
```

#### 7단계: 로컬 테스트

플러그인을 자신의 프로젝트에 설치하여 작동하는지 확인합니다:

```bash
# 플러그인을 Claude Code 플러그인 디렉토리에 복사
cp -r test-helper/ ~/.claude/plugins/test-helper/

# 또는 활발한 개발을 위해 심볼릭 링크
ln -s $(pwd)/test-helper ~/.claude/plugins/test-helper

# Claude를 시작하고 각 스킬을 테스트
claude
> /generate-test src/utils.ts
> /run-tests --coverage
```

각 구성 요소를 확인합니다:
- 스킬이 나타나고 올바르게 기능함
- 훅이 예상된 이벤트에서 트리거됨
- 서브에이전트가 유용한 출력을 생성함

### 연습

**과제**: 완전한 플러그인 구축하기

1. 일상적인 개발 루틴에서 워크플로우를 선택합니다
2. 최소 두 개의 스킬이 포함된 전체 디렉토리 구조를 만듭니다
3. 최소 하나의 훅 또는 서브에이전트를 추가합니다
4. plugin.json 매니페스트를 작성합니다
5. 로컬에 설치하고 모든 구성 요소를 테스트합니다

### 확인사항
- [ ] 플러그인 디렉토리가 표준 구조를 따름
- [ ] 최소 두 개의 스킬이 생성되고 기능함
- [ ] plugin.json이 유효하고 완전함
- [ ] 플러그인이 로컬에서 설치되고 작동함
- [ ] 모든 구성 요소가 개별적으로 테스트됨

---

## 레슨 83: 플러그인 배포

### 학습 목표
- 배포를 위해 플러그인을 패키징합니다
- 레지스트리 또는 GitHub에 게시합니다
- 사용자를 위한 종합적인 문서를 작성합니다
- 버전 관리 전략을 수립합니다

### 내용

#### 플러그인 패키징

배포 전에 플러그인을 검증하고 정리합니다:

```bash
# plugin.json 검증
cat test-helper/plugin.json | python -m json.tool

# .pluginignore 파일을 생성합니다
cat > test-helper/.pluginignore << 'EOF'
node_modules/
.DS_Store
*.log
.env
test-output/
EOF
```

#### GitHub에 게시

GitHub은 가장 간단한 배포 채널입니다:

```bash
cd test-helper
git init
git add .
git commit -m "Initial release v1.0.0"
git tag v1.0.0
git remote add origin https://github.com/yourname/claude-plugin-test-helper.git
git push origin main --tags
```

GitHub Releases를 사용하여 릴리스 노트와 변경 로그를 제공합니다:

```bash
gh release create v1.0.0 \
  --title "test-helper v1.0.0" \
  --notes "Initial release with generate-test and run-tests skills"
```

#### 종합적인 README 작성

README는 사용자를 위한 주요 문서입니다. 다음 섹션을 포함하세요:

```markdown
# test-helper

Streamline test creation, execution, and quality review in Claude Code.

## Installation

    claude plugin install github:yourname/claude-plugin-test-helper

## Features

- **/generate-test** - Auto-generate test files from source code
- **/run-tests** - Execute tests with coverage and failure analysis
- **Auto-run hook** - Tests execute automatically when test files change
- **Test reviewer** - AI-powered test quality analysis

## Requirements

- Node.js >= 18.0.0
- A supported test framework (Jest, Vitest, Mocha)

## Configuration

Add to your project's `.claude/settings.json`:

    {
      "plugins": {
        "test-helper": {
          "framework": "jest",
          "coverageThreshold": 80
        }
      }
    }

## Usage Examples

### Generate tests for a file
    /generate-test src/utils/parser.ts

### Run all tests with coverage
    /run-tests --coverage

### Run tests for a specific directory
    /run-tests src/api/

## Changelog

### 1.0.0
- Initial release
- Skills: generate-test, run-tests
- Hook: auto-run on test file changes
- Subagent: test-reviewer
```

#### 설치 안내

여러 가지 설치 방법을 제공합니다:

```bash
# GitHub에서
claude plugin install github:yourname/claude-plugin-test-helper

# 로컬 디렉토리에서
claude plugin install ./path/to/test-helper

# 특정 버전에서
claude plugin install github:yourname/claude-plugin-test-helper@v1.2.0
```

#### 버전 관리 전략

시맨틱 버전 관리(semver)를 따릅니다:

| 변경 유형 | 버전 범프 | 예시 |
|-----------|----------|------|
| 버그 수정, 사소한 조정 | 패치: x.x.X | 1.0.0 -> 1.0.1 |
| 새 스킬 또는 기능 | 마이너: x.X.0 | 1.0.1 -> 1.1.0 |
| 기존 스킬의 호환성 파괴 변경 | 메이저: X.0.0 | 1.1.0 -> 2.0.0 |

Keep a Changelog 형식을 따르는 CHANGELOG.md를 유지합니다:

```markdown
## [1.1.0] - 2025-06-15
### Added
- New /test-coverage skill for detailed coverage reports
### Changed
- generate-test now detects framework automatically
### Fixed
- Hook pattern now matches .mjs and .cjs files
```

### 연습

**과제**: 플러그인을 패키징하고 게시하기

1. plugin.json과 모든 구성 요소 파일을 검증합니다
2. 설치 및 사용 안내가 포함된 종합적인 README를 작성합니다
3. git 리포지토리를 초기화하고 태그된 릴리스를 만듭니다
4. 릴리스 노트가 포함된 GitHub 릴리스를 만듭니다
5. GitHub에서 설치 프로세스를 테스트합니다

### 확인사항
- [ ] plugin.json이 검증을 통과함
- [ ] README가 설치, 기능, 구성, 사용법을 다룸
- [ ] Git 리포지토리에 태그된 릴리스가 있음
- [ ] 게시된 소스에서 플러그인이 성공적으로 설치됨
- [ ] CHANGELOG.md가 초기 릴리스를 문서화함

---

## 레슨 84: 플러그인 마켓플레이스

### 학습 목표
- 커뮤니티 채널을 통해 플러그인을 발견합니다
- 마켓플레이스 또는 레지스트리에서 플러그인을 설치합니다
- 평점과 리뷰를 통해 플러그인 품질을 평가합니다
- 플러그인 커뮤니티에 기여합니다

### 내용

#### 플러그인 발견

여러 채널을 통해 플러그인을 찾습니다:

```bash
# 키워드로 플러그인 검색
claude plugin search "docker"

# 인기 플러그인 탐색
claude plugin list --sort=popular

# 카테고리별 플러그인 목록
claude plugin list --category=devops
```

플러그인을 찾기 위한 커뮤니티 리소스:
- 공식 Claude Code 플러그인 레지스트리
- GitHub 토픽: `claude-code-plugin`
- 커뮤니티 포럼 및 Discord 채널
- 블로그 게시물 및 큐레이션된 목록

#### 마켓플레이스에서 설치

```bash
# 플러그인 이름으로 설치 (레지스트리에서)
claude plugin install devops-toolkit

# 특정 버전 설치
claude plugin install devops-toolkit@2.1.0

# GitHub에서 직접 설치
claude plugin install github:author/plugin-name

# 설치 전 플러그인 세부 정보 보기
claude plugin info devops-toolkit
```

#### 플러그인 품질 평가

플러그인을 설치하기 전에 다음 품질 신호를 확인합니다:

| 신호 | 확인할 사항 |
|------|------------|
| **버전** | >= 1.0.0은 안정성을 나타냄 |
| **최종 업데이트** | 활발한 유지보수 (6개월 이내 업데이트) |
| **문서** | 예시가 포함된 완전한 README |
| **라이선스** | 오픈 소스 라이선스 존재 |
| **의존성** | 최소한의 외부 요구 사항 |
| **스타/다운로드** | 커뮤니티 채택 정도 |
| **이슈** | 반응적인 유지보수자, 낮은 오픈 버그 수 |

#### 플러그인 평가 및 리뷰

플러그인을 사용한 후 커뮤니티에 기여합니다:

```bash
# 플러그인 평가 (1-5 별)
claude plugin rate devops-toolkit 5

# 리뷰 작성
claude plugin review devops-toolkit --body "Excellent deploy skill.
Saved hours of manual work. The auto-rollback hook is especially useful."
```

다음을 언급하는 유용한 리뷰를 작성합니다:
- 사용한 기능과 사용 방법
- 설정 중 겪은 어려움
- 플러그인이 워크플로우를 개선한 방법
- 개선 제안

#### 커뮤니티 기여 지침

플러그인 생태계에 기여하기:

1. **버그 보고** - 재현 단계가 포함된 이슈 제출
2. **개선 사항 제출** - 테스트가 포함된 PR 열기
3. **구성 공유** - 유용한 구성 스니펫 게시
4. **튜토리얼 작성** - 다른 사람들의 플러그인 채택 돕기
5. **품질 유지** - 자신의 플러그인에서 표준 따르기

### 연습

**과제**: 커뮤니티 플러그인 탐색 및 설치하기

1. 자신의 기술 스택에 관련된 플러그인을 검색합니다
2. 품질 신호 표를 사용하여 최소 세 개의 플러그인을 평가합니다
3. 하나의 플러그인을 설치하고 기능을 테스트합니다
4. 테스트한 플러그인에 대한 리뷰를 작성합니다
5. 마켓플레이스의 공백을 식별하고 플러그인 아이디어를 기록합니다

### 확인사항
- [ ] 마켓플레이스를 검색하고 관련 플러그인을 찾음
- [ ] 품질 기준을 사용하여 플러그인을 평가함
- [ ] 플러그인을 성공적으로 설치하고 테스트함
- [ ] 건설적인 리뷰를 작성함
- [ ] 구축할 잠재적 새 플러그인을 식별함

---

## 레슨 85: 엔터프라이즈 플러그인 관리

### 학습 목표
- 조직을 위한 승인된 플러그인 목록을 만들고 관리합니다
- 엔터프라이즈 플러그인 배포 채널을 설정합니다
- 플러그인에 대한 보안 검토 프로세스를 구현합니다
- 플러그인 거버넌스 정책을 수립합니다

### 내용

#### 승인된 플러그인 목록

엔터프라이즈는 개발자가 사용할 수 있는 플러그인을 제어해야 합니다. 조직 수준 설정에서 허용 목록을 정의합니다:

```json
{
  "plugins": {
    "policy": "allowlist",
    "approved": [
      "devops-toolkit@>=2.0.0",
      "test-helper@^1.2.0",
      "security-scanner@latest",
      "internal:code-standards"
    ],
    "blocked": [
      "untrusted-plugin",
      "deprecated-tool"
    ],
    "requireApproval": true,
    "approvalContact": "platform-team@company.com"
  }
}
```

#### 엔터프라이즈 플러그인 배포

비공개 채널을 통해 내부 플러그인을 호스팅합니다:

```bash
# 비공개 GitHub 조직에서 설치
claude plugin install github:mycompany/internal-standards --token $GH_TOKEN

# 내부 레지스트리에서 설치
claude plugin install registry:internal.company.com/code-standards

# 공유 네트워크 경로에서 설치
claude plugin install file:///shared/plugins/code-standards
```

내부 플러그인 레지스트리를 설정합니다:

```json
{
  "pluginRegistries": [
    {
      "name": "internal",
      "url": "https://plugins.internal.company.com",
      "auth": "bearer",
      "priority": 1
    },
    {
      "name": "public",
      "url": "https://plugins.claude.ai",
      "priority": 2
    }
  ]
}
```

#### 보안 검토 프로세스

플러그인을 승인하기 전에 공식 검토 프로세스를 수립합니다:

```
1단계: 제출
  개발자가 내부 도구 또는 PR을 통해 플러그인을 검토에 제출

2단계: 자동화된 스캔
  - 모든 플러그인 파일의 정적 분석
  - 하드코딩된 시크릿 또는 자격 증명 확인
  - 훅에서 네트워크 호출 확인 (예상되지 않는 경우)
  - plugin.json 스키마 검증

3단계: 수동 검토
  - 보안 팀에 의한 코드 리뷰
  - MCP 서버 권한이 최소한인지 확인
  - 훅 명령의 인젝션 위험 확인
  - 서브에이전트를 통한 데이터 노출 평가

4단계: 테스트
  - 샌드박스 환경에서 설치
  - 모든 스킬 실행 및 동작 확인
  - 네트워크 및 파일 시스템 접근 모니터링
  - 테스트 프로젝트에 대해 검증

5단계: 승인
  - 보안 팀의 승인
  - 버전 고정으로 승인된 목록에 추가
  - 사용 제한 사항 문서화
```

보안 검토 체크리스트:

| 확인 항목 | 위험도 | 확인할 사항 |
|-----------|--------|------------|
| 훅 명령 | 높음 | 쉘 인젝션, 데이터 유출 |
| MCP 서버 접근 | 높음 | 과도한 권한, 외부 호출 |
| 스킬 지침 | 중간 | 프롬프트 인젝션, 안전하지 않은 작업 |
| 의존성 | 중간 | 알려진 취약점, 유지보수 안 됨 |
| 데이터 처리 | 중간 | 민감한 정보 로깅, 임시 파일 |
| 네트워크 접근 | 높음 | 예상치 못한 아웃바운드 연결 |

#### 플러그인 거버넌스 정책

조직의 플러그인 정책을 문서화하고 시행합니다:

```markdown
# 플러그인 거버넌스 정책

## 설치
- 회사 머신에는 승인된 플러그인만 설치 가능
- 개발자는 플랫폼 팀을 통해 새 플러그인을 요청 가능
- 모든 플러그인은 승인 전 보안 검토를 통과해야 함

## 업데이트
- 플러그인 업데이트는 릴리스 후 5영업일 이내에 검토
- 중요 보안 패치는 24시간 이내 긴급 처리
- 메이저 버전 업그레이드는 재검토 필요

## 내부 플러그인
- 회사 코딩 표준을 따라야 함
- 문서와 테스트가 필요
- 온콜 지원이 있는 특정 팀이 소유
- 지속적인 관련성을 위해 분기별 검토

## 인시던트 대응
- 플러그인 관련 인시던트는 플랫폼 팀으로 에스컬레이션
- 침해된 플러그인은 승인된 목록에서 즉시 제거
- 인시던트 후 48시간 이내 사후 검토
```

### 연습

**과제**: 엔터프라이즈 플러그인 관리 전략 설계하기

1. 팀 또는 조직을 위한 승인된 플러그인 목록을 초안합니다
2. 최소 8개 항목이 포함된 보안 검토 체크리스트를 정의합니다
3. 설치, 업데이트, 인시던트를 다루는 플러그인 거버넌스 정책을 작성합니다
4. 내부 플러그인 배포 메커니즘을 설정합니다
5. 새 플러그인 승인을 요청하는 프로세스를 문서화합니다

### 확인사항
- [ ] 버전 제약이 있는 승인된 플러그인 목록이 생성됨
- [ ] 보안 검토 체크리스트가 철저하고 실행 가능함
- [ ] 거버넌스 정책이 전체 플러그인 생명주기를 다룸
- [ ] 내부 배포 채널이 구성됨
- [ ] 승인 요청 프로세스가 문서화됨

---

## 레슨 86: 플러그인 모범 사례

### 학습 목표
- 플러그인을 구축할 때 검증된 설계 패턴을 적용합니다
- 버전 관리 및 하위 호환성 전략을 구현합니다
- 플러그인 구성 요소에 대한 테스트 전략을 개발합니다
- 문서 표준을 따릅니다
- 일반적인 함정을 피합니다

### 내용

#### 플러그인 설계 패턴

**단일 책임**: 각 플러그인은 하나의 명확한 목적을 가져야 합니다.

```
좋음:  "docker-deploy" - Docker 배포를 관리
나쁨:  "everything-tool" - 배포, 테스트, 린트, 데이터베이스를 모두 관리
```

**점진적 공개**: 간단한 기본값으로 시작하고 고급 구성을 허용합니다.

```json
{
  "config": {
    "target": {
      "type": "string",
      "default": "staging",
      "description": "Deployment target (staging, production)"
    },
    "advanced": {
      "timeout": {
        "type": "number",
        "default": 300,
        "description": "Deployment timeout in seconds"
      },
      "rollbackOnFailure": {
        "type": "boolean",
        "default": true,
        "description": "Auto-rollback on deployment failure"
      }
    }
  }
}
```

**우아한 성능 저하**: 누락된 의존성을 충돌 없이 처리합니다.

```markdown
## Skill: /deploy

### Instructions
1. Check if Docker is installed by running `docker --version`
2. If Docker is not available:
   - Inform the user that Docker is required
   - Provide installation instructions for their platform
   - Exit gracefully without errors
3. If Docker is available, proceed with deployment...
```

**조합 가능성**: 다른 플러그인과 잘 작동하도록 플러그인을 설계합니다.

```markdown
## Skill: /generate-api-types

### Instructions
- Output TypeScript types to a standard location (src/types/generated/)
- Use standard naming conventions so other plugins can discover the output
- Do not modify files owned by other plugins or tools
```

#### 버전 관리 및 하위 호환성

업데이트를 릴리스할 때 다음 규칙을 따릅니다:

**패치 릴리스 (1.0.x)** - 사용자 조치 불필요:
```
- 스킬 지침의 오타 수정
- 훅 정규식 패턴 수정
- 문서 업데이트
```

**마이너 릴리스 (1.x.0)** - 새 기능, 완전한 하위 호환:
```
- 플러그인에 새 스킬 추가
- 기본값이 있는 선택적 구성 필드 추가
- 훅에서 추가 파일 패턴 지원
```

**메이저 릴리스 (x.0.0)** - 호환성 파괴 변경, 마이그레이션 필요:
```
- 기존 스킬 이름 변경 또는 제거
- 필수 구성 필드 변경
- 사용자가 의존하는 훅 동작 변경
```

메이저 릴리스에 대한 마이그레이션 가이드를 제공합니다:

```markdown
# Migrating from v1.x to v2.0

## Breaking Changes

### Skill renamed: /deploy -> /deploy-app
Update any scripts or documentation referencing the old name.

### Configuration change: `target` moved under `deploy` key
Before:
    { "target": "staging" }
After:
    { "deploy": { "target": "staging" } }
```

#### 테스트 전략

**각 구성 요소 유형을 독립적으로 테스트합니다:**

```bash
# 스킬 테스트: 수동으로 호출하고 출력을 확인
claude
> /generate-test src/utils.ts
# 확인: 테스트 파일이 생성되고, 테스트가 통과함

# 훅 테스트: 일치하는 이벤트를 트리거하고 동작을 확인
# 테스트 파일을 수정하고 훅이 실행되는지 확인

# 서브에이전트 테스트: 샘플 입력으로 호출
# 출력 품질과 형식을 확인

# MCP 서버 테스트: 연결 및 도구 가용성을 확인
claude
> Use the db-query tool to run SELECT 1
```

**플러그인을 위한 테스트 프로젝트를 만듭니다:**

```
test-helper-tests/
├── fixtures/
│   ├── sample-source.ts
│   └── expected-test-output.ts
├── test-skills.sh
├── test-hooks.sh
└── README.md
```

`test-skills.sh`:
```bash
#!/bin/bash
set -e

echo "Testing /generate-test skill..."
cd fixtures
claude --print "/generate-test sample-source.ts"

if [ -f "sample-source.test.ts" ]; then
    echo "PASS: Test file generated"
else
    echo "FAIL: Test file not found"
    exit 1
fi

echo "Running generated tests..."
npx jest sample-source.test.ts
echo "PASS: Generated tests execute successfully"
```

**회귀 테스트**: 각 릴리스 전에 모든 스킬을 실행하고 예상대로 작동하는지 확인합니다. 수동 확인 단계의 체크리스트를 유지합니다.

#### 문서 표준

모든 플러그인에 포함해야 할 항목:

| 문서 | 목적 | 위치 |
|------|------|------|
| README.md | 설치, 기능, 사용 예시 | 루트 디렉토리 |
| CHANGELOG.md | 버전 히스토리 및 마이그레이션 노트 | 루트 디렉토리 |
| 스킬 헤더 | 각 스킬의 사용법 및 동작 | 각 스킬 .md 내부 |
| 설정 문서 | 기본값이 있는 모든 구성 옵션 | README 또는 별도 문서 |
| 문제 해결 | 일반적인 문제 및 해결 방법 | README 섹션 |

스킬 문서 템플릿:

```markdown
# Skill Name

Brief one-line description.

## Usage
/skill-name <required-arg> [optional-arg] [--flag]

## Arguments
- `required-arg` - Description of what this argument does
- `optional-arg` - (Optional) Description with default value noted

## Flags
- `--flag` - What this flag controls

## Examples

### Basic usage
    /skill-name src/index.ts

### With options
    /skill-name src/index.ts --coverage

## Instructions
[Detailed instructions for Claude on how to execute the skill]
```

#### 일반적인 함정 및 회피 방법

| 함정 | 문제 | 해결 방법 |
|------|------|----------|
| 과도하게 넓은 범위 | 플러그인이 모든 것을 하려 함 | 하나의 도메인으로 제한; 여러 플러그인으로 분리 |
| 하드코딩된 경로 | 다른 OS 또는 프로젝트 레이아웃에서 깨짐 | 상대 경로 사용 및 프로젝트 구조 감지 |
| 누락된 오류 처리 | 잘못된 입력에서 스킬이 조용히 실패 | 스킬 지침에 명시적 오류 케이스 포함 |
| 버전 고정 없음 | 사용자가 예기치 않게 호환성 파괴 변경을 받음 | 의존성을 고정하고 claude_code_version 선언 |
| 과도한 권한 | MCP 서버가 필요 이상의 접근을 요청 | 최소 권한 원칙 따르기 |
| 불량한 명명 | 스킬이 내장 명령과 충돌 | 고유하고 설명적인 이름 사용; 필요시 플러그인 이름 접두사 |
| 테스트 없음 | 버그가 사용자에게 배포됨 | 릴리스 전 로컬 및 테스트 프로젝트에서 테스트 |
| 오래된 문서 | README가 현재 동작과 일치하지 않음 | 모든 릴리스의 일부로 문서 업데이트 |

### 연습

**과제**: 기존 플러그인 감사 및 개선하기

1. 구축했거나 설치한 플러그인을 선택합니다
2. 이 레슨의 각 모범 사례 카테고리에 대해 평가합니다
3. 최소 세 가지 개선 영역을 식별합니다
4. 개선 사항을 적용합니다 (더 나은 문서, 테스트, 오류 처리 등)
5. 개선 사항에 대한 CHANGELOG 항목을 작성합니다
6. semver를 따라 버전을 적절히 범프합니다

### 확인사항
- [ ] 플러그인이 단일 책임 원칙을 따름
- [ ] 버전 관리가 semver를 올바르게 사용함
- [ ] 최소 하나의 테스트 접근 방식이 구현됨
- [ ] 문서가 모든 스킬, 구성, 문제 해결을 다룸
- [ ] 일반적인 함정을 확인하고 해결함

---

## 섹션 13 요약

### 핵심 포인트

| 레슨 | 핵심 개념 | 주요 스킬 |
|------|----------|----------|
| 81 | 플러그인 아키텍처 | 네 가지 구성 요소와 plugin.json 매니페스트 이해 |
| 82 | 플러그인 만들기 | 플러그인에서 스킬, 훅, 서브에이전트, MCP 서버 구축 |
| 83 | 플러그인 배포 | 플러그인을 패키징, 버전 관리, GitHub 또는 레지스트리에 게시 |
| 84 | 플러그인 마켓플레이스 | 커뮤니티 플러그인을 발견, 평가, 설치, 리뷰 |
| 85 | 엔터프라이즈 관리 | 승인된 목록, 보안 검토, 정책으로 플러그인 거버넌스 |
| 86 | 모범 사례 | 설계 패턴, 테스트, 버전 관리, 문서 표준 적용 |

### 필수 명령어

```bash
# 플러그인 생명주기
claude plugin install <source>        # 플러그인 설치
claude plugin list                     # 설치된 플러그인 목록
claude plugin info <name>              # 플러그인 세부 정보 보기
claude plugin update <name>            # 플러그인 업데이트
claude plugin remove <name>            # 플러그인 제거

# 발견
claude plugin search <keyword>         # 플러그인 검색
claude plugin list --sort=popular      # 인기 플러그인 탐색
```

### 플러그인 품질 체크리스트

- [ ] plugin.json이 모든 필수 필드와 함께 유효함
- [ ] README가 설치, 사용법, 구성을 다룸
- [ ] CHANGELOG가 버전 히스토리를 추적함
- [ ] 모든 스킬에 명확한 지침과 예시가 있음
- [ ] 훅이 정확한 매처 패턴을 사용함
- [ ] 의존성이 선언되고 버전이 고정됨
- [ ] 보안 검토 완료 (엔터프라이즈 사용의 경우)
- [ ] 실제 프로젝트에서 로컬 테스트됨

### 다음 단계
섹션 14: 고급 워크플로우로 진행하여 병렬 개발, CI/CD 통합, 다중 리포지토리 운영을 마스터합니다.
