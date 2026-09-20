# Changelog

## 1.2.0 (2026-09-20)

**수집 프로토콜 도입** — 리서치 "유형"과 직교하는 두 번째 축을 추가했다.
유형(1~8)이 *무엇을 답할지*를 정한다면, 프로토콜은 *근거를 어디서 어떻게 긁어올지*를 정한다.

- 신규 스킬 `research-sources` — API 기반 1차 자료 수집 플레이북 라우터
  - `domains/kr-academic.md` — 국내 논문·학위논문·국가R&D보고서. 예산 배분, 수집 순서, 출처 등급 A~D
  - `domains/epc.md` — 건설·계약·발주 동향·표준. 예산 배분, 수집 순서, 출처 등급 T1~T5
  - `sources/*.md` — OpenAlex / Crossref / Semantic Scholar / KCI / ScienceON / CODIL /
    공공데이터포털의 호출법과 함정. 두 도메인이 공유하는 소스는 한 번만 존재한다
- **`cell-researcher`에 `Bash` 도구 추가** — 기존에는 `curl` 실행 권한이 없어 어떤 API
  수집 경로도 실행되지 않았다. 프로토콜 도입의 전제 조건
- `:design` 1.5단계 신설 — 유형 판별 직후 프로토콜을 부착하고, 그 예산·순서·등급표를
  브리프의 "출처 요건"에 옮겨 적는다
- `:run` WebSearch 예산 분배 — 한도는 모든 병렬 에이전트가 공유한다. 배정하지 않으면
  먼저 도는 에이전트가 전부 소진하고 나머지 셀은 **에러 없이 빈손으로 돌아온다**.
  이제 셀별로 숫자를 배정하고, 총합이 한도에 근접하면 실행 전 상향을 안내한다
- 조용한 실패 감지 규칙을 `SKILL.md` §2에 단일 정의 — WebSearch 0건을 "자료 없음"으로
  판정하기 전 API 경로 재확인을 강제하고, 연속 2회 0건이면 한도 소진으로 간주한다
- `research.json`에 `protocols`, `search_budget` 필드 추가
- `settings.sample.json` 저장소 포함 — 권한·환경변수 템플릿.
  `Bash(python3:*)`(ScienceON URL 인코딩용), RISS·Construction Dive 도메인 추가
- 스킬 단독 사용 경로 문서화 — `~/.claude/skills/`로 복사하면 플러그인 없이도 동작

## 1.1.0

- Obsidian 하위 폴더 설정 가능 (`obsidian_research_folder`, 기본값 `Research`)
- `synthesizer`·`cell-researcher`에 한글 서술 규율(실무 보고서체) 추가

## 1.0.0 (2026-07-04)

- 최초 릴리스
- 8개 리서치 유형 템플릿 (기술검증 / 시장·경쟁 / 타당성 / 투자 / 경영진보고 / 학습 / 학술 / 자녀교육)
- 커맨드: design(설계+실행경로 안내), run(--light/--full 병렬 조사), verify(레드팀 검증)
- 에이전트: cell-researcher, synthesizer, red-teamer
- 유형별 실행 적합도 라우팅 (자동 실행 / 외부 딥리서치 / 브리프만)
- Obsidian 볼트 연동 (.research-prompter.json)
- 산출물: 한글 보고서 + research.json (출처 등급·확신도 포함)
