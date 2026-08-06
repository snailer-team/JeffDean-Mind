# JeffDean-Mind 🧠

**Jeff Dean의 엔지니어링 사고방식을 Claude에 이식하는 Claude Skill**

Google Senior Fellow Jeff Dean(MapReduce, BigTable, Spanner, TensorFlow, TPU,
Gemini의 설계자)의 개발 방식·사고력·문제 해결 접근법을 Claude Skills 형식으로
체계화한 저장소입니다. 이 스킬이 활성화되면 Claude는 시스템 설계, 성능 최적화,
아키텍처 결정, 코드 리뷰를 Dean의 방법론으로 수행합니다.

## 핵심 철학 (스킬이 재현하는 것)

> "성능을 먼저 정량적으로 추정하고(백오브엔벨로프 계산), 실패를 정상 상태로
> 가정하며, 단순하고 범용적인 추상화로 복잡성을 라이브러리 뒤에 숨긴다."

1. **백오브엔벨로프 우선** — 코드를 쓰기 전에 지연시간 수치("Numbers Everyone
   Should Know")로 설계 성능을 계산한다. TPU는 냅킨 계산 한 장에서 태어났다.
2. **실패는 정상 상태** — "Things will crash. Deal with it!" 자가 치유·부분 기능
   저하·관측 가능성을 기본 내장한다.
3. **꼬리 지연 우선** — 평균이 아니라 p99를 SLO로 삼는다 (The Tail at Scale).
4. **코딩 전 설계 리뷰** — 몇 문단의 설계 메모 → 2~3개 대안 비교 → 인터페이스
   확정 → 그 다음에 코드.
5. **페어 프로그래밍 렌즈** — Dean 렌즈(성능 모델·전체 구조)와 Ghemawat 렌즈
   (6개월 뒤 터질 엣지 케이스)를 모두 적용한다.
6. **성장 설계** — 10~20배 성장에 대비하되 100배에 최적화하지 않는다.
7. **1% 규칙 (AI 시대)** — 모델이 0~1% 성공하는 문제를 골라라. 20% 되는 일은
   프론티어가 곧 삼킨다.

## 저장소 구조

```
JeffDean-Mind/
├── README.md                          ← 이 가이드
├── LICENSE
└── skills/
    └── jeffdean-mind/
        ├── SKILL.md                   ← 스킬 본체 (트리거 + 작업 절차)
        └── references/                ← 필요할 때만 로드되는 상세 지식
            ├── latency-numbers.md     ← Numbers Everyone Should Know + 현대화 보정
            ├── back-of-envelope.md    ← 계산 방법론 + 워크드 예시 (이미지 서버, TPU)
            ├── design-principles.md   ← LADIS/Stanford 강연 설계 원칙 10가지
            ├── distributed-systems.md ← 실패 정상화, The Tail at Scale, 장애 통계
            ├── case-studies.md        ← MapReduce~Gemini 각 시스템의 설계 철학
            ├── problem-solving.md     ← 3단계 문제 해결, 마이크로벤치마크, 인코딩
            ├── collaboration.md       ← 25년 페어 프로그래밍, 2000년 인덱싱 위기
            ├── ai-era.md              ← 1% 규칙, 유기적 모델 비전, 2026 동향
            └── background.md          ← 이력이 사고방식을 형성한 경위, 밈 vs 실제
```

**설계 의도 (Progressive Disclosure):** `SKILL.md`는 가볍게 유지하고(트리거 조건과
작업 절차만), 상세 지식은 `references/`에 분리했습니다. Claude는 스킬이 트리거되면
`SKILL.md`를 읽고, 실제로 필요한 참조 파일만 추가로 읽습니다. 이는 컨텍스트를
아끼는 Claude Skills의 표준 패턴이며 — 공교롭게도 "복잡성을 라이브러리 뒤에
숨긴다"는 Dean의 철학 그대로입니다.

## 트리거 방식

스킬은 `SKILL.md` frontmatter의 `description`을 보고 **Claude가 자동으로**
활성화합니다. 다음 상황에서 트리거됩니다:

| 상황 | 트리거 키워드 예시 |
|---|---|
| 시스템/아키텍처 설계 | "설계해줘", "아키텍처", "design review" |
| 성능 작업 | "성능", "최적화", "병목", "지연시간", "latency", "p99" |
| 확장성 | "확장", "스케일", "capacity planning", "10배 커지면" |
| 분산 시스템 | "분산", "distributed", "내결함성", "SLO" |
| 정량 추정 | "백오브엔벨로프", "back-of-envelope", "얼마나 걸릴까" |
| 명시적 호출 | "제프 딘", "jeff dean", "제프 딘처럼 생각해봐" |
| AI 제품 전략 | 모델 선택, 에이전트 제품 설계, 스타트업 기술 전략 |

명시적으로 강제하려면 프롬프트에 **"제프 딘 마인드로"** 또는 **"jeffdean-mind
스킬을 사용해서"**라고 쓰면 됩니다.

## 설치 방법

### 1. Claude Code (CLI / 데스크톱 앱 / IDE 확장)

**개인 스킬로 설치** (모든 프로젝트에서 사용):

```bash
git clone https://github.com/snailer-team/JeffDean-Mind.git
mkdir -p ~/.claude/skills
cp -r JeffDean-Mind/skills/jeffdean-mind ~/.claude/skills/
```

**프로젝트 스킬로 설치** (해당 저장소에서만 사용, 팀과 공유 가능):

```bash
mkdir -p your-project/.claude/skills
cp -r JeffDean-Mind/skills/jeffdean-mind your-project/.claude/skills/
```

설치 후 새 세션을 시작하면 Claude가 스킬 목록에서 자동 인식합니다. 확인:

```
> 시스템 설계 관련해서 어떤 스킬을 쓸 수 있어?
```

### 2. claude.ai (웹/앱)

Settings → Capabilities → Skills 에서 스킬 업로드를 지원하는 플랜이라면,
`skills/jeffdean-mind` 디렉터리를 zip으로 묶어 업로드합니다:

```bash
cd JeffDean-Mind/skills && zip -r jeffdean-mind.zip jeffdean-mind
```

### 3. Claude API (Agent SDK / Messages API)

Agent SDK를 사용하는 경우 스킬 디렉터리를 에이전트의 작업 디렉터리
`.claude/skills/`에 배치하면 됩니다. 커스텀 하네스라면 `SKILL.md` 본문을 시스템
프롬프트에 주입하고 `references/`를 파일 도구로 읽게 하는 방식도 동작합니다.

## 실제 적용 예시 프롬프트

### 예시 1: 시스템 설계

```
썸네일 이미지 서빙 API를 설계해줘. 하루 100만 요청, 이미지 평균 200KB.
스토리지는 S3, 캐시 계층을 넣을지 고민 중이야.
```

기대 동작: Claude가 (1) 설계 메모를 먼저 쓰고, (2) 캐시 유/무 각 대안의 지연시간을
Numbers 표로 계산하고 (S3 GET ~수십ms vs 메모리 캐시 ~수백µs, 히트율 가정 명시),
(3) p99 관점의 트레이드오프와 10배 성장 시나리오를 제시한 뒤 추천안을 냅니다.

### 예시 2: 성능 병목 분석

```
이 API 엔드포인트가 평균 80ms인데 p99가 2초야. 원인 후보와 접근법을 알려줘.
```

기대 동작: The Tail at Scale 프레임으로 팬아웃 구조 확인 → 편차 원인 후보
(GC, 큐잉, 콜드 캐시, 느린 샤드) → hedged requests/타임아웃 등 원인 무관 완화 기법
→ "왜 느린지 알아낼 수 있는가?"를 묻는 관측 도구 점검 순으로 분석합니다.

### 예시 3: 코드 리뷰

```
이 PR을 제프 딘 마인드로 리뷰해줘. 특히 6개월 뒤에 터질 만한 것 위주로.
```

기대 동작: Dean 렌즈(성능 모델 — 이 루프는 N이 10배면 어떻게 되나, 이 직렬 RPC는
병렬화 가능한가)와 Ghemawat 렌즈(엣지 케이스 — 부분 실패, 동시성, 손상 입력,
업그레이드 경로)를 모두 적용해 리뷰합니다.

### 예시 4: 기술 선택 / 백오브엔벨로프

```
이벤트 로그를 Postgres에 넣을지 Kafka + 오브젝트 스토리지로 갈지 고민이야.
현재 초당 500 이벤트, 이벤트당 1KB. 백오브엔벨로프로 판단해줘.
```

기대 동작: 500 events/s × 1KB = 500KB/s ≈ 43GB/day 계산 → 각 대안의 쓰기 처리량·
저장 비용·조회 패턴을 자릿수로 비교 → 10~20배 성장(초당 1만 이벤트) 시나리오 검증
→ "판단 기준 변경 신호"(어느 조건에서 결론이 뒤집히는지) 명시.

### 예시 5: AI 제품 전략

```
AI 코딩 에이전트 스타트업 아이디어가 있어. 어떤 문제를 골라야 할지 조언해줘.
```

기대 동작: 1% 규칙 적용 — 현재 프론티어 모델의 성공률이 0~1%인 문제인지 검증,
20% 영역이면 6~12개월 내 잠식 위험 경고, "taste"와 스펙 작성 역량의 중요성,
성공률 20% 돌파 시 재검토 트리거 제시.

### 예시 6: 설계 리뷰 프로세스 도입 (팀)

```
우리 팀에 제프 딘식 설계 리뷰 프로세스를 도입하고 싶어. 템플릿을 만들어줘.
```

기대 동작: 코딩 전 몇 문단 설계 메모 → 2~3개 대안 + 백오브엔벨로프 비교 →
인터페이스 먼저 확정·피드백 → 유사 시스템 경험자 리뷰, 순서의 템플릿을 생성합니다.

## 스킬이 강제하는 출력 스타일

- **숫자 없는 설계 제안 금지** — "빠르다"가 아니라 "~0.5ms vs ~50ms".
- **단일안 금지** — 최소 2개 대안 + 정량 비교 + 추천.
- **트레이드오프 명시** — 버린 대안이 유리해지는 조건("판단 기준 변경 신호") 포함.
- **과장 금지** — "아직 완성되지 않은 걸 과장하지 않는다" (Dean). 검증 안 된 것은
  검증 안 됐다고 표기.

## 주의사항 (Caveats)

- **밈 vs 실제**: "Jeff Dean Facts"는 2007년 만우절 유머입니다. 이 스킬은 초인
  신화가 아니라 실제 방법(페어링·설계 리뷰·정량 추정)을 재현합니다.
- **수치의 연대**: 2009년 지연시간 표는 절대값이 아닌 **자릿수 직관**으로
  사용하세요. SSD/NVMe·100GbE로 디스크·네트워크 항목은 크게 개선됐습니다
  (스킬의 `latency-numbers.md`에 현대화 보정 포함).
- **인용 정확성**: 스킬 내 인용문은 출처(논문 원문, 강연, 팟캐스트)와 확실성
  수준을 구분해 표기했습니다. 진행자의 프레이밍과 Dean 본인 발언을 구분합니다.
- **2026년 동향**: Discovery Loop 창업 뉴스는 발표 당일(2026-08-05) 시점 정보이며
  실제 성과는 검증되지 않았습니다.

## 출처

- Jeff Dean, "Designs, Lessons and Advice from Building Large Distributed
  Systems" (LADIS 2009 keynote) — Numbers Everyone Should Know, 설계 원칙
- Dean & Ghemawat, "MapReduce: Simplified Data Processing on Large Clusters"
  (OSDI 2004)
- Dean & Barroso, "The Tail at Scale" (CACM 2013)
- Jouppi et al., "In-Datacenter Performance Analysis of a Tensor Processing
  Unit" (ISCA 2017, arXiv:1704.04760)
- Dean et al., "Large Scale Distributed Deep Networks" (NeurIPS 2012)
- James Somers, "The Friendship That Made Google Huge" (The New Yorker, 2018)
- Dwarkesh Patel 팟캐스트 (2025-02), YC Startup School 강연 (2026)

## License

MIT — see [LICENSE](LICENSE).
