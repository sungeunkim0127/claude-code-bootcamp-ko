# 섹션 9: MCP - Model Context Protocol (레슨 51-62)

## 개요
Model Context Protocol(MCP)을 마스터하여 외부 데이터 소스, API, 서비스로 Claude Code를 확장하세요. MCP는 AI 어시스턴트를 외부 세계와 연결하기 위한 표준 프로토콜입니다.

---

## 레슨 51: MCP 개요

### 학습 목표
- MCP가 무엇이고 왜 존재하는지 이해하기
- MCP 서버 유형 알기
- 일반적인 사용 사례 파악하기
- MCP 아키텍처 이해하기

### 내용

#### MCP란 무엇인가?

**Model Context Protocol(MCP)**은 AI 어시스턴트를 외부 도구와 데이터 소스에 연결하기 위한 개방형 표준입니다. Claude가 내장 기능 이상의 서비스와 상호작용할 수 있게 해줍니다.

**MCP 없이**:
```
Claude 가능: 파일 읽기, 코드 편집, 명령 실행
Claude 불가: 데이터베이스 쿼리, GitHub 이슈 확인,
             Slack 검색, API 접근
```

**MCP 있을 때**:
```
Claude 가능: 위의 모든 것 + 추가로
- 라이브 데이터베이스 쿼리
- GitHub 이슈 및 PR 관리
- Slack 메시지 검색
- 커스텀 API 호출
- MCP 서버가 있는 모든 서비스 접근
```

#### MCP 아키텍처

```
┌──────────────┐     MCP Protocol     ┌──────────────────┐
│  Claude Code │ ◄──────────────────► │   MCP Server     │
│  (Client)    │   JSON-RPC / stdio   │  (Your Service)  │
└──────────────┘                      └──────────────────┘
                                              │
                                              ▼
                                      ┌──────────────────┐
                                      │  External API    │
                                      │  Database        │
                                      │  File System     │
                                      └──────────────────┘
```

**핵심 구성 요소**:
- **클라이언트** (Claude Code): MCP 도구를 발견하고 호출
- **서버**: 도구, 리소스, 프롬프트를 노출
- **전송**: 통신 계층 (stdio, HTTP, SSE)

#### MCP 서버가 제공하는 것

**1. 도구(Tools)** — Claude가 호출할 수 있는 함수:
```
github.create_issue(title, body)
postgres.query(sql)
slack.send_message(channel, text)
```

**2. 리소스(Resources)** — Claude가 @-멘션으로 접근할 수 있는 데이터:
```
@github:issue:123
@confluence:doc:456
@slack:channel:general
```

**3. 프롬프트(Prompts)** — 재사용 가능한 프롬프트 템플릿:
```
/github:summarize-pr 42
/postgres:explain-query "SELECT..."
```

#### 일반적인 사용 사례

| 사용 사례 | MCP 서버 | 가능한 작업 |
|-----------|----------|------------|
| GitHub 관리 | GitHub 서버 | 이슈, PR, 저장소 |
| 데이터베이스 쿼리 | Postgres/MySQL 서버 | 직접 SQL 쿼리 |
| 문서 관리 | Confluence/Notion 서버 | 문서 읽기/검색 |
| 커뮤니케이션 | Slack 서버 | 메시지 읽기/전송 |
| 파일 탐색 | Filesystem 서버 | 원격 파일 접근 |
| API 통합 | 커스텀 서버 | 모든 REST/GraphQL API |

### 연습

**과제**: MCP 개념 이해하기

1. 매일 사용하는 서비스 중 MCP 통합이 유용할 5가지를 나열하세요
2. 각각에 대해 어떤 도구, 리소스, 프롬프트가 유용할지 식별하세요
3. 통합으로 절약될 시간 순으로 순위를 매기세요

### 확인사항

다음으로 넘어가기 전에 확인하세요:
- [ ] MCP가 무엇이고 그 목적을 이해함
- [ ] 클라이언트-서버 아키텍처를 이해함
- [ ] 세 가지 MCP 기본 요소(도구, 리소스, 프롬프트)를 이해함
- [ ] MCP의 일반적인 사용 사례를 파악함

---

## 레슨 52: MCP 서버 설치

### 학습 목표
- 설정에서 MCP 서버 구성하기
- npm 패키지에서 서버 설치하기
- 서버 라이프사이클 관리하기
- 설치 문제 해결하기

### 내용

#### MCP 서버 구성

MCP 서버는 Claude Code 설정 파일에서 구성합니다:

**사용자 수준** (`~/.claude/settings.json`):
```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "ghp_xxxxxxxxxxxx"
      }
    }
  }
}
```

**프로젝트 수준** (`.claude/settings.json`):
```json
{
  "mcpServers": {
    "project-db": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "DATABASE_URL": "postgresql://localhost:5432/mydb"
      }
    }
  }
}
```

#### 구성 필드

```json
{
  "mcpServers": {
    "server-name": {
      "command": "npx",           // 서버를 시작하는 명령어
      "args": ["-y", "package"],  // 명령어의 인수
      "env": {                    // 환경 변수
        "API_KEY": "value"
      },
      "cwd": "/path/to/dir"      // 작업 디렉토리 (선택사항)
    }
  }
}
```

