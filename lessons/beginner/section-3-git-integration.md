# 섹션 3: Git 통합 (레슨 13-17)

## 개요
Claude Code가 Git과 어떻게 통합되어 버전 관리 워크플로우 전체를 관리하는지 배우세요. 상태 확인과 커밋 생성부터 브랜치 관리, Pull Request 생성, 코드 리뷰까지 — Claude가 Git 작업을 안전하고 효율적으로 처리합니다.

---

## 레슨 13: Claude Code와 Git 기초

### 학습 목표
- Claude로 저장소 상태 확인하기
- diff 보기와 변경사항 이해하기
- 커밋 히스토리 검토하기
- Git 스테이징 영역 이해하기

### 내용

#### Claude가 Git과 상호작용하는 방법

Claude는 **Bash 도구**를 사용하여 Git 명령을 대신 실행합니다. 엄격한 안전 프로토콜을 따릅니다:

- 명시적 허가 없이 절대 force push 하지 않음
- 파괴적 명령(`reset --hard`, `clean -f`)을 묻지 않고 실행하지 않음
- 항상 amend 대신 새 커밋을 생성 (요청하지 않는 한)
- `git add -A` 대신 특정 파일 스테이징을 선호

#### 저장소 상태 확인

**기본 상태 확인**:
```
사용자: "git status 보여줘"

Claude:
  [Bash: git status]
  → "수정된 파일 3개, 추적되지 않는 파일 1개가 있습니다:
     수정됨:
       src/app.js
       src/config.js
       tests/app.test.js
     추적 안됨:
       src/newFeature.js"
```

**빠른 상태 확인**:
```
사용자: "마지막 커밋 이후 뭐가 바뀌었어?"

Claude:
  [Bash: git status]
  [Bash: git diff --stat]
  → "3개 파일 변경, 45줄 추가, 12줄 삭제"
```

#### Diff 보기

**모든 변경사항 보기**:
```
사용자: "diff 보여줘"

Claude:
  [Bash: git diff]
  → + 와 - 마커로 스테이징되지 않은 변경사항 표시
```

**스테이징된 변경사항**:
```
사용자: "커밋 대기 중인 것은?"

Claude:
  [Bash: git diff --staged]
  → 스테이징된 변경사항만 표시
```

**특정 파일 diff**:
```
사용자: "src/app.js에서 뭐가 바뀌었는지 보여줘"

Claude:
  [Bash: git diff src/app.js]
  → 해당 파일의 변경사항만 표시
```

#### 커밋 히스토리 검토

**최근 커밋**:
```
사용자: "최근 커밋 보여줘"

Claude:
  [Bash: git log --oneline -10]
  → a1b2c3d 로그인 유효성 검사 수정
  → d4e5f6g 사용자 프로필 페이지 추가
  → h7i8j9k 의존성 업데이트
```

**상세 히스토리**:
```
사용자: "마지막 3개 커밋을 자세히 보여줘"

Claude:
  [Bash: git log -3 --stat]
  → 작성자, 날짜, 메시지, 변경된 파일 표시
```

**파일별 히스토리**:
```
사용자: "auth.js의 변경 히스토리 보여줘"

Claude:
  [Bash: git log --oneline -- src/auth.js]
  → auth.js를 수정한 커밋들
```

#### 스테이징 영역 이해

```
작업 디렉토리 → 스테이징 영역 → 저장소
   (수정됨)     (git add)      (git commit)
```

**어디에 있는지 확인**:
```
사용자: "스테이징된 파일과 안 된 파일은?"

Claude:
  [Bash: git status]
  → "스테이징됨 (커밋 준비 완료):
       src/app.js
     스테이징 안됨:
       src/config.js
     추적 안됨:
       src/newFile.js"
```

### 연습

**과제**: Claude로 Git 저장소 탐색하기

