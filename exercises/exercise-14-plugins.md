# 연습 14: 첫 번째 플러그인 만들기
**소요 시간**: 2시간
**사전 조건**: 레슨 81-86

## 목표
스킬, 훅, 로컬 테스트, 문서화를 포함하여 Claude Code 플러그인을 처음부터 완성합니다.

## 설정
```bash
mkdir -p ~/plugin-lab/my-plugin/skills ~/plugin-lab/my-plugin/hooks
mkdir -p ~/plugin-lab/test-project/src
cd ~/plugin-lab/test-project
git init
cat > src/index.js << 'EOF'
function greet(name) {
  console.log("Hello, " + name);
}

function farewell(name) {
  console.log("Goodbye, " + name);
}

greet("World");
farewell("World");
EOF
cat > src/utils.js << 'EOF'
function isEmpty(value) {
  return value === null || value === undefined || value === "";
}

function capitalize(str) {
  return str.charAt(0).toUpperCase() + str.slice(1);
}

module.exports = { isEmpty, capitalize };
EOF
git add -A && git commit -m "Initial test project"
```

## 과제

### 과제 1: 플러그인 매니페스트 생성 (20분)
플러그인의 메타데이터와 기능을 정의하는 `plugin.json` 매니페스트 파일을 생성하세요.

```bash
cd ~/plugin-lab/my-plugin
```

`~/plugin-lab/my-plugin/plugin.json`을 다음 구조로 생성하세요:
- `name`: `"code-quality-plugin"`
- `version`: `"1.0.0"`
- `description`: 플러그인의 목적에 대한 짧은 설명 (코드 품질 검사 및 컨벤션 적용).
- `author`: 본인의 이름 또는 핸들.
- `skills`: 과제 2에서 생성할 스킬 파일을 참조하는 배열.
- `hooks`: 과제 3에서 생성할 훅 파일을 참조하는 배열.
- `permissions`: 플러그인이 `Read`, `Glob`, `Grep`, `Bash` 도구를 필요로 한다고 선언.

JSON이 유효한지 확인하세요:
```bash
node -e "JSON.parse(require('fs').readFileSync('plugin.json','utf8')); console.log('Valid JSON')"
```

**확인**: 위 명령이 오류 없이 `Valid JSON`을 출력하는지 확인하세요. 파일에 필수 필드 (`name`, `version`, `description`, `skills`, `hooks`, `permissions`)가 모두 포함되어 있는지 확인하세요.

---

### 과제 2: 플러그인에 스킬 추가 (25분)
코드 품질 리뷰를 수행하는 스킬을 생성하세요. 스킬 파일은 `~/plugin-lab/my-plugin/skills/review.md`에 위치합니다.

스킬에 다음이 정의되어야 합니다:
- **트리거**: 사용자가 `/review`를 입력하거나 "code quality review"를 요청할 때 스킬이 활성화됩니다.
- **동작**: 스킬은 `Glob`과 `Read`를 사용하여 현재 프로젝트의 모든 `.js` 파일을 스캔한 다음 다음을 확인합니다:
  1. 20줄을 초과하는 함수.
  2. 함수를 정의하는 파일에서 누락된 `module.exports`.
  3. `const`나 `let` 대신 `var` 사용.
  4. 프로덕션에서 제거해야 할 Console.log 문.
- **출력 형식**: File, Line, Issue, Severity (low/medium/high) 열이 있는 마크다운 테이블.

스킬이 생성해야 하는 출력 예시:
```
| File          | Line | Issue                        | Severity |
|---------------|------|------------------------------|----------|
| src/index.js  | 2    | console.log in production    | medium   |
| src/index.js  | 6    | console.log in production    | medium   |
| src/index.js  | -    | Missing module.exports       | low      |
```

**확인**: `cat ~/plugin-lab/my-plugin/skills/review.md`를 실행하여 트리거 정의, 네 가지 검사 규칙, 출력 형식 명세가 포함되어 있는지 확인하세요.

---

### 과제 3: 플러그인에 훅 추가 (25분)
모든 커밋 전에 자동으로 실행되는 훅을 생성하세요. 훅 파일은 `~/plugin-lab/my-plugin/hooks/pre-commit-check.sh`에 위치합니다.

훅은 다음을 수행해야 합니다:
1. 스테이지된 `.js` 파일에서 `debugger` 문과 `TODO` 코멘트를 스캔합니다.
2. `debugger` 문이 발견되면 커밋을 차단하고 해당 파일과 줄 번호를 나열하는 명확한 오류 메시지를 출력합니다.
3. `TODO` 코멘트가 발견되면 경고를 출력합니다 (하지만 커밋은 진행 허용).
4. 성공 시 종료 코드 0, `debugger` 문이 발견된 경우 종료 코드 1로 종료합니다.

```bash
cat > ~/plugin-lab/my-plugin/hooks/pre-commit-check.sh << 'HOOKEOF'
#!/bin/bash
# Pre-commit hook: block debugger statements, warn on TODOs

STAGED_JS=$(git diff --cached --name-only --diff-filter=ACM | grep '\.js$')

if [ -z "$STAGED_JS" ]; then
  exit 0
fi

HAS_ERROR=0

# Check for debugger statements
for file in $STAGED_JS; do
  DEBUGGER_LINES=$(grep -n 'debugger' "$file" 2>/dev/null)
  if [ -n "$DEBUGGER_LINES" ]; then
    echo "ERROR: debugger statement found in $file:"
    echo "$DEBUGGER_LINES"
    HAS_ERROR=1
  fi
done

# Check for TODO comments (warning only)
for file in $STAGED_JS; do
  TODO_LINES=$(grep -n 'TODO' "$file" 2>/dev/null)
  if [ -n "$TODO_LINES" ]; then
    echo "WARNING: TODO found in $file:"
    echo "$TODO_LINES"
  fi
done

exit $HAS_ERROR
HOOKEOF
chmod +x ~/plugin-lab/my-plugin/hooks/pre-commit-check.sh
```

