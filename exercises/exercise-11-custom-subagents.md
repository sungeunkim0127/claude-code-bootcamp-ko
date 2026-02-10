# 연습 11: 사용자 정의 서브에이전트 구축
**소요 시간**: 1.5시간
**사전 조건**: 레슨 63-70

## 목표
복잡한 작업을 전문화된 도구 제한 작업 단위로 분할하는 사용자 정의 서브에이전트를 설계, 구성 및 오케스트레이션합니다.

## 설정
```bash
mkdir -p ~/subagent-lab/src ~/subagent-lab/tests ~/subagent-lab/reports
cd ~/subagent-lab
git init
cat > src/app.js << 'EOF'
const express = require("express");
const app = express();

function calculateTotal(items) {
  let total = 0;
  for (let i = 0; i <= items.length; i++) { // off-by-one bug
    total += items[i].price * items[i].qty;
  }
  return total;
}

app.get("/total", (req, res) => {
  const items = JSON.parse(req.query.items);
  res.json({ total: calculateTotal(items) });
});

app.listen(3000);
module.exports = { calculateTotal };
EOF
npm init -y && npm install express
```

## 과제

### 과제 1: 코드 분석기 서브에이전트 생성 (15분)
`~/subagent-lab/subagents/code-analyzer/SUBAGENT.md` 경로에 읽기 전용 코드 분석 서브에이전트를 정의하는 `SUBAGENT.md` 파일을 생성하세요.

파일에 다음이 포함되어야 합니다:
- 명확한 역할 설명: "You are a static code analyzer. You examine source files for bugs, code smells, and style issues."
- `file`, `line`, `severity`, `message` 필드를 포함한 구조화된 JSON 보고서를 요구하는 출력 형식 명세.
- 소스 파일을 절대 수정하지 말라는 명시적 지침.

```bash
mkdir -p ~/subagent-lab/subagents/code-analyzer
```

**확인**: `cat ~/subagent-lab/subagents/code-analyzer/SUBAGENT.md`를 실행하여 역할, 출력 형식, 읽기 전용 제약 조건이 포함되어 있는지 확인하세요.

---

### 과제 2: 도구 제한 구성 (15분)
`code-analyzer` 서브에이전트 디렉토리 내에 서브에이전트를 읽기 전용 도구로 제한하는 `.claude/settings.json`을 추가하세요.

설정에 다음이 포함되어야 합니다:
- `Read`, `Glob`, `Grep` 도구만 허용.
- `Write`, `Edit`, `Bash`, `NotebookEdit` 도구 거부.
- `SUBAGENT.md`를 시스템 프롬프트 소스로 설정.

```bash
mkdir -p ~/subagent-lab/subagents/code-analyzer/.claude
```

**확인**: `cat ~/subagent-lab/subagents/code-analyzer/.claude/settings.json`을 실행하여 허용 목록에 읽기 도구만 있는지 확인하세요.

---

### 과제 3: 모델 선택 로직 설정 (15분)
`SUBAGENT.md`를 편집하여 모델 선택 지침을 포함하세요:

- 단순 단일 파일 린팅 작업 (100줄 미만)의 경우, 서브에이전트는 속도와 비용 효율성을 위해 Haiku를 요청해야 합니다.
- 복잡한 다중 파일 분석이나 아키텍처 리뷰의 경우, 서브에이전트는 더 깊은 추론을 위해 Sonnet을 요청해야 합니다.

이러한 규칙과 각 티어에 대한 예시 트리거 구문을 포함하는 `## Model Selection` 섹션을 `SUBAGENT.md`에 추가하세요.

**확인**: `SUBAGENT.md`에 명시적인 Haiku 및 Sonnet 기준이 포함된 `## Model Selection` 제목이 있는지 확인하세요.

---

### 과제 4: 코드 분석기 서브에이전트 테스트 (20분)
Claude Code를 사용하여 `src/app.js`에 대해 코드 분석기 서브에이전트를 호출하세요.

```bash
cd ~/subagent-lab
claude --print "Using the code-analyzer subagent instructions in subagents/code-analyzer/SUBAGENT.md, analyze src/app.js and produce a JSON report."
```

출력을 평가하세요:
- 루프 경계의 off-by-one 버그를 식별하는가?
- 보고서가 지정된 JSON 형식인가?
- 서브에이전트가 어떤 파일도 수정하지 않았는가?

**확인**: `~/subagent-lab`에서 `git status`를 실행하여 소스 파일이 수정되지 않았는지 확인하세요. JSON 출력에 루프 버그를 참조하는 최소 하나의 발견 사항이 있는지 검토하세요.

---

### 과제 5: 수정 제안 서브에이전트 생성 (15분)
`~/subagent-lab/subagents/fix-suggester/SUBAGENT.md` 경로에 다음과 같은 두 번째 서브에이전트를 생성하세요:

- 코드 분석기가 생성한 JSON 보고서를 입력으로 받습니다.
- 각 발견 사항에 대해 패치 스타일 수정 제안 (통합 diff 형식)을 생성합니다.
- `Read`와 `Bash` (`diff` 실행용)는 사용 가능하지만, `Write`나 `Edit`는 사용 불가합니다.

```bash
mkdir -p ~/subagent-lab/subagents/fix-suggester/.claude
```

**확인**: `cat ~/subagent-lab/subagents/fix-suggester/SUBAGENT.md`를 실행하여 분석기의 JSON 형식을 참조하고 diff를 출력하는지 확인하세요.

---

### 과제 6: 두 서브에이전트 오케스트레이션 (15분)
`~/subagent-lab/`에 다음을 지시하는 최상위 `CLAUDE.md`를 생성하세요:

1. 먼저 `src/`의 모든 파일에 대해 code-analyzer 서브에이전트를 호출합니다.
2. 결과 JSON 보고서를 fix-suggester 서브에이전트에 전달합니다.
3. 결합된 출력을 제시합니다: 발견 사항 및 제안된 패치.

전체 파이프라인을 테스트하세요:
```bash
cd ~/subagent-lab
claude --print "Run the full analysis and fix-suggestion pipeline on the src/ directory."
```

**확인**: 출력에 JSON 분석 보고서와 off-by-one 버그에 대한 최소 하나의 통합 diff 제안이 모두 포함되어 있는지 확인하세요.

## 완료 기준
- [ ] 역할, 출력 형식, 읽기 전용 제약 조건이 포함된 코드 분석기 `SUBAGENT.md` 존재
- [ ] 읽기 작업만 허용하도록 도구 제한이 올바르게 구성됨
- [ ] 모델 선택 섹션이 Haiku 및 Sonnet 사용 기준을 정의
- [ ] 코드 분석기가 파일 수정 없이 버그를 올바르게 식별
- [ ] 수정 제안 서브에이전트가 분석기 출력에서 diff 형식 패치를 생성
- [ ] 오케스트레이션 `CLAUDE.md`가 두 서브에이전트를 파이프라인으로 연결