**설정**:
```bash
mkdir -p ~/claude-practice/git-demo && cd ~/claude-practice/git-demo
git init
echo "# Git Demo" > README.md
git add README.md && git commit -m "Initial commit"
echo "const app = 'hello'" > app.js
echo "const config = {}" > config.js
git add app.js && git commit -m "Add app.js"
echo "// updated" >> app.js
echo "const utils = {}" > utils.js
git add config.js
claude
```

**과제들**:

1. **상태 확인**:
```
"git status를 보여주고 각 섹션이 무슨 의미인지 설명해줘"
```

2. **Diff 보기**:
```
"작업 디렉토리의 변경사항과 스테이징된 것의 차이를 보여줘"
```

3. **히스토리 검토**:
```
"이 저장소의 모든 커밋을 보여줘"
```

4. **파일 히스토리**:
```
"app.js의 커밋 히스토리를 보여줘"
```

5. **전체 개요**:
```
"이 저장소의 전체 개요를 알려줘:
상태, 최근 변경사항, 커밋 히스토리"
```

### 확인사항

- [ ] Claude에게 git status 요청 가능
- [ ] diff 보기 (스테이징/비스테이징)
- [ ] 커밋 히스토리 검토
- [ ] 스테이징 영역 이해
- [ ] 저장소 개요 확인

### 흔한 실수

1. **커밋 전 상태 확인 안 하기**:
   - 항상 무엇이 커밋될지 검토하세요
   - Claude에게 먼저 상태를 보여달라고 요청하세요

2. **스테이징 vs 비스테이징 혼동**:
   - 스테이징됨 = 커밋 준비 완료 (`git add`)
   - 비스테이징 = 수정되었지만 아직 추가 안 됨

3. **추적되지 않는 파일 무시**:
   - 새 파일은 명시적으로 추가해야 합니다
   - status를 확인하여 추적되지 않는 파일을 보세요

---

## 레슨 14: 커밋 생성

### 학습 목표
- Claude로 잘 구조화된 커밋 생성하기
- 효과적인 커밋 메시지 작성법 배우기
- Co-Authored-By 규칙 이해하기
- 특정 파일 선택적으로 스테이징하기

### 내용

#### Claude의 커밋 생성 방식

Claude에게 커밋을 요청하면 세심한 프로토콜을 따릅니다:

```
1. git status 실행 → 모든 변경사항 확인
2. git diff 실행   → 커밋될 내용 검토
3. git log 실행    → 커밋 메시지 스타일 맞추기
4. 파일 스테이징   → 특정 파일 추가 (-A 아님)
5. 커밋 생성       → 설명적 메시지 작성
6. 확인            → 이후 git status 실행
```

#### 파일 스테이징

**특정 파일** (권장):
```
사용자: "app.js와 config.js를 스테이징해줘"

Claude:
  [Bash: git add src/app.js src/config.js]
  → "2개 파일 스테이징됨"
```

**패턴으로**:
```
사용자: "src/의 모든 JavaScript 파일 스테이징해줘"

Claude:
  [Bash: git add src/*.js]
```

**스테이징 전 검토**:
```
사용자: "커밋될 내용을 보여주고 스테이징해줘"

Claude:
  [Bash: git status]
  [Bash: git diff]
  → "커밋될 내용은 다음과 같습니다..."
  [Bash: git add src/app.js src/utils.js]
```

#### 커밋 메시지 작성

Claude가 작성하는 커밋 메시지는:
- **왜** 변경했는지에 초점 (무엇이 아닌)
- 간결함 (요약 1-2문장)
- 저장소의 기존 스타일을 따름
- `Co-Authored-By` 태그로 마무리

**좋은 커밋 메시지**:
```
사용자 미발견 시 null 포인터 수정

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>
```

```
무차별 대입 방지를 위해 /api/login 엔드포인트에 속도 제한 추가

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>
```

**나쁜 커밋 메시지**:
```
fixed stuff          ← 너무 모호함
update app.js        ← 왜가 아니라 무엇만 설명
WIP                  ← 설명이 없음
```

#### Co-Authored-By 규칙

Claude가 생성하는 모든 커밋에는 다음이 포함됩니다:
```
Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>
```

