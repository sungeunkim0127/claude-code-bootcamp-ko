# 프로젝트 15: 커스텀 MCP 서버 구축

**레벨**: 고급
**소요 시간**: 6-8시간
**사전 조건**: 레슨 51-62 (MCP 섹션) 완료

---

## 개요

Claude Code를 OpenWeatherMap API에 연결하는 Model Context Protocol (MCP) 서버를 구축합니다. 타입이 지정된 도구 정의, 인메모리 캐싱, 속도 제한, 재시도 로직, 포괄적인 에러 처리를 갖춘 프로덕션 품질의 서버를 만들게 됩니다. 완성되면 Claude가 자연어 대화를 통해 날씨 데이터를 가져오고, 예보를 검색하고, API 상태를 확인할 수 있습니다.

OpenWeatherMap은 넉넉한 무료 티어(1일 1,000회 호출), 예측 가능한 JSON, 간단한 API 키 인증을 제공하여 MCP 서버 개발 학습에 이상적입니다.

---

## 아키텍처

```
┌──────────────────────────────────────────────────────────────┐
│                       Claude Code                            │
│  "도쿄 날씨가 어때?" --> get_weather 도구 호출               │
└────────────────────────────┬─────────────────────────────────┘
                             │ MCP Protocol (stdio)
                             ▼
┌──────────────────────────────────────────────────────────────┐
│                   MCP Weather Server                         │
│  ┌──────────┐  ┌──────────────┐  ┌────────────────────────┐ │
│  │  Tools    │  │  Middleware   │  │  API Client            │ │
│  │get_weather│  │ Cache Layer  │  │ OpenWeatherMap REST    │ │
│  │search_   │──│ Rate Limiter │──│ Authentication         │ │
│  │ forecast │  │ Retry Logic  │  │ Response Parsing       │ │
│  │get_api_  │  │              │  │                        │ │
│  │ status   │  │              │  │                        │ │
│  └──────────┘  └──────────────┘  └────────────────────────┘ │
└──────────────────────────────────────────────────────────────┘
                             │
                             ▼
                ┌──────────────────────┐
                │  OpenWeatherMap API   │
                └──────────────────────┘
```

### 프로젝트 구조

```
weather-mcp-server/
├── src/
│   ├── index.ts           # 진입점
│   ├── server.ts          # MCP 서버 + 도구 등록
│   ├── services/
│   │   └── weatherApi.ts  # OpenWeatherMap API 클라이언트
│   ├── middleware/
│   │   ├── cache.ts       # TTL 기반 인메모리 캐시
│   │   └── rateLimiter.ts # 슬라이딩 윈도우 속도 제한기
│   ├── utils/
│   │   └── retry.ts       # 지수 백오프 재시도
│   └── types.ts           # 공유 TypeScript 인터페이스
├── tests/
│   ├── middleware/         # 캐시 + 속도 제한기 테스트
│   ├── tools/             # API 클라이언트 테스트
│   └── integration/       # 엔드투엔드 서버 테스트
├── package.json
├── tsconfig.json
└── .env.example
```

---

## 페이즈 1: 프로젝트 설정 및 설계 (1시간)

### 태스크 1.1: 프로젝트 초기화

```bash
mkdir weather-mcp-server && cd weather-mcp-server
npm init -y
npm install @modelcontextprotocol/sdk zod
npm install -D typescript @types/node vitest tsx
```

### 태스크 1.2: TypeScript와 package.json 설정

strict 모드, ES2022 타겟, Node16 모듈 해상도로 `tsconfig.json`을 생성하세요. `package.json`에 `"type": "module"`을 설정하고 `build` (tsc), `dev` (tsx), `test` (vitest run), `start` (node dist/index.js) 스크립트를 추가하세요.

### 태스크 1.3: 공유 타입 정의

`src/types.ts` 생성:

```typescript
export interface WeatherData {
  city: string; country: string;
  temperature: { current: number; feelsLike: number; min: number; max: number };
  conditions: { main: string; description: string; icon: string };
  wind: { speed: number; direction: number };
  humidity: number; visibility: number; timestamp: string;
}

export interface ForecastEntry {
  datetime: string;
  temperature: { current: number; min: number; max: number };
  conditions: { main: string; description: string };
  precipitation: number;
  wind: { speed: number; direction: number };
}

export interface ForecastData { city: string; country: string; entries: ForecastEntry[] }

export interface ApiStatusData {
  operational: boolean; responseTimeMs: number;
  rateLimitRemaining: number;
  cacheStats: { entries: number; hitRate: number };
}

export interface CacheEntry<T> { data: T; expiresAt: number }

export interface ServerConfig {
  apiKey: string; cacheTtlSeconds: number;
  rateLimitPerMinute: number; baseUrl: string;
}
```

---

## 페이즈 2: 코어 서버 구현 (2시간)

### 태스크 2.1: API 클라이언트 구축

`src/services/weatherApi.ts`를 생성하세요. 이 클래스가 모든 OpenWeatherMap 통신을 처리합니다:

```typescript
import { WeatherData, ForecastData, ServerConfig } from "../types.js";

export class WeatherApiClient {
  constructor(private config: ServerConfig) {}

  async getCurrentWeather(city: string, units = "metric"): Promise<WeatherData> {
    const url = new URL(`${this.config.baseUrl}/weather`);
    url.searchParams.set("q", city);
    url.searchParams.set("units", units);
    url.searchParams.set("appid", this.config.apiKey);

    const response = await fetch(url.toString());
    if (!response.ok) {
      if (response.status === 401) throw new Error("Invalid API key.");
      if (response.status === 404) throw new Error(`City not found: "${city}".`);
      if (response.status === 429) throw new Error("Rate limit exceeded.");
      throw new Error(`API error ${response.status}: ${await response.text()}`);
    }

    const raw = await response.json() as Record<string, any>;
    return {
      city: raw.name, country: raw.sys.country,
      temperature: { current: raw.main.temp, feelsLike: raw.main.feels_like,
                     min: raw.main.temp_min, max: raw.main.temp_max },
      conditions: { main: raw.weather[0].main,
                    description: raw.weather[0].description, icon: raw.weather[0].icon },
      wind: { speed: raw.wind.speed, direction: raw.wind.deg },
      humidity: raw.main.humidity, visibility: raw.visibility,
      timestamp: new Date(raw.dt * 1000).toISOString(),
    };
  }

  async getForecast(city: string, days = 5, units = "metric"): Promise<ForecastData> {
    // 동일 패턴: URL 구성, fetch, 에러 처리, 응답 파싱
    // 무료 티어는 5일/3시간 예보 반환; days * 8 항목으로 슬라이스
    const url = new URL(`${this.config.baseUrl}/forecast`);
    url.searchParams.set("q", city);
    url.searchParams.set("units", units);
    url.searchParams.set("appid", this.config.apiKey);

    const response = await fetch(url.toString());
    if (!response.ok) {
      if (response.status === 404) throw new Error(`City not found: "${city}".`);
      throw new Error(`API error ${response.status}: ${await response.text()}`);
    }
    const raw = await response.json() as Record<string, any>;
    const entries = raw.list.slice(0, days * 8).map((item: any) => ({
      datetime: item.dt_txt,
      temperature: { current: item.main.temp, min: item.main.temp_min, max: item.main.temp_max },
      conditions: { main: item.weather[0].main, description: item.weather[0].description },
      precipitation: item.pop ?? 0,
      wind: { speed: item.wind.speed, direction: item.wind.deg },
    }));
    return { city: raw.city.name, country: raw.city.country, entries };
  }

  async healthCheck(): Promise<{ ok: boolean; responseTimeMs: number }> {
    const start = Date.now();
    try {
      const url = new URL(`${this.config.baseUrl}/weather`);
      url.searchParams.set("q", "London");
      url.searchParams.set("appid", this.config.apiKey);
      const r = await fetch(url.toString());
      return { ok: r.ok, responseTimeMs: Date.now() - start };
    } catch { return { ok: false, responseTimeMs: Date.now() - start }; }
  }
}
```