#### 인기 서버 설치

**GitHub 서버**:
```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "ghp_your_token_here"
      }
    }
  }
}
```

**Filesystem 서버**:
```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": [
        "-y", "@modelcontextprotocol/server-filesystem",
        "/path/to/allowed/directory"
      ]
    }
  }
}
```

**PostgreSQL 서버**:
```json
{
  "mcpServers": {
    "postgres": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "DATABASE_URL": "postgresql://user:pass@localhost:5432/dbname"
      }
    }
  }
}
```

#### 설치 확인

MCP 서버를 설정에 추가한 후:

```
사용자: "연결된 MCP 서버가 뭐가 있어?"

Claude: 사용 가능한 MCP 서버와 도구 목록을 표시
```

```
사용자: "GitHub MCP 서버가 어떤 도구를 제공해?"

Claude: 모든 GitHub 도구 나열 (create_issue, list_repos 등)
```

#### 문제 해결

**서버가 시작되지 않을 때**:
```
1. 명령어가 존재하는지 확인 (npx, node, python)
2. 패키지 이름이 올바른지 확인
3. 환경 변수가 설정되었는지 확인
4. 터미널에서 수동으로 명령 실행해보기
```

**인증 오류**:
```
1. 토큰/키가 유효한지 확인
2. 토큰에 필요한 권한이 있는지 확인
3. 환경 변수가 올바른 설정 파일에 있는지 확인
4. 프로젝트 설정의 토큰은 git에 커밋하면 안 됨
```

**서버 충돌**:
```
1. 서버 로그(stderr 출력) 확인
2. 서버 버전 호환성 확인
3. 재설치 시도: node_modules/.cache 삭제 후 재시도
4. HTTP 전송 사용 시 포트 충돌 확인
```

### 연습

**과제**: 첫 번째 MCP 서버 설치하기

1. **설정 파일 생성**:
```bash
mkdir -p ~/.claude
```

2. **Filesystem 서버 추가** (가장 안전한 시작):
```json
// ~/.claude/settings.json에 추가
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": [
        "-y", "@modelcontextprotocol/server-filesystem",
        "/tmp/mcp-test"
      ]
    }
  }
}
```

3. **테스트 디렉토리 생성**:
```bash
mkdir -p /tmp/mcp-test
echo "Hello MCP!" > /tmp/mcp-test/test.txt
```

4. **Claude에서 확인**:
```
claude
"사용 가능한 MCP 서버가 뭐가 있어?"
"filesystem MCP 서버로 test.txt 파일 읽어줘"
```

### 확인사항

다음으로 넘어가기 전에 확인하세요:
- [ ] settings.json에 MCP 서버를 추가할 수 있음
- [ ] 환경 변수를 구성할 수 있음
- [ ] 서버 설치를 확인할 수 있음
- [ ] 일반적인 문제를 해결할 수 있음

---

## 레슨 53: MCP 도구 사용

### 학습 목표
- 사용 가능한 MCP 도구 발견하기
- MCP 도구를 자연스럽게 호출하기
- 도구 매개변수 이해하기
- 도구 응답 처리하기

### 내용

#### 도구 발견

Claude는 연결된 MCP 서버에서 도구를 자동으로 발견합니다:

```
사용자: "GitHub 서버에서 어떤 도구를 사용할 수 있어?"

Claude: "GitHub MCP 서버는 다음 도구를 제공합니다:
- github_create_issue: 새 이슈 생성
- github_list_issues: 저장소 이슈 나열
- github_create_pr: 풀 리퀘스트 생성
- github_search_repos: 저장소 검색
- ..."
```

#### MCP 도구 호출

정확한 도구 이름을 알 필요 없이, 원하는 것을 자연어로 설명하면 됩니다:

**자연어 사용**:
```
사용자: "로그인 버그에 대한 GitHub 이슈 만들어줘"

Claude:
  [MCP Tool: github_create_issue]
  → 이슈 #45 생성됨: "Login bug: 500 error on invalid password"
```

**상세 정보 제공**:
```
사용자: "'myorg/myapp' 리포에 'Fix pagination' 제목의
GitHub 이슈를 버그 설명과 함께 만들어줘"

Claude:
  [MCP Tool: github_create_issue(
    repo: "myorg/myapp",
    title: "Fix pagination",
    body: "The pagination on /users endpoint..."
  )]
  → 이슈 #46 생성됨
```

#### 일반적인 MCP 도구 패턴

**GitHub 도구**:
```
"myorg/myapp에서 열린 이슈 모두 보여줘"
"@alice가 올린 PR 검색해줘"
"리포에 'feature/search' 브랜치 만들어줘"
"이슈 #42를 댓글과 함께 닫아줘"
```

**데이터베이스 도구**:
```
"데이터베이스의 모든 테이블 보여줘"
"최근 7일간 가입한 사용자 조회해줘"
"이 느린 쿼리의 실행 계획 설명해줘"
"모든 테이블과 행 수 보여줘"
```

**Filesystem 도구**:
```
"/data/exports의 파일 목록 보여줘"
"최신 로그 파일 읽어줘"
"모든 로그 파일에서 'error' 검색해줘"
```