**이것이 중요한 이유**:
- AI 지원 작업의 투명성
- AI가 관여한 커밋 추적
- 일부 팀 정책에서 필수
- 책임 추적을 위한 좋은 관행

#### 완전한 커밋 워크플로우

```
사용자: "인증 모듈 변경사항을 커밋해줘"

Claude:
  [Bash: git status]
  → 수정됨: src/auth.js, src/middleware/auth.js

  [Bash: git diff]
  → 변경사항 표시

  [Bash: git log --oneline -5]
  → 최근 커밋 스타일 확인

  [Bash: git add src/auth.js src/middleware/auth.js]

  [Bash: git commit -m "만료된 토큰을 거부하도록 토큰 유효성 검사 수정

  Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>"]

  [Bash: git status]
  → "깨끗한 작업 트리 — 커밋 성공"
```

#### Pre-commit 훅 처리

pre-commit 훅이 실패하면:
```
Claude:
  [Bash: git commit -m "..."]
  → 훅 실패: 린트 오류

  [Read: 실패한 파일]
  [Edit: 린트 문제 수정]
  [Bash: git add fixed-file.js]
  [Bash: git commit -m "..."]  ← 새 커밋, --amend 아님
```

**중요**: Claude는 훅 실패 후 항상 **새** 커밋을 생성하며, 절대 amend하지 않습니다.

### 연습

**과제**: Claude로 커밋 생성 연습하기

**설정**:
```bash
cd ~/claude-practice/git-demo
echo "function login(user) { return true }" > auth.js
echo "function getUser(id) { return null }" > users.js
echo "PORT=3000" > .env
claude
```

**과제들**:

1. **간단한 커밋**:
```
"auth.js를 스테이징하고 설명적인 메시지로 커밋해줘"
```

2. **여러 파일**:
```
"users.js를 스테이징하고 기능을 설명하는 메시지로 커밋해줘"
```

3. **선택적 스테이징**:
```
"상태를 보여줘. 소스 파일만 스테이징하고
.env 파일은 제외해줘. 스테이징된 파일을 커밋해줘."
```

4. **검토 후 커밋**:
```
"커밋될 내용을 보여주고 커밋을 생성해줘"
```

5. **히스토리 확인**:
```
"모든 커밋을 보여줘서 올바른지 확인해줘"
```

### 확인사항

- [ ] Claude에게 커밋 생성 요청 가능
- [ ] 특정 파일 스테이징 (전체 아님)
- [ ] 설명적 커밋 메시지 작성
- [ ] Co-Authored-By 규칙 이해
- [ ] 커밋 후 확인

### 흔한 실수

1. **민감한 파일 커밋**:
   - `.env`, 자격 증명, 비밀 정보는 절대 커밋하지 마세요
   - Claude가 자동으로 경고합니다

2. **모든 것 스테이징**:
   - `git add -A`는 원치 않는 파일을 포함할 수 있습니다
   - 항상 특정 파일을 스테이징하세요

3. **모호한 커밋 메시지**:
   - "fix bug"는 미래의 자신에게 도움이 안 됩니다
   - 무엇을 왜 수정했는지 설명하세요

### 프로 팁

**관련 변경사항 묶기**:
```
"인증 변경은 하나의 커밋으로, UI 변경은
별도의 커밋으로 그룹화해줘"
```

**작업마다 커밋하기**:
```
"각 버그 수정 후 커밋을 만들어서
깨끗한 히스토리를 유지하자"
```

---

## 레슨 15: 브랜치 관리

### 학습 목표
- Claude로 브랜치 생성 및 전환하기
- 기능 브랜치 워크플로우 이해하기
- 안전하게 브랜치 병합하기
- 머지 충돌 처리하기

### 내용

#### Claude의 브랜치 기초

**브랜치 생성**:
```
사용자: "feature/user-profile 브랜치를 만들어줘"

Claude:
  [Bash: git checkout -b feature/user-profile]
  → "feature/user-profile 브랜치를 생성하고 전환했습니다"
```