이제 `hooks` 배열이 이벤트 유형 `"pre-commit"`으로 `"hooks/pre-commit-check.sh"`를 참조하도록 `plugin.json`에 훅을 등록하세요.

**확인**: `~/plugin-lab/test-project` 내에서 (파일을 스테이징한 후) `bash ~/plugin-lab/my-plugin/hooks/pre-commit-check.sh`를 실행하여 console.log에 대한 경고와 함께 정상 종료되고 debugger 오류가 없는지 확인하세요.

---

### 과제 4: 플러그인 로컬 테스트 (25분)
테스트 프로젝트에 플러그인을 설치하고 테스트하세요.

단계 1 -- 플러그인을 테스트 프로젝트에 연결:
```bash
cd ~/plugin-lab/test-project
mkdir -p .claude/plugins
ln -s ~/plugin-lab/my-plugin .claude/plugins/code-quality-plugin
```

단계 2 -- 스킬 테스트:
```bash
cd ~/plugin-lab/test-project
claude --print "Run /review on this project"
```

출력을 평가하세요:
- `src/index.js`와 `src/utils.js`를 모두 스캔하는가?
- `src/index.js`의 `console.log` 호출을 플래그하는가?
- `src/index.js`의 누락된 `module.exports`를 플래그하는가?
- 출력이 지정된 마크다운 테이블 형식인가?

단계 3 -- debugger 문을 추가하여 훅 테스트:
```bash
cd ~/plugin-lab/test-project
echo "debugger;" >> src/index.js
git add src/index.js
bash .claude/plugins/code-quality-plugin/hooks/pre-commit-check.sh
echo "Exit code: $?"
```

단계 4 -- debugger를 제거하고 훅이 통과하는지 확인:
```bash
cd ~/plugin-lab/test-project
git checkout -- src/index.js
git add src/index.js
bash .claude/plugins/code-quality-plugin/hooks/pre-commit-check.sh
echo "Exit code: $?"
```

**확인**: `debugger`가 있을 때 훅이 종료 코드 1로 종료하고 (단계 3), 없을 때 종료 코드 0으로 종료하는지 확인하세요 (단계 4). 스킬 출력에 발견 사항이 포함된 마크다운 테이블이 있는지 확인하세요.

---

### 과제 5: 플러그인 문서 작성 (20분)
다른 사용자를 위해 플러그인을 문서화하는 `README.md`를 `~/plugin-lab/my-plugin/README.md`에 생성하세요.

README에 다음이 포함되어야 합니다:
1. **플러그인 이름과 설명** -- 한 단락 요약.
2. **설치** -- 프로젝트에 플러그인을 연결하는 단계별 지침.
3. **스킬 참조** -- `/review` 스킬, 트리거, 검사 내용, 출력 예시를 설명.
4. **훅 참조** -- pre-commit 훅, 차단 대상, 경고 대상을 설명.
5. **구성** -- 사용자가 커스터마이징할 수 있는 환경 변수나 설정을 나열.
6. **권한** -- 플러그인이 필요로 하는 도구와 그 이유를 명시.

```bash
cd ~/plugin-lab/my-plugin
```

**확인**: README에 위에 나열된 여섯 개 섹션이 모두 포함되어 있는지 확인하세요. `grep -c "^##" ~/plugin-lab/my-plugin/README.md`를 실행하여 최소 6개의 섹션 제목이 있는지 확인하세요.

---

### 과제 6: 플러그인 패키징 및 검증 (15분)
전체 플러그인 구조에 대한 최종 검증을 수행하세요:

```bash
cd ~/plugin-lab/my-plugin
echo "=== Structure ==="
find . -type f | sort
echo ""
echo "=== Manifest ==="
node -e "const p=JSON.parse(require('fs').readFileSync('plugin.json','utf8')); console.log('Name:', p.name); console.log('Skills:', p.skills.length); console.log('Hooks:', p.hooks.length); console.log('Permissions:', p.permissions.join(', '));"
echo ""
echo "=== Hook executable? ==="
test -x hooks/pre-commit-check.sh && echo "Yes" || echo "No"
```

출력에 다음이 표시되는지 확인하세요:
- 최소 5개 파일: `plugin.json`, `skills/review.md`, `hooks/pre-commit-check.sh`, `README.md`, 및 기타 추가 설정.
- 매니페스트가 1개 스킬, 1개 훅, 올바른 권한을 보고.
- 훅 파일이 실행 가능.

**확인**: 세 가지 검증 항목이 모두 통과하는지 확인하세요. 플러그인 디렉토리가 자체 완결적이고 배포 준비가 되었는지 확인하세요.

## 완료 기준
- [ ] `plugin.json`이 name, version, description, skills, hooks, permissions를 포함한 유효한 JSON임
- [ ] `/review` 스킬이 트리거, 네 가지 코드 품질 검사, 마크다운 테이블 출력을 정의
- [ ] Pre-commit 훅이 `debugger` 문을 차단하고 `TODO` 코멘트에 대해 경고
- [ ] 플러그인이 로컬에서 테스트됨: 스킬이 발견 사항을 생성하고 훅이 규칙을 적용
- [ ] README가 필수 여섯 개 섹션을 모두 문서화
- [ ] 최종 검증이 올바른 구조, 매니페스트, 파일 권한을 확인
