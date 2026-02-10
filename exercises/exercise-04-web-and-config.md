# 연습 4: 웹 도구와 설정

**소요 시간**: 45분
**사전 조건**: 레슨 18-25

## 목표

웹 도구를 활용한 조사와 CLAUDE.md 및 설정을 통한 프로젝트 구성을 실습합니다.

## 설정

```bash
mkdir -p ~/claude-exercises/ex4
cd ~/claude-exercises/ex4
git init
npm init -y
echo "const express = require('express')" > app.js
claude
```

## 과제

### 과제 1: 웹 검색으로 최신 정보 조사 (10분)

Claude에게 요청: "2024년 Express.js 에러 처리 모범 사례가 뭐야? 웹을 검색해서 요약해줘."

**확인**: Claude가 WebSearch를 사용하고 출처가 포함된 권장 사항을 제공함

Claude에게 요청: "Express.js 최신 버전과 변경 사항을 검색해줘"

**확인**: Claude가 출처와 함께 현재 버전 정보를 제공함

---

### 과제 2: 웹 페치로 문서 가져오기 (10분)

Claude에게 요청: "Express.js 시작 가이드를 가져와서 그들의 권장 설정에 기반한 기본 app.js를 만들어줘"

**확인**: Claude가 문서를 가져와서 올바르게 구성된 Express 앱을 생성함

---

### 과제 3: CLAUDE.md 생성 (10분)

Claude에게 요청: "이 프로젝트의 CLAUDE.md를 만들어줘. Node.js Express API 프로젝트이고 다음을 사용해야 해:
- ESM imports (import/export)
- 모든 비동기 작업에 async/await
- 2칸 들여쓰기
- 테스트에 Jest 사용
- 서술적인 에러 메시지"

**확인**: CLAUDE.md가 프로젝트 컨텍스트, 스타일 규칙, 규약을 포함하여 생성됨

---

### 과제 4: CLAUDE.md 동작 확인 (5분)

같은 디렉토리에서 새 Claude 세션을 시작합니다:
```bash
exit  # 현재 세션 종료
claude  # 새로 시작 — Claude가 CLAUDE.md를 읽음
```

Claude에게 요청: "GET과 POST 엔드포인트가 있는 products 라우트 파일을 새로 만들어줘"

**확인**: Claude가 CLAUDE.md의 규약을 따름 (ESM imports, async/await, 2칸 들여쓰기)

---

### 과제 5: 설정 탐색 (10분)

Claude에게 요청: ".claude 디렉토리 구조를 보여주고 각 파일이 무엇을 하는지 설명해줘"

**확인**: Claude가 설정 계층 구조를 설명함

Claude에게 요청: "현재 어떤 권한 모드를 사용하고 있어?"

**확인**: Claude가 현재 권한 설정을 설명함

## 완료 기준

- [ ] WebSearch를 사용하여 최신 정보를 검색함
- [ ] WebFetch를 사용하여 문서를 읽음
- [ ] 동작하는 CLAUDE.md를 생성함
- [ ] Claude가 CLAUDE.md 규약을 따르는지 확인함
- [ ] 설정 구조와 범위를 이해함

## 보너스 도전

프로젝트에 커스텀 권한 규칙이 포함된 `.claude/settings.json`을 만들어 보세요:
```
"모든 git 명령어 허용"
"npm test와 npm run build 허용"
```