**브랜치 전환**:
```
사용자: "main 브랜치로 전환해줘"

Claude:
  [Bash: git checkout main]
  → "main 브랜치로 전환했습니다"
```

**브랜치 목록**:
```
사용자: "모든 브랜치 보여줘"

Claude:
  [Bash: git branch -a]
  → "로컬 브랜치:
       main
     * feature/user-profile
       feature/auth
     원격 브랜치:
       origin/main"
```

#### 기능 브랜치 워크플로우

Claude가 지원하는 표준 워크플로우:

```
main ─────────────────────────────────────
        \                        /
         feature/xyz ──────────
         (생성)    (작업)    (병합)
```

**완전한 워크플로우**:
```
사용자: "새 검색 기능을 위한 기능 브랜치를 만들어줘"

Claude:
  [Bash: git checkout -b feature/search]
  → "main에서 feature/search 생성"

사용자: [... Claude로 변경 작업 ...]

사용자: "main에 다시 병합해줘"

Claude:
  [Bash: git checkout main]
  [Bash: git merge feature/search]
  → "feature/search를 main에 병합 완료"
```

#### 브랜치 명명 규칙

Claude가 따르는 일반적인 패턴:
```
feature/설명    → 새 기능
bugfix/설명     → 버그 수정
hotfix/설명     → 긴급 수정
chore/설명      → 유지보수 작업
docs/설명       → 문서 업데이트
```

**예시**:
```
feature/user-authentication
bugfix/login-500-error
hotfix/security-patch
chore/update-dependencies
```

#### 브랜치 병합

**간단한 병합**:
```
사용자: "feature/search를 main에 병합해줘"

Claude:
  [Bash: git checkout main]
  [Bash: git merge feature/search]
  → "병합 성공, 충돌 없음"
```

**충돌 감지 시**:
```
사용자: "feature/auth를 main에 병합해줘"

Claude:
  [Bash: git merge feature/auth]
  → "src/app.js에서 충돌 감지"

  [Read: src/app.js]  → 충돌 마커 확인
  [Edit: src/app.js]  → 충돌 해결
  [Bash: git add src/app.js]
  [Bash: git commit]
  → "머지 충돌 해결 및 커밋 완료"
```

#### 머지 충돌 처리

충돌이 발생하면 Claude는:

1. 충돌 파일을 **식별**합니다
2. 각 파일을 **읽어** 충돌 마커를 확인합니다
3. 양쪽 변경사항을 **이해**합니다
4. 최선의 조합으로 **해결**합니다
5. 해결된 파일을 **스테이징**합니다
6. 병합을 **커밋**합니다

**충돌 마커**:
```
<<<<<<< HEAD
const port = 3000
=======
const port = process.env.PORT || 3000
>>>>>>> feature/config
```

Claude는 올바른 버전을 선택하여 해결합니다 (보통 더 완전한 것):
```
const port = process.env.PORT || 3000
```

#### 브랜치 삭제

**병합 후**:
```
사용자: "feature/search 브랜치를 삭제해줘"

Claude:
  [Bash: git branch -d feature/search]
  → "feature/search 브랜치 삭제됨"
```

**참고**: Claude는 명시적으로 요청하지 않는 한 `-D` (강제 삭제)가 아닌 `-d` (안전 삭제)를 사용합니다.

### 연습

**과제**: 기능 브랜치 워크플로우 완성하기

**설정**:
```bash
cd ~/claude-practice/git-demo
git checkout main  # main 브랜치에 있는지 확인
claude
```

**과제들**:

1. **기능 브랜치 생성**:
```
"feature/greeting 브랜치를 만들고 전환해줘"
```

2. **변경사항 만들기**:
```
"greet(name) 함수를 export하는 greeting.js 파일을
만들고 커밋해줘"
```

3. **다른 브랜치 전환 및 생성**:
```
"main으로 전환하고 feature/farewell이라는
다른 브랜치를 만들어줘"
```