### 태스크 2.2: 도구 등록이 포함된 MCP 서버 생성

프로젝트의 핵심인 `src/server.ts`를 생성하세요:

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { z } from "zod";
import { WeatherApiClient } from "./services/weatherApi.js";
import { Cache } from "./middleware/cache.js";
import { RateLimiter } from "./middleware/rateLimiter.js";
import { withRetry } from "./utils/retry.js";
import { ServerConfig, WeatherData, ForecastData } from "./types.js";

export function createWeatherServer(config: ServerConfig): McpServer {
  const server = new McpServer({ name: "weather-server", version: "1.0.0" });
  const api = new WeatherApiClient(config);
  const cache = new Cache(config.cacheTtlSeconds);
  const limiter = new RateLimiter(config.rateLimitPerMinute);

  server.tool("get_weather", "Get current weather for a city.", {
    city: z.string().describe("City name, e.g. 'Tokyo'"),
    units: z.enum(["metric", "imperial"]).default("metric"),
  }, async ({ city, units }) => {
    const key = `weather:${city.toLowerCase()}:${units}`;
    const cached = cache.get<WeatherData>(key);
    if (cached) return { content: [{ type: "text" as const, text: fmtWeather(cached, true) }] };
    if (!limiter.allowRequest()) return { content: [{ type: "text" as const, text: "Rate limit reached." }] };
    const data = await withRetry(() => api.getCurrentWeather(city, units));
    cache.set(key, data);
    return { content: [{ type: "text" as const, text: fmtWeather(data, false) }] };
  });

  server.tool("search_forecast", "Get multi-day forecast (up to 5 days).", {
    city: z.string(), days: z.number().int().min(1).max(5).default(3),
    units: z.enum(["metric", "imperial"]).default("metric"),
  }, async ({ city, days, units }) => {
    const key = `forecast:${city.toLowerCase()}:${days}:${units}`;
    const cached = cache.get<ForecastData>(key);
    if (cached) return { content: [{ type: "text" as const, text: fmtForecast(cached, true) }] };
    if (!limiter.allowRequest()) return { content: [{ type: "text" as const, text: "Rate limit reached." }] };
    const data = await withRetry(() => api.getForecast(city, days, units));
    cache.set(key, data);
    return { content: [{ type: "text" as const, text: fmtForecast(data, false) }] };
  });

  server.tool("get_api_status", "Check API health and cache stats.", {}, async () => {
    const h = await api.healthCheck();
    const s = cache.getStats();
    const text = [`API Status`, `Operational: ${h.ok ? "Yes" : "No"}`,
      `Response time: ${h.responseTimeMs}ms`, `Rate limit remaining: ${limiter.remaining()}`,
      `Cache: ${s.entries} entries, ${Math.round(s.hitRate * 100)}% hit rate`].join("\n");
    return { content: [{ type: "text" as const, text }] };
  });

  return server;
}

function fmtWeather(d: WeatherData, cached: boolean): string {
  const tag = cached ? " (cached)" : "";
  return [`Weather for ${d.city}, ${d.country}${tag}`,
    `Temp: ${d.temperature.current} (feels like ${d.temperature.feelsLike})`,
    `${d.conditions.main}: ${d.conditions.description}`,
    `Humidity: ${d.humidity}% | Wind: ${d.wind.speed} m/s`].join("\n");
}

function fmtForecast(d: ForecastData, cached: boolean): string {
  const tag = cached ? " (cached)" : "";
  const rows = d.entries.map(e =>
    `[${e.datetime}] ${e.temperature.current} | ${e.conditions.description} | Precip: ${Math.round(e.precipitation * 100)}%`);
  return [`Forecast for ${d.city}, ${d.country}${tag}`, ...rows].join("\n");
}
```

### 태스크 2.3: 진입점 생성

`src/index.ts` 생성:

```typescript
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { createWeatherServer } from "./server.js";

const apiKey = process.env.OPENWEATHER_API_KEY;
if (!apiKey) { console.error("OPENWEATHER_API_KEY required."); process.exit(1); }

