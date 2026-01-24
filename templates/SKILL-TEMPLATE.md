# 스킬 템플릿

이 템플릿을 사용하여 커스텀 스킬을 만드세요.

---

## 기본 템플릿

`~/.claude/skills/스킬이름/SKILL.md` 또는 `.claude/skills/스킬이름/SKILL.md`에 저장

```markdown
---
name: 스킬이름
description: 스킬이 하는 일 설명
commands:
  - 명령어1
  - 명령어2
tools:
  - Read
  - Write
  - Edit
  - Glob
  - Grep
---

# 스킬 제목

## 목적
이 스킬의 목적을 설명합니다.

## 사용법
`/명령어1` 또는 `/명령어2`로 호출합니다.

## 수행 절차
1. 첫 번째 단계
2. 두 번째 단계
3. 세 번째 단계

## 출력 형식
결과는 다음 형식으로 반환됩니다:
- 항목 1
- 항목 2
```

---

## 예제 1: 코드 리뷰 스킬

```markdown
---
name: code-review
description: 코드 품질, 보안, 성능을 검토합니다
commands:
  - review
  - 리뷰
tools:
  - Read
  - Glob
  - Grep
---

# 코드 리뷰 스킬

## 리뷰 기준

### 1. 코드 품질
- 가독성
- 유지보수성
- 네이밍 컨벤션

### 2. 보안
- 입력 유효성 검사
- 인증/인가
- 민감 데이터 처리

### 3. 성능
- 알고리즘 효율성
- 리소스 관리
- 캐싱 기회

## 리뷰 절차
1. 지정된 파일 읽기
2. 각 기준으로 분석
3. 문제점을 심각도별로 분류
4. 구체적인 개선안 제시

## 출력 형식

\`\`\`markdown
# 코드 리뷰 결과

## 요약
[전체 평가]

## 발견된 문제

### 치명적
- [문제]: [위치] - [설명]

### 주요
- [문제]: [위치] - [설명]

### 경미
- [문제]: [위치] - [설명]

## 개선 제안
1. [제안 내용]
2. [제안 내용]
\`\`\`
```

---

## 예제 2: 테스트 생성 스킬

```markdown
---
name: test-gen
description: 코드에 대한 테스트 케이스를 생성합니다
commands:
  - test
  - 테스트
tools:
  - Read
  - Write
  - Glob
---

# 테스트 생성 스킬

## 사용법
`/test [파일경로]` - 지정된 파일에 대한 테스트 생성

## 테스트 유형

### 단위 테스트
- 개별 함수 동작 확인
- 경계값 테스트
- 에러 케이스

### 통합 테스트
- 모듈 간 상호작용
- API 엔드포인트

## 생성 절차
1. 소스 파일 읽기
2. 테스트 가능한 함수 식별
3. 각 함수에 대한 테스트 케이스 생성
4. 테스트 파일 작성

## 테스트 구조

\`\`\`javascript
describe('[함수명]', () => {
  describe('성공 케이스', () => {
    it('정상 입력 처리', () => {});
  });

  describe('에러 케이스', () => {
    it('잘못된 입력 처리', () => {});
  });

  describe('엣지 케이스', () => {
    it('빈 입력 처리', () => {});
    it('null 처리', () => {});
  });
});
\`\`\`
```

---

## 예제 3: TODO 찾기 스킬

```markdown
---
name: find-todos
description: 코드베이스에서 TODO 주석을 찾아 보고합니다
commands:
  - todos
  - todo
tools:
  - Grep
  - Read
---

# TODO 찾기 스킬

## 사용법
`/todos` - 프로젝트의 모든 TODO 검색

## 검색 대상
- TODO
- FIXME
- HACK
- XXX

## 우선순위 분류
- **치명적**: FIXME
- **높음**: TODO
- **참고**: XXX, HACK

## 출력 형식

\`\`\`markdown
# TODO 보고서

## 치명적 (FIXME)
- file.js:45 - 설명

## 높음 (TODO)
- file.js:23 - 설명

## 참고 (XXX, HACK)
- file.js:67 - 설명

## 통계
- 총 개수: X
- 치명적: X
- 높음: X
- 참고: X
\`\`\`
```

---

## 프론트매터 옵션

```yaml
---
name: 스킬이름            # 필수
description: 설명         # 필수
commands:                  # 호출 명령어
  - cmd1
  - cmd2
tools:                     # 허용할 도구
  - Read
  - Write
  - Edit
  - Glob
  - Grep
  - Bash
model: sonnet              # 사용할 모델 (haiku, sonnet, opus)
autoInvoke: false          # 자동 호출 여부
---
```

---

## 스킬 저장 위치

| 위치 | 범위 |
|------|------|
| `~/.claude/skills/` | 사용자 전역 |
| `.claude/skills/` | 프로젝트 한정 |

---

## 모범 사례

1. **명확한 이름**: 스킬이 하는 일을 나타내는 이름
2. **상세한 지시사항**: Claude가 따를 구체적인 단계
3. **출력 형식 정의**: 일관된 결과물
4. **최소 권한**: 필요한 도구만 허용
5. **예제 포함**: 사용 예시 제공

---

## 스킬 테스트

```bash
# Claude 세션에서
/스킬명

# 또는
/스킬명 인자값
```

작동하지 않으면:
1. SKILL.md 경로 확인
2. YAML 문법 확인
3. Claude 재시작