4. **충돌 가능한 변경**:
```
"farewell.js 파일을 만들고 app.js도 수정해줘.
변경사항을 커밋해줘."
```

5. **두 기능 병합**:
```
"main으로 전환하고 feature/greeting을 먼저 병합한 다음,
feature/farewell을 병합해줘. 충돌이 있으면 처리해줘."
```

### 확인사항

- [ ] 브랜치 생성 및 전환 가능
- [ ] 기능 브랜치 워크플로우 따르기
- [ ] 브랜치 병합
- [ ] Claude의 머지 충돌 처리 방식 이해
- [ ] 병합 후 브랜치 삭제

### 흔한 실수

1. **main에서 직접 작업**:
   - 항상 기능 브랜치를 만드세요
   - main을 깨끗하고 배포 가능한 상태로 유지

2. **전환 전 커밋 안 하기**:
   - 커밋하지 않은 변경사항이 넘어가거나 충돌 발생
   - 전환 전에 커밋하거나 stash하세요

3. **병합되지 않은 브랜치 강제 삭제**:
   - `-d`는 안전 (병합된 경우만 삭제)
   - `-D`는 강제 삭제 — 주의해서 사용

---

## 레슨 16: Pull Request 생성

### 학습 목표
- Claude로 Pull Request 생성하기
- 효과적인 PR 설명 작성하기
- gh CLI 도구 사용하기
- PR 모범 사례 이해하기

### 내용

#### Claude의 PR 생성 방식

Claude는 **GitHub CLI (`gh`)**를 사용하여 Pull Request를 생성합니다. 워크플로우:

```
1. 현재 브랜치와 상태 확인
2. 베이스 브랜치와의 diff 확인
3. 브랜치의 모든 커밋 검토
4. 원격에 push (필요한 경우)
5. 제목과 설명으로 PR 생성
```

#### PR 생성 워크플로우

**전체 흐름**:
```
사용자: "이 브랜치에 대한 Pull Request를 만들어줘"

Claude:
  [Bash: git status]
  → 깨끗한 작업 트리

  [Bash: git log main..HEAD --oneline]
  → 이 브랜치에 3개 커밋

  [Bash: git diff main...HEAD]
  → 모든 변경사항 표시

  [Bash: git push -u origin feature/search]
  → 원격에 push 완료

  [Bash: gh pr create --title "검색 기능 추가"
    --body "## Summary
    - /api/search 엔드포인트에 전문 검색 추가
    - 사용자 이름과 이메일 주소 인덱싱
    - 검색 결과 페이지네이션 추가

    ## Test plan
    - [ ] 검색이 올바른 결과를 반환하는지 확인
    - [ ] 대규모 데이터셋으로 페이지네이션 테스트
    - [ ] 빈 검색 처리 확인

    🤖 Generated with Claude Code"]
  → PR #42 생성됨
```

#### 좋은 PR 설명 작성하기

Claude는 PR 설명을 다음과 같이 구조화합니다:

**제목** (70자 미만):
```
페이지네이션 지원과 함께 사용자 검색 추가
느린 연결에서의 인증 타임아웃 수정
성능 향상을 위한 데이터베이스 쿼리 리팩토링
```

**본문**:
```markdown
## Summary
- [변경 내용과 이유를 설명하는 1-3개의 글머리 기호]

## Test plan
- [ ] 수동 테스트 단계
- [ ] 자동화된 테스트 커버리지
- [ ] 엣지 케이스 확인

🤖 Generated with Claude Code
```

#### PR 생성 커스터마이징

**특정 리뷰어 지정**:
```
사용자: "PR을 만들고 @alice와 @bob에게 리뷰를 요청해줘"

Claude:
  [Bash: gh pr create --title "..." --body "..."
    --reviewer alice,bob]
```

**라벨 추가**:
```
사용자: "'enhancement' 라벨로 PR을 만들어줘"

Claude:
  [Bash: gh pr create --title "..." --body "..."
    --label enhancement]
```