#### 도구 응답 이해

MCP 도구는 Claude가 해석하는 구조화된 데이터를 반환합니다:

```
사용자: "열린 이슈가 몇 개야?"

Claude:
  [MCP Tool: github_list_issues(state: "open")]
  → 이슈 JSON 배열 반환

  "열린 이슈가 23개 있습니다. 가장 최근 5개는:
  1. #89 - Fix login timeout (어제 생성)
  2. #88 - Update documentation (2일 전 생성)
  ..."
```

#### 오류 처리

MCP 도구가 실패할 때:

```
사용자: "사용자 통계 데이터베이스 조회해줘"

Claude:
  [MCP Tool: postgres_query(...)]
  → Error: Connection refused

  "데이터베이스 MCP 서버가 오류를 반환했습니다:
  'Connection refused'. 일반적인 원인:
  1. 데이터베이스 서버가 실행 중이 아님
  2. 설정의 연결 URL이 올바르지 않음
  3. 방화벽이 연결을 차단 중

  문제 해결을 도와드릴까요?"
```

### 연습

**과제**: MCP 도구 사용하기 (설치된 서버로)

GitHub 토큰이 있는 경우:
```
"최근 리포지토리 목록 보여줘"
"[리포 이름]에서 열린 이슈 보여줘"
"[테스트 리포]에 테스트 이슈 생성해줘"
```

Filesystem 서버를 사용하는 경우:
```
"MCP 접근 가능한 디렉토리의 모든 파일 보여줘"
"test.txt 내용 읽어줘"
"'Hello from MCP!' 내용으로 hello.txt 파일 만들어줘"
```

### 확인사항

다음으로 넘어가기 전에 확인하세요:
- [ ] MCP 서버에서 도구를 발견할 수 있음
- [ ] 자연어로 도구를 호출할 수 있음
- [ ] 도구 응답을 이해할 수 있음
- [ ] 도구 오류를 처리할 수 있음

---

## 레슨 54: MCP 리소스와 @-멘션

### 학습 목표
- MCP 리소스 이해하기
- @-멘션으로 MCP 데이터 참조하기
- 원격 콘텐츠에 인라인으로 접근하기
- 리소스와 도구를 결합하기

### 내용

#### MCP 리소스란?

리소스는 MCP 서버가 노출하는 데이터 소스로, Claude가 직접 참조할 수 있습니다. Claude가 읽을 수 있는 원격 파일이나 데이터라고 생각하면 됩니다.

**예시**:
```
@github:issue:123       → GitHub 이슈 #123의 내용
@postgres:table:users   → users 테이블의 스키마
@slack:channel:general  → #general의 최근 메시지
```

#### @-멘션으로 리소스 사용

**대화에서 참조**:
```
사용자: "@github:issue:123 요약해줘"

Claude:
  [MCP를 통해 이슈 #123 가져오기]
  "이슈 #123 'Fix login timeout'은 @alice가
  1월 15일에 열었습니다. 30초 타임아웃이..."
```

**리소스 결합**:
```
사용자: "@github:issue:123과 src/auth.js의
구현을 비교해줘"

Claude:
  [MCP를 통해 이슈 가져오기]
  [로컬에서 src/auth.js 읽기]
  "이슈는 10초 타임아웃을 요청하지만,
  현재 구현은 30초를 사용하고 있습니다..."
```

#### 리소스 유형

**읽기 전용 리소스** (가장 일반적):
- GitHub 이슈, PR, 파일
- 데이터베이스 스키마 및 쿼리 결과
- 문서 페이지
- Slack 메시지

**동적 리소스** (요청 시 계산):
- 데이터베이스 쿼리 결과
- API 응답 데이터
- 집계된 메트릭

#### 리소스의 장점

**인라인 컨텍스트**:
```
"@github:pr:42를 기반으로 src/search.js의
구현을 승인된 설계에 맞게 업데이트해줘"
```

**크로스 서비스 참조**:
```
"@github:issue:99에 설명된 버그가
@slack:thread:abc123의 논의와 관련될 수 있어"
```

### 연습

**과제**: MCP 리소스 사용하기

GitHub 서버가 구성된 경우:
```
"테스트 리포지토리에서 @github:issue:1 읽어줘"
"@github:pr:1의 논의를 요약해줘"
```

**실용적인 워크플로우**:
```
"@github:issue:5를 읽고, 설명된 기능을
src/feature.js에 구현해줘"
```

### 확인사항

다음으로 넘어가기 전에 확인하세요:
- [ ] MCP 리소스가 무엇인지 이해함
- [ ] @-멘션으로 리소스를 참조하는 방법을 앎
- [ ] 리소스와 로컬 파일을 결합하는 방법을 앎
- [ ] 리소스와 도구를 언제 사용하는지 구분할 수 있음

---

## 레슨 55: MCP 프롬프트

### 학습 목표
- MCP 프롬프트 템플릿 이해하기
- 서버 제공 프롬프트 사용하기
- 프롬프트와 도구 결합하기
- 프롬프트가 가치를 더하는 시점 알기

### 내용

#### MCP 프롬프트란?

