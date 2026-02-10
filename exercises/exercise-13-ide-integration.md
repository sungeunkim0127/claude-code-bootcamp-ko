# 연습 13: IDE 통합
**소요 시간**: 45분
**사전 조건**: 레슨 75-80

## 목표
VS Code 내에서 Claude Code를 구성하고, 인라인 diff 워크플로우를 연습하며, @-멘션, 다중 대화 관리, 키보드 단축키를 활용한 효율적인 습관을 구축합니다.

## 설정
```bash
mkdir -p ~/ide-lab/src ~/ide-lab/tests ~/ide-lab/docs
cd ~/ide-lab
git init
cat > src/calculator.ts << 'EOF'
export class Calculator {
  add(a: number, b: number): number {
    return a + b;
  }

  subtract(a: number, b: number): number {
    return a - b;
  }

  multiply(a: number, b: number): number {
    return a * b;
  }

  divide(a: number, b: number): number {
    return a / b; // no zero-division guard
  }
}
EOF
cat > src/formatter.ts << 'EOF'
export function formatCurrency(amount: number, currency: string): string {
  return currency + amount.toFixed(2);
}

export function formatPercentage(value: number): string {
  return (value * 100).toFixed(1) + "%";
}
EOF
cat > tests/calculator.test.ts << 'EOF'
import { Calculator } from "../src/calculator";

const calc = new Calculator();
console.assert(calc.add(2, 3) === 5, "add failed");
console.assert(calc.subtract(5, 3) === 2, "subtract failed");
console.log("Basic tests passed");
EOF
npm init -y
npx tsc --init --outDir dist --rootDir . --strict true 2>/dev/null || true
git add -A && git commit -m "Initial calculator project"
```

VS Code에서 프로젝트를 여세요:
```bash
code ~/ide-lab
```

## 과제

### 과제 1: VS Code 확장 프로그램 설정 (10분)
Claude Code VS Code 확장 프로그램을 설치하고 구성하세요:

1. VS Code 확장 프로그램 패널을 여세요 (`Ctrl+Shift+X`).
2. "Claude Code"를 검색하고 공식 Anthropic 확장 프로그램을 설치하세요.
3. 사이드바에서 Claude Code 패널을 여세요 (Claude 아이콘을 찾으세요) 또는 명령 팔레트를 사용하세요 (`Ctrl+Shift+P`에서 "Claude Code" 입력).
4. 간단한 프롬프트를 보내 확장 프로그램이 연결되는지 확인하세요: `"What files are in this project?"`
5. 응답에 `src/calculator.ts`, `src/formatter.ts`, `tests/calculator.test.ts`가 나열되는지 확인하세요.

**확인**: Claude Code 패널이 VS Code 사이드바에 표시되고, 프롬프트에 응답하며, 프로젝트 파일을 올바르게 식별하는지 확인하세요.

---

### 과제 2: 인라인 Diff 리뷰 사용 (10분)
Claude에게 변경을 요청하고 인라인 diff 리뷰 워크플로우를 연습하세요:

1. Claude Code 패널에서 다음을 입력하세요: `"Add a zero-division guard to the divide method in src/calculator.ts that throws an Error if b is zero."`
2. Claude가 diff를 제안합니다. 자동 수락 대신 인라인으로 리뷰하세요:
   - 수정된 줄이 에디터에서 강조 표시됩니다.
   - 녹색 줄은 추가, 빨간색 줄은 삭제를 나타냅니다.
   - 각 변경 헝크에서 수락/거부 버튼을 사용하세요.
3. 0으로 나누기 가드 변경을 수락하세요.
4. 이제 다음을 요청하세요: `"Add a modulo method to the Calculator class."`
5. 이번에는 diff를 거부하여 제안을 거절하는 연습을 하세요.

**확인**: `git diff src/calculator.ts`를 실행하여 0으로 나누기 가드만 적용되었고 modulo 메서드는 적용되지 않았는지 확인하세요. divide 메서드는 이제 `b === 0`일 때 `Error`를 throw해야 합니다.

---

### 과제 3: @-멘션으로 파일 참조하기 (10분)
@-멘션을 사용하여 Claude에게 정확한 파일 컨텍스트를 제공하는 연습을 하세요:

