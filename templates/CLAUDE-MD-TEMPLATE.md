# CLAUDE.md 템플릿

프로젝트에 맞는 CLAUDE.md를 만들어 Claude가 프로젝트를 더 잘 이해하도록 하세요.

---

## 기본 템플릿

```markdown
# 프로젝트 이름

## 개요
프로젝트가 무엇을 하는지 간단히 설명합니다.

## 기술 스택
- Frontend: React, TypeScript
- Backend: Node.js, Express
- Database: PostgreSQL
- 기타: Docker, Redis

## 프로젝트 구조
src/
├── components/    # React 컴포넌트
├── pages/        # 페이지 컴포넌트
├── api/          # API 라우트
├── utils/        # 유틸리티 함수
└── types/        # TypeScript 타입

## 주요 명령어
- npm run dev    - 개발 서버 시작
- npm test       - 테스트 실행
- npm run build  - 프로덕션 빌드
- npm run lint   - 린트 실행

## 코딩 규칙
- 함수형 컴포넌트 사용
- async/await 선호
- 한글 주석 허용
```

---

## 종합 템플릿 (상세)

```markdown
# 프로젝트 이름

## 개요
이 프로젝트는 [목적]을 위한 [유형] 애플리케이션입니다.

## 기술 스택

### Frontend
- Framework: React 18
- Language: TypeScript 5
- Styling: Tailwind CSS
- State: Zustand

### Backend
- Runtime: Node.js 20
- Framework: Express
- ORM: Prisma
- Database: PostgreSQL

### Infrastructure
- Container: Docker
- CI/CD: GitHub Actions
- Hosting: AWS

## 프로젝트 구조

\`\`\`
project/
├── src/
│   ├── components/     # 재사용 컴포넌트
│   │   ├── common/    # 공통 컴포넌트
│   │   └── features/  # 기능별 컴포넌트
│   ├── pages/         # 페이지
│   ├── api/           # API 라우트
│   ├── hooks/         # 커스텀 훅
│   ├── utils/         # 유틸리티
│   ├── types/         # 타입 정의
│   └── styles/        # 스타일
├── tests/             # 테스트
├── prisma/            # DB 스키마
└── docs/              # 문서
\`\`\`

## 명령어

### 개발
\`\`\`bash
npm run dev        # 개발 서버
npm run build      # 빌드
npm run start      # 프로덕션 실행
\`\`\`

### 테스트
\`\`\`bash
npm test           # 전체 테스트
npm run test:unit  # 단위 테스트
npm run test:e2e   # E2E 테스트
\`\`\`

### 데이터베이스
\`\`\`bash
npm run db:migrate # 마이그레이션
npm run db:seed    # 시드 데이터
npm run db:studio  # Prisma Studio
\`\`\`

## 코딩 컨벤션

### 컴포넌트
- 함수형 컴포넌트만 사용
- Props는 interface로 정의
- 컴포넌트당 하나의 파일

### 네이밍
- 컴포넌트: PascalCase (Button.tsx)
- 함수/변수: camelCase
- 상수: UPPER_SNAKE_CASE
- 파일: kebab-case (api-utils.ts)

### 스타일
- Tailwind 클래스 사용
- 복잡한 스타일은 @apply
- 반응형: mobile-first

### 에러 처리
- try-catch 필수
- 사용자 친화적 메시지
- 에러 로깅 필수

## 환경 변수

\`\`\`
DATABASE_URL=postgresql://...
NEXT_PUBLIC_API_URL=...
JWT_SECRET=...
\`\`\`

⚠️ .env 파일은 절대 커밋하지 마세요!

## 중요 규칙

1. **커밋 전 테스트 실행**
2. **PR은 리뷰 후 머지**
3. **main 브랜치 직접 푸시 금지**
4. **console.log 사용 금지** (logger 사용)

## 알려진 이슈

- [ ] 대량 데이터 로딩 시 성능 저하
- [ ] Safari에서 일부 애니메이션 문제

## 담당자

- Frontend: @frontend-team
- Backend: @backend-team
- DevOps: @devops-team
```

---

## 최소 템플릿

```markdown
# 프로젝트 이름

Node.js + Express API

## 명령어
- npm run dev - 개발
- npm test - 테스트

## 구조
- src/routes/ - API 라우트
- src/models/ - DB 모델
- src/utils/ - 유틸리티
```

---

## API 프로젝트 템플릿

```markdown
# API 프로젝트

## 개요
RESTful API 서버

## 기술 스택
- Node.js + Express
- PostgreSQL + Prisma
- Jest

## 엔드포인트 구조

\`\`\`
/api
├── /auth
│   ├── POST /login
│   ├── POST /register
│   └── POST /logout
├── /users
│   ├── GET /
│   ├── GET /:id
│   ├── PUT /:id
│   └── DELETE /:id
└── /products
    ├── GET /
    └── POST /
\`\`\`

## 응답 형식

\`\`\`json
{
  "success": true,
  "data": {},
  "error": null
}
\`\`\`

## 인증
- JWT 토큰 사용
- Authorization: Bearer <token>

## 에러 코드
- 400: 잘못된 요청
- 401: 인증 필요
- 403: 권한 없음
- 404: 리소스 없음
- 500: 서버 에러
```

---

## React 프로젝트 템플릿

```markdown
# React 앱

## 개요
React + TypeScript SPA

## 기술 스택
- React 18 + TypeScript
- React Router v6
- TanStack Query
- Tailwind CSS

## 컴포넌트 구조

\`\`\`
src/components/
├── common/          # Button, Input, Modal 등
├── layout/          # Header, Footer, Sidebar
└── features/        # 기능별 컴포넌트
    ├── auth/
    ├── dashboard/
    └── settings/
\`\`\`

## 상태 관리
- 서버 상태: TanStack Query
- 클라이언트 상태: Zustand
- 폼 상태: React Hook Form

## 컴포넌트 패턴

\`\`\`tsx
interface Props {
  title: string;
  onClick: () => void;
}

export function Component({ title, onClick }: Props) {
  return (
    <button onClick={onClick}>
      {title}
    </button>
  );
}
\`\`\`
```

---

## 저장 위치

| 위치 | 우선순위 |
|------|----------|
| `CLAUDE.md` (루트) | 높음 |
| `.claude/CLAUDE.md` | 중간 |
| `~/.claude/CLAUDE.md` | 낮음 (기본값) |

---

## 팁

1. **간결하게**: 필요한 정보만
2. **최신 유지**: 변경 시 업데이트
3. **구체적으로**: 모호함 피하기
4. **예시 포함**: 코드 예시 추가
5. **Git 커밋**: 팀과 공유

---

이 템플릿을 기반으로 프로젝트에 맞게 수정하세요!
