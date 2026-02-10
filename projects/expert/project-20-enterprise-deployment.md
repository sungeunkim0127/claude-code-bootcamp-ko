# 프로젝트 20: 엔터프라이즈 Claude Code 배포

**레벨**: 전문가
**소요 시간**: 20-40시간
**사전 조건**: 모든 이전 섹션 완료 (프로젝트 1-19, 레슨 1-100)

---

## 개요

50명 이상의 개발자로 구성된 엔지니어링 조직을 위한 엔터프라이즈급 Claude Code 배포를 설계, 구현, 롤아웃합니다. 이 캡스톤 프로젝트는 부트캠프의 모든 개념 -- 스킬, 훅, 서브에이전트, MCP 서버, CLAUDE.md, 관리 설정, CI/CD 통합 -- 을 거버넌스, 컴플라이언스, 교육, 측정 가능한 결과가 있는 프로덕션 준비 시스템으로 통합합니다.

이 프로젝트가 끝나면 회사 전체 플러그인, CI/CD 파이프라인, 거버넌스 프레임워크, 교육 자료, 지속적 개선을 위한 메트릭 대시보드가 포함된 완전히 운영 가능한 엔터프라이즈 배포를 갖게 됩니다.

---

## 아키텍처 개요

```
+-----------------------------------------------------------------------+
|                     Enterprise Claude Code Platform                    |
+-----------------------------------------------------------------------+
|                                                                        |
|  +------------------+    +-------------------+    +-----------------+  |
|  |  Managed         |    |  Enterprise       |    |  CI/CD          |  |
|  |  Settings        |    |  Plugin           |    |  Integration    |  |
|  |  (settings.json) |    |  (skills, hooks,  |    |  (GitHub        |  |
|  |                  |    |   subagents, MCP)  |    |   Actions)      |  |
|  +--------+---------+    +--------+----------+    +--------+--------+  |
|           |                       |                        |           |
|           v                       v                        v           |
|  +--------+---------+    +--------+----------+    +--------+--------+  |
|  |  Governance       |    |  Developer        |    |  Automation     |  |
|  |  - Permissions    |    |  Workstations     |    |  - Code Review  |  |
|  |  - Compliance     |    |  - Local Claude   |    |  - Testing      |  |
|  |  - Cost Tracking  |    |  - Shared Config  |    |  - Security     |  |
|  |  - Audit Logs     |    |  - Team Skills    |    |  - Deploy Gates |  |
|  +------------------+    +-------------------+    +-----------------+  |
|                                                                        |
|  +------------------+    +-------------------+    +-----------------+  |
|  |  Training &       |    |  Metrics &        |    |  Support &      |  |
|  |  Onboarding       |    |  Analytics        |    |  Maintenance    |  |
|  |  - Bootcamp       |    |  - Adoption       |    |  - Help Desk    |  |
|  |  - Documentation  |    |  - Velocity       |    |  - Updates      |  |
|  |  - Best Practices |    |  - Quality        |    |  - Roadmap      |  |
|  +------------------+    +-------------------+    +-----------------+  |
+-----------------------------------------------------------------------+
```

---

## 페이즈 1: 평가 및 계획 (4시간)

### 태스크 1.1: 현재 개발 워크플로우 감사

조직의 기존 개발 프로세스를 철저히 감사합니다. Claude Code가 보강하거나 자동화할 수 있는 모든 워크플로우를 문서화합니다.

평가 문서를 작성합니다:

```markdown
# Enterprise Claude Code Assessment

## Current Development Workflows

### Code Review Process
- **Current**: Manual PR reviews, average 24h turnaround
- **Pain Points**: Inconsistent standards, reviewer fatigue, missed issues
- **Opportunity**: Automated first-pass review, style enforcement, security scanning

### Testing Practices
- **Current**: Manual test writing, ~60% coverage
- **Pain Points**: Low coverage on legacy code, inconsistent test quality
- **Opportunity**: Automated test generation, coverage gap analysis

### Documentation
- **Current**: Ad-hoc, often outdated
- **Pain Points**: New engineers struggle to onboard, API docs incomplete
- **Opportunity**: Auto-generated docs, enforced documentation on new code

### Deployment
- **Current**: Semi-manual deploys with checklist
- **Pain Points**: Missed steps, configuration drift, rollback complexity
- **Opportunity**: Automated pre-deploy validation, deployment gate checks

### Security
- **Current**: Quarterly security audits, manual review
- **Pain Points**: Vulnerabilities found late, inconsistent scanning
- **Opportunity**: Continuous security scanning in hooks and CI/CD
```

### 태스크 1.2: 자동화 기회 식별

각 기회를 영향도와 노력으로 순위를 매깁니다:

```markdown
## Automation Priority Matrix

| Opportunity              | Impact | Effort | Priority |
|--------------------------|--------|--------|----------|
| Automated code review    | High   | Medium | P0       |
| Pre-commit security scan | High   | Low    | P0       |
| Test generation          | High   | Medium | P1       |
| Documentation generation | Medium | Low    | P1       |
| Deploy gate checks       | High   | Medium | P1       |
| Onboarding automation    | Medium | Medium | P2       |
| Incident response assist | Medium | High   | P2       |
| Architecture review      | Low    | High   | P3       |
```

### 태스크 1.3: 거버넌스 요구 사항 정의

```markdown
## Governance Requirements

### Security Policies
- No secrets in prompts or CLAUDE.md files
- All Bash commands must be explicitly allowlisted
- Network access restricted to approved domains
- File system access scoped to project directories

### Compliance Requirements
- Audit log of all Claude Code invocations
- Data residency constraints (no code sent to unapproved regions)
- License compliance checking on generated code
- SOC 2 / ISO 27001 alignment

### Cost Controls
- Per-team monthly spend limits
- Per-developer daily token budgets
- Alerting thresholds at 80% and 95% of budget
- Quarterly cost review process

### Access Controls
- Role-based permission tiers (junior, senior, lead, admin)
- Project-level access restrictions
- Approval workflow for production-touching operations
```

### 태스크 1.4: 배포 계획 수립

```markdown
## Deployment Plan

### Timeline
- Weeks 1-2: Build enterprise plugin and managed settings
- Weeks 3-4: CI/CD integration and governance framework
- Weeks 5-6: Pilot with 10 engineers (2 teams)
- Weeks 7-8: Incorporate feedback, iterate
- Weeks 9-10: Training materials and onboarding program
- Weeks 11-12: Company-wide rollout (phased by team)
- Ongoing: Measurement, optimization, support

### Success Criteria
- 90%+ adoption within 3 months of rollout
- 2x improvement in PR turnaround time
- 50% reduction in bugs reaching production
- Positive ROI within first quarter
- 90%+ developer satisfaction score

### Risk Mitigation
| Risk                        | Mitigation                              |
|-----------------------------|-----------------------------------------|
| Low adoption                | Champions program, executive sponsorship|
| Security incident           | Strict allowlists, audit logging        |
| Cost overrun                | Budget limits, alerting, monthly review |
| Quality degradation         | Mandatory human review, test gates      |
| Vendor lock-in              | Abstract integration points, document   |
```

### 페이즈 1 산출물
- [ ] 워크플로우 감사가 포함된 완성된 평가 문서
- [ ] 우선순위가 매겨진 자동화 기회 매트릭스
- [ ] 거버넌스 요구 사항 사양
- [ ] 타임라인, 위험, 성공 기준이 포함된 배포 계획

---

## 페이즈 2: 엔터프라이즈 플러그인 개발 (8시간)

### 태스크 2.1: 플러그인 디렉토리 구조

포괄적인 구조로 엔터프라이즈 플러그인을 만듭니다:

