# 빠른 시작 가이드 ⚡

**30분 안에 Claude Code 시작하기**

---

## 1단계: 설치 (5분)

```bash
# Node.js 18+ 필요
node --version

# Claude Code 설치
npm install -g @anthropic-ai/claude-code

# 설치 확인
claude --version
```

---

## 2단계: 첫 세션 (5분)

```bash
# 프로젝트 폴더로 이동 (없으면 생성)
mkdir my-project && cd my-project

# Claude 시작
claude
```

### 기본 대화
```
사용자: 안녕, 넌 뭘 할 수 있어?

Claude: 안녕하세요! 저는 다음을 도와드릴 수 있습니다:
- 파일 읽기, 쓰기, 편집
- 코드 검색 및 분석
- 명령 실행 (npm, git 등)
- Git 작업 (커밋, PR 등)
```

---

## 3단계: 핵심 기능 체험 (15분)

### 파일 생성
```
"hello.js 파일을 만들고 Hello World를 출력하는 코드 작성해줘"
```

### 파일 읽기
```
"hello.js 읽어줘"
"package.json 분석해줘"
```

### 파일 편집
```
"hello.js에 이름을 입력받아 인사하는 기능 추가해줘"
```

### 파일 검색
```
"모든 JavaScript 파일 찾아줘"
"TODO 주석이 있는 곳 찾아줘"
```

### 명령 실행
```
"npm init -y 실행해줘"
"git init 해줘"
```

---

## 4단계: 기본 명령어 (5분)

| 명령어 | 기능 |
|--------|------|
| `/help` | 도움말 |
| `/clear` | 대화 초기화 |
| `/exit` | 종료 |
| `/compact` | 컨텍스트 압축 |

### 세션 관리
```bash
# 이름 지정 세션
claude --session "내프로젝트"

# 세션 재개
claude --resume "내프로젝트"
```

---

## 핵심 개념 요약

### 6가지 핵심 도구
| 도구 | 용도 |
|------|------|
| **Read** | 파일 읽기 |
| **Write** | 새 파일 생성 |
| **Edit** | 파일 수정 |
| **Glob** | 파일 검색 |
| **Grep** | 내용 검색 |
| **Bash** | 명령 실행 |

### @-멘션
```
@파일명     # 파일 참조
@폴더/파일  # 경로 포함
@파일:10-20 # 라인 범위
```

### 권한 모드
| 모드 | 설명 |
|------|------|
| `Y` | 이번만 승인 |
| `n` | 이번만 거부 |
| `always` | 항상 승인 |
| `never` | 항상 거부 |

---

## 팁 5가지

1. **명확하게 요청하기**
   ```
   ❌ "코드 고쳐줘"
   ✅ "handleSubmit 함수에 null 체크 추가해줘"
   ```

2. **@-멘션 활용**
   ```
   "@src/app.js 분석해줘"
   ```

3. **단계적 접근**
   ```
   1. "현재 구조 설명해줘"
   2. "개선점 제안해줘"
   3. "제안대로 수정해줘"
   ```

4. **권한 요청 읽기**
   - 승인 전 변경 내용 확인
   - 의심되면 n으로 거부

5. **CLAUDE.md 작성**
   ```markdown
   # 프로젝트 이름
   ## 개요
   프로젝트 설명
   ## 기술 스택
   사용 기술 목록
   ```

---

## 다음 단계

### 학습 계속하기
1. `lessons/beginner/section-1-getting-started.md` 읽기
2. `exercises/exercise-01-basics.md` 실습
3. `resources/CHEAT-SHEET.md` 북마크

### 추천 실습
```
1. 간단한 함수 작성 요청
2. 파일 검색 및 분석
3. Git 초기화 및 첫 커밋
4. CLAUDE.md 작성
```

---

## 도움이 필요하면

| 상황 | 참고 |
|------|------|
| 명령어 모름 | `resources/CHEAT-SHEET.md` |
| 오류 발생 | `resources/TROUBLESHOOTING.md` |
| 질문 있음 | `resources/FAQ.md` |

---

**축하합니다! Claude Code를 시작했습니다!** 🎉

더 깊이 배우려면 전체 커리큘럼을 확인하세요: `CURRICULUM.md`