프롬프트는 MCP 서버가 제공하는 재사용 가능한 템플릿입니다. 일반적인 워크플로우를 단일 명령으로 패키징합니다.

**GitHub 서버의 프롬프트 예시**:
```
/github:summarize-pr 42     → 주요 변경사항으로 PR 요약
/github:release-notes v2.0  → 릴리스 노트 생성
/github:issue-triage         → 태그 없는 이슈 분류
```

#### MCP 프롬프트 사용

**프롬프트 호출**:
```
사용자: /github:summarize-pr 42

Claude:
  [프롬프트 템플릿 실행]
  [MCP 도구로 PR 데이터 가져오기]
  "## PR #42 요약
  **제목**: Add search functionality
  **작성자**: @alice
  **변경사항**: 5개 파일, +200/-30줄
  **주요 변경**:
  - /api/search에 새 검색 엔드포인트
  - Elasticsearch 통합
  - 검색 결과 페이지네이션..."
```

#### 프롬프트 vs 스킬

| 측면 | MCP 프롬프트 | 스킬 |
|------|-------------|------|
| 출처 | MCP 서버 | 로컬 파일 |
| 범위 | 서버의 도메인 | 모든 작업 |
| 데이터 접근 | 서버의 데이터 | 로컬 파일 + 도구 |
| 설치 | 서버와 함께 제공 | 직접 생성 |

**프롬프트 사용 시점**: MCP 서버가 해당 도메인에 유용한 워크플로우를 제공할 때.

**스킬 사용 시점**: 커스텀 로직이나 크로스 서비스 워크플로우가 필요할 때.

### 연습

**과제**: 사용 가능한 MCP 프롬프트 탐색하기

```
"연결된 MCP 서버에서 사용 가능한 프롬프트가 뭐가 있어?"
"최근 PR에 GitHub summarize-pr 프롬프트 사용해봐"
```

### 확인사항

다음으로 넘어가기 전에 확인하세요:
- [ ] MCP 프롬프트가 무엇인지 이해함
- [ ] MCP 프롬프트를 호출하는 방법을 앎
- [ ] 프롬프트와 스킬의 차이를 이해함
- [ ] 프롬프트가 가치를 더하는 시점을 앎

---

## 레슨 56: MCP 인증과 보안

### 학습 목표
- MCP 자격 증명을 안전하게 구성하기
- 토큰 범위 지정 이해하기
- 시크릿을 안전하게 관리하기
- 보안 모범 사례 따르기

### 내용

#### 자격 증명 관리

**환경 변수** (권장):
```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "ghp_xxxxxxxxxxxx"
      }
    }
  }
}
```

**보안 규칙**:
1. **토큰을 git에 절대 커밋하지 마세요** — 프로젝트 설정이 아닌 사용자 수준 설정 사용
2. **최소 권한 사용** — 필요한 범위만 있는 토큰 생성
3. **토큰 정기 교체** — 캘린더 알림 설정
4. **별도 토큰 사용** — MCP 서버당 하나씩, 쉽게 폐기 가능

#### 토큰 범위 지정

**GitHub 토큰** — 필요한 것만 부여:
```
repo (리포 접근이 필요한 경우)
read:org (조직 데이터용)
read:user (사용자 데이터용)
```

**데이터베이스** — 쿼리 서버에는 읽기 전용 자격 증명 사용:
```
CREATE USER mcp_reader WITH PASSWORD 'secure_password';
GRANT SELECT ON ALL TABLES TO mcp_reader;
-- INSERT, UPDATE, DELETE 없음
```

#### 설정 파일 보안

**사용자 설정** (`~/.claude/settings.json`):
- 개인 토큰 포함
- 버전 관리에 커밋하지 않음
- OS 파일 권한으로 보호

