# 연습 18: 엔터프라이즈 배포

**소요 시간**: 1.5시간
**사전 조건**: 레슨 97-100

## 목표
관리 설정, 권한, 모니터링을 통해 안전하고 정책 준수하는 팀 배포를 위한 Claude Code를 구성합니다.

## 셋업
```bash
mkdir ~/enterprise-deploy-lab && cd ~/enterprise-deploy-lab && git init
mkdir -p src/{api,auth,db} tests docs .claude
echo '{ "name": "enterprise-app", "version": "2.0.0", "private": true }' > package.json
echo "export function getUser(id) { return db.query('SELECT * FROM users WHERE id = ?', [id]); }" > src/db/users.js
echo "export function authenticate(token) { return jwt.verify(token, process.env.SECRET_KEY); }" > src/auth/auth.js
echo "export function handleRequest(req, res) { res.json({ status: 'ok' }); }" > src/api/handler.js
git add -A && git commit -m "Initial enterprise project"
```

## 과제
### 과제 1: 엔터프라이즈 관리 설정 생성 (20분)
조직 전체 정책으로 `.claude/settings.json`을 생성합니다:
```json
{
  "permissions": {
    "allow": ["Read", "Grep", "Glob", "WebSearch"],
    "deny": ["Bash(rm -rf *)", "Bash(curl * | bash)", "Bash(chmod 777 *)"]
  }
}
```
그런 다음 팀원을 위한 `docs/user-settings-template.json`을 생성합니다:
```json
{
  "permissions": {
    "allow": ["Bash(npm test)", "Bash(npm run lint)", "Bash(npm run build)", "Bash(git *)"]
  },
  "preferences": { "notifChannel": "terminal", "verbose": false }
}
```
**확인**: 두 파일 모두 유효한 JSON을 포함합니다. `cat .claude/settings.json | claude -p "Is this valid Claude Code settings? List any issues."`를 실행합니다.
---
### 과제 2: 팀을 위한 권한 정책 설정 (15분)
```bash
mkdir -p docs/policies
```
Claude에게 `docs/policies/`에 세 가지 역할 기반 정책 파일을 생성하도록 요청합니다:
- **junior-developer.json**: 파일 읽기, 테스트/린터 실행만 가능. 임의의 bash, 파일 삭제, git config 변경 거부.
- **senior-developer.json**: 주니어 권한에 추가로 빌드, 마이그레이션, main이 아닌 브랜치로의 push 가능. force-push 거부.
- **tech-lead.json**: 시니어 권한에 추가로 CI/CD 설정 변경, 배포 스크립트, 프로덕션 로그 가능. 여전히 `rm -rf`와 main으로의 force-push 거부.

**확인**: `ls docs/policies/`가 세 개의 파일을 모두 보여줍니다. Claude에게 요청합니다: "Read all files in docs/policies/ and verify junior < senior < tech-lead permission hierarchy is consistent."
---
### 과제 3: 컴플라이언스를 위한 보안 훅 구성 (20분)
하드코딩된 시크릿을 차단하는 pre-commit 훅을 생성합니다:
```bash
cat > .git/hooks/pre-commit << 'HOOK'
#!/bin/bash
SECRETS=$(grep -rniE '(password|secret|api_key|token)\s*[:=]\s*"[^"]+"' --include="*.js" src/ 2>/dev/null)
if [ -n "$SECRETS" ]; then
  echo "BLOCKED: Hardcoded secrets detected:" && echo "$SECRETS" && exit 1
fi
echo "Compliance checks passed."
HOOK
chmod +x .git/hooks/pre-commit
```
테스트합니다:
```bash
echo 'const API_KEY = "sk-1234567890";' > src/api/config.js
git add src/api/config.js && git commit -m "Add config"   # 실패해야 함

echo 'const API_KEY = process.env.API_KEY;' > src/api/config.js
git add src/api/config.js && git commit -m "Add config"   # 성공해야 함
```
**확인**: 첫 번째 커밋이 차단됩니다. 두 번째가 성공합니다. `git log --oneline -1`이 안전한 커밋을 보여줍니다.
---
### 과제 4: 온보딩 CLAUDE.md 생성 (15분)
Claude에게 프로젝트 루트에 다음을 다루는 `CLAUDE.md`를 생성하도록 요청합니다:
1. **프로젝트 개요**: 인증, 데이터베이스, 핸들러 레이어가 있는 엔터프라이즈 Node.js API
2. **코드 규칙**: ESM import, 공개 함수에 JSDoc, 커스텀 AppError 클래스, console.log 금지
3. **테스트**: 모든 함수에 테스트 필요, assert 모듈 사용, 테스트가 src/ 구조를 미러링
4. **보안**: 하드코딩된 시크릿 금지, 파라미터화된 SQL, 입력 검증, eval() 금지
5. **Git 워크플로우**: main에서 브랜치, conventional commits, PR에 리뷰 필요, force-push 금지
6. **Claude 지침**: 항상 JSDoc 포함, 항상 테스트 추가, npm test 실행, 보안 우려사항 플래그

**확인**: 프로젝트에서 새 Claude 세션을 시작합니다. "What are the code conventions?"라고 물어봅니다 -- Claude가 CLAUDE.md 규칙을 참조해야 합니다.
---
### 과제 5: 사용량 모니터링 및 비용 추적 설정 (20분)
```bash
mkdir -p scripts
```
Claude에게 두 개의 스크립트를 생성하도록 요청합니다:
- **scripts/track-usage.sh**: Claude 세션 로그를 파싱하고, 날짜/모델/토큰/비용을 추출하고, `~/claude-usage-log.csv`에 추가하고, 오늘의 요약을 출력합니다.
- **scripts/usage-report.sh**: CSV를 읽고, 주간 비용, 일일 평균, 가장 많이 사용된 모델, 전체 세션을 계산합니다. $10을 초과하는 날에 플래그를 표시합니다.

```bash
chmod +x scripts/track-usage.sh scripts/usage-report.sh
```
**확인**: 두 스크립트가 모두 실행 가능합니다. `bash scripts/track-usage.sh`가 오류 없이 실행됩니다 (또는 명확한 "no logs found" 메시지를 표시합니다). `bash scripts/usage-report.sh`가 형식화된 보고서를 출력하거나 빈 CSV에 헤더를 생성합니다.

## 완료 기준
- [ ] 허용/거부 목록이 있는 관리 설정이 `.claude/settings.json`에 있음
- [ ] 일관된 계층 구조를 가진 세 가지 역할 기반 권한 정책
- [ ] Pre-commit 훅이 시크릿을 차단하고 깨끗한 코드를 통과시킴
- [ ] CLAUDE.md가 규칙, 보안, Claude 관련 지침을 다룸
- [ ] 사용량 추적 및 보고 스크립트가 작동함

## 보너스 도전
- **30일 롤아웃 계획**: 20명 팀을 위한 `docs/rollout-plan.md`를 작성합니다: 1주차 -- Opus를 사용하는 3명의 파일럿 테크 리드; 2주차 -- Sonnet 기본으로 8명의 시니어 개발자; 3주차 -- 역할 기반 정책으로 전체 팀; 4주차 -- 메트릭 검토, 조정, 교훈 문서화. 각 단계의 성공 기준을 포함합니다.
- **정책 테스트 스위트**: 각 역할에 대해 허용/거부 작업을 시뮬레이션하고 합격/불합격 보고서를 출력하는 테스트를 작성합니다.