```
company-claude-plugin/
├── CLAUDE.md                          # 조직 전체 지침
├── settings.json                      # 관리 설정
├── skills/
│   ├── code-review/
│   │   └── SKILL.md                   # 자동화된 코드 리뷰
│   ├── deploy/
│   │   └── SKILL.md                   # 배포 워크플로우
│   ├── test-gen/
│   │   └── SKILL.md                   # 테스트 생성
│   ├── security-scan/
│   │   └── SKILL.md                   # 보안 스캐닝
│   ├── doc-gen/
│   │   └── SKILL.md                   # 문서 생성
│   ├── incident-response/
│   │   └── SKILL.md                   # 인시던트 대응 어시스턴트
│   ├── onboard/
│   │   └── SKILL.md                   # 새 엔지니어 온보딩
│   ├── architecture-review/
│   │   └── SKILL.md                   # 아키텍처 결정 리뷰
│   ├── migrate/
│   │   └── SKILL.md                   # 코드 마이그레이션 어시스턴트
│   └── perf-audit/
│       └── SKILL.md                   # 성능 감사
├── hooks/
│   ├── pre-commit-security.sh         # 보안 pre-commit 검사
│   ├── pre-commit-lint.sh             # 린팅 시행
│   ├── post-tool-use-audit.sh         # 감사 로깅
│   ├── pre-tool-use-permission.sh     # 권한 시행
│   └── notification.sh                # Slack/Teams 알림
├── agents/
│   ├── security-reviewer/
│   │   └── SUBAGENT.md                # 보안 리뷰 서브에이전트
│   ├── test-writer/
│   │   └── SUBAGENT.md                # 테스트 작성 서브에이전트
│   ├── doc-writer/
│   │   └── SUBAGENT.md                # 문서 작성 서브에이전트
│   └── code-analyzer/
│       └── SUBAGENT.md                # 코드 분석 서브에이전트
├── mcp-servers/
│   ├── jira-integration/              # Jira 티켓 관리
│   ├── slack-notifier/                # Slack 알림
│   └── metrics-collector/             # 사용량 메트릭 수집
├── templates/
│   ├── CLAUDE.md.team-template        # 팀별 CLAUDE.md 템플릿
│   ├── CLAUDE.md.project-template     # 프로젝트별 CLAUDE.md 템플릿
│   └── onboarding-checklist.md        # 새 엔지니어 체크리스트
└── scripts/
    ├── install.sh                     # 플러그인 설치 스크립트
    ├── update.sh                      # 플러그인 업데이트 스크립트
    └── verify.sh                      # 설치 확인
```

### 태스크 2.2: 조직 전체 CLAUDE.md

모든 프로젝트에 적용되는 루트 CLAUDE.md를 만듭니다:

```markdown
# CLAUDE.md - Acme Corp Engineering Standards

## Company Context
Acme Corp is a SaaS platform serving enterprise customers. Our stack is
TypeScript/Node.js (backend), React/TypeScript (frontend), PostgreSQL
(database), and AWS (infrastructure). We practice trunk-based development
with short-lived feature branches.

## Code Standards

### General
- All code must pass ESLint and Prettier before commit
- Maximum function length: 50 lines
- Maximum file length: 400 lines
- Maximum cyclomatic complexity: 10
- All public functions must have JSDoc documentation
- All new code must have corresponding tests (minimum 80% coverage)

### TypeScript
- Strict mode enabled, no `any` types without documented justification
- Use interfaces over type aliases for object shapes
- Prefer immutable patterns (const, readonly, Object.freeze)
- Use barrel exports (index.ts) for module boundaries

### React
- Functional components only (no class components)
- Custom hooks for shared logic (prefix with `use`)
- Component files: one component per file
- Props interfaces exported alongside components
- Use React.memo only with measured performance justification

### API Design
- RESTful conventions for CRUD operations
- GraphQL for complex data fetching
- All endpoints must have OpenAPI documentation
- Rate limiting on all public endpoints
- Input validation using Zod schemas

### Database
- All schema changes via migrations (never manual DDL)
- Indexes required for any column used in WHERE clauses
- No N+1 queries (use DataLoader or eager loading)
- Soft deletes preferred over hard deletes

## Security Requirements

### Mandatory
- Never commit secrets, tokens, or credentials
- All user input must be validated and sanitized
- SQL queries must use parameterized statements
- Authentication required on all non-public endpoints
- HTTPS only, no mixed content

### Prohibited Patterns
- eval() or Function() constructor
- innerHTML with user-supplied content
- Disabling CORS without security review
- Storing passwords in plaintext
- Logging sensitive user data (PII, credentials)

## Git Conventions
- Branch naming: feature/JIRA-123-short-description
- Commit messages: conventional commits format
  - feat: new feature
  - fix: bug fix
  - refactor: code change that neither fixes nor adds
  - test: adding or updating tests
  - docs: documentation changes
  - chore: maintenance tasks
- PRs require at least 1 approval
- Squash merge to main

## Testing Standards
- Unit tests: Jest with React Testing Library
- Integration tests: Supertest for APIs
- E2E tests: Playwright for critical user flows
- Test naming: "should [expected behavior] when [condition]"
- Arrange-Act-Assert structure
- No test interdependence (each test must be independent)

## Error Handling
- Use custom error classes extending BaseError
- All async operations must have error handling
- User-facing errors must have error codes
- Log errors with structured format (JSON)
- Never swallow errors silently

## Performance Guidelines
- API response time target: <200ms (p95)
- Bundle size budget: <250KB gzipped
- Database query time target: <50ms
- Cache frequently accessed data (Redis, 5-minute TTL default)
- Lazy load non-critical components
```

### 태스크 2.3: 표준화된 스킬

#### 코드 리뷰 스킬

`skills/code-review/SKILL.md`를 생성합니다:

```markdown
---
name: code-review
description: Comprehensive automated code review following Acme Corp standards
commands:
  - review
  - review-pr
  - review-security
tools:
  - Read
  - Glob
  - Grep
  - Bash(git diff)
  - Bash(git log)
  - Bash(gh pr view)
  - Bash(gh pr diff)
---

# Code Review Skill

You are a senior engineer at Acme Corp performing a thorough code review.

## Invocation

- `/review` - Review staged or uncommitted changes
- `/review-pr <number>` - Review a specific pull request
- `/review-security` - Security-focused review only

## Review Process

1. **Gather Context**
   - Identify all changed files
   - Read the PR description or commit messages
   - Understand the intent of the changes

2. **Standards Compliance**
   - Check against CLAUDE.md coding standards
   - Verify TypeScript strict mode compliance
   - Confirm JSDoc on all public functions
   - Validate naming conventions

3. **Security Analysis**
   - Input validation present on all user inputs
   - No hardcoded secrets or credentials
   - SQL injection prevention (parameterized queries)
   - XSS prevention (no innerHTML with user data)
   - Authentication and authorization checks
   - Dependency vulnerabilities (check package.json changes)

4. **Code Quality**
   - Function length (<50 lines)
   - Cyclomatic complexity (<10)
   - DRY violations (duplicated logic)
   - Error handling completeness
   - Edge case coverage

5. **Performance**
   - N+1 query detection
   - Unnecessary re-renders in React components
   - Missing database indexes for new queries
   - Bundle size impact of new dependencies

6. **Testing**
   - New code has corresponding tests
   - Test quality (meaningful assertions, not just coverage)
   - Edge cases tested
   - Error paths tested

## Output Format

    # Code Review Report

    ## Summary
    [1-2 sentence overview of changes and overall assessment]

    ## Critical Issues (must fix before merge)
    - **[CRIT-1]** [file:line] - [description]
      - **Why**: [explanation of risk]
      - **Fix**: [specific recommendation]

    ## Major Issues (should fix before merge)
    - **[MAJ-1]** [file:line] - [description]
      - **Fix**: [recommendation]

    ## Minor Issues (consider fixing)
    - **[MIN-1]** [file:line] - [description]

    ## Suggestions (optional improvements)
    - **[SUG-1]** [description]

    ## Positive Notes
    - [What was done well]

    ## Verdict
    APPROVED / CHANGES REQUESTED / BLOCKED
    [Reasoning for verdict]
```