**드래프트 PR**:
```
사용자: "이 작업 중인 것에 대해 드래프트 PR을 만들어줘"

Claude:
  [Bash: gh pr create --draft --title "WIP: ..." --body "..."]
```

#### PR 보기 및 관리

**PR 상세 보기**:
```
사용자: "PR #42 보여줘"

Claude:
  [Bash: gh pr view 42]
  → 제목, 설명, 상태, 체크
```

**열린 PR 목록**:
```
사용자: "모든 열린 Pull Request 보여줘"

Claude:
  [Bash: gh pr list]
  → 열린 PR 목록과 상태
```

**PR 코멘트 보기**:
```
사용자: "PR #42의 리뷰 코멘트 보여줘"

Claude:
  [Bash: gh api repos/owner/repo/pulls/42/comments]
  → 모든 리뷰 코멘트
```

### 연습

**과제**: Claude로 첫 번째 Pull Request 만들기

**설정** (GitHub 저장소 필요):
```bash
# 테스트 저장소 클론하거나 기존 것 사용
cd ~/claude-practice/git-demo
git remote add origin <your-repo-url>  # 필요한 경우
git checkout -b feature/practice-pr
echo "console.log('Hello PR!')" > pr-demo.js
git add pr-demo.js
git commit -m "Add PR demo file

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>"
claude
```

**과제들**:

1. **기본 PR 생성**:
```
"이 브랜치에 대한 Pull Request를 만들어줘"
```

2. **커스텀 PR 설명**:
```
"이 브랜치가 무엇을 추가하고 어떻게 테스트하는지
자세한 설명이 포함된 PR을 만들어줘"
```

3. **PR 보기**:
```
"방금 만든 PR을 보여줘"
```

4. **모든 PR 나열**:
```
"이 저장소의 모든 열린 PR을 보여줘"
```

### 확인사항

- [ ] Claude에게 Pull Request 생성 요청 가능
- [ ] PR 설명 구조 이해
- [ ] 기존 PR 보기 및 관리
- [ ] PR 커스터마이징 (리뷰어, 라벨, 드래프트)
- [ ] `gh` CLI 사용 시기 파악

### 흔한 실수

1. **잘못된 원격에 push**:
   - `git remote -v`로 원격 확인
   - Claude가 자동으로 확인합니다

2. **PR 설명 누락**:
   - 항상 요약과 테스트 계획 포함
   - Claude가 기본으로 생성합니다

3. **push 잊기**:
   - PR은 원격에 코드가 있어야 합니다
   - Claude가 PR 생성 전에 push합니다

### 프로 팁

**대화에서 PR 만들기**:
```
"인증 수정 작업을 해왔잖아. 우리가 만든
모든 변경사항을 요약하는 PR을 만들어줘."
```

**이슈 연결**:
```
"이슈 #15를 닫는 PR을 만들어줘"
→ Claude가 설명에 "Closes #15" 추가
```

---

## 레슨 17: Claude로 코드 리뷰

### 학습 목표
- Claude로 Pull Request 리뷰하기
- 보안 문제, 버그, 스타일 문제 식별하기
- 리뷰 피드백 생성하기
- 리뷰 워크플로우 이해하기

### 내용

#### Claude의 코드 리뷰 방식

Claude는 여러 수준에서 코드를 리뷰할 수 있습니다:

**1. 빠른 리뷰** — 표면적 확인:
```
사용자: "이 PR 빠르게 리뷰해줘"
Claude: 명백한 문제, 오타, 누락된 테스트 확인
```

**2. 심층 리뷰** — 철저한 분석:
```
사용자: "PR #42를 철저하게 코드 리뷰해줘"
Claude: 보안, 성능, 아키텍처, 테스트 분석
```

**3. 집중 리뷰** — 특정 관심사:
```
사용자: "PR #42를 보안 문제 위주로 리뷰해줘"
Claude: 보안 패턴에 대한 깊은 분석만
```

#### Pull Request 리뷰하기