**프로젝트 설정** (`.claude/settings.json`):
- git에 커밋됨 (팀과 공유)
- 시크릿을 포함하면 안 됨
- 대신 환경 변수 참조 사용:

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"
      }
    }
  }
}
```

#### 보안 모범 사례

1. **최소 권한 원칙**: MCP 서버에 필요한 최소 접근 권한 부여
2. **정기 감사**: 어떤 서버가 무엇에 접근하는지 검토
3. **네트워크 격리**: 가능한 경우 샌드박스 환경에서 MCP 서버 실행
4. **접근 로깅**: MCP 서버가 접근하는 데이터 모니터링
5. **환경 분리**: 개발/스테이징/프로덕션에 다른 토큰 사용

### 연습

**과제**: 안전한 MCP 자격 증명 설정하기

1. 최소 범위로 GitHub 개인 접근 토큰 생성
2. 사용자 수준 설정에 추가 (프로젝트 설정 아님)
3. 리포지토리 목록 조회로 동작 확인
4. 토큰이 가진 권한 문서화

### 확인사항

다음으로 넘어가기 전에 확인하세요:
- [ ] 사용자 설정에서 토큰을 안전하게 구성할 수 있음
- [ ] 토큰 범위 지정을 이해함
- [ ] 시크릿을 버전 관리에서 제외할 수 있음
- [ ] 최소 권한 원칙을 따를 수 있음

---

## 레슨 57: 인기 MCP 서버

### 학습 목표
- GitHub, Slack, 데이터베이스 서버 구성하기
- 각 서버의 기능 이해하기
- 필요에 맞는 올바른 서버 선택하기
- 여러 서버 결합하기

### 내용

#### GitHub MCP 서버

**설정**:
```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "ghp_xxxxxxxxxxxx"
      }
    }
  }
}
```

**기능**:
- 이슈 생성/읽기/업데이트
- 풀 리퀘스트 생성/리뷰
- 저장소 및 코드 검색
- 브랜치 및 릴리스 관리
- 리포에서 파일 내용 읽기

**사용 예시**:
```
"myorg/backend에서 열린 버그 모두 보여줘"
"로그인 타임아웃 버그에 대한 이슈 만들어줘"
"#42의 PR 리뷰 댓글 보여줘"
"'machine learning' 관련 리포지토리 검색해줘"
```

#### PostgreSQL MCP 서버

**설정**:
```json
{
  "mcpServers": {
    "postgres": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "DATABASE_URL": "postgresql://user:pass@localhost:5432/mydb"
      }
    }
  }
}
```

**기능**:
- SQL 쿼리 실행 (SELECT)
- 테이블 스키마 검사
- 데이터베이스 및 테이블 나열
- 쿼리 실행 계획 설명

**사용 예시**:
```
"users 테이블의 스키마 보여줘"
"이번 주 가입한 사용자 수는?"
"orders 테이블에 대한 가장 느린 쿼리는?"
"모든 테이블과 행 수 보여줘"
```

#### Filesystem MCP 서버

**설정**:
```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": [
        "-y", "@modelcontextprotocol/server-filesystem",
        "/path/to/data"
      ]
    }
  }
}
```

**기능**:
- 허용된 디렉토리에서 파일 읽기/쓰기
- 디렉토리 내용 나열
- 파일 내용 검색
- 파일 생성 및 삭제

**사용 사례**: 프로젝트 디렉토리 외부의 파일(로그, 데이터 내보내기, 공유 드라이브)에 접근.

#### 여러 서버 결합

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": { "GITHUB_TOKEN": "ghp_xxx" }
    },
    "postgres": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": { "DATABASE_URL": "postgresql://..." }
    },
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/data"]
    }
  }
}
```

**크로스 서버 워크플로우**:
```
"GitHub 이슈 #42를 확인하고, 이슈에 언급된
사용자 수를 데이터베이스에서 확인한 다음, 결과를
보고서 파일로 저장해줘"
```

### 연습

**과제**: 인기 MCP 서버 설정 및 사용하기

작업에 맞는 서버를 선택하세요:
1. **GitHub**: GitHub을 자주 사용하는 경우
2. **PostgreSQL**: 로컬 데이터베이스가 있는 경우
3. **Filesystem**: 외부 디렉토리 접근이 필요한 경우

설정하고, 동작을 확인하고, 3가지 다른 작업을 시도하세요.

### 확인사항

다음으로 넘어가기 전에 확인하세요:
- [ ] 인기 MCP 서버 하나 이상 구성할 수 있음
- [ ] 대화에서 도구를 자연스럽게 사용할 수 있음
- [ ] 기능과 제한사항을 이해함
- [ ] 다른 도구 및 서버와 결합할 수 있음

---

## 레슨 58: MCP 범위

### 학습 목표
- 사용자, 프로젝트, 엔터프라이즈 MCP 범위 이해하기
- 서버 가시성 구성하기
- 범위 우선순위 관리하기
- 조직 정책 따르기

### 내용

#### MCP 서버 범위

**사용자 범위** (`~/.claude/settings.json`):
- 모든 프로젝트에서 사용 가능
- 개인 토큰과 서버
- 팀과 공유되지 않음

**프로젝트 범위** (`.claude/settings.json`):
- 프로젝트의 모든 사람이 사용 가능
- git으로 공유
- 프로젝트별 서비스

**엔터프라이즈 범위** (관리형 설정):
- 조직이 관리
- 모든 팀원에게 적용
- 재정의 불가

#### 범위 우선순위

```
엔터프라이즈 설정  →  최고 우선순위 (강제 적용)
사용자 설정        →  개인 기본값
프로젝트 설정      →  프로젝트별 설정
```

동일한 서버 이름이 여러 범위에 나타나면, 상위 범위가 우선합니다.

#### 각 범위의 사용 시점

**사용자 범위**:
- 개인 GitHub 토큰
- 개인 데이터베이스 접근
- 테스트 중인 실험적 서버

**프로젝트 범위**:
- 공유 개발 데이터베이스
- 프로젝트 문서 서비스
- 팀별 API (설정에 시크릿 없이)

**엔터프라이즈 범위**:
- 승인된 서버 목록
- 보안 스캔 서버
- 컴플라이언스 체크 도구
- 제한된 서버 구성

### 연습

**과제**: 다른 범위에서 MCP 구성하기

1. 사용자 설정에 서버 추가 (개인 용도)
2. 프로젝트 설정에 서버 추가 (팀 용도)
3. Claude 세션에서 둘 다 사용 가능한지 확인
4. 같은 이름으로 어떤 것이 우선하는지 테스트

### 확인사항

