# Claude Code 치트시트 📋

모든 명령어와 패턴을 빠르게 참조하세요.

---

## 🖥️ CLI 명령어

### 기본 명령어
```bash
claude                    # Claude Code 시작
claude --help             # 도움말 보기
claude --version          # 버전 확인
claude --plan             # 플랜 모드로 시작
claude --thinking         # 확장 사고 모드
claude --verbose          # 상세 출력 모드
```

### 세션 관리
```bash
claude --session "이름"   # 이름 있는 세션 시작
claude --resume "이름"    # 이전 세션 재개
claude --print "질문"     # 단일 질문 후 종료
```

### 파이프 사용
```bash
cat file.js | claude "이 코드 설명해줘"
git diff | claude "변경사항 검토해줘"
```

---

## ⌨️ 세션 내 명령어

### 기본 명령어
| 명령어 | 설명 |
|--------|------|
| `/help` | 도움말 표시 |
| `/clear` | 대화 초기화 |
| `/exit` | 세션 종료 |
| `/compact` | 컨텍스트 압축 |

### 세션 관리
| 명령어 | 설명 |
|--------|------|
| `/name 이름` | 세션 이름 지정 |
| `/history` | 이전 세션 목록 |
| `/resume 이름` | 세션 재개 |
| `/session` | 현재 세션 정보 |

### 모드 전환
| 명령어 | 설명 |
|--------|------|
| `/settings` | 설정 보기 |
| `/usage` | 토큰 사용량 확인 |
| `/tasks` | 백그라운드 작업 목록 |

### 키보드 단축키
| 단축키 | 동작 |
|--------|------|
| `Ctrl+C` | 취소/중단 |
| `Ctrl+B` | 백그라운드로 보내기 |
| `Shift+Tab` (2회) | 플랜 모드 전환 |

---

## 🔧 핵심 도구

### Read (파일 읽기)
```
"app.js 읽어줘"
"@src/utils/helper.ts 설명해줘"
"@config.json:10-50 보여줘"     # 특정 라인
```

### Write (파일 생성)
```
"hello.js 파일 만들어줘"
"utils/logger.ts 새로 생성해줘"
```

### Edit (파일 편집)
```
"함수 이름을 handleClick에서 onClick으로 변경해줘"
"에러 처리 추가해줘"
"console.log 전부 삭제해줘"
```

### Glob (파일 검색)
```
"모든 TypeScript 파일 찾아줘"     # **/*.ts
"src 폴더의 테스트 파일 찾아줘"   # src/**/*.test.ts
"설정 파일들 목록 보여줘"         # *.config.js
```

### Grep (내용 검색)
```
"TODO 주석 모두 찾아줘"
"useState 사용하는 곳 찾아줘"
"handleError 함수 정의 찾아줘"
```

### Bash (명령 실행)
```
"npm test 실행해줘"
"git status 보여줘"
"현재 브랜치 확인해줘"
```

---

## 📁 @-멘션 문법

### 파일 참조
```
@파일명.js           # 파일 전체
@파일명.js:10        # 특정 라인
@파일명.js:10-50     # 라인 범위
@폴더/파일.ts        # 경로 포함
```

### 예시
```
"@src/App.tsx 리팩토링해줘"
"@package.json 의존성 분석해줘"
"@utils/api.ts:25-40 설명해줘"
```

---

## 🔍 검색 패턴

### Glob 패턴
| 패턴 | 의미 |
|------|------|
| `*.js` | 현재 폴더 JS 파일 |
| `**/*.ts` | 모든 하위 폴더 TS 파일 |
| `src/**/*.tsx` | src 하위 TSX 파일 |
| `*.{js,ts}` | JS 또는 TS 파일 |
| `test/*.test.js` | test 폴더 테스트 파일 |

