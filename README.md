# Claude Code 부트캠프 🎓

**완전 초보자에서 전문가까지, Claude Code의 모든 것을 마스터하세요**

[![레슨](https://img.shields.io/badge/레슨-100%2B-blue)](./CURRICULUM.md)
[![프로젝트](https://img.shields.io/badge/프로젝트-7개-green)](./projects/)
[![한국어](https://img.shields.io/badge/언어-한국어-red)](./README.md)

---

## 📖 소개

Claude Code 부트캠프는 Anthropic의 Claude Code CLI 도구를 완전히 마스터하기 위한 종합 학습 과정입니다. 100개 이상의 레슨, 실습 프로젝트, 그리고 전문가 수준의 기법까지 모두 다룹니다.

### 이 부트캠프의 특징

- **📚 100+ 레슨**: 체계적으로 구성된 커리큘럼
- **🏗️ 7개 프로젝트**: 실제 적용 가능한 프로젝트
- **📝 실습 연습**: 직접 해보며 배우기
- **📋 템플릿**: 바로 사용 가능한 템플릿
- **🎓 전문가 가이드**: 상위 0.1% 기법

---

## 🚀 빠른 시작

### 필수 조건

```bash
# Node.js 18+ 필요
node --version

# Claude Code 설치
npm install -g @anthropic-ai/claude-code

# 설치 확인
claude --version
```

### 시작하기

1. **이 저장소 클론**
   ```bash
   git clone https://github.com/sungeunkim0127/claude-code-bootcamp-ko.git
   cd claude-code-bootcamp-ko
   ```

2. **시작 문서 열기**
   ```bash
   # START-HERE.md 읽기
   cat START-HERE.md
   ```

3. **첫 번째 레슨 시작**
   ```bash
   # 레슨 시작
   cat lessons/beginner/section-1-getting-started.md
   ```

---

## 📚 커리큘럼 개요

### 🟢 초급 (레슨 1-25)
**목표**: Claude Code 기초 익히기

| 섹션 | 내용 | 레슨 |
|------|------|------|
| 1 | 시작하기 | 1-5 |
| 2 | 핵심 파일 도구 | 6-12 |
| 3 | Git 통합 | 13-17 |
| 4 | 웹 도구 | 18-20 |
| 5 | 기본 설정 | 21-25 |

### 🟡 중급 (레슨 26-50)
**목표**: 고급 도구 및 자동화 마스터

| 섹션 | 내용 | 레슨 |
|------|------|------|
| 6 | 고급 도구 사용법 | 26-32 |
| 7 | 스킬 시스템 | 33-42 |
| 8 | 훅 시스템 | 43-50 |

### 🟠 고급 (레슨 51-80)
**목표**: MCP, 서브에이전트, IDE 통합

| 섹션 | 내용 | 레슨 |
|------|------|------|
| 9 | MCP (Model Context Protocol) | 51-62 |
| 10 | 커스텀 서브에이전트 | 63-70 |
| 11 | 플랜 모드 | 71-74 |
| 12 | IDE 통합 | 75-80 |

### 🔴 전문가 (레슨 81-100+)
**목표**: 기업 배포 및 최적화

| 섹션 | 내용 | 레슨 |
|------|------|------|
| 13 | 플러그인 | 81-86 |
| 14 | 고급 워크플로우 | 87-92 |
| 15 | 성능 최적화 | 93-96 |
| 16 | 기업 및 보안 | 97-100+ |

---

## 🏗️ 프로젝트

### 초급 프로젝트
1. **Todo CLI** - 명령줄 할일 관리 앱
2. **문서 생성기** - 자동 문서화 시스템

### 중급 프로젝트
3. **스킬 라이브러리** - 커스텀 스킬 모음
4. **테스트 스위트** - 자동 테스트 생성

### 고급 프로젝트
5. **멀티 에이전트 워크플로우** - 협업 에이전트 시스템
6. **MCP 서버** - 커스텀 MCP 서버 구축

### 전문가 프로젝트
7. **기업 배포** - 조직 전체 배포 시스템

---

## 📁 디렉토리 구조

```
claude-code-bootcamp-ko/
├── START-HERE.md           # 시작점
├── README.md               # 이 파일
├── CURRICULUM.md           # 전체 커리큘럼
├── PROGRESS.md             # 진행 추적
├── QUICK-START.md          # 빠른 시작 가이드
├── EXPERT-MASTERY-GUIDE.md # 전문가 가이드
│
├── lessons/                # 레슨 파일
│   ├── beginner/          # 초급 (1-25)
│   ├── intermediate/      # 중급 (26-50)
│   ├── advanced/          # 고급 (51-80)
│   └── expert/            # 전문가 (81-100+)
│
├── projects/               # 프로젝트
│   ├── beginner/
│   ├── intermediate/
│   ├── advanced/
│   └── expert/
│
├── exercises/              # 연습문제
├── templates/              # 템플릿
└── resources/              # 참고 자료
    ├── CHEAT-SHEET.md     # 명령어 치트시트
    ├── TROUBLESHOOTING.md # 문제 해결
    ├── FAQ.md             # 자주 묻는 질문
    └── example-*/         # 예제 파일
```

---

## 🎯 학습 경로

### 경로 1: 빠른 시작 (2주)
- 초급 레슨 완료
- 프로젝트 1개 완성
- 기본 활용 가능

### 경로 2: 전문가 과정 (4주)
- 초급 + 중급 완료
- 스킬 및 훅 마스터
- 프로젝트 3개 완성

### 경로 3: 마스터 과정 (6-8주)
- 전체 커리큘럼 완료
- 모든 프로젝트 완성
- 기업 배포 준비 완료

---

## 💡 핵심 파일 안내

| 파일 | 용도 | 언제 사용 |
|------|------|----------|
| `START-HERE.md` | 시작점 | 처음 시작할 때 |
| `CURRICULUM.md` | 전체 레슨 목록 | 레슨 찾을 때 |
| `PROGRESS.md` | 진행 추적 | 매일 학습 후 |
| `resources/CHEAT-SHEET.md` | 명령어 참조 | 명령어 찾을 때 |
| `resources/TROUBLESHOOTING.md` | 문제 해결 | 에러 발생 시 |

---

## 🤝 기여하기

개선 사항이나 추가 내용이 있으면 Pull Request를 보내주세요!

---

## 📄 라이선스

이 프로젝트는 MIT 라이선스 하에 배포됩니다.

---

## 🙏 감사의 글

- Anthropic의 Claude Code 팀
- 오픈소스 커뮤니티

---

**지금 바로 시작하세요!** 👉 [START-HERE.md](./START-HERE.md)
