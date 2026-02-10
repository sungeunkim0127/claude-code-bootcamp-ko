# 연습 17: 성능 및 비용 최적화

**소요 시간**: 45분
**사전 조건**: 레슨 93-96

## 목표
토큰 사용량을 최소화하고, 도구 호출을 줄이며, 적절한 모델을 선택하여 속도와 비용을 최적화합니다.

## 셋업
```bash
mkdir ~/perf-optimization-lab && cd ~/perf-optimization-lab && git init
mkdir -p src/{utils,services} config
for i in $(seq 1 5); do echo "export function util$i() { return 'utility $i'; }" > src/utils/helper$i.js; done
for i in $(seq 1 3); do echo "export class Service$i { constructor() { this.name = 'svc$i'; } }" > src/services/service$i.js; done
echo "export const config = { debug: false, port: 3000 };" > config/app.js
echo '{ "name": "perf-lab", "version": "1.0.0" }' > package.json
git add -A && git commit -m "Initial setup"
```

## 과제
### 과제 1: 도구 사용의 비효율성 감사 (10분)
**비효율적** -- 세션을 시작하고 파일을 하나씩 읽습니다:
```
You: "Read src/utils/helper1.js"
You: "Read src/utils/helper2.js"
...5개 파일 모두 반복...
You: "Summarize what these do"
```
이것은 5번의 Read 호출 + 분석입니다. 이제 최적화된 접근 방식으로 **새 세션**을 시작합니다:
```
You: "Read all files in src/utils/ and summarize what each function does"
```
Claude가 읽기를 더 적은 도구 호출로 일괄 처리합니다.

**확인**: 두 세션에서 `/cost`를 실행합니다. 최적화된 세션이 더 적은 도구 호출과 전체 토큰을 사용합니다.
---
### 과제 2: 효율적인 프롬프트 작성 (10분)
순차적 요청 대신 관련 요청을 단일 프롬프트로 결합합니다:
```
# 나쁨: 3개의 별도 프롬프트
You: "Add a logger import to src/services/service1.js"
You: "Add logging in the constructor"
You: "Add an error handler method"

# 좋음: 1개의 프롬프트
You: "Update src/services/service1.js: add a logger import, logging in the constructor,
and an error handler method that logs and rethrows."
```
검색도 마찬가지입니다 -- 함수, 클래스, 설정을 별도로 요청하는 대신 모든 export를 한 번에 요청합니다.

**확인**: 각 접근 방식 후 `/cost`를 실행합니다. 단일 프롬프트 버전이 더 적은 교환으로 동일한 작업을 완료합니다.
---
### 과제 3: 모델 비용 비교 (10분)
모델을 전환하고 적절한 티어에서 작업을 실행합니다:
```
You: /model haiku
You: "Rename variable 'i' to 'index' in src/utils/helper1.js"

You: /model sonnet
You: "Refactor src/services/service1.js to use dependency injection"

You: /model opus
You: "Analyze the project and design an authentication layer with JWT and RBAC"
```
`/cost` 출력에서 참조표를 만듭니다:

| 작업 유형 | 모델 | 근거 |
|-----------|------|------|
| 이름 변경, 포맷팅 | Haiku | 빠르고, 저렴하고, 충분한 품질 |
| 리팩토링, 기능 구현 | Sonnet | 품질과 비용의 균형 |
| 아키텍처, 계획 수립 | Opus | 복잡한 작업에 최고의 추론 |

**확인**: 최소 두 개의 모델을 사용했습니다. `/cost`가 모델별 분석을 보여줍니다.
---
### 과제 4: /compact로 컨텍스트 관리 (10분)
컨텍스트를 쌓은 다음 압축합니다:
```
You: "Read every file in src/ and summarize each"
You: "List all function and class names across the project"
You: "What design patterns are used?"
You: /cost                    # 전체 컨텍스트 토큰 기록
You: /compact Focus on project structure only
You: /cost                    # 컨텍스트가 더 작아야 함
You: "Add a new AuthService in src/services/ matching existing patterns"
```
**확인**: `/compact` 후 컨텍스트 토큰이 줄어듭니다. Claude가 여전히 올바르게 구조화된 서비스 파일을 생성합니다.
---
### 과제 5: 터미널 및 인덱싱 최적화 (5분)
속도를 위해 환경을 구성합니다:
```bash
# 셸 시작 시간 확인
time bash -i -c exit

# Claude 설정 확인
claude config list

# 프로덕션에서 verbose 모드 비활성화
claude config set --global verbose false
```
프로젝트 루트에 `.claudeignore`를 생성합니다:
```
node_modules/
dist/
build/
coverage/
*.min.js
*.map
.git/
```
**확인**: `.claudeignore`가 존재합니다. `claude config list`가 예상된 값을 보여줍니다. 새 Claude 세션이 반응성 있게 느껴집니다.

## 완료 기준
- [ ] 측정 가능한 차이로 일괄 읽기 대 순차 읽기를 시연함
- [ ] 도구 호출을 줄이는 결합된 프롬프트를 작성함
- [ ] 최소 두 개의 모델을 사용하고 비용 차이를 문서화함
- [ ] /compact를 사용하여 세션 중간에 컨텍스트를 줄임
- [ ] 합리적인 제외 항목으로 .claudeignore를 생성함

## 보너스 도전
- 동일한 5가지 작업을 Haiku, Sonnet, Opus에서 A/B 테스트합니다 -- 시간, 비용, 품질(1-5)을 비교 표에 기록합니다.
- 리뷰, 리팩토링, 테스트를 위한 최적화된 프롬프트가 포함된 프롬프트 템플릿 라이브러리를 `docs/prompt-templates/`에 생성합니다.