const server = createWeatherServer({
  apiKey,
  cacheTtlSeconds: parseInt(process.env.CACHE_TTL_SECONDS ?? "300", 10),
  rateLimitPerMinute: parseInt(process.env.RATE_LIMIT_PER_MINUTE ?? "30", 10),
  baseUrl: "https://api.openweathermap.org/data/2.5",
});
server.connect(new StdioServerTransport()).catch(e => { console.error(e); process.exit(1); });
```

---

## 페이즈 3: 캐싱 및 에러 처리 (1.5시간)

### 태스크 3.1: TTL 기반 인메모리 캐시

`src/middleware/cache.ts` 생성:

```typescript
import { CacheEntry } from "../types.js";

export class Cache {
  private store = new Map<string, CacheEntry<unknown>>();
  private ttlMs: number;
  private hits = 0; private misses = 0;

  constructor(ttlSeconds: number) {
    this.ttlMs = ttlSeconds * 1000;
    const t = setInterval(() => this.evictExpired(), 60_000);
    if (t.unref) t.unref();
  }

  get<T>(key: string): T | null {
    const e = this.store.get(key);
    if (!e) { this.misses++; return null; }
    if (Date.now() > e.expiresAt) { this.store.delete(key); this.misses++; return null; }
    this.hits++; return e.data as T;
  }

  set<T>(key: string, data: T): void {
    this.store.set(key, { data, expiresAt: Date.now() + this.ttlMs });
  }

  invalidate(key: string) { return this.store.delete(key); }
  clear() { this.store.clear(); this.hits = 0; this.misses = 0; }

  getStats() {
    const total = this.hits + this.misses;
    return { entries: this.store.size, hitRate: total ? this.hits / total : 0,
             hits: this.hits, misses: this.misses };
  }

  private evictExpired() {
    const now = Date.now();
    for (const [k, e] of this.store) if (now > e.expiresAt) this.store.delete(k);
  }
}
```

### 태스크 3.2: 슬라이딩 윈도우 속도 제한기

`src/middleware/rateLimiter.ts` 생성:

```typescript
export class RateLimiter {
  private timestamps: number[] = [];
  constructor(private max: number, private windowMs = 60_000) {}

  allowRequest(): boolean {
    const now = Date.now();
    this.timestamps = this.timestamps.filter(t => now - t < this.windowMs);
    if (this.timestamps.length >= this.max) return false;
    this.timestamps.push(now); return true;
  }

  remaining(): number {
    this.timestamps = this.timestamps.filter(t => Date.now() - t < this.windowMs);
    return Math.max(0, this.max - this.timestamps.length);
  }

  reset() { this.timestamps = []; }
}
```

### 태스크 3.3: 지수 백오프 재시도

`src/utils/retry.ts` 생성:

```typescript
export async function withRetry<T>(fn: () => Promise<T>, maxAttempts = 3): Promise<T> {
  let lastErr: Error | undefined;
  for (let i = 1; i <= maxAttempts; i++) {
    try { return await fn(); }
    catch (err) {
      lastErr = err instanceof Error ? err : new Error(String(err));
      if (/not found|invalid api key/i.test(lastErr.message)) throw lastErr; // 4xx에서는 재시도 안 함
      if (i < maxAttempts) {
        const delay = Math.min(1000 * 2 ** (i - 1), 10000);
        await new Promise(r => setTimeout(r, delay + delay * 0.25 * (Math.random() * 2 - 1)));
      }
    }
  }
  throw lastErr ?? new Error("Retry failed");
}
```

`npx tsc --noEmit`을 실행하여 모든 것이 컴파일되는지 확인하세요. 일반적인 문제: 상대 import에서 `.js` 확장자 누락.

---

## 페이즈 4: 테스트 (1.5시간)

### 태스크 4.1: Vitest 설정

`globals: true`, `environment: "node"`, `include: ["tests/**/*.test.ts"]`로 `vitest.config.ts`를 생성하세요.

### 태스크 4.2: 캐시 유닛 테스트 (`tests/middleware/cache.test.ts`)

다음을 커버하는 6개 테스트 작성:
- 캐시 미스 시 null 반환
- 값 저장 및 조회
- TTL 이후 항목 만료 (`vi.useFakeTimers()` / `vi.advanceTimersByTime()` 사용)
- 히트/미스 통계 추적 및 히트율 계산
- 모든 항목 클리어 및 통계 리셋
- 다른 항목에 영향 없이 특정 키 무효화

### 태스크 4.3: 속도 제한기 테스트 (`tests/middleware/rateLimiter.test.ts`)

다음을 커버하는 5개 테스트 작성:
- 제한 내 요청 허용
- 제한 초과 요청 거부
- 남은 요청 수 정확히 보고
- 슬라이딩 윈도우 경과 후 요청 허용 (fake timers)
- 추적된 타임스탬프 리셋

### 태스크 4.4: API 클라이언트 테스트 (`tests/tools/getWeather.test.ts`)

`vi.fn()`으로 `global.fetch`를 모킹하고 5개 테스트 작성:

```typescript
const mockFetch = vi.fn();
global.fetch = mockFetch;

