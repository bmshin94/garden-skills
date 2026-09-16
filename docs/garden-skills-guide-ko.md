# Garden Skills 완전 분석 가이드 (한국어)

> 이 문서는 `garden-skills` 저장소를 직접 열어보고 분석한 내용을 한국어로 정리한
> 자료입니다. 처음 접하는 사람도 "이게 뭔지 / 언제 쓰는지 / 어떤 가치가 있는지"를
> 한 번에 파악할 수 있도록 작성했습니다.

---

## 🔗 저장소 주소

| 구분 | 주소 |
| --- | --- |
| **이 저장소 (포크)** | https://github.com/bmshin94/garden-skills |
| **원본 저장소** | https://github.com/ConardLi/garden-skills |
| 원작자 | [ConardLi](https://github.com/ConardLi) — "code秘密花园" |
| 라이선스 | MIT |
| 관련 스펙 | [Agent Skills](https://agentskills.io) · [anthropics/skills](https://github.com/anthropics/skills) |

---

## 목차

1. [한 줄 요약](#1-한-줄-요약)
2. [수록된 스킬 5종](#2-수록된-스킬-5종)
3. [설치 및 사용법](#3-설치-및-사용법)
4. [스킬 vs 플러그인 vs MCP](#4-스킬-vs-플러그인-vs-mcp)
5. [API 토큰이 필요한가](#5-api-토큰이-필요한가)
6. [왜 GitHub에서 주목받았나](#6-왜-github에서-주목받았나)
7. [로컬 에이전트 구축에 주는 도움](#7-로컬-에이전트-구축에-주는-도움)
8. [수익화 전략 7가지](#8-수익화-전략-7가지)
9. [법적 체크리스트](#9-법적-체크리스트)
10. [90일 실행 로드맵](#10-90일-실행-로드맵)
11. [React / PHP 프로젝트와의 궁합](#11-react--php-프로젝트와의-궁합)
12. [저장소 실측 데이터](#12-저장소-실측-데이터)

---

## 1. 한 줄 요약

> **AI 에이전트(Claude Code, Cursor, Codex 등)에게 설치해주는 "전문가 매뉴얼" 5종 모음집.**

AI는 기본적으로 범용이라 "영상 만들어줘", "예쁜 페이지 만들어줘" 같은 요청의
결과 품질이 매번 달라집니다. Garden Skills는 그 노하우를 `SKILL.md`라는
마크다운 문서에 박제해두고, AI가 관련 요청을 받으면 **자동으로 그 매뉴얼을
읽고 전문가 모드로 작업**하게 만듭니다.

**비유**: AI를 요리사라고 하면, 이 저장소는 주방에 꽂아두는 **전문 레시피북 묶음**입니다.

---

## 2. 수록된 스킬 5종

| 스킬 | 버전 | 하는 일 | 규모 |
| --- | --- | --- | --- |
| 🎬 `web-video-presentation` | 1.2.2 | 대본/글 → **"영상처럼 보이는" 16:9 웹 프레젠테이션**. Vite + React + TS 스캐폴드, 테마 23종, TTS 음성합성 포함. 화면 녹화하면 그대로 유튜브 영상 | 95개 파일 / 712KB |
| 🎨 `web-design-engineer` | 1.3.0 | 페이지·대시보드·프로토타입·슬라이드를 **"작동하는" 수준이 아니라 "숨막히게 예쁜"** 수준으로 제작. 브라우저 QA 검수 하네스 포함 | 39개 파일 / 308KB |
| 📄 `beautiful-article` | 0.1.0 | URL / PDF / DOCX / 스크린샷 → **오프라인에서도 열리는 단일 HTML 아티클**. 기본값은 정보 100% 보존 장문 | 58개 파일 / 404KB |
| 🖼️ `gpt-image-2` | 1.0.4 | GPT Image 2 이미지 생성·편집. **18개 카테고리 / 80+ 구조화 프롬프트 템플릿** (포스터·UI·인포그래픽·웹툰·스토리보드 등) | 103개 파일 / 1.1MB |
| 🔎 `kb-retriever` | 1.0.1 | 로컬 폴더를 **지식베이스처럼 점진 검색**. PDF/Excel은 통째로 읽지 않고 구조화 처리 후 검색 | 8개 파일 / 72KB |

### 각 스킬의 폴더 구조 (공통 패턴)

```
skills/<이름>/
├── SKILL.md          ← 진입점 (얇게 유지!)
├── manifest.json     ← 버전 / 호환 에이전트 목록
├── README.md         ← 사람이 읽는 설명
├── references/       ← 상세 문서 (필요할 때만 로드)
├── templates/        ← 코드 스캐폴드
├── themes/           ← 디자인 토큰 프리셋
└── scripts/          ← 실제 실행 코드 (Node/Bash)
```

> 💡 **핵심 패턴**: `SKILL.md`는 얇게, 디테일은 `references/`로 분리.
> 이것이 컨텍스트(토큰) 절약의 핵심입니다.

---

## 3. 설치 및 사용법

### 설치 — 5가지 경로

| 방법 | 명령어 | 적합한 상황 |
| --- | --- | --- |
| **A. `skills` CLI** ⭐ | `npx skills add bmshin94/garden-skills -s web-design-engineer` | 가장 쉬움. Cursor·Codex·Claude 자동 인식 |
| **B. 플러그인 마켓** | `/plugin marketplace add bmshin94/garden-skills`<br>`/plugin install web-design-skills@garden-skills` | Claude Code 사용자 |
| **C. Release `.zip`** | GitHub Releases에서 다운로드 (SHA-256 체크섬 제공) | CI / 폐쇄망 / 재현 가능한 설치 |
| **D. 수동 복사** | `git clone` 후 `.claude/skills/`에 폴더 복사 | 스킬 자체를 수정할 때 |
| **E. Git 서브모듈** | `git submodule add ...` | 큰 프로젝트에 벤더링 |

**유용한 옵션**
```bash
npx skills add bmshin94/garden-skills              # 5개 전부 설치
npx skills add ... --global                        # ~/.skills 전역 설치
npx skills add ... -a claude-code                  # 특정 에이전트 지정
npx skills list                                    # 설치 목록 확인
npx skills update                                  # 전체 업데이트
```

### 사용법 — **별도 명령어가 필요 없습니다**

```
사용자: "이 PDF를 예쁜 웹문서로 만들어줘"
   ↓
AI가 각 스킬의 description을 스캔 → "beautiful-article이 해당됨"
   ↓
해당 SKILL.md를 로드하고 그 워크플로우대로 진행
```

이를 **자동 발동(progressive disclosure)** 이라고 합니다. 평소에는 스킬 이름과
설명 한 줄만 메모리에 두고, 필요할 때만 본문을 로드해 토큰을 절약합니다.

### 실제 작업 흐름 예시 (`web-video-presentation`)

```
Phase 1  대본(script.md) + 개발계획(outline.md) 동시 생성
   ⏸️  [Checkpoint Plan] 필수 정지 — 5가지 항목 한 번에 확인
        (대본 / outline / 테마 / 소재 / 개발모드)
Phase 2  Vite + React 스캐폴드 → 1챕터 개발
   ⏸️  [하드 노드] 1챕터 사용자 검수 — 건너뛸 수 없음
Phase 3  나머지 챕터 개발 (순차 / 병렬 선택)
Phase 4  음성 합성 (선택)
```

> 중간에 **강제 체크포인트**가 있어 AI가 혼자 폭주하지 않습니다.

---

## 4. 스킬 vs 플러그인 vs MCP

가장 혼동되는 부분입니다. 셋은 완전히 다른 개념입니다.

| | **Skill** | **Plugin** | **MCP** |
| --- | --- | --- | --- |
| 정체 | **매뉴얼 / 레시피** 📖 | **포장 상자** 📦 | **전화선** 🔌 |
| 실체 | `SKILL.md` (마크다운) | `marketplace.json` | 별도 실행되는 서버 프로세스 |
| 역할 | AI에게 **"어떻게"** 할지 알려줌 | 스킬·명령어·훅을 **묶어 배포** | AI를 **외부 시스템에 연결** |
| 비유 | 요리 레시피 | 레시피 세트 박스 | 슈퍼마켓 배달 전화 |

### 이 저장소의 정답

> **본체는 "스킬(Skill)". 플러그인은 배포용 포장지. MCP는 전혀 아님.**

`.claude-plugin/marketplace.json` 실제 내용:

```json
{
  "name": "web-design-skills",                  // 플러그인 이름 (포장 상자)
  "source": "./skills/web-design-engineer",     // 내용물은 결국 스킬
  "skills": ["./"]
}
```

### 플러그인 팩 구성

| 플러그인 팩 | 포함 스킬 |
| --- | --- |
| `presentation-skills` | `web-video-presentation` |
| `web-design-skills` | `web-design-engineer` |
| `knowledge-base-skills` | `kb-retriever` |
| `image-generation-skills` | `gpt-image-2` |
| `beautiful-article-skills` | `beautiful-article` |

### MCP와의 결정적 차이

```
Skill = AI의 "지식 / 방법론" 확장  → 실행하지 않음, 읽는 문서
MCP   = AI의 "손과 발" 확장        → 서버를 띄우고 실제 API 호출
```

> ⚠️ 단, `gpt-image-2`는 `scripts/generate.js` 같은 **실행 스크립트를 품고 있어**
> 실제 API를 호출합니다. 스킬이 MCP 영역을 일부 침범하는 최신 트렌드 사례입니다.

---

## 5. API 토큰이 필요한가

| 스킬 | 토큰 | 비고 |
| --- | --- | --- |
| 🎨 `web-design-engineer` | ❌ **불필요** | 순수 문서. AI 자체 능력만 사용 |
| 📄 `beautiful-article` | ❌ **불필요** | 동일 |
| 🔎 `kb-retriever` | ❌ **불필요** | 로컬 grep / Read만 사용 |
| 🖼️ `gpt-image-2` | ⚠️ **모드별** | 아래 참고 |
| 🎬 `web-video-presentation` | ⚠️ **음성 합성만** | 아래 참고 |

### `gpt-image-2` — 3가지 실행 모드

`scripts/check-mode.js`가 환경을 자동 탐지해 모드를 결정합니다.

```javascript
if (ENABLE_GARDEN_IMAGEGEN && OPENAI_API_KEY) → MODE A  // 직접 생성 (유료)
else if (호스트 에이전트에 이미지 툴 있음)      → MODE B  // 호스트 툴에 위임 (무료)
else                                           → MODE C  // 프롬프트만 제공 (무료)
```

**MODE A 설정**
```bash
export ENABLE_GARDEN_IMAGEGEN=1
export OPENAI_API_KEY=sk-...
export OPENAI_BASE_URL=https://api.openai.com/v1   # 선택 (프록시 / Azure)
export OPENAI_IMAGE_MODEL=...                      # 선택
```

- 프롬프트 저장 위치: `garden-gpt-image-2/prompt/`
- 이미지 저장 위치: `garden-gpt-image-2/image/`

> **MODE B/C는 비용이 0원입니다.** 80개 프롬프트 템플릿은 문서일 뿐이라
> ChatGPT나 다른 이미지 툴에 복사해서 써도 그대로 가치가 있습니다.

### `web-video-presentation` — 음성 합성만 유료

웹 페이지 제작 자체는 무료입니다. 내레이션을 얹을 때만 TTS가 필요합니다.

| Provider | 내장 | 필요 항목 |
| --- | --- | --- |
| `minimax` | ✓ | `mmx-cli` + MiniMax API key (중국어 최적) |
| `openai` | ✓ | `OPENAI_API_KEY`, 모델 `tts-1` / `tts-1-hd` |
| ElevenLabs | 스니펫 제공 | `ELEVENLABS_API_KEY` |
| edge-tts / macOS `say` | 스니펫 제공 | **무료** ✅ |
| Azure / Google | 스니펫 제공 | 각 서비스 키 |

```bash
PRESENTATION_TTS=openai npm run synthesize-audio
OPENAI_TTS_MODEL=tts-1-hd PRESENTATION_TTS=openai npm run synthesize-audio
```

> 빈 내레이션은 자동으로 건너뛰어 TTS 토큰을 낭비하지 않습니다.

**결론: 5개 중 3개는 완전 무료. 나머지 2개도 무료 우회 경로가 존재합니다.**

---

## 6. 왜 GitHub에서 주목받았나

> ⚠️ 실시간 스타 수는 이 문서 작성 시점에 조회하지 않았습니다.
> 아래는 저장소 내용에 근거한 **구조적 인기 요인 분석**입니다.

### ① 원작자가 이미 영향력을 보유

`ConardLi` = 중국 개발자 인플루언서 **"code秘密花园"**.
README 하단 Connect 섹션에 X/Twitter · B站 · 抖音 · YouTube · 샤오홍슈 ·
위챗 공중계정 · 개인 사이트가 모두 연결되어 있고, 대표작 `easy-dataset`
(LLM 파인튜닝 데이터셋 도구)도 널리 알려져 있습니다.
→ **팬덤이 있는 상태에서 출시**.

### ② 타이밍

Anthropic이 Agent Skills 스펙을 공개한 직후, "그래서 스킬을 어떻게 잘 쓰는가"라는
수요가 폭발한 시점에 **완성도 높은 실물 5개**를 제시했습니다.

### ③ 레퍼런스 구현으로서의 가치

README의 Acknowledgments에 결정적 단서가 있습니다.

> **Claude Design** — `web-design-engineer`의 영감이 된 시스템 프롬프트.
> 원본이 `dist/prompts/claude-design-system-prompt.md`에 보존되어 있음.

즉 **Anthropic의 Claude Design 시스템 프롬프트를 확보해 스킬로 재가공**한 것입니다.

### ④ 압도적인 볼륨

말로만 스킬이 아니라 레퍼런스 문서 · 테마 · 스캐폴드 · 실행 스크립트까지 완비.
마크다운만 **34,372줄**입니다.

### ⑤ 프로급 운영

- README **3개국어** (영 / 중 / 일)
- `.github/workflows/` 에 자동 검증(`validate-skills.yml`) + 릴리즈(`release-skill.yml`) CI
- `manifest.json` 기반 **스킬별 개별 버전 관리**
- **5가지 설치 경로** 지원 (진입장벽 제거)
- `demo/` 에 **스킬 적용 전후 비교 HTML** — 눈에 보이는 증거

### ⑥ 시각적 마케팅

README 최상단이 이미지 갤러리 테이블로 시작해, 스크롤 중 시선을 잡습니다.

---

## 7. 로컬 에이전트 구축에 주는 도움

### ✅ 배울 수 있는 것

**1) 스킬 설계 패턴**
`SKILL.md`는 얇게, 디테일은 `references/`로. 컨텍스트 절약의 정석.

**2) 워크플로우 엔지니어링**
`beautiful-article`의 Phase 구조:

```
Phase 0 Intake → 1 Source → 2 Planning → ★Checkpoint 1 (필수 정지)
→ 4 First Spread → SubAgent 리뷰 → ★Checkpoint 2 (필수 정지)
→ 생성 → 최종 심사 → 수리
```

- **강제 체크포인트** = 폭주 방지
- **SubAgent 리뷰** = 자기 검증 루프
- **단계별 산출물** (`source.md`, `plan.md`, `review/*.md`) = 추적 가능성

**3) 환경 적응 (Graceful Degradation)**
`check-mode.js`처럼 **환경을 먼저 탐지하고 모드를 결정**하는 패턴.
(GPU 있으면 로컬 모델, 없으면 API 같은 분기에 그대로 적용 가능)

**4) 배포 파이프라인**
`scripts/release/` + `.github/workflows/` 가 완성된 참고 구현.
`npm run validate` 한 번으로 전체 검증.

### ⚠️ 한계

- 이것은 **Claude / Cursor 위에 올라타는** 방식이며,
  LangChain·AutoGen 같은 **밑바닥 에이전트 프레임워크가 아닙니다**.
- "Python으로 자체 에이전트 루프"를 만든다면 코드 직접 재사용은 어렵습니다.
- 다만 **프롬프트 설계 · 워크플로우 구조 · 컨텍스트 관리 철학**은 100% 이식 가능합니다.

---

## 8. 수익화 전략 7가지

> ⚠️ 아래 금액은 시장 통념에 기반한 **추정치**이며, 실시간 시세 조사 결과가
> 아닙니다. 실제 단가는 포지셔닝과 포트폴리오에 따라 크게 달라집니다.

### 🔑 출발점이 되는 핵심 발견

실측 결과: **마크다운 196개 파일 중 195개(99.5%)가 중국어를 포함**합니다.

```
영어 README로 포장되어 있지만, 실제 알맹이는 대부분 중국어.
→ 서구권 / 한국 개발자는 설치는 해도 깊게 커스터마이징하기 어렵다.
→ 시장이 비어 있다.
```

---

### 🥇 전략 1. 한국어 스킬팩 — "Garden Skills KR"

**왜 1번인가**: MIT 라이선스로 번역·수정·재배포·판매가 모두 합법이고,
195개 파일의 언어 장벽 자체가 해자(moat)가 됩니다.

**실행 단계**
```
1주차  수요 높은 2개만 번역 (web-design-engineer + beautiful-article)
       → AI 초벌 번역 후 기술 용어 감수
2주차  한국 사례로 예시 교체 ⭐ 핵심
       - 중국식 레이아웃 → 한국 웹 감성 (토스 / 당근 / 배민)
       - 폰트: Pretendard, SUIT, 나눔스퀘어 네오
       - 한글 조판 규칙 (자간 / 행간 / 줄바꿈)
3주차  GitHub 공개 + 커뮤니티 배포
       (긱뉴스, 커리어리, OKKY, 개발자 커뮤니티)
4주차  유입 데이터 기반으로 유료화 지점 결정
```

**⭐ 차별화 포인트 — 단순 번역이 아니라 현지화**

```
❌ 단순 번역: "Use elegant typography" → "우아한 타이포그래피를 사용하라"
✅ 현지화:    "한글은 영문 대비 행간 1.7~1.8 권장, 자간 -0.02em,
              장평 조정 금지, 어절 단위 줄바꿈(word-break: keep-all) 필수"
```

→ 중국 원본에도, 영어권에도 없는 진짜 부가가치.

**난이도** 🟢 하 | **초기비용** ≈ 0 | **리스크** 낮음

---

### 🥈 전략 2. 영상 / 콘텐츠 제작 대행 — 현금 회수가 가장 빠름

```
기존 모션그래픽 영상 제작:  2~4주,  추정 200~500만원
이 스킬 활용:              2~4일,  동일 단가 청구 가능
                           ↑ 여기서 마진 발생
```

**타겟 고객 (우선순위순)**
1. 스타트업 IR / 제품소개 — 예산 있고 급함 ⭐⭐⭐
2. SaaS 온보딩 영상 — 반복 수요 = 재계약
3. 교육기관 강의자료 — 볼륨 크고 단가 낮음
4. 컨퍼런스 발표자 — 단가 낮지만 포트폴리오 / 바이럴에 최적

**실행 순서**
```
① 포트폴리오 3편 먼저 제작 (필수)
   → "AI 스킬 소개 영상"을 그 스킬로 제작 = 메타 마케팅
② 유튜브 / 링크드인 / X 공개
③ 크몽·숨고·위시켓 등록 (초반 저단가로 후기 확보)
④ 후기 10개 이상이면 단가 2~3배 인상
```

**💡 패키지 전략**: `beautiful-article` + `gpt-image-2`를 묶어
"제품소개 **영상** + 웹 **백서** + 소셜 **이미지**" 세트로 판매 →
단가 상승 + 경쟁자 모방 난이도 상승.

**난이도** 🟢 하 | **회수 속도** ⚡ 최상 | **한계** 내 시간 = 내 수익

---

### 🥉 전략 3. 버티컬(도메인 특화) 스킬 판매

```
현재 5개 = 전부 범용   → 경쟁 치열, 무료 대체재 많음
돈이 되는 곳 = 버티컬  → 좁지만 대체 불가, 가격 저항 낮음
```

**한국 시장 유망 버티컬**

| 버티컬 | 내용 | 추천도 |
| --- | --- | --- |
| **이커머스 상세페이지** | 스마트스토어 / 쿠팡 / 카페24 규격, 필수 고지, 과장광고 금지 표현 체크 | ⭐⭐⭐ |
| **공공 제안서 / 입찰** | 나라장터 양식, 평가 배점표 기반 구성, 심사 순서 설계 | ⭐⭐⭐ |
| **스타트업 IR 덱** | 팀 / 시장 / 트랙션 / 재무 표준 구조, VC 관점 지표 | ⭐⭐ |
| **개발팀 컨벤션** | 사내 React / Spring 규칙 자동 적용, 온보딩 단축 | ⭐⭐ |

**판매 채널**

| 채널 | 수수료 | 특징 |
| --- | --- | --- |
| Gumroad / Lemon Squeezy | ~10% | 해외 결제 편리 |
| 자체 사이트 + 토스페이먼츠 | ~3% | 마진 최고, 셋업 필요 |
| 크몽 (전자책 / 템플릿) | ~20% | 기존 트래픽 활용 |

**추정 단가**: 개당 3~15만원 / 번들 30~50만원

**난이도** 🟡 중 | **확장성** ⭐⭐⭐

---

### 💎 전략 4. 기업 사내 스킬 구축 컨설팅 — 마진 최고

**왜 지금인가**
```
2024~25:  기업들 "AI 도입해야 한다" → 계정만 구매
2026 현재: "도입은 했는데 왜 생산성이 안 오르지?"
           → 원인: 회사 맥락이 AI에게 없음
           → 해결: 사내 전용 스킬팩 (= 이 저장소가 보여주는 것)
```

**3단 패키지**

| 단계 | 기간 | 내용 | 추정 단가 |
| --- | --- | --- | --- |
| **진단** | 1~2주 | 워크플로우 인터뷰, 반복 업무 식별, 기존 문서 수집 | 300~800만원 |
| **구축** | 4~8주 | 전용 스킬 3~5개 제작, `kb-retriever` 사내 위키 연결, 브랜드 가이드 반영 | 2,000~5,000만원 |
| **운영** 🔁 | 월 단위 | 스킬 업데이트 / 신규 제작 / 사내 교육 | 월 200~500만원 |

> 운영 단계가 핵심입니다. **구독형 = 예측 가능한 현금흐름**.

**진입 방법**
```
1. 지인 회사 1곳을 무료 / 저가로 진행 → 케이스 스터디 확보 (필수)
2. 정량 성과 측정 ("제안서 작성 5일 → 1일")
3. 그 숫자를 들고 세일즈
```

**난이도** 🔴 상 | **수익** 💰💰💰 최고 | **필수 조건** 레퍼런스 1건

---

### 📹 전략 5. 콘텐츠 + 교육 (자산형 수익)

**역할**: 위 모든 전략의 유입 깔때기.

```
유튜브 / 블로그 (무료 콘텐츠)
    ↓ 신뢰 형성
전자책 / 강의 (소액 결제)
    ↓ 검증된 리드
컨설팅 / 대행 (고액 계약)  ← 실제 수익
```

**콘텐츠 아이디어**
- "AI에게 우리 회사 규칙 가르치기" 시리즈
- "스킬 만드는 법 7일 챌린지"
- "Before/After: AI 디자인 품질 비교" — `demo/` 폴더의 비교 HTML을 그대로 소재로 활용

**상품화**

| 상품 | 추정 가격 | 비고 |
| --- | --- | --- |
| 전자책 (PDF) | 2~3만원 | 제작 용이 |
| 온라인 강의 (인프런 등) | 5~15만원 | 플랫폼 수수료 |
| 라이브 워크숍 | 20~50만원/인 | 기업 단체 수강 |
| 기업 출강 | 100~300만원/일 | 💎 단가 최고 |

**🔥 메타 마케팅**: 이 스킬로 만든 영상으로 이 스킬을 소개.
"이 영상 자체가 AI로 제작되었습니다"가 그대로 증거이자 훅이 됩니다.

**난이도** 🟡 중 | **누적 효과** ⭐⭐⭐

---

### 🏗️ 전략 6. SaaS화 (장기전)

**아이디어 A — 영상 생성 SaaS**
```
사용자: 대본 텍스트 업로드
  ↓
서버: web-video-presentation 워크플로우 자동 실행
  ↓
결과: 브라우저 재생 + MP4 내보내기
```
추정 가격: 월 3~10만원 구독

**아이디어 B — 팀 스킬 관리 플랫폼**
```
"팀 스킬을 만들고, 버전 관리하고, 배포하는 도구"
→ 이 저장소의 scripts/release/ + .github/workflows/ 가 이미 프로토타입
```
추정 가격: 시트당 월 1~3만원

**⚠️ 현실 체크**: 개발 6개월+, 인프라 비용, LLM API 원가 관리 필요.
다른 전략으로 현금을 확보한 뒤 착수하는 것이 안전합니다.

**난이도** 🔴🔴 최상 | **회수** 느림 | **잠재력** 최대

---

### 🎨 전략 7. 에셋 판매 — 가장 저평가된 기회

`themes/` 폴더의 테마는 결국 **CSS 토큰 파일**(`tokens.css` + `theme.json`)입니다.
즉 **테마를 추가 제작해 파는 것은 순수 창작물 판매**이며, 원본 코드가 필요 없습니다.

| 에셋 | 추정 가격 |
| --- | --- |
| 프리미엄 테마 팩 (10종) | 5~10만원 |
| 한국 브랜드 감성 테마 | 3~8만원 |
| 산업별 프롬프트 템플릿 100선 | 3~5만원 |
| 업종별 IR / 제안서 아웃라인 모음 | 5~15만원 |

**장점**: 제작 시간 짧음(테마 1개 ≈ 반나절) · 재고 없음 · 무한 복제 ·
디자인 감각만으로 가능 · 기존 사용자 애드온 판매 용이.

**난이도** 🟢 하 | **확장성** ⭐⭐⭐

---

## 9. 법적 체크리스트

### ✅ MIT 라이선스로 허용되는 것

```
✅ 상업적 이용      ✅ 수정
✅ 재배포           ✅ 서브라이선스
✅ 유료 판매        ✅ 브랜드 변경
```

### ⚠️ 반드시 지켜야 할 것

1. **저작권 고지 + MIT 라이선스 전문 포함** (`LICENSE` 파일 유지)
2. **출처 명시** — "ConardLi/garden-skills 기반" (법적 의무이자 예의)
3. **면책 조항 유지** — 원저자는 보증하지 않음

### 🚨 특별 주의 항목

| 항목 | 주의 사항 |
| --- | --- |
| `dist/prompts/claude-design-system-prompt.md` | Anthropic의 Claude Design 시스템 프롬프트 원본. **MIT 범위 밖일 수 있으므로 상품에서 제외 권장** |
| README의 데모 이미지 | jsdelivr CDN 외부 링크. 재배포 시 직접 제작 권장 |
| `gpt-image-2`로 생성한 이미지 | OpenAI 이용약관의 상업 이용 조건 확인 필요 |

> 💡 **안전한 접근**: "원본 fork + 내 창작물 추가" 형태로 가고,
> 판매 대상은 **내가 추가한 부분(번역 · 테마 · 버티컬 스킬)** 으로 포지셔닝.

---

## 10. 90일 실행 로드맵

```
┌─ 1개월차: 자산 만들기 (수익 0원) ────────────────────────┐
│  Week 1-2  web-design-engineer + beautiful-article 한국어화
│            + 한글 타이포그래피 규칙 추가 ⭐
│  Week 3    포트폴리오 3개 제작
│            (소개 영상 1 / 웹 아티클 1 / 랜딩 1)
│  Week 4    GitHub 공개 + 커뮤니티 배포 + 유튜브 1편
└──────────────────────────────────────────────────────┘
                    ↓
┌─ 2개월차: 첫 현금 ──────────────────────────────────────┐
│  Week 5-6  크몽 / 숨고 등록, 저단가로 후기 3개 확보
│  Week 7-8  버티컬 스킬 1개 제작 (이커머스 상세페이지 추천)
│            + 테마팩 5종 제작 → Gumroad 등록
└──────────────────────────────────────────────────────┘
                    ↓
┌─ 3개월차: 스케일 ───────────────────────────────────────┐
│  Week 9-10  단가 인상, 지인 회사 1곳 무료 컨설팅 → 케이스 확보
│  Week 11-12 케이스 스터디 공개 → 기업 인바운드 유도
│             전자책 또는 워크숍 오픈
└──────────────────────────────────────────────────────┘
```

### 우선순위 TOP 3

| 순위 | 전략 | 선정 이유 |
| --- | --- | --- |
| 🥇 | **한국어 스킬팩 + 한글 타이포그래피** | 유일무이한 해자, 진입비용 0, 모든 전략의 기반 |
| 🥈 | **제작 대행** | 현금 회수 최속, 포트폴리오가 곧 마케팅 |
| 🥉 | **기업 컨설팅** | 앞선 두 단계의 신뢰를 수익으로 전환하는 최종 단계 |

### 실행 원칙 4가지

1. **"저장소를 파는 것"이 아니라 "저장소로 문제를 푸는 것"을 판다**
   - ❌ "Garden Skills 한국어판 판매" → 잘 안 팔림
   - ✅ "제안서 작성 5일 → 1일로 단축" → 팔림
2. **1개월은 무조건 무료 배포** — 신뢰가 없으면 아무도 사지 않음
3. **모방 불가능한 요소에 집중** — 한글 조판 + 한국 플랫폼 규격 + 국내 브랜드 감성
4. **작게 시작** — 7개 전부 시도하면 아무것도 완성되지 않음. 번역 2개부터 2주 안에

---

## 11. React / PHP 프로젝트와의 궁합

### 해석 A: "스킬을 React나 PHP로 만들 수 있나?"

**질문 자체가 성립하지 않습니다. 스킬은 프로그래밍 언어가 아닙니다.**

```markdown
---
name: my-skill
description: 언제 이 스킬을 사용할지에 대한 설명
---

# 여기부터는 그냥 마크다운 문서
1. 이렇게 하고
2. 저렇게 한다
```

이것이 전부입니다. `SKILL.md`는 **텍스트 파일**이라 React도 PHP도 필요 없습니다.

```bash
mkdir -p .claude/skills/my-skill
# SKILL.md 하나만 작성하면 끝
```

**단, `scripts/`는 언어 선택이 자유롭습니다.**
- 이 저장소는 **Node.js**(`generate.js`)와 **Bash**(`scaffold.sh`) 사용
- Python도 흔함
- PHP도 가능하지만, 에이전트 실행 환경에 PHP가 있어야 하고
  생태계가 Node / Python 중심이라 **권장하지 않음**

### 해석 B: "React / PHP 프로젝트에 이걸 쓸 수 있나?"

| 스킬 | React 프로젝트 | PHP 프로젝트 |
| --- | --- | --- |
| `web-design-engineer` | ✅✅ 최적 (React 직접 지원) | ✅ HTML/CSS 결과물 활용 가능 |
| `web-video-presentation` | ✅✅ Vite + React + TS 스캐폴드 내장 | ❌ 산출물이 React 프로젝트 |
| `beautiful-article` | ✅ 단일 HTML 산출 | ✅ 단일 HTML이라 어디든 삽입 가능 |
| `gpt-image-2` | ✅ Node 스크립트 | ⚠️ Node 병행 설치 필요 |
| `kb-retriever` | ✅ 언어 무관 | ✅ 언어 무관 |

- **React 사용자**: 현재 최상의 궁합. `web-video-presentation`은
  `vite.config.ts` · `src/` · `index.html`이 포함된 프로젝트를 통째로 생성합니다.
- **PHP(라라벨 등) 사용자**: 백엔드는 PHP, **프론트 산출물만 이 스킬로 생성**해
  `public/`에 얹는 하이브리드가 정석. `beautiful-article`의 단일 HTML은
  PHP 뷰에 그대로 include 가능합니다.

### 💡 가장 실용적인 활용

자기 팀 전용 스킬을 직접 만드는 것입니다.

```markdown
---
name: our-laravel-convention
description: 우리 팀 라라벨 프로젝트에서 컨트롤러/서비스/리포지토리를 작성할 때 사용
---
# 우리 팀 규칙
- 컨트롤러에 비즈니스 로직 금지, 서비스로 위임
- Repository 패턴 필수
- ...
```

→ AI가 우리 팀 스타일대로 코드를 작성하게 됩니다. 이것이 스킬의 진짜 가치입니다.

---

## 12. 저장소 실측 데이터

분석 시점에 저장소를 직접 스캔해 얻은 수치입니다.

| 항목 | 수치 |
| --- | --- |
| 마크다운 총 라인 수 | **34,372줄** |
| 마크다운 파일 수 | 196개 |
| **중국어 포함 파일** | **195개 (99.5%)** |
| 스킬 개수 | 5개 |
| 플러그인 팩 개수 | 5개 |
| `gpt-image-2` 템플릿 문서 | 94개 |
| `web-video-presentation` 테마 | 23종 |
| 마켓플레이스 버전 | 0.4.0 |
| 라이선스 | MIT |

### 테마 전체 목록 (23종)

```
bauhaus-bold      blueprint          bold-signal       chalk-garden
creative-voltage  dark-botanical     dune              electric-studio
forest-ink        indigo-porcelain   kraft-paper       midnight-press
monochrome-print  neon-cyber         newsroom          paper-press
pastel-dream      split-canvas       sunset-zine       swiss-ikb
terminal-green    vintage-editorial  warm-keynote
```

### 저장소 루트 구조

```
garden-skills/
├── .claude-plugin/
│   └── marketplace.json        ← 플러그인 팩 정의
├── .github/workflows/
│   ├── release-skill.yml       ← 릴리즈 자동화
│   └── validate-skills.yml     ← 스킬 검증 CI
├── skills/                     ← 🎯 본체
│   ├── beautiful-article/
│   ├── gpt-image-2/
│   ├── kb-retriever/
│   ├── web-design-engineer/
│   └── web-video-presentation/
├── demo/web-design-demo/       ← 스킬 적용 전후 비교 HTML
├── dist/prompts/               ← Claude Design 시스템 프롬프트 원본
├── scripts/release/            ← 릴리즈 스크립트 (cut-release, pack-skill 등)
├── website/                    ← 스킬 소개 웹사이트 2종
├── README.md / .zh-CN.md / .ja-JP.md
├── CONTRIBUTING.md / .zh-CN.md / .ja-JP.md
└── package.json                ← 메인테이너 스크립트 전용
```

### 메인테이너 명령어

```bash
npm run list          # 스킬 목록
npm run pack          # 단일 스킬 패키징
npm run pack:all      # 전체 패키징
npm run readme:sync   # README 다운로드 링크 동기화
npm run readme:check  # README 검증
npm run validate      # list + pack:all + readme:check 전체 검증
npm run release       # 릴리즈 컷
npm run release:dry   # 릴리즈 드라이런
```

---

## 📌 최종 요약

| 질문 | 답 |
| --- | --- |
| **이게 뭔가?** | AI 에이전트용 전문가 매뉴얼(Skill) 5종 모음 |
| **정체는?** | 스킬(본체) + 플러그인(포장지). **MCP 아님** |
| **설치는?** | `npx skills add` 한 줄. 이후 **자동 발동**, 별도 명령 불필요 |
| **비용은?** | 5개 중 3개 완전 무료. 나머지도 무료 우회 가능 |
| **왜 유명?** | 유명 원작자 + 타이밍 + 압도적 볼륨 + 프로급 운영 |
| **에이전트 구축?** | 설계 패턴 교재로 최상급 (단, 프레임워크는 아님) |
| **수익화?** | 한국어 스킬팩 → 제작 대행 → 기업 컨설팅 순 |
| **React/PHP?** | 스킬은 마크다운. React는 최적, PHP는 하이브리드 |

---

*이 문서는 저장소를 직접 분석해 작성되었습니다.
금액 관련 수치는 시장 통념 기반 추정치이며 실제와 다를 수 있습니다.*