#### 배포 스킬

`skills/deploy/SKILL.md`를 생성합니다:

```markdown
---
name: deploy
description: Safe deployment workflow with pre-flight checks
commands:
  - deploy
  - deploy-check
  - rollback
tools:
  - Read
  - Glob
  - Grep
  - Bash(git status)
  - Bash(git log)
  - Bash(git tag)
  - Bash(npm run build)
  - Bash(npm test)
  - Bash(npm audit)
  - Bash(curl)
---

# Deployment Skill

## Commands

### /deploy [environment]
Full deployment workflow with safety checks.

### /deploy-check
Pre-deployment validation only (dry run).

### /rollback [version]
Emergency rollback procedure.

## Pre-Deployment Checklist

Before ANY deployment, verify ALL of the following:

1. **Git State**
   - On correct branch (main for production, develop for staging)
   - No uncommitted changes
   - Branch is up to date with remote
   - All CI checks passing

2. **Code Quality**
   - All tests pass locally
   - Linter passes with no errors
   - No TypeScript compilation errors
   - No known critical vulnerabilities (npm audit)

3. **Database**
   - Migrations are ready and tested
   - Rollback migrations exist
   - No breaking schema changes without migration plan

4. **Configuration**
   - Environment variables documented
   - Feature flags configured
   - Third-party service configurations verified

5. **Approval**
   - PR approved by at least 1 reviewer
   - Product owner sign-off for user-facing changes
   - Security review for auth/data changes

## Deployment Steps

1. Create deployment tag: v{major}.{minor}.{patch}
2. Build production assets
3. Run database migrations
4. Deploy to target environment
5. Health check: GET /api/health (expect 200)
6. Smoke tests: critical user flows
7. Monitor error rates for 10 minutes
8. Update deployment log

## Post-Deployment Verification

- Health endpoint returns 200
- Key API endpoints respond correctly
- Error rate has not increased
- No new error types in logs
- Performance metrics within baseline
```

#### 테스트 생성 스킬

`skills/test-gen/SKILL.md`를 생성합니다:

```markdown
---
name: test-gen
description: Generate comprehensive test suites following Acme Corp standards
commands:
  - gen-tests
  - gen-tests-coverage
tools:
  - Read
  - Write
  - Glob
  - Grep
  - Bash(npm test)
  - Bash(npx jest --coverage)
---

# Test Generation Skill

## Commands

- `/gen-tests [file]` - Generate tests for a specific file
- `/gen-tests-coverage` - Analyze coverage gaps and generate missing tests

## Process

1. **Analyze Source File**
   - Read the source file
   - Identify all exported functions, classes, and methods
   - Determine dependencies that need mocking
   - Identify edge cases and error conditions

2. **Check Existing Tests**
   - Look for existing test file
   - Analyze current coverage if tests exist
   - Identify untested code paths

3. **Detect Testing Framework**
   - Check package.json for jest, mocha, vitest
   - Check existing test files for patterns
   - Default to Jest if unclear

4. **Generate Tests**
   - Follow Arrange-Act-Assert pattern
   - Use descriptive test names: "should [behavior] when [condition]"
   - Include happy path, error cases, and edge cases
   - Mock external dependencies
   - Test async operations with proper await/resolve patterns

5. **Verify Tests**
   - Run the generated tests
   - Fix any failures
   - Report coverage achieved
```

### 태스크 2.4: 시행 훅

표준을 자동으로 시행하는 훅을 만듭니다.

#### Pre-Commit 보안 훅

`hooks/pre-commit-security.sh`를 생성합니다:

```bash
#!/bin/bash
# Pre-commit security scanning hook for Claude Code
# Blocks commits containing secrets, credentials, or security violations

set -euo pipefail

STAGED_FILES=$(git diff --cached --name-only --diff-filter=ACM)
EXIT_CODE=0

echo "Running security pre-commit checks..."

# Check for hardcoded secrets
SECRET_PATTERNS=(
  'AKIA[0-9A-Z]{16}'                    # AWS Access Key
  'password\s*=\s*["\x27][^"\x27]+'     # Hardcoded passwords
  'api[_-]?key\s*=\s*["\x27][^"\x27]+'  # API keys
  'secret\s*=\s*["\x27][^"\x27]+'       # Generic secrets
  'BEGIN\s+(RSA|DSA|EC)\s+PRIVATE'       # Private keys
  'ghp_[a-zA-Z0-9]{36}'                 # GitHub PAT
  'sk-[a-zA-Z0-9]{48}'                  # OpenAI/Anthropic keys
)

for file in $STAGED_FILES; do
  if [[ -f "$file" ]]; then
    for pattern in "${SECRET_PATTERNS[@]}"; do
      if grep -qEi "$pattern" "$file" 2>/dev/null; then
        echo "BLOCKED: Potential secret found in $file (pattern: $pattern)"
        EXIT_CODE=1
      fi
    done
  fi
done

# Check for prohibited patterns
for file in $STAGED_FILES; do
  if [[ "$file" =~ \.(js|ts|tsx|jsx)$ ]] && [[ -f "$file" ]]; then
    if grep -q 'eval(' "$file" 2>/dev/null; then
      echo "BLOCKED: eval() usage found in $file"
      EXIT_CODE=1
    fi
    if grep -q '\.innerHTML\s*=' "$file" 2>/dev/null; then
      echo "WARNING: innerHTML assignment in $file - verify no user input"
    fi
  fi
done

# Check for .env files being committed
for file in $STAGED_FILES; do
  if [[ "$file" =~ \.env$ ]] || [[ "$file" =~ \.env\. ]]; then
    echo "BLOCKED: Environment file $file should not be committed"
    EXIT_CODE=1
  fi
done

if [[ $EXIT_CODE -eq 0 ]]; then
  echo "Security checks passed."
else
  echo ""
  echo "Security checks FAILED. Fix the issues above before committing."
fi

exit $EXIT_CODE
```

#### Post-Tool-Use 감사 훅

`hooks/post-tool-use-audit.sh`를 생성합니다:

```bash
#!/bin/bash
# Audit logging hook - records all Claude Code tool invocations
# Writes structured logs for compliance and analytics

AUDIT_LOG="${HOME}/.claude/audit/audit-$(date +%Y-%m-%d).jsonl"
mkdir -p "$(dirname "$AUDIT_LOG")"

TIMESTAMP=$(date -u +"%Y-%m-%dT%H:%M:%SZ")
USER=$(whoami)
PROJECT=$(basename "$(pwd)")

cat >> "$AUDIT_LOG" << JSONEOF
{"timestamp":"${TIMESTAMP}","user":"${USER}","project":"${PROJECT}","tool":"${CLAUDE_TOOL_NAME:-unknown}","session":"${CLAUDE_SESSION_ID:-unknown}","working_dir":"$(pwd)"}
JSONEOF
```

### 태스크 2.5: 전문 서브에이전트

#### 보안 리뷰어 서브에이전트

`agents/security-reviewer/SUBAGENT.md`를 생성합니다:

```markdown
---
name: security-reviewer
description: Performs deep security analysis of code changes
model: opus
tools:
  - Read
  - Glob
  - Grep
---

# Security Reviewer Subagent

You are a senior application security engineer performing a thorough
security review.

## Responsibilities

1. **Vulnerability Detection**
   - Injection flaws (SQL, NoSQL, LDAP, OS command)
   - Cross-site scripting (XSS) - reflected, stored, DOM-based
   - Broken authentication and session management
   - Insecure direct object references
   - Security misconfiguration
   - Sensitive data exposure
   - Missing function-level access control
   - Cross-site request forgery (CSRF)
   - Using components with known vulnerabilities
   - Insufficient logging and monitoring

2. **Authentication & Authorization**
   - Verify auth checks on all protected endpoints
   - Check for privilege escalation paths
   - Validate session handling
   - Review token generation and validation

3. **Data Protection**
   - PII handling compliance
   - Encryption at rest and in transit
   - Secure key management
   - Data retention policies

## Output Format

    # Security Review Report

    ## Risk Level: [CRITICAL / HIGH / MEDIUM / LOW / NONE]

    ## Findings

    ### [SEV-1] Critical
    - [Finding with file:line reference]
    - **Impact**: [description]
    - **Remediation**: [specific fix]

    ### [SEV-2] High
    - [Finding with file:line reference]

    ### [SEV-3] Medium
    - [Finding with file:line reference]

    ## Compliance Notes
    - [Relevant compliance observations]

    ## Recommendations
    - [Proactive security improvements]
```

#### 테스트 작성 서브에이전트

`agents/test-writer/SUBAGENT.md`를 생성합니다:

```markdown
---
name: test-writer
description: Generates comprehensive test suites for code changes
model: sonnet
tools:
  - Read
  - Write
  - Glob
  - Grep
  - Bash(npm test)
  - Bash(npx jest)
---

# Test Writer Subagent

You are a QA engineer specializing in automated test development.

## Responsibilities

1. **Analyze Code Under Test**
   - Read source files and understand behavior
   - Identify all code paths and branches
   - Determine external dependencies to mock
   - Find edge cases and boundary conditions

2. **Generate Tests**
   - Unit tests for individual functions
   - Integration tests for module interactions
   - Error handling tests for failure modes
   - Boundary value tests for edge cases

3. **Verify Quality**
   - Run tests and confirm they pass
   - Check coverage meets 80% minimum threshold
   - Ensure tests are deterministic (no flaky tests)
   - Validate mocks accurately represent dependencies

## Test Writing Rules
- One assertion concept per test
- Descriptive test names that document behavior
- No test interdependence
- Clean setup and teardown
- Meaningful assertions (not just "does not throw")
```

### 페이즈 2 산출물
- [ ] 완전한 플러그인 디렉토리 구조 생성
- [ ] 모든 코딩 표준이 포함된 조직 전체 CLAUDE.md
- [ ] 10개의 표준화된 스킬 (code-review, deploy, test-gen, security-scan, doc-gen, incident-response, onboard, architecture-review, migrate, perf-audit)
- [ ] 5개의 시행 훅 (pre-commit 보안, 린트, 감사, 권한, 알림)
- [ ] 4개의 전문 서브에이전트 (security-reviewer, test-writer, doc-writer, code-analyzer)
- [ ] 설치 및 업데이트 스크립트

---

## 페이즈 3: CI/CD 통합 (6시간)

### 태스크 3.1: 자동화된 코드 리뷰 워크플로우

`.github/workflows/claude-code-review.yml`을 생성합니다:

```yaml
name: Claude Code Review

on:
  pull_request:
    types: [opened, synchronize, reopened]

permissions:
  contents: read
  pull-requests: write

jobs:
  claude-review:
    runs-on: ubuntu-latest
    timeout-minutes: 15

    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install dependencies
        run: npm ci

      - name: Run Claude Code Review
        uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            Review this pull request following our CLAUDE.md standards.

            Focus on:
            1. Code quality and standards compliance
            2. Security vulnerabilities
            3. Performance concerns
            4. Test coverage for new code
            5. Documentation completeness

            For each issue found, provide:
            - Severity (critical, major, minor, suggestion)
            - File and line reference
            - Clear explanation
            - Specific fix recommendation

            End with an overall assessment and verdict.
          allowed_tools: |
            Read
            Glob
            Grep
            Bash(git diff)
            Bash(git log)
          direct_prompt: true

      - name: Post Review Summary
        if: always()
        uses: actions/github-script@v7
        with:
          script: |
            const fs = require('fs');
            const reviewOutput = process.env.CLAUDE_OUTPUT || 'Review completed.';
            await github.rest.issues.createComment({
              owner: context.repo.owner,
              repo: context.repo.repo,
              issue_number: context.issue.number,
              body: `## Claude Code Review\n\n${reviewOutput}`
            });
```

### 태스크 3.2: 보안 스캐닝 워크플로우

`.github/workflows/claude-security-scan.yml`을 생성합니다:

```yaml
name: Claude Security Scan

on:
  pull_request:
    types: [opened, synchronize]
    paths:
      - 'src/**'
      - 'packages/**'
      - 'package.json'
      - 'package-lock.json'

permissions:
  contents: read
  pull-requests: write
  security-events: write

jobs:
  security-scan:
    runs-on: ubuntu-latest
    timeout-minutes: 10

    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Run npm audit
        id: npm-audit
        run: |
          npm audit --json > audit-results.json 2>&1 || true
        continue-on-error: true

      - name: Claude Security Analysis
        uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            Perform a security-focused review of the changes in this PR.

            Also review the npm audit results in audit-results.json if present.

            Check for:
            1. OWASP Top 10 vulnerabilities
            2. Hardcoded secrets or credentials
            3. Injection vulnerabilities (SQL, XSS, command injection)
            4. Authentication and authorization issues
            5. Insecure data handling
            6. Dependency vulnerabilities
            7. Configuration security

            Rate overall security risk: CRITICAL / HIGH / MEDIUM / LOW

            If CRITICAL or HIGH, this PR should be blocked until resolved.
          allowed_tools: |
            Read
            Glob
            Grep
            Bash(git diff)
          direct_prompt: true

      - name: Block on Critical Security Issues
        if: contains(env.CLAUDE_OUTPUT, 'CRITICAL')
        run: |
          echo "::error::Critical security issues detected. PR blocked."
          exit 1
```

### 태스크 3.3: 테스트 자동화 워크플로우

`.github/workflows/claude-test-coverage.yml`을 생성합니다:

```yaml
name: Claude Test Coverage

on:
  pull_request:
    types: [opened, synchronize]

permissions:
  contents: read
  pull-requests: write

jobs:
  test-coverage:
    runs-on: ubuntu-latest
    timeout-minutes: 20

    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install dependencies
        run: npm ci

      - name: Run existing tests with coverage
        id: test-run
        run: |
          npx jest --coverage --coverageReporters=json-summary \
            --coverageReporters=text 2>&1 | tee test-output.txt
        continue-on-error: true

      - name: Claude Test Analysis
        uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            Analyze the test results and coverage for this PR.

            1. Review test-output.txt for test results
            2. Identify changed files that lack test coverage
            3. Check if new functions have corresponding tests
            4. Verify test quality (meaningful assertions, edge cases)

            Report:
            - Files with insufficient coverage (<80%)
            - New code paths without tests
            - Suggestions for additional test cases
            - Overall test health assessment
          allowed_tools: |
            Read
            Glob
            Grep
          direct_prompt: true
```

### 태스크 3.4: 배포 게이트 워크플로우

`.github/workflows/claude-deploy-gate.yml`을 생성합니다:

```yaml
name: Claude Deployment Gate

on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Target environment'
        required: true
        type: choice
        options:
          - staging
          - production
      version:
        description: 'Version to deploy'
        required: true
        type: string

permissions:
  contents: read
  deployments: write