다음으로 넘어가기 전에 확인하세요:
- [ ] 세 가지 MCP 범위를 이해함
- [ ] 각 범위를 언제 사용하는지 앎
- [ ] 우선순위 작동 방식을 이해함
- [ ] 각 범위의 보안 영향을 이해함

---

## 레슨 59: 커스텀 MCP 서버 만들기

### 학습 목표
- MCP 서버 프로토콜 이해하기
- 기본 MCP 서버 만들기
- 도구, 리소스, 프롬프트 구현하기
- Claude로 서버 테스트하기

### 내용

#### MCP 서버 기본

MCP 서버는 다음과 같은 프로그램입니다:
1. stdio (stdin/stdout) 또는 HTTP를 통해 통신
2. JSON-RPC 메시지에 응답
3. 도구, 리소스, 및/또는 프롬프트를 노출

#### 최소 MCP 서버 (TypeScript)

**설정**:
```bash
mkdir my-mcp-server && cd my-mcp-server
npm init -y
npm install @modelcontextprotocol/sdk
npm install -D typescript @types/node
```

**`src/index.ts`**:
```typescript
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

const server = new Server(
  { name: "my-server", version: "1.0.0" },
  { capabilities: { tools: {} } }
);

// 도구 정의
server.setRequestHandler("tools/list", async () => ({
  tools: [
    {
      name: "get_weather",
      description: "Get current weather for a city",
      inputSchema: {
        type: "object",
        properties: {
          city: { type: "string", description: "City name" }
        },
        required: ["city"]
      }
    }
  ]
}));

// 도구 호출 처리
server.setRequestHandler("tools/call", async (request) => {
  if (request.params.name === "get_weather") {
    const city = request.params.arguments?.city;
    // 실제 서버에서는 여기서 날씨 API를 호출
    return {
      content: [
        {
          type: "text",
          text: `Weather in ${city}: 72°F, Sunny`
        }
      ]
    };
  }
  throw new Error(`Unknown tool: ${request.params.name}`);
});

// 서버 시작
const transport = new StdioServerTransport();
await server.connect(transport);
```

**빌드 및 구성**:
```bash
npx tsc
```

```json
// ~/.claude/settings.json에 추가
{
  "mcpServers": {
    "my-server": {
      "command": "node",
      "args": ["path/to/my-mcp-server/dist/index.js"]
    }
  }
}
```

#### 리소스 추가

```typescript
server.setRequestHandler("resources/list", async () => ({
  resources: [
    {
      uri: "weather://forecast/today",
      name: "Today's Forecast",
      description: "Current weather forecast"
    }
  ]
}));

server.setRequestHandler("resources/read", async (request) => {
  if (request.params.uri === "weather://forecast/today") {
    return {
      contents: [
        {
          uri: "weather://forecast/today",
          text: "Today: Sunny, high of 75°F, low of 55°F"
        }
      ]
    };
  }
  throw new Error(`Unknown resource: ${request.params.uri}`);
});
```

#### 프롬프트 추가

```typescript
server.setRequestHandler("prompts/list", async () => ({
  prompts: [
    {
      name: "weather-report",
      description: "Generate a weather report for a region",
      arguments: [
        { name: "region", description: "Geographic region", required: true }
      ]
    }
  ]
}));

server.setRequestHandler("prompts/get", async (request) => {
  if (request.params.name === "weather-report") {
    return {
      messages: [
        {
          role: "user",
          content: {
            type: "text",
            text: `Generate a detailed weather report for ${request.params.arguments?.region}`
          }
        }
      ]
    };
  }
  throw new Error(`Unknown prompt: ${request.params.name}`);
});
```

#### 서버 테스트

**수동 테스트**:
```bash
echo '{"jsonrpc":"2.0","id":1,"method":"tools/list"}' | node dist/index.js
```

**Claude로 테스트**:
```
claude
"my-server가 어떤 도구를 제공해?"
"샌프란시스코 날씨 알려줘"
```

### 연습

**과제**: 간단한 커스텀 MCP 서버 만들기

단일 도구를 제공하는 MCP 서버 만들기:
1. 공개 API 선택 (농담, 명언, 퀴즈 등)
2. API를 호출하는 도구 생성
3. Claude 설정에 구성
4. Claude 세션에서 테스트

### 확인사항

다음으로 넘어가기 전에 확인하세요:
- [ ] SDK로 기본 MCP 서버를 만들 수 있음
- [ ] 하나 이상의 도구를 구현할 수 있음
- [ ] 설정에서 서버를 구성할 수 있음
- [ ] Claude로 서버를 테스트할 수 있음

---

## 레슨 60: MCP 오류 처리와 캐싱

### 학습 목표
- MCP 서버에서 오류를 우아하게 처리하기
- API 응답에 캐싱 구현하기
- 속도 제한 관리하기
- 탄력적인 서버 만들기

### 내용

#### 오류 처리

**도구 핸들러에서**:
```typescript
server.setRequestHandler("tools/call", async (request) => {
  try {
    const result = await callExternalAPI(request.params.arguments);
    return {
      content: [{ type: "text", text: JSON.stringify(result) }]
    };
  } catch (error) {
    // 에러를 콘텐츠로 반환, throw하지 않음
    return {
      content: [
        {
          type: "text",
          text: `Error: ${error.message}. Please check your input and try again.`
        }
      ],
      isError: true
    };
  }
});
```