1. Claude Code 패널에서 다음을 입력하세요: `@src/formatter.ts Add locale-aware currency formatting using Intl.NumberFormat. Keep the existing functions and add a new formatCurrencyLocale function.`
2. Claude가 분석과 변경 범위를 참조된 파일로 제한하는 것을 관찰하세요.
3. 제안된 변경을 수락하세요.
4. 이제 다중 파일 참조를 사용하세요: `@src/calculator.ts @tests/calculator.test.ts Add tests for the divide method including a test that verifies the zero-division error is thrown.`
5. 테스트 추가를 수락하세요.

**확인**: `npx ts-node tests/calculator.test.ts 2>/dev/null || node tests/calculator.test.ts`를 실행하거나 (또는 파일을 수동으로 검토하여) 새 테스트가 divide 메서드와 0으로 나누기 케이스를 참조하는지 확인하세요.

---

### 과제 4: 다중 대화 관리 (10분)
여러 Claude Code 대화를 동시에 작업하는 연습을 하세요:

1. `+` 버튼이나 명령 팔레트 (`Claude Code: New Conversation`)를 사용하여 새 대화를 시작하세요.
2. 대화 1에서 다음을 요청하세요: `@src/calculator.ts Explain the design of this Calculator class and suggest improvements.`
3. 대화 2에서 다음을 요청하세요: `@src/formatter.ts Write JSDoc comments for every exported function.`
4. Claude Code 패널의 대화 목록을 사용하여 대화 간에 전환하세요.
5. 대화 1을 참조 토론으로 유지하면서 대화 2의 JSDoc 변경 사항을 수락하세요.

각 대화가 자체 컨텍스트와 히스토리를 독립적으로 유지하는 것을 관찰하세요.

**확인**: `src/formatter.ts`에 이제 각 함수에 JSDoc 코멘트가 있는지 확인하세요. 대화 1의 제안은 자동 적용되지 않았습니다 (자문 전용이었음).

---

### 과제 5: 키보드 단축키 구성 (5분)
Claude Code 워크플로우를 빠르게 하기 위한 키보드 단축키를 설정하세요:

1. 키보드 단축키를 여세요: `Ctrl+K Ctrl+S` (또는 macOS에서 `Cmd+K Cmd+S`).
2. "Claude"를 검색하여 사용 가능한 모든 Claude Code 명령을 확인하세요.
3. 다음 단축키를 지정하세요 (또는 기본값을 확인하세요):
   - **Claude Code 패널 열기**: `Ctrl+Shift+L`에 바인딩 (또는 원하는 키).
   - **Diff 수락**: 인라인 제안 수락의 기본 키 바인딩을 확인하세요.
   - **Diff 거부**: 인라인 제안 거부의 기본 키 바인딩을 확인하세요.
   - **새 대화**: `Ctrl+Shift+N`에 바인딩 (또는 원하는 키).
4. 각 단축키가 작동하는지 테스트하세요.
5. 치트 시트 파일을 생성하세요:

```bash
cat > ~/ide-lab/docs/shortcuts.txt << 'EOF'
Claude Code VS Code Shortcuts
==============================
Open Panel:        Ctrl+Shift+L
New Conversation:  Ctrl+Shift+N
Accept Diff:       [your binding]
Reject Diff:       [your binding]
Toggle Inline:     [your binding]
EOF
```

**확인**: 단축키 파일이 `~/ide-lab/docs/shortcuts.txt`에 존재하고 나열된 모든 단축키가 VS Code 인스턴스에서 작동하는지 확인하세요.

## 완료 기준
- [ ] Claude Code VS Code 확장 프로그램이 설치되어 프롬프트에 응답함
- [ ] 인라인 diff 리뷰에서 한 변경을 수락하고 다른 변경을 올바르게 거부함
- [ ] @-멘션이 Claude의 변경 범위를 지정된 파일로 제한함
- [ ] 여러 대화가 별도의 컨텍스트로 독립적으로 관리됨
- [ ] 키보드 단축키가 구성되고 shortcuts.txt에 문서화됨