jobs:
  pre-deploy-validation:
    runs-on: ubuntu-latest
    timeout-minutes: 15
    environment: ${{ github.event.inputs.environment }}

    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          ref: ${{ github.event.inputs.version }}

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install and build
        run: |
          npm ci
          npm run build

      - name: Run full test suite
        run: npm test

      - name: Claude Pre-Deploy Validation
        uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            Perform pre-deployment validation for deploying version
            ${{ github.event.inputs.version }} to
            ${{ github.event.inputs.environment }}.

            Checks:
            1. All tests pass (review test output)
            2. No critical npm audit findings
            3. Database migrations are safe (no data loss risk)
            4. Environment configuration is complete
            5. No breaking API changes without version bump
            6. Feature flags configured for gradual rollout
            7. Rollback plan exists

            For production deployments, additionally verify:
            - Load testing results acceptable
            - Monitoring and alerting configured
            - Runbook updated
            - On-call team notified

            Provide a GO / NO-GO recommendation with reasoning.
          allowed_tools: |
            Read
            Glob
            Grep
            Bash(npm audit)
            Bash(npm test)
          direct_prompt: true

      - name: Gate Decision
        if: contains(env.CLAUDE_OUTPUT, 'NO-GO')
        run: |
          echo "::error::Deployment blocked by pre-deploy validation."
          exit 1
```

### 페이즈 3 산출물
- [ ] 자동화된 코드 리뷰 GitHub Action 워크플로우
- [ ] 중요 이슈에서 PR 차단하는 보안 스캐닝 워크플로우
- [ ] 테스트 커버리지 분석 워크플로우
- [ ] GO/NO-GO 결정이 있는 배포 게이트 워크플로우
- [ ] 모든 워크플로우가 파일럿 리포지토리에서 테스트됨

---

## 페이즈 4: 거버넌스 및 컴플라이언스 (4시간)

### 태스크 4.1: 엔터프라이즈 관리 설정

모든 팀에 걸쳐 정책을 시행하는 조직 수준 `settings.json`을 만듭니다:

```jsonc
{
  "permissions": {
    "allow": [
      "Read", "Glob", "Grep", "Write", "Edit",
      "Bash(npm test)", "Bash(npm run lint)", "Bash(npm run build)",
      "Bash(npx jest*)", "Bash(npx eslint*)", "Bash(npx prettier*)", "Bash(npx tsc*)",
      "Bash(git status)", "Bash(git diff*)", "Bash(git log*)", "Bash(git branch*)",
      "Bash(git add*)", "Bash(git commit*)", "Bash(git checkout*)", "Bash(git switch*)",
      "Bash(git merge*)", "Bash(git rebase*)", "Bash(git stash*)", "Bash(git tag*)",
      "Bash(gh pr*)", "Bash(gh issue*)",
      "Bash(curl --max-time 10 https://*.acme-corp.com/*)",
      "Bash(docker compose*)", "Bash(docker build*)",
      "WebFetch(*.acme-corp.com/*)"
    ],
    "deny": [
      "Bash(rm -rf /)", "Bash(chmod 777*)",
      "Bash(curl * | bash)", "Bash(wget * | sh)",
      "Bash(npm publish*)", "Bash(git push --force*)", "Bash(git reset --hard*)",
      "Bash(docker push*)", "Bash(ssh *)", "Bash(scp *)", "Bash(sudo *)",
      "WebFetch(*.competitor.com/*)"
    ]
  },
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write|Edit",
        "command": "node /opt/claude-enterprise/hooks/validate-write.js",
        "description": "Validate file writes against policy"
      }
    ],
    "PostToolUse": [
      {
        "matcher": ".*",
        "command": "/opt/claude-enterprise/hooks/audit-log.sh",
        "description": "Log all tool usage for compliance"
      }
    ]
  },
  "env": {
    "CLAUDE_MAX_TOKENS_PER_SESSION": "100000",
    "CLAUDE_AUDIT_LOG_PATH": "/var/log/claude/audit",
    "CLAUDE_ENTERPRISE_MODE": "true"
  }
}
```

### 태스크 4.2: 역할 기반 권한 티어

다양한 역할에 대한 권한 정책을 정의합니다:

```jsonc
// junior-developer-settings.json
// 입사 첫 6개월 엔지니어에게 적용
{
  "permissions": {
    "allow": [
      "Read", "Glob", "Grep", "Write", "Edit",
      "Bash(npm test)", "Bash(npm run lint)",
      "Bash(git status)", "Bash(git diff*)", "Bash(git log*)",
      "Bash(git add*)", "Bash(git commit*)"
    ],
    "deny": [
      "Bash(git push*)", "Bash(npm publish*)",
      "Bash(docker*)", "Bash(curl*)", "Bash(wget*)"
    ]
  }
}
```

```jsonc
// senior-developer-settings.json
// 시니어 엔지니어 이상에게 적용
{
  "permissions": {
    "allow": [
      "Read", "Glob", "Grep", "Write", "Edit",
      "Bash(npm *)", "Bash(npx *)", "Bash(git *)", "Bash(gh *)",
      "Bash(docker *)", "Bash(curl --max-time 30 *)", "Bash(make *)",
      "WebFetch"
    ],
    "deny": [
      "Bash(rm -rf /)", "Bash(sudo *)", "Bash(ssh *)",
      "Bash(npm publish*)", "Bash(git push --force*)"
    ]
  }
}
```

### 태스크 4.3: 사용량 분석 및 비용 추적

메트릭 수집 시스템을 만듭니다:

```markdown
## Usage Analytics Architecture

### Data Collection Points

1. **Session Metrics** (세션별 수집)
   - Session duration
   - Tool invocations (count by tool type)
   - Token usage (input + output)
   - Model used (Sonnet, Opus, Haiku)
   - Project/repository
   - User identity

2. **Quality Metrics** (PR별 수집)
   - Issues found by automated review
   - Issues that matched human review findings
   - False positive rate
   - Time saved vs. manual review

3. **Cost Metrics** (일별 집계)
   - Token cost per user
   - Token cost per team
   - Token cost per project
   - Cost trend (week-over-week)
```

`scripts/usage-report.sh`를 생성합니다:

```bash
#!/bin/bash
# Generate weekly usage report from audit logs

AUDIT_DIR="${HOME}/.claude/audit"
REPORT_DATE=$(date +%Y-%m-%d)
WEEK_START=$(date -d "7 days ago" +%Y-%m-%d)
REPORT_FILE="/var/reports/claude/weekly-${REPORT_DATE}.md"

echo "# Claude Code Usage Report" > "$REPORT_FILE"
echo "## Week of ${WEEK_START} to ${REPORT_DATE}" >> "$REPORT_FILE"
echo "" >> "$REPORT_FILE"

echo "### Sessions by User" >> "$REPORT_FILE"
echo "| User | Sessions | Tool Calls |" >> "$REPORT_FILE"
echo "|------|----------|------------|" >> "$REPORT_FILE"

for log_file in "${AUDIT_DIR}"/audit-*.jsonl; do
  if [[ -f "$log_file" ]]; then
    while IFS= read -r line; do
      user=$(echo "$line" | python3 -c "import sys,json; print(json.load(sys.stdin).get('user','unknown'))" 2>/dev/null)
      echo "$user"
    done < "$log_file"
  fi
done | sort | uniq -c | sort -rn | while read count user; do
  echo "| ${user} | ${count} | - |" >> "$REPORT_FILE"
done

echo "" >> "$REPORT_FILE"
echo "### Cost Summary" >> "$REPORT_FILE"
echo "*(Requires API usage data from Anthropic dashboard)*" >> "$REPORT_FILE"

echo "Report generated: ${REPORT_FILE}"
```

### 태스크 4.4: 컴플라이언스 검사

`scripts/compliance-check.sh`를 생성합니다:

```bash
#!/bin/bash
# Compliance verification script

set -euo pipefail

PASS=0
FAIL=0
WARN=0