// 테스트: 성공 응답을 WeatherData로 파싱
// 테스트: 404에서 "City not found" throw
// 테스트: 401에서 "Invalid API key" throw
// 테스트: 429에서 "Rate limit exceeded" throw
// 테스트: URL에 units 파라미터 올바르게 전달
```

### 태스크 4.5: 통합 테스트 (`tests/integration/server.test.ts`)

MCP SDK의 `InMemoryTransport`와 `Client`를 사용하여 전체 서버 테스트:

```typescript
import { Client } from "@modelcontextprotocol/sdk/client/index.js";
import { InMemoryTransport } from "@modelcontextprotocol/sdk/inMemory.js";
import { createWeatherServer } from "../../src/server.js";

// beforeEach: 서버 생성, 트랜스포트 연결, 클라이언트 연결
const [clientTransport, serverTransport] = InMemoryTransport.createLinkedPair();
const server = createWeatherServer(config);
await server.connect(serverTransport);
const client = new Client({ name: "test", version: "1.0.0" });
await client.connect(clientTransport);

// 테스트: listTools()가 세 개의 도구 이름을 모두 반환
// 테스트: callTool("get_weather")가 포맷된 날씨 텍스트 반환
// 테스트: 반복 호출 시 "(cached)"를 반환하고 fetch는 한 번만 호출
// 테스트: callTool("get_api_status")가 상태 텍스트 반환
```

### 태스크 4.6: 테스트 스위트 실행

```bash
npx vitest run
```

예상: 4개 파일에 걸쳐 20개 통과 테스트. 테스트 실패 시 모킹 설정(`global.fetch`), import 경로(`.js` 확장자), 타이머 정리(`vi.useRealTimers()`)를 확인하세요.

---

## 페이즈 5: 문서화 및 통합 (1시간)

### 태스크 5.1: Claude Code 설정

`~/.claude/settings.json` (전역) 또는 `.claude/settings.json` (프로젝트 수준)에 추가:

```json
{
  "mcpServers": {
    "weather": {
      "command": "node",
      "args": ["/absolute/path/to/weather-mcp-server/dist/index.js"],
      "env": { "OPENWEATHER_API_KEY": "your_key_here" }
    }
  }
}
```

### 태스크 5.2: 통합 확인

Claude Code를 재시작하고 다음 점검을 수행하세요:

1. **도구 검색**: "사용 가능한 MCP 도구가 뭐야?"라고 물어보세요 — 세 개 모두 나타나는지 확인
2. **get_weather**: "뉴욕 날씨가 어때?"라고 물어보세요 — 포맷된 날씨 정보 반환
3. **search_forecast**: "베를린 3일 예보 보여줘"라고 물어보세요 — 다일 데이터 반환
4. **get_api_status**: "날씨 API 상태 확인해줘"라고 물어보세요 — 상태 및 캐시 통계 표시
5. **캐싱**: 같은 질문을 두 번 하세요 — 두 번째 응답에 "(cached)" 표시
6. **에러 처리**: "Xyzzyplugh 날씨 알려줘"라고 물어보세요 — "City not found"를 우아하게 반환

### 태스크 5.3: 문제 해결

| 문제 | 해결 방법 |
|---|---|
| 도구가 목록에 없음 | 절대 경로 확인, `npm run build` 실행, env 블록 확인 |
| "Invalid API key" | 새 키는 활성화까지 최대 2시간 소요 |
| 도구가 에러 반환 | 네트워크, API 상태 페이지, 일일 한도(1,000/일) 확인 |

---

## 산출물

1. 3개 도구를 갖춘 **MCP 서버**: `get_weather`, `search_forecast`, `get_api_status`
2. 인증과 응답 파싱을 갖춘 **API 클라이언트**
3. TTL 만료와 히트율 추적을 갖춘 **캐시 미들웨어**
4. 슬라이딩 윈도우 알고리즘을 사용하는 **속도 제한기**
5. 지수 백오프와 지터를 갖춘 **재시도 로직**
6. 20개 이상의 테스트를 갖춘 **테스트 스위트** (유닛, 통합, 에러 시나리오)
7. 실제 쿼리로 확인된 **Claude Code 통합**

---

## 성공 기준

### 기능성
- [ ] `OPENWEATHER_API_KEY`가 설정되면 서버가 에러 없이 시작됨
- [ ] `get_weather`가 유효한 도시의 현재 날씨를 반환
- [ ] `search_forecast`가 3시간 간격의 다일 예보를 반환
- [ ] `get_api_status`가 상태, 응답 시간, 캐시 통계를 보고
- [ ] 유효하지 않은 도시에 대해 명확한 "City not found" 메시지 반환
- [ ] API 키 누락 시 도움이 되는 시작 에러 생성

### 캐싱 및 성능
- [ ] 반복 요청 시 캐시된 데이터 반환 ("(cached)" 마커 확인)
- [ ] 설정된 TTL 이후 캐시 항목 만료
- [ ] 통계가 히트, 미스, 히트율을 정확히 추적
- [ ] 만료된 항목이 제거됨 (무한 증가 없음)

### 속도 제한 및 복원력
- [ ] 속도 제한기가 분당 임계값 초과 요청을 차단
- [ ] 슬라이딩 윈도우 경과 후 제한기 복구
- [ ] 5xx/네트워크 에러에서 재시도, 4xx 클라이언트 에러에서는 안 함
- [ ] 백오프가 지터와 함께 지수 지연 사용

### 테스트
- [ ] 모든 테스트 통과 (`npx vitest run`)
- [ ] 캐시: 미스, 히트, 만료, 통계, 클리어, 무효화
- [ ] 속도 제한기: 제한 내, 초과, 윈도우 슬라이드, 리셋
- [ ] API 클라이언트: 성공, 404, 401, 429, 파라미터 전달
- [ ] 통합: 도구 등록, 엔드투엔드 호출, 캐싱

### 통합
- [ ] Claude Code 설정에 서버 구성됨
- [ ] Claude Code가 세 개의 날씨 도구를 모두 인식
- [ ] 자연어 쿼리가 올바른 도구를 트리거

---

## 확장 아이디어

기본 프로젝트가 완성되면 다음 확장을 고려하세요:

**MCP 리소스**: 자주 확인하는 도시를 위한 `weather://favorites` 리소스를 노출하여 Claude가 명시적 도구 호출 없이 날씨 데이터를 읽을 수 있게 합니다.

**MCP 프롬프트**: Claude에게 예보를 확인하고 무엇을 챙길지 조언하도록 요청하는 `travel-weather` 프롬프트를 등록합니다.

**영구 캐시**: 인메모리 캐시를 SQLite로 교체하여 서버 재시작 후에도 데이터가 유지되게 합니다.

**다중 제공자 폴백**: OpenWeatherMap이 다운될 때 백업으로 WeatherAPI.com을 추가합니다.

---

**예상 소요 시간**: 6-8시간
**난이도**: 고급
**연습하는 기술**: MCP 서버 개발, TypeScript, API 통합, 캐싱, 속도 제한, 테스트, Claude Code 설정
