# 연습 16: 고급 Git 워크플로우

**소요 시간**: 1시간
**사전 조건**: 레슨 87-92

## 목표
Claude Code로 워크트리, 파이프 stdin, CI/CD 통합, 다중 리포지토리 워크플로우를 마스터합니다.

## 셋업
```bash
mkdir ~/advanced-workflows-lab && cd ~/advanced-workflows-lab
git init
mkdir src tests
echo '{ "name": "workflow-lab", "version": "1.0.0" }' > package.json
echo 'export function add(a, b) { return a + b; }' > src/math.js
echo 'import { add } from "../src/math.js"; console.assert(add(1,2)===3);' > tests/math.test.js
git add -A && git commit -m "Initial commit"
git branch feature/auth && git branch feature/api && git branch bugfix/validation
```

## 과제
### 과제 1: 병렬 개발을 위한 Git 워크트리 (15분)
워크트리를 생성하고 각각에서 Claude를 동시에 실행합니다:
```bash
git worktree add ../wt-auth feature/auth
git worktree add ../wt-api feature/api
git worktree add ../wt-bugfix bugfix/validation
```
세 개의 터미널을 엽니다. 각 워크트리 디렉토리에서 `claude`를 실행합니다. 각각에 다른 과제를 부여합니다:
- **wt-auth**: "Add src/auth.js with login/logout functions using JWT. Include JSDoc."
- **wt-api**: "Add src/api.js with GET/POST handlers returning JSON. Include error handling."
- **wt-bugfix**: "Fix src/math.js to validate inputs are numbers, throw TypeError if not."

**확인**: `git worktree list`가 세 개의 워크트리를 표시합니다. `git -C ../wt-auth diff`가 브랜치별 고유한 변경 사항을 보여줍니다.
---
### 과제 2: Unix 유틸리티로서의 Claude (10분)
비대화형 모드에서 Claude에 데이터를 파이프합니다:
```bash
cat src/math.js | claude -p "Review this code for edge cases"
git -C ../wt-auth diff | claude -p "Write a conventional commit message for these changes"
find src/ -name "*.js" -exec cat {} + | claude -p "List all exported functions"
echo "name,age\nAlice,30\nBob,25" | claude -p "Convert this CSV to JSON"
```
**확인**: 각 명령이 대화형 모드에 진입하지 않고 출력을 반환합니다. 커밋 메시지가 `feat:`/`fix:` 형식을 사용합니다.
---
### 과제 3: CI/CD 자동 리뷰 워크플로우 (15분)
```bash
mkdir -p .github/workflows
```
Claude에게 요청합니다: "Create `.github/workflows/claude-review.yml` that triggers on pull_request, gets the diff, pipes it to `claude -p` for review, and posts a PR comment. Use ANTHROPIC_API_KEY from secrets."

그런 다음 pre-push 훅을 추가합니다:
```bash
cat > .git/hooks/pre-push << 'EOF'
#!/bin/bash
DIFF=$(git diff @{upstream}...HEAD 2>/dev/null)
[ -n "$DIFF" ] && echo "$DIFF" | claude -p "Any obvious bugs or security issues? YES or NO with explanation."
EOF
chmod +x .git/hooks/pre-push
```
**확인**: `.github/workflows/claude-review.yml`이 유효한 YAML로 존재합니다. 훅이 실행 가능합니다: `ls -la .git/hooks/pre-push`.
---
### 과제 4: 다중 리포지토리 조율 (10분)
```bash
mkdir ~/workflow-lab-backend && cd ~/workflow-lab-backend && git init && mkdir src
echo 'export function handleRequest(req) { return { status: 200 }; }' > src/server.js
git add -A && git commit -m "Initial backend"
```
여기서 Claude를 시작하고 말합니다: "Read ~/advanced-workflows-lab/src/api.js, then update src/server.js to implement matching endpoints with aligned request/response formats."

**확인**: `claude -p "Compare ~/advanced-workflows-lab/src/api.js and ~/workflow-lab-backend/src/server.js -- do formats match?"`
---
### 과제 5: 헤드리스 일괄 자동화 (10분)
모든 JS 파일을 리뷰하는 `batch-review.sh`를 생성합니다:
```bash
#!/bin/bash
REPO="${1:-.}" && echo "# Review Report" > review-report.md
for f in $(find "$REPO/src" -name "*.js"); do
  echo "## $(basename $f)" >> review-report.md
  cat "$f" | claude -p "Rate 1-5. Format: **Rating: N/5** then bullet points." >> review-report.md
done
```
```bash
chmod +x batch-review.sh && ./batch-review.sh ~/advanced-workflows-lab
```
**확인**: `review-report.md`에 각 `.js` 파일에 대한 평점이 포함된 섹션이 있습니다.

## 완료 기준
- [ ] 독립적인 Claude 세션이 있는 세 개의 워크트리 생성됨
- [ ] Claude에 stdin을 파이프하고 구조화된 출력을 받음
- [ ] CI/CD 워크플로우와 pre-push 훅 생성됨
- [ ] 일치하는 API 계약으로 다중 리포지토리 조율됨
- [ ] 일괄 리뷰 스크립트가 형식화된 보고서를 생성함

## 보너스 도전
- 워크트리를 생성하고, 프롬프트 파일로 Claude를 실행하고, 커밋하고, 하나의 명령으로 정리하는 `worktree-flow.sh`를 작성합니다: `./worktree-flow.sh feature/cache "Add caching to api.js"`
- `batch-review.sh`를 확장하여 색상 코딩된 평점(초록 4-5, 노랑 3, 빨강 1-2)이 있는 HTML 보고서를 생성합니다.