check() {
  local description="$1"
  local result="$2"
  if [[ "$result" == "pass" ]]; then
    echo "  PASS: $description"
    PASS=$((PASS + 1))
  elif [[ "$result" == "warn" ]]; then
    echo "  WARN: $description"
    WARN=$((WARN + 1))
  else
    echo "  FAIL: $description"
    FAIL=$((FAIL + 1))
  fi
}

echo "============================================"
echo "Enterprise Claude Code Compliance Check"
echo "============================================"
echo ""

echo "## Settings & Configuration"
if [[ -f "/etc/claude/settings.json" ]]; then
  check "Managed settings file exists" "pass"
else
  check "Managed settings file exists" "fail"
fi

echo ""
echo "## Audit & Logging"
AUDIT_DIR="${HOME}/.claude/audit"
if [[ -d "$AUDIT_DIR" ]]; then
  check "Audit log directory exists" "pass"
else
  check "Audit log directory exists" "fail"
fi

echo ""
echo "## Security Controls"
if grep -q "pre-commit-security" "/etc/claude/settings.json" 2>/dev/null; then
  check "Pre-commit security hook configured" "pass"
else
  check "Pre-commit security hook configured" "fail"
fi

echo ""
echo "============================================"
echo "Results: ${PASS} passed, ${FAIL} failed, ${WARN} warnings"
echo "============================================"

if [[ "$FAIL" -gt 0 ]]; then
  echo "COMPLIANCE STATUS: NOT COMPLIANT"
  exit 1
else
  echo "COMPLIANCE STATUS: COMPLIANT"
  exit 0
fi
```

### 페이즈 4 산출물
- [ ] 허용 목록과 거부 목록이 있는 엔터프라이즈 관리 settings.json
- [ ] 역할 기반 권한 티어 구성 (주니어, 시니어, 관리자)
- [ ] 사용량 분석 수집 및 주간 보고 스크립트
- [ ] 예산 알림이 있는 비용 추적 프레임워크
- [ ] 컴플라이언스 검증 스크립트
- [ ] 감사 로깅 파이프라인

---

## 페이즈 5: 교육 및 온보딩 (4시간)

### 태스크 5.1: 교육 커리큘럼 생성

구조화된 교육 프로그램을 설계합니다:

```markdown
# Claude Code Enterprise Training Program

## Track 1: Getting Started (2 hours)
Target audience: All engineers

### Module 1.1: Introduction (30 min)
- What is Claude Code and why we use it
- How it fits into our development workflow
- Company policies and guidelines
- Quick demo of common workflows

### Module 1.2: Core Skills (30 min)
- Using /review for code review
- Using /gen-tests for test generation
- Using /deploy-check for deployment validation
- Understanding CLAUDE.md and how it guides behavior

### Module 1.3: Hands-On Lab (60 min)
- Exercise 1: Use Claude Code to review a sample PR
- Exercise 2: Generate tests for an untested module
- Exercise 3: Run a deploy-check on staging
- Exercise 4: Create a CLAUDE.md for your project

## Track 2: Advanced Usage (2 hours)
Target audience: Senior engineers and tech leads

### Module 2.1: Custom Skills (45 min)
- Creating project-specific skills
- Skill frontmatter and tool restrictions
- Sharing skills across the team

### Module 2.2: Hooks and Automation (45 min)
- Understanding the hook lifecycle
- Creating custom enforcement hooks
- CI/CD integration patterns

### Module 2.3: Subagents and Orchestration (30 min)
- When to use subagents
- Creating specialized agents
- Multi-agent workflows

## Track 3: Administration (1 hour)
Target audience: Platform team and engineering managers

### Module 3.1: Governance (30 min)
- Managed settings configuration
- Permission management
- Compliance monitoring

### Module 3.2: Metrics and Optimization (30 min)
- Reading usage reports
- Cost optimization strategies
- Measuring ROI
```

### 태스크 5.2: 내부 문서 작성

팀이 커스터마이징할 수 있는 CLAUDE.md 템플릿을 만듭니다:

```markdown
# CLAUDE.md Template for [Team Name]

## Team Context
[Describe your team, domain, and primary responsibilities]

## Project Overview
[Brief description of the project, its purpose, and users]

## Architecture
[High-level architecture description and key components]

## Technology Stack
- **Language**: [e.g., TypeScript 5.x]
- **Framework**: [e.g., NestJS 10.x]
- **Database**: [e.g., PostgreSQL 15]
- **Cache**: [e.g., Redis 7]
- **Cloud**: [e.g., AWS (ECS, RDS, ElastiCache)]

## Code Organization
    src/
      modules/         # Feature modules
      common/          # Shared utilities
      config/          # Configuration
      database/        # Database entities and migrations
      tests/           # Test files mirror src/ structure

## Team Conventions
[List team-specific conventions beyond the org-wide standards]

## Known Gotchas
- [List things that commonly trip people up]
- [Include workarounds and explanations]

## Contacts
- **Tech Lead**: [name]
- **Product Owner**: [name]
- **On-Call**: [rotation link]
```

### 태스크 5.3: 모범 사례 가이드

```markdown
# Claude Code Best Practices at Acme Corp

## Effective Prompting

### Be Specific
Instead of:
> "Fix the bug"

Say:
> "The /api/users endpoint returns 500 when the email field contains
> special characters. The error originates in src/validators/email.ts.
> Fix the validation logic to handle special characters per RFC 5322."

### Provide Context
Instead of:
> "Add authentication"

Say:
> "Add JWT authentication to the /api/orders endpoints using our
> existing AuthService in src/common/auth.service.ts. Follow the
> same pattern used in src/modules/users/users.controller.ts."

### Iterate Incrementally
1. Start with a clear, scoped request
2. Review the output
3. Ask for specific adjustments
4. Verify with tests before moving on

## Security Guidelines

### Always Do
- Review generated code before committing
- Run security scans on all changes
- Keep secrets in environment variables
- Use parameterized queries for database access

### Never Do
- Paste secrets or credentials into prompts
- Accept generated code without review
- Bypass security hooks
- Use Claude Code for production database access

## Cost Optimization

### Use the Right Model
- **Haiku**: Simple questions, quick lookups, formatting
- **Sonnet**: Most development tasks, code generation, review
- **Opus**: Complex architecture decisions, security audits

### Efficient Sessions
- Provide relevant context upfront to reduce back-and-forth
- Use CLAUDE.md to avoid repeating project context
- Use skills for repetitive workflows
- Close sessions when done (do not leave idle)

## Quality Assurance

### Human Review Required
All Claude Code output must be reviewed by a human before:
- Merging to main branch
- Deploying to production
- Modifying authentication or authorization logic
- Changing database schemas
- Updating infrastructure configuration

### Testing Standards
- All generated code must have corresponding tests
- Run the full test suite after changes
- Verify edge cases manually for critical paths
```

### 태스크 5.4: 지원 프로세스

```markdown
# Claude Code Support Process

## Self-Service (Tier 0)
- Internal documentation wiki
- Best practices guide
- FAQ document
- Recorded training sessions

## Team Champions (Tier 1)
Each team has a designated Claude Code champion who:
- Answers day-to-day questions
- Helps with CLAUDE.md customization
- Shares tips and tricks
- Escalates issues to platform team

### Champions Roster
| Team          | Champion       | Slack Channel          |
|---------------|----------------|------------------------|
| Platform      | [Name]         | #claude-platform       |
| Payments      | [Name]         | #claude-payments       |
| Growth        | [Name]         | #claude-growth         |
| Infrastructure| [Name]         | #claude-infra          |

## Platform Team (Tier 2)
For issues that champions cannot resolve:
- Plugin bugs or feature requests
- Settings and permission changes
- CI/CD workflow issues
- Performance problems

**Contact**: #claude-code-support on Slack
**SLA**: 4-hour response during business hours

## Vendor Support (Tier 3)
For issues requiring Anthropic support:
- API errors or outages
- Model behavior concerns
- Billing questions
- Feature requests