**전체 PR 리뷰**:
```
사용자: "PR #42 리뷰해줘"

Claude:
  [Bash: gh pr view 42]
  → PR 세부정보 확인

  [Bash: gh pr diff 42]
  → 모든 코드 변경사항 확인

  [Read: 변경된 파일의 컨텍스트 읽기]

  → "## 코드 리뷰: PR #42

  ### 요약
  이 PR은 사용자 검색 기능을 추가합니다.

  ### 발견된 문제

  🔴 **치명적: SQL 인젝션**
  파일: src/search.js:15
  검색 쿼리가 매개변수화되지 않았습니다.
  수정: 준비된 구문을 사용하세요.

  🟡 **경고: 에러 처리 누락**
  파일: src/search.js:22
  데이터베이스 쿼리 주변에 try/catch가 없습니다.
  수정: try/catch로 감싸고 오류 시 500 반환.

  🔵 **제안: 성능**
  파일: src/search.js:8
  검색 컬럼에 인덱스 추가를 고려하세요.

  ### 긍정적 사항
  ✅ 좋은 테스트 커버리지
  ✅ 깔끔한 코드 구조
  ✅ 문서 업데이트됨"
```

#### 리뷰 체크리스트

Claude가 확인하는 항목:

**보안**:
- SQL 인젝션
- XSS 취약점
- 인증/인가 문제
- 하드코딩된 비밀 정보
- 입력 유효성 검사