**처리해야 할 오류 유형**:
- 네트워크 오류 (API 접근 불가)
- 인증 실패 (만료된 토큰)
- 속도 제한 (너무 많은 요청)
- 잘못된 입력 (필수 필드 누락)
- 타임아웃 (느린 API 응답)

#### 캐싱

**간단한 인메모리 캐시**:
```typescript
const cache = new Map<string, { data: any; expires: number }>();
const CACHE_TTL = 5 * 60 * 1000; // 5분

function getCached(key: string): any | null {
  const entry = cache.get(key);
  if (entry && entry.expires > Date.now()) {
    return entry.data;
  }
  cache.delete(key);
  return null;
}

function setCache(key: string, data: any): void {
  cache.set(key, { data, expires: Date.now() + CACHE_TTL });
}
```

**도구에서 캐시 사용**:
```typescript
async function getWeather(city: string) {
  const cacheKey = `weather:${city}`;
  const cached = getCached(cacheKey);
  if (cached) return cached;

  const result = await fetchWeatherAPI(city);
  setCache(cacheKey, result);
  return result;
}
```

#### 속도 제한

**토큰 버킷 구현**:
```typescript
class RateLimiter {
  private tokens: number;
  private lastRefill: number;
  private maxTokens: number;
  private refillRate: number; // 초당 토큰

  constructor(maxTokens: number, refillRate: number) {
    this.tokens = maxTokens;
    this.maxTokens = maxTokens;
    this.refillRate = refillRate;
    this.lastRefill = Date.now();
  }

  async acquire(): Promise<void> {
    this.refill();
    if (this.tokens <= 0) {
      const waitTime = (1 / this.refillRate) * 1000;
      await new Promise(resolve => setTimeout(resolve, waitTime));
      this.refill();
    }
    this.tokens--;
  }

  private refill(): void {
    const now = Date.now();
    const elapsed = (now - this.lastRefill) / 1000;
    this.tokens = Math.min(this.maxTokens, this.tokens + elapsed * this.refillRate);
    this.lastRefill = now;
  }
}
```

### 연습

**과제**: MCP 서버에 오류 처리와 캐싱 추가하기

1. 모든 도구 핸들러에 try/catch 추가
2. 간단한 인메모리 캐시 구현
3. 속도 제한 추가 (분당 최대 10요청)
4. 오류 시나리오 테스트 (잘못된 입력, API 다운)

### 확인사항

다음으로 넘어가기 전에 확인하세요:
- [ ] 서버 충돌 없이 오류를 처리할 수 있음
- [ ] 기본 캐싱을 구현할 수 있음
- [ ] 속도 제한을 추가할 수 있음
- [ ] Claude에 도움되는 오류 메시지를 반환할 수 있음

---

## 레슨 61: 엔터프라이즈 환경의 MCP

### 학습 목표
- 팀을 위한 MCP 서버 배포하기
- 서버 접근 제어 관리하기
- MCP 사용량 모니터링하기
- 컴플라이언스 요구사항 따르기

### 내용

#### 엔터프라이즈 MCP 배포

**관리형 설정** (엔터프라이즈 강제):
```json
{
  "mcpServers": {
    "company-api": {
      "command": "npx",
      "args": ["-y", "@company/mcp-api-server"],
      "env": {
        "API_URL": "https://internal-api.company.com",
        "API_KEY": "${COMPANY_API_KEY}"
      }
    }
  },
  "allowedMcpServers": [
    "company-api",
    "github",
    "postgres"
  ],
  "blockedMcpServers": [
    "untrusted-server"
  ]
}
```

#### 접근 제어

**서버 허용 목록**:
- 승인된 MCP 서버만 사용 가능
- 엔터프라이즈 설정이 사용자 설정보다 우선
- 승인되지 않은 서버 설치 방지

**자격 증명 관리**:
- 시크릿 관리 시스템 사용 (Vault, AWS Secrets Manager)
- 자격 증명 자동 교체
- 자격 증명 사용 감사

#### 모니터링

조직 전체 MCP 사용량 추적:
- 가장 많이 사용되는 서버
- 서버별 오류율
- 데이터 접근 패턴
- 토큰 사용량 및 비용

#### 컴플라이언스 고려사항

- **데이터 거주지**: MCP 서버가 승인되지 않은 지역으로 데이터를 전송하지 않도록 보장
- **접근 로깅**: 감사 추적을 위해 모든 MCP 도구 호출 기록
- **데이터 분류**: MCP 서버가 접근할 수 있는 데이터 제한
- **승인 워크플로우**: 새 MCP 서버 설치에 승인 필요

### 연습

**과제**: 엔터프라이즈 MCP 전략 설계하기

다음을 포함하는 문서 작성:
1. 팀이 사용할 MCP 서버는?
2. 필요한 접근 제어는?
3. 자격 증명은 어떻게 관리할 것인가?
4. 어떤 모니터링을 구현할 것인가?

### 확인사항