**Contact**: Platform team escalates via enterprise support portal
```

### 페이즈 5 산출물
- [ ] 구조화된 교육 커리큘럼 (3트랙, 총 5시간)
- [ ] 팀 커스터마이징을 위한 CLAUDE.md 템플릿
- [ ] 프롬프팅, 보안, 비용 최적화가 포함된 모범 사례 가이드
- [ ] 계층화된 에스컬레이션이 있는 지원 프로세스 문서
- [ ] 챔피언 프로그램 정의 및 명단 템플릿
- [ ] 샘플 프로젝트가 있는 교육 실습 연습

---

## 페이즈 6: 측정 및 최적화 (4시간)

### 태스크 6.1: 채택 메트릭 정의

```markdown
## Adoption Metrics Dashboard

### Primary Metrics

| Metric                    | Target     | Measurement Method               |
|---------------------------|------------|----------------------------------|
| Active users (weekly)     | 90% of eng | Audit log unique users           |
| Sessions per user (daily) | 3+         | Audit log session count          |
| Skill usage rate          | 5+ per day | Audit log skill invocations      |
| CLAUDE.md coverage        | 100% repos | Git scan for CLAUDE.md files     |

### Engagement Metrics

| Metric                    | Target     | Measurement Method               |
|---------------------------|------------|----------------------------------|
| Training completion       | 100%       | LMS completion records           |
| Support tickets (weekly)  | Declining  | Ticketing system                 |
| Champion consultations    | Stable     | Champion activity log            |
| Skill contributions       | Growing    | Plugin repository PRs            |
```

### 태스크 6.2: 속도 개선 추적

```markdown
## Velocity Metrics

### PR Cycle Time
Measure time from PR open to merge, comparing before and after deployment.

**Baseline** (pre-Claude Code):
- Mean PR cycle time: [X hours]
- Median PR cycle time: [X hours]
- P95 PR cycle time: [X hours]

**Current** (with Claude Code):
- Mean PR cycle time: [X hours] ([X%] change)
- Median PR cycle time: [X hours] ([X%] change)
- P95 PR cycle time: [X hours] ([X%] change)

### Code Review Turnaround
- Time to first review comment (automated)
- Time to human review after automated review
- Number of review cycles before approval

### Development Velocity
- Story points completed per sprint (team average)
- PRs merged per developer per week
- Time from ticket creation to PR submission
```

### 태스크 6.3: 품질 메트릭

```markdown
## Quality Metrics

### Bug Rates
| Metric                         | Baseline | Target  | Current |
|--------------------------------|----------|---------|---------|
| Bugs per 1000 lines of code   | [X]      | [X/2]   | [X]     |
| Bugs found in code review     | [X%]     | [+20%]  | [X%]    |
| Bugs reaching production      | [X/mo]   | [X/2]   | [X/mo]  |
| Security vulnerabilities/mo   | [X]      | [X/4]   | [X]     |

### Test Coverage
| Metric                         | Baseline | Target  | Current |
|--------------------------------|----------|---------|---------|
| Overall test coverage          | [X%]     | 80%+    | [X%]    |
| New code coverage              | [X%]     | 90%+    | [X%]    |
| Critical path coverage         | [X%]     | 95%+    | [X%]    |

### Code Quality
| Metric                         | Baseline | Target  | Current |
|--------------------------------|----------|---------|---------|
| Avg cyclomatic complexity      | [X]      | <10     | [X]     |
| Linting violations per PR      | [X]      | 0       | [X]     |
| Documentation coverage         | [X%]     | 90%+    | [X%]    |
```

### 태스크 6.4: ROI 계산

```markdown
## ROI Calculation Framework

### Costs

| Cost Category              | Monthly Amount |
|----------------------------|----------------|
| Anthropic API usage        | $[X]           |
| Platform team maintenance  | $[X] (FTE%)    |
| Training time (amortized)  | $[X]           |
| Infrastructure (CI/CD)     | $[X]           |
| **Total Monthly Cost**     | **$[X]**       |

### Savings

| Savings Category                      | Monthly Amount |
|---------------------------------------|----------------|
| Developer time saved (code review)    | $[X]           |
| Developer time saved (test writing)   | $[X]           |
| Developer time saved (documentation)  | $[X]           |
| Reduced bug fix costs                 | $[X]           |
| Reduced security incident costs       | $[X]           |
| Faster onboarding (reduced ramp time) | $[X]           |
| **Total Monthly Savings**             | **$[X]**       |

### ROI Formula

ROI = ((Total Monthly Savings - Total Monthly Cost) / Total Monthly Cost) x 100

### Time Savings Calculation

Average developer cost: $[X]/hour

| Activity            | Before (hrs/wk) | After (hrs/wk) | Savings/wk | Monthly $ |
|---------------------|------------------|-----------------|------------|-----------|
| Code review         | [X]              | [X]             | [X]        | $[X]      |
| Test writing        | [X]              | [X]             | [X]        | $[X]      |
| Documentation       | [X]              | [X]             | [X]        | $[X]      |
| Bug investigation   | [X]              | [X]             | [X]        | $[X]      |
| Onboarding support  | [X]              | [X]             | [X]        | $[X]      |
| **Total per dev**   | **[X]**          | **[X]**         | **[X]**    | **$[X]**  |

Total monthly savings = savings per dev x number of developers
```

### 태스크 6.5: 지속적 개선 프로세스

```markdown
## Continuous Improvement Cycle

### Monthly Review
1. **Metrics Review** (30 min)
   - Review adoption dashboard
   - Analyze velocity trends
   - Check quality metrics
   - Review cost data

2. **Feedback Collection** (ongoing)
   - Developer satisfaction survey (quarterly)
   - Champion feedback sessions (monthly)
   - Support ticket analysis (weekly)
   - Skill usage analytics (weekly)

3. **Improvement Actions**
   - Update CLAUDE.md based on common issues
   - Add new skills based on repeated requests
   - Refine hooks based on false positive rates
   - Adjust permissions based on security incidents
   - Update training based on support patterns

### Quarterly Planning
1. Review ROI and present to leadership
2. Identify top 3 improvement opportunities
3. Plan skill/hook development for next quarter
4. Update training materials
5. Adjust budget and cost controls
6. Update 6-month roadmap

### 6-Month Roadmap Template