**코드 품질**:
- 명확한 명명
- 적절한 복잡도
- DRY (Don't Repeat Yourself)
- 적절한 에러 처리
- 일관된 스타일

**성능**:
- N+1 쿼리
- 불필요한 계산
- 누락된 캐싱 기회
- 대규모 데이터 처리

**테스트**:
- 적절한 테스트 커버리지
- 엣지 케이스 커버
- 테스트 품질과 가독성
- 중요 경로의 통합 테스트

**모범 사례**:
- 프로젝트 규칙 준수
- 문서 업데이트
- 디버깅 코드 남아있지 않음
- 적절한 로깅

#### 로컬 변경사항 리뷰

PR 없이도 리뷰를 받을 수 있습니다:

**현재 변경사항 리뷰**:
```
사용자: "마지막 커밋 이후 변경사항을 리뷰해줘"

Claude:
  [Bash: git diff]
  [Read: 수정된 파일의 컨텍스트]
  → 리뷰 피드백 제공
```

**특정 파일 리뷰**:
```
사용자: "src/auth.js를 보안 문제 위주로 리뷰해줘"

Claude:
  [Read: src/auth.js]
  → 집중 보안 리뷰
```

**커밋 리뷰**:
```
사용자: "마지막 커밋을 리뷰해줘"

Claude:
  [Bash: git show HEAD]
  → 커밋된 변경사항 리뷰
```

#### 리뷰 피드백에 대응

**리뷰의 문제 수정**:
```
사용자: "리뷰에서 찾은 모든 문제를 수정해줘"

Claude:
  [Edit: src/search.js]  → SQL 인젝션 수정
  [Edit: src/search.js]  → 에러 처리 추가
  [Bash: npm test]        → 수정 확인
  [Bash: git add ...]
  [Bash: git commit -m "코드 리뷰 피드백 반영"]
```

**트레이드오프 논의**:
```
사용자: "캐싱 제안에 동의하지 않아. 데이터가
자주 바뀌거든. 어떻게 생각해?"

Claude: "좋은 지적이에요. 데이터가 몇 초마다 바뀐다면
캐싱이 오래된 결과를 줄 수 있어요. 짧은
TTL (5-10초)을 타협안으로 고려하거나,
캐싱을 건너뛰고 쿼리를 최적화하는 것도 방법이에요."
```

### 연습

**과제**: Claude로 코드 리뷰 수행하기

**설정**:
```bash
cd ~/claude-practice
mkdir -p review-demo/src

cat > review-demo/src/api.js << 'EOF'
const express = require('express')
const mysql = require('mysql')
const app = express()

const db = mysql.createConnection({
  host: 'localhost',
  user: 'root',
  password: 'password123',
  database: 'myapp'
})

app.get('/users', (req, res) => {
  const query = "SELECT * FROM users WHERE name = '" + req.query.name + "'"
  db.query(query, (err, results) => {
    res.json(results)
  })
})

app.post('/login', (req, res) => {
  const { username, password } = req.body
  const query = `SELECT * FROM users WHERE username='${username}' AND password='${password}'`
  db.query(query, (err, result) => {
    if (result.length > 0) {
      res.json({ token: 'abc123' })
    }
  })
})

app.listen(3000)
EOF

cd review-demo
claude
```

**과제들**:

1. **일반 리뷰**:
```
"src/api.js에 문제가 있는지 리뷰해줘"
```

2. **보안 집중**:
```
"src/api.js를 보안 관점에서 리뷰해줘"
```

3. **문제 수정**:
```
"발견한 모든 보안 및 품질 문제를 수정해줘"
```

4. **수정 확인**:
```
"변경한 모든 내용의 diff를 보여주고 각 수정을 설명해줘"
```

5. **최종 리뷰**:
```
"수정된 코드를 한 번 더 리뷰해서
놓친 것이 없는지 확인해줘"
```

### 확인사항

- [ ] Claude에게 코드 또는 PR 리뷰 요청 가능
- [ ] 리뷰 심각도 수준 이해
- [ ] 집중 리뷰 요청 (보안, 성능)
- [ ] Claude가 리뷰 발견 사항 수정하게 하기
- [ ] 로컬 변경사항 리뷰 (PR뿐만 아니라)

### 흔한 실수

1. **컨텍스트 제공 안 하기**:
   - "이거 리뷰해줘" — 무엇을 리뷰?
   - PR 번호, 파일, 범위를 지정하세요

2. **경고 무시**:
   - 제안을 고려 없이 무시하지 마세요
   - 동의하지 않으면 Claude와 트레이드오프를 논의하세요

3. **수정 후 재리뷰 안 하기**:
   - 수정이 새로운 문제를 만들 수 있습니다
   - 항상 최종 리뷰 패스를 거치세요

### 프로 팁

**커밋 전 리뷰**:
```
"커밋하기 전에 모든 변경사항을 리뷰하고
문제가 있으면 알려줘"
```

**팀 표준**:
```
"CLAUDE.md에 문서화된 팀 코딩 표준에 대해
이 PR을 리뷰해줘"
```

**리뷰에서 배우기**:
```
"발견한 SQL 인젝션 취약점을 설명해줘
더 잘 이해할 수 있게"
```

---

## 섹션 3 요약

이제 Claude가 전체 Git 워크플로우를 어떻게 처리하는지 알게 되었습니다:

| 작업 | Claude가 하는 일 |
|------|-----------------|
| **상태** | 수정됨, 스테이징됨, 추적 안 된 파일 표시 |
| **Diff** | 컨텍스트와 함께 변경사항 표시 |
| **커밋** | 특정 파일 스테이징, 설명적 메시지 작성 |
| **브랜치** | 브랜치 생성, 전환, 병합, 삭제 |
| **Pull Request** | 코드 push, 설명이 포함된 PR 생성 |
| **코드 리뷰** | 보안, 품질, 성능, 테스트 분석 |

### 핵심 안전 원칙

- Claude는 **특정 파일**을 스테이징합니다 (전체 아님)
- Claude는 **새 커밋**을 만듭니다 (amend가 아님, 요청하지 않는 한)
- Claude는 **안전 삭제**를 사용합니다 (`-D`가 아닌 `-d`)
- Claude는 명시적 허가 없이 **절대 force push하지 않습니다**
- Claude는 **민감한 파일**에 대해 경고합니다 (`.env`, 자격 증명)

### 다음 단계

**섹션 4: 웹 도구**에서는 Claude가 웹을 검색하고 콘텐츠를 가져와서 최신 정보로 문제를 해결하는 방법을 배웁니다.