### Grep 패턴 (정규식)
| 패턴 | 의미 |
|------|------|
| `TODO` | TODO 문자열 |
| `function\s+\w+` | 함수 선언 |
| `import.*from` | import 문 |
| `console\.log` | console.log 호출 |

---

## 🤖 서브에이전트

### 내장 에이전트
| 타입 | 용도 | 도구 접근 |
|------|------|----------|
| `Explore` | 코드베이스 분석 | 읽기 전용 |
| `Plan` | 구현 계획 | 읽기 전용 |
| `general-purpose` | 복잡한 작업 | 전체 |

### 사용법
```
"Explore 에이전트로 아키텍처 분석해줘"
"서브에이전트로 테스트 작성해줘"
"백그라운드에서 분석해줘"
```

---

## ⚙️ 스킬 시스템

### 스킬 호출
```
/스킬명
/스킬명 인자값
```

### SKILL.md 구조
```markdown
---
name: 스킬이름
description: 스킬 설명
commands:
  - 명령어1
  - 명령어2
tools:
  - Read
  - Write
---

# 스킬 지시사항

여기에 Claude가 따를 지시사항 작성
```

### 스킬 위치
```
~/.claude/skills/        # 사용자 스킬
.claude/skills/          # 프로젝트 스킬
```

---

## 🪝 훅 시스템

### 훅 이벤트
| 이벤트 | 시점 |
|--------|------|
| `PreToolUse` | 도구 실행 전 |
| `PostToolUse` | 도구 실행 후 |
| `Permission` | 권한 요청 시 |
| `Notification` | 알림 발생 시 |

### 설정 예시
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit:.*\\.ts$",
        "command": "npx prettier --write \"$FILE_PATH\""
      }
    ]
  }
}
```

---

## 📝 CLAUDE.md 템플릿

```markdown
# 프로젝트 이름

## 개요
프로젝트 설명

## 기술 스택
- Frontend: React, TypeScript
- Backend: Node.js, Express
- Database: PostgreSQL

## 프로젝트 구조
src/
├── components/  # React 컴포넌트
├── api/         # API 라우트
└── utils/       # 유틸리티

## 코딩 컨벤션
- 함수형 컴포넌트 사용
- async/await 선호
- 에러 핸들링 필수

## 명령어
- npm run dev    # 개발 서버
- npm test       # 테스트
- npm run build  # 빌드
```

---

## 🔧 설정 파일 위치

| 범위 | 위치 |
|------|------|
| 사용자 | `~/.claude/settings.json` |
| 프로젝트 (공유) | `.claude/settings.json` |
| 프로젝트 (개인) | `.claude/settings.local.json` |
| 기업 | 관리 설정 |

---

## 📊 권한 모드

| 모드 | 설명 |
|------|------|
| `normal` | 각 작업에 권한 요청 |
| `auto-accept` | 허용된 작업 자동 승인 |
| `plan` | 읽기 전용 탐색 후 계획 |

---

## 💡 유용한 패턴

### 코드 리뷰
```
"이 PR 변경사항 리뷰해줘"
"보안 취약점 확인해줘"
"성능 개선점 찾아줘"
```

### 리팩토링
```
"이 함수 가독성 개선해줘"
"중복 코드 제거해줘"
"타입 안전성 추가해줘"
```

### 디버깅
```
"이 에러 왜 발생하는지 분석해줘"
"null 체크 추가해줘"
"엣지 케이스 처리해줘"
```

### 문서화
```
"JSDoc 주석 추가해줘"
"README 작성해줘"
"API 문서 생성해줘"
```

---

## 🚀 생산성 팁

1. **@-멘션 활용**: 파일 직접 참조
2. **병렬 요청**: 독립적인 작업 동시 실행
3. **플랜 모드**: 복잡한 작업 전 계획
4. **/compact**: 긴 세션에서 정기적 압축
5. **CLAUDE.md**: 프로젝트 컨텍스트 유지

---

**이 치트시트를 북마크해두고 참조하세요!** 📌
