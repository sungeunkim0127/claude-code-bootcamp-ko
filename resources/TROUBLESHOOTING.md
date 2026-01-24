# 문제 해결 가이드 🔧

일반적인 문제와 해결 방법입니다.

---

## 설치 문제

### Claude Code가 설치되지 않음

**증상**: `claude: command not found`

**해결**:
```bash
# npm 전역 설치 확인
npm list -g @anthropic-ai/claude-code

# 재설치
npm install -g @anthropic-ai/claude-code

# PATH 확인
echo $PATH
```

### Node.js 버전 오류

**증상**: `Error: Requires Node.js 18+`

**해결**:
```bash
# 현재 버전 확인
node --version

# nvm으로 업데이트
nvm install 18
nvm use 18
```

---

## 도구 관련 문제

### Edit 도구 "string not found" 오류

**증상**: Edit이 실패하고 문자열을 찾을 수 없다고 함

**원인**:
- 공백/탭이 정확히 일치하지 않음
- 파일 내용이 변경됨
- 특수 문자 이스케이프 문제

**해결**:
```
1. Read로 파일을 먼저 읽어 정확한 내용 확인
2. 공백과 탭을 정확히 복사
3. 더 많은 컨텍스트 포함 (전후 코드)
4. replace_all 사용 시 주의
```

### Write 도구가 기존 파일 덮어씀

**증상**: 파일 편집을 원했는데 전체가 교체됨

**해결**:
```
파일 수정 시에는 Write가 아닌 Edit 사용
- Write: 새 파일 또는 전체 교체
- Edit: 기존 파일 부분 수정
```

### Bash 명령이 실행되지 않음

**증상**: 권한 거부 또는 명령 실패

**해결**:
```bash
# 권한 설정 확인
~/.claude/settings.json의 permissions.allow 확인

# 명령어 직접 테스트
터미널에서 같은 명령 실행해보기

# 경로 확인
which npm, which git 등
```

---

## 컨텍스트 문제

### Claude가 이전 대화를 잊어버림

**증상**: 앞서 논의한 내용을 모르는 것처럼 행동

**해결**:
```
1. /compact 실행하여 컨텍스트 압축
2. 이전 결정을 명시적으로 다시 언급
3. CLAUDE.md에 중요 정보 저장
4. 새 주제는 새 세션 시작
```

### 토큰 한도 초과

**증상**: 컨텍스트가 너무 길다는 오류

**해결**:
```
1. /compact 실행
2. 새 세션 시작
3. 작업을 작은 단위로 나누기
4. @-멘션으로 필요한 파일만 참조
```

---

## 세션 문제

### 세션을 재개할 수 없음

**증상**: `Session not found` 오류

**해결**:
```bash
# 세션 목록 확인
/history

# 정확한 이름으로 재개
claude --resume "정확한-세션-이름"
```

### 세션이 느려짐

**증상**: 응답이 점점 느려짐

**해결**:
```
1. /compact로 컨텍스트 압축
2. 간단한 작업에 Haiku 모델 사용
3. 새 세션 시작 고려
4. 요청을 작게 나누기
```

---

## Git 문제

### Git 명령이 작동하지 않음

**증상**: git status 등이 실패

**해결**:
```bash
# Git 설치 확인
git --version

# 저장소 확인
git status  # 터미널에서 직접

# .git 폴더 존재 확인
ls -la .git
```

### 커밋 실패

**증상**: 커밋이 되지 않음

**해결**:
```
1. staged 파일 확인: git status
2. 사용자 설정 확인: git config user.email
3. 훅 오류 확인: .git/hooks/
4. 권한 문제 확인
```

---

## 스킬 및 훅 문제

### 스킬이 인식되지 않음

**증상**: `/스킬명`이 작동하지 않음

**해결**:
```
1. 파일 위치 확인:
   - ~/.claude/skills/스킬명/SKILL.md
   - .claude/skills/스킬명/SKILL.md

2. SKILL.md 형식 확인:
   - YAML 프론트매터 필수
   - name 필드 필수

3. Claude 재시작
```

### 훅이 실행되지 않음

**증상**: 훅이 트리거되지 않음

**해결**:
```
1. settings.json 문법 확인 (JSON 유효성)
2. matcher 패턴 확인 (정규식)
3. 명령어 경로 확인
4. --verbose 모드로 디버깅
```

### 훅 명령 실패

**증상**: 훅은 트리거되지만 명령 실패

**해결**:
```bash
# 명령어 직접 테스트
터미널에서 훅 명령 실행해보기

# 경로 확인
절대 경로 사용 권장

# 오류 무시 설정
"command": "명령어 || true"
```

---

## MCP 문제

### MCP 서버 연결 실패

**증상**: MCP 서버에 연결할 수 없음

**해결**:
```
1. 서버 설치 확인:
   npm list -g mcp-server-xxx

2. 설정 확인:
   ~/.claude/settings.json의 mcp 섹션

3. 인증 정보 확인:
   API 키, 토큰 등

4. 서버 로그 확인
```

### MCP 도구가 나타나지 않음

**증상**: MCP 도구를 사용할 수 없음

**해결**:
```
1. MCP 서버가 실행 중인지 확인
2. 설정의 command 경로 확인
3. Claude 재시작
4. "MCP 도구 목록 보여줘" 요청
```

---

## 성능 문제

### 응답이 너무 느림

**해결**:
```
1. Haiku 모델 사용 (간단한 작업)
2. /compact 자주 사용
3. 요청 크기 줄이기
4. 병렬 요청 활용
```

### 높은 토큰 사용량

**해결**:
```
1. 적절한 모델 선택
   - Haiku: 간단한 작업
   - Sonnet: 일반 작업
   - Opus: 복잡한 작업

2. 효율적인 프롬프팅
   - 간결하게 요청
   - 필요한 파일만 참조

3. 정기적 컨텍스트 관리
   - /compact 사용
   - 새 세션 시작
```

---

## 권한 문제

### 항상 권한 요청됨

**증상**: 매번 같은 작업에 권한 요청

**해결**:
```json
// ~/.claude/settings.json
{
  "permissions": {
    "allow": [
      "Read",
      "Glob",
      "Grep",
      "Bash(npm test*)",
      "Bash(git status)"
    ]
  }
}
```

### 위험한 명령 차단

**증상**: 특정 명령이 차단됨

**해결**:
```
1. 차단된 이유 확인
2. 정말 필요한 명령인지 재고
3. 필요하다면 settings.json에서 허용
4. 보안 위험 인지
```

---

## 일반적인 팁

### 디버깅 모드

```bash
# 상세 출력
claude --verbose

# 도구 호출 확인
대화 중 [Tool: xxx] 메시지 확인
```

### 깨끗한 시작

```bash
# 새 세션
claude

# 또는 명시적으로
/clear
```

### 도움 요청

```
대화 중: "왜 이게 작동하지 않는지 설명해줘"
Claude가 디버깅 도와줄 수 있음
```

---

## 추가 도움

문제가 해결되지 않으면:
1. 공식 문서 확인
2. GitHub Issues 검색
3. 커뮤니티 포럼 질문
4. 최신 버전으로 업데이트

```bash
npm update -g @anthropic-ai/claude-code
```

---

**문제가 있으면 차분하게 하나씩 확인하세요!** 🔍