다음으로 넘어가기 전에 확인하세요:
- [ ] 엔터프라이즈 MCP 배포 패턴을 이해함
- [ ] 접근 제어 메커니즘을 이해함
- [ ] 모니터링 및 감사 요구사항을 이해함
- [ ] 컴플라이언스 고려사항을 이해함

---

## 레슨 62: MCP 모범 사례

### 학습 목표
- MCP 개발 모범 사례 따르기
- 서버 성능 최적화하기
- 좋은 도구 인터페이스 설계하기
- 유지보수 계획하기

### 내용

#### 도구 설계 모범 사례

**1. 명확하고 설명적인 이름**:
```typescript
// 좋음
"get_user_by_email"
"create_github_issue"
"search_documentation"

// 나쁨
"getData"
"doThing"
"process"
```

**2. 완전한 입력 스키마**:
```typescript
{
  name: "search_issues",
  description: "Search GitHub issues by query, state, and labels",
  inputSchema: {
    type: "object",
    properties: {
      query: {
        type: "string",
        description: "Search query text"
      },
      state: {
        type: "string",
        enum: ["open", "closed", "all"],
        description: "Issue state filter (default: open)"
      },
      labels: {
        type: "array",
        items: { type: "string" },
        description: "Filter by label names"
      }
    },
    required: ["query"]
  }
}
```

**3. 도움이 되는 오류 메시지**:
```typescript
// 좋음
"Authentication failed: GitHub token expired. Generate a new token at
https://github.com/settings/tokens and update GITHUB_TOKEN in settings."

// 나쁨
"Error: 401 Unauthorized"
```

#### 성능 모범 사례

1. **적극적으로 캐싱** — 대부분의 데이터는 요청마다 변하지 않음
2. **배치 처리** — 가능한 경우 여러 API 호출 결합
3. **결과 페이지네이션** — 10,000개 결과를 한 번에 반환하지 않기
4. **적절한 타임아웃** — 느린 API가 영원히 멈추지 않게
5. **커넥션 풀링** — 데이터베이스 서버용

#### 유지보수 모범 사례

1. **서버 버전 관리** — semver 사용
2. **호환성 깨지는 변경 문서화** — 변경 로그에 기록
3. **철저한 테스트** — 모든 도구에 단위 테스트
4. **오류 모니터링** — 실패 추적 및 수정
5. **의존성 업데이트** — 보안 패치 중요

#### 일반적인 실수

| 실수 | 해결 방법 |
|------|----------|
| 불필요하게 쓰기 접근 노출 | 기본적으로 읽기 전용 도구 사용 |
| 입력 검증 안 함 | 모든 매개변수 검증 |
| 속도 제한 무시 | 속도 제한 구현 |
| 자격 증명 하드코딩 | 환경 변수 사용 |
| 오류 처리 없음 | 모든 오류 잡아서 도움되는 메시지 반환 |
| 너무 많은 데이터 반환 | 페이지네이션 및 요약 |

### 연습

**과제**: MCP 서버 검토 및 개선하기

만든 MCP 서버에 모범 사례 체크리스트를 적용하세요:
- [ ] 모든 도구에 명확한 이름과 설명이 있음
- [ ] 입력 스키마가 설명과 함께 완전함
- [ ] 오류가 도움되는 메시지를 반환함
- [ ] 캐싱이 구현됨
- [ ] 속도 제한이 적용됨
- [ ] 자격 증명이 환경 변수에 있음
- [ ] 서버가 버전 관리됨
- [ ] 각 도구에 테스트가 있음

### 확인사항

다음으로 넘어가기 전에 확인하세요:
- [ ] 잘 구조화된 MCP 도구를 설계할 수 있음
- [ ] 성능 모범 사례를 따를 수 있음
- [ ] 유지보수 및 업데이트를 계획할 수 있음
- [ ] 일반적인 MCP 실수를 피할 수 있음

---

## 섹션 9 요약

Model Context Protocol을 마스터했습니다:

| 레슨 | 주제 | 핵심 교훈 |
|------|------|----------|
| 51 | MCP 개요 | Claude를 외부 서비스와 연결하는 프로토콜 |
| 52 | 서버 설치 | settings.json에서 명령어와 환경 변수로 구성 |
| 53 | 도구 사용 | 대화에서 자연스럽게 MCP 도구 호출 |
| 54 | 리소스 | @-멘션으로 원격 데이터 접근 |
| 55 | 프롬프트 | MCP 서버의 재사용 가능한 템플릿 |
| 56 | 인증 | 안전한 자격 증명 관리 |
| 57 | 인기 서버 | GitHub, Postgres, Filesystem 서버 |
| 58 | 범위 | 사용자, 프로젝트, 엔터프라이즈 범위 지정 |
| 59 | 커스텀 서버 | SDK로 자신만의 MCP 서버 만들기 |
| 60 | 오류 처리 | 캐싱, 속도 제한, 탄력성 |
| 61 | 엔터프라이즈 | 팀 배포 및 컴플라이언스 |
| 62 | 모범 사례 | 설계, 성능, 유지보수 |

### 다음 단계

**섹션 10: 커스텀 서브에이전트**에서는 특화된 기능, 제어된 도구 접근, 특정 워크플로우에 최적화된 전문 AI 에이전트를 만드는 방법을 배웁니다.