| Quarter | Focus Area              | Key Initiatives                    |
|---------|-------------------------|------------------------------------|
| Q1      | Foundation              | Plugin, CI/CD, pilot rollout       |
| Q2      | Scale                   | Company-wide rollout, training     |
| Q3      | Optimization            | Custom skills, advanced workflows  |
| Q4      | Innovation              | MCP servers, multi-agent pipelines |
```

### 페이즈 6 산출물
- [ ] 채택 메트릭 대시보드 정의
- [ ] 기준선이 있는 속도 개선 추적
- [ ] 품질 메트릭 프레임워크 (버그, 커버리지, 코드 품질)
- [ ] 수식이 있는 ROI 계산 스프레드시트
- [ ] 지속적 개선 프로세스 문서
- [ ] 6개월 로드맵

---

## 최종 산출물 체크리스트

### 1. 엔터프라이즈 플러그인 (프로덕션 준비)
- [ ] 완전한 코딩 표준이 포함된 조직 전체 CLAUDE.md
- [ ] 리뷰, 배포, 테스트, 보안, 문서 등을 다루는 10개 이상의 표준화된 스킬
- [ ] 보안, 린팅, 감사, 권한, 알림을 위한 5개 이상의 시행 훅
- [ ] 보안, 테스트, 문서, 분석을 위한 4개 이상의 전문 서브에이전트
- [ ] 설치 및 업데이트 스크립트
- [ ] 플러그인 버전 관리 및 변경 로그

### 2. CI/CD 파이프라인 (모든 리포지토리 준비)
- [ ] 자동화된 코드 리뷰 워크플로우 (GitHub Actions)
- [ ] 중요 이슈에서 차단하는 보안 스캐닝 워크플로우
- [ ] 테스트 커버리지 분석 워크플로우
- [ ] GO/NO-GO 결정이 있는 배포 게이트 워크플로우
- [ ] 쉬운 채택을 위한 재사용 가능한 워크플로우 템플릿

### 3. 거버넌스 프레임워크 (정책 및 모니터링)
- [ ] 엔터프라이즈 관리 settings.json
- [ ] 역할 기반 권한 티어 (주니어, 시니어, 관리자)
- [ ] 감사 로깅 파이프라인
- [ ] 비용 추적 및 예산 알림
- [ ] 컴플라이언스 검증 스크립트

### 4. 교육 자료 (완전한 온보딩 프로그램)
- [ ] 3트랙 교육 커리큘럼 (총 5시간)
- [ ] 실습 연습
- [ ] 팀 커스터마이징을 위한 CLAUDE.md 템플릿
- [ ] 모범 사례 가이드
- [ ] 계층화된 에스컬레이션이 있는 지원 프로세스
- [ ] 챔피언 프로그램 정의

### 5. 메트릭 대시보드 (측정 및 추적)
- [ ] 목표가 있는 채택 메트릭
- [ ] 속도 개선 추적
- [ ] 품질 메트릭 프레임워크
- [ ] ROI 계산 모델
- [ ] 주간/월간 보고 스크립트

### 6. 로드맵 (지속적 개선 계획)
- [ ] 분기별 마일스톤이 있는 6개월 로드맵
- [ ] 지속적 개선 프로세스
- [ ] 피드백 수집 메커니즘
- [ ] 분기별 검토 주기

---

## 성공 기준

### 정량적 목표
- [ ] 롤아웃 후 3개월 이내 90% 이상 팀 채택
- [ ] PR 주기 시간 2배 이상 개선
- [ ] 프로덕션 도달 버그 50% 이상 감소
- [ ] 모든 활발한 프로젝트에서 80% 이상 테스트 커버리지
- [ ] 첫 분기 내 긍정적 ROI
- [ ] Claude Code로 인한 보안 인시던트 제로

### 정성적 목표
- [ ] 90% 이상 개발자 만족도 점수 (분기별 설문)
- [ ] 모든 팀이 커스터마이징된 CLAUDE.md 파일 보유
- [ ] 챔피언 프로그램이 활발하고 효과적
- [ ] 지원 티켓이 감소 추세
- [ ] 지속적 투자에 대한 리더십 지지

---

## 검증 체크리스트

### 플러그인 검증
- [ ] 새 개발자 머신에서 플러그인이 깔끔하게 설치됨
- [ ] 모든 스킬이 올바르게 호출되고 예상된 출력을 생성함
- [ ] 훅이 올바른 생명주기 지점에서 실행됨
- [ ] 서브에이전트가 적절히 위임하고 결과를 반환함
- [ ] 설정 시행이 허용되지 않은 작업을 방지함
- [ ] 감사 로그가 모든 도구 호출을 캡처함

### CI/CD 검증
- [ ] 코드 리뷰 워크플로우가 PR 열기/업데이트에서 트리거됨
- [ ] 보안 스캔이 중요 발견에서 PR을 차단함
- [ ] 테스트 커버리지 워크플로우가 갭을 정확히 보고함
- [ ] 배포 게이트가 올바른 GO/NO-GO 결정을 생성함
- [ ] 모든 워크플로우가 타임아웃 제한 내에 완료됨

### 거버넌스 검증
- [ ] 관리 설정이 모든 개발자 머신에 적용됨
- [ ] 역할 기반 권한이 작업을 올바르게 제한함
- [ ] 감사 로그가 완전하고 파싱 가능함
- [ ] 비용 보고서가 정확하게 생성됨
- [ ] 컴플라이언스 스크립트가 모든 머신에서 통과함

### 교육 검증
- [ ] 모든 교육 모듈이 이해관계자에 의해 검토됨
- [ ] 실습 연습이 처음부터 끝까지 올바르게 작동함
- [ ] CLAUDE.md 템플릿이 명확하고 완전함
- [ ] 지원 프로세스가 문서화되고 공유됨
- [ ] 모든 팀에 챔피언이 식별됨

### 메트릭 검증
- [ ] 롤아웃 전에 기준선 측정이 기록됨
- [ ] 대시보드가 실시간 데이터를 정확하게 표시함
- [ ] ROI 계산이 검증된 입력을 사용함
- [ ] 보고 스크립트가 예정대로 실행됨
- [ ] 개선 조치가 추적되고 후속 조치됨

---

## 롤아웃 타임라인

| 주차    | 페이즈             | 활동                                          |
|---------|-------------------|-----------------------------------------------|
| 1-2     | 구축              | 플러그인 개발, 관리 설정                      |
| 3-4     | 통합              | CI/CD 워크플로우, 거버넌스 프레임워크         |
| 5-6     | 파일럿            | 10명 엔지니어 (2팀), 피드백 수집              |
| 7-8     | 반복              | 이슈 수정, 파일럿 피드백 기반 개선            |
| 9-10    | 교육              | 교육 자료, 챔피언 온보딩                      |
| 11-12   | 롤아웃            | 회사 전체 배포 (팀별 단계적)                  |
| 13+     | 최적화            | 메트릭 추적, 지속적 개선                      |

---

## 성공을 위한 팁

### 경영진 후원
엔지니어링 리더십의 지지를 일찍 확보하세요. 예상 ROI와 위험 완화가 포함된 비즈니스 케이스를 발표하세요. 리더십에 대한 정기적인 업데이트로 프로그램의 자금과 지원을 유지하세요.

### 작게 시작하고, 빠르게 확장
열정적인 얼리 어답터인 2팀(10명 엔지니어)의 파일럿으로 시작하세요. 그들의 성공 사례와 피드백이 하향식 지시보다 훨씬 효과적으로 조직 전체의 채택을 이끕니다.

### 모든 것을 측정
롤아웃 전에 기준선을 설정하세요. 배포 전 메트릭 없이는 개선을 입증할 수 없습니다. 첫날 전에 PR 주기 시간, 버그 비율, 테스트 커버리지, 개발자 만족도를 추적하세요.

### 챔피언에 투자
챔피언 프로그램이 채택을 위한 가장 중요한 레버입니다. 챔피언은 동료 지원을 제공하고, 컨텍스트별 팁을 공유하며, 질문과 이슈에 대한 첫 번째 방어선 역할을 합니다.

### CLAUDE.md를 반복
조직 전체 CLAUDE.md는 살아있는 문서입니다. 매월 검토하세요. 코드 리뷰나 지원 티켓에서 반복되는 이슈를 발견하면 CLAUDE.md를 업데이트하여 예방하세요. CLAUDE.md가 구체적이고 정확할수록 Claude Code가 더 잘 수행합니다.

### 자동화와 자율성의 균형
중요한 표준(보안, 테스트)은 시행하되 개발자를 과도하게 제약하지 마세요. 너무 많은 제한은 마찰을 만들고 사람들을 도구에서 멀어지게 합니다. 훅과 게이트를 영향도가 높은 검사에 집중하세요.

---

**예상 시간**: 20-40시간
**난이도**: 전문가
**연습되는 스킬**: 엔터프라이즈 아키텍처, 플러그인 개발, CI/CD, 거버넌스, 교육 설계, 메트릭, 변경 관리
**결과**: 속도, 품질, 개발자 만족도에서 측정 가능한 개선과 함께 50명 이상의 엔지니어에게 서비스하는 프로덕션 준비 엔터프라이즈 Claude Code 배포
