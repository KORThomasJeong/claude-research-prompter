# claude-research-prompter

> 한국어 | [English below](#english)

Claude Code용 **리서치 설계·실행 플러그인** — 리서치 유형을 판별해 MECE 브리프를 설계하고,
셀 단위 병렬 조사 → 종합 → 레드팀 검증까지 자동화합니다.
결과는 한글 보고서 + 머신리더블 `research.json`으로 저장되며, 설정 시 Obsidian 볼트에도 적재됩니다.

**8개 리서치 유형**

| # | 유형 | 산출물 |
|---|---|---|
| 1 | 기술 검증형 (Tech DD) | 비교표 + ADR 초안 |
| 2 | 시장·경쟁 분석형 | 랜드스케이프 맵 + 시사점 |
| 3 | 도입 타당성형 | Go/No-Go 프레임 + 리스크 매트릭스 |
| 4 | 투자 판단형 | Bull/Bear 케이스 + 촉매 캘린더 |
| 5 | 경영진 보고형 | 메시지 피라미드 + Q&A 시트 |
| 6 | 탐색·학습형 | 개념 지도 + 학습 로드맵 |
| 7 | 학술 연구형 (대학원) | 문헌 매트릭스 + 연구 갭 |
| 8 | 자녀 교육형 (초·중등) | 부모용 지도 가이드 + 활동 계획 |

이 저장소는 **Claude Code 플러그인 마켓플레이스**입니다.

## 설치

```
/plugin marketplace add KORThomasJeong/claude-research-prompter
/plugin install research-prompter@claude-research-prompter
/reload-plugins
```

## 사용

```
/research-prompter:design <리서치 주제>     # 유형 판별 → 브리프 설계 → 실행 경로 안내
/research-prompter:run [--light|--full]     # 셀 병렬 조사 → 종합 → 보고서 저장
/research-prompter:verify                   # 레드팀 검증 (반박 탐색·환각 인용 확인)
```

자연어로 "이 주제 리서치 설계해줘"라고 해도 동작합니다.

**실행 경로는 유형별로 다르게 안내됩니다** — 모든 리서치를 자동 실행하지 않습니다.

- 기술 검증·도입 타당성·경영진 보고: `:run` 적합
- 시장 분석·학술 연구: 브리프를 외부 Deep Research에 입력하는 편이 유리 (run은 보조)
- 투자 판단·학술 연구: `:verify` 필수 (확증편향 / 환각 인용 검증)
- 자녀 교육: 브리프만 생성 (자동 실행 대상 아님)

`--full` 모드는 매트릭스 셀당 서브에이전트가 병렬 웹서치를 수행하므로 토큰 소모가 큽니다.
빠른 스캔은 `--light`(핵심 셀 3~4개)를 사용하세요.

## 산출물

```
docs/research/
├── briefs/BRIEF_<주제>_<날짜>.md       # 리서치 브리프 (외부 딥리서치에도 그대로 사용)
├── RESEARCH_<주제>_<날짜>.md           # 최종 보고서 (검증 후 _verified.md)
└── research_<주제>_<날짜>.json         # 출처 등급·확신도 포함 머신리더블
```

**Obsidian 연동**: 프로젝트 루트 또는 `~/.research-prompter.json`에

```json
{ "obsidian_vault_path": "/path/to/vault", "obsidian_research_folder": "Research" }
```

를 두면 보고서가 볼트의 지정 폴더에 frontmatter·위키링크와 함께 저장됩니다.
`obsidian_research_folder`는 선택값이며, 생략 시 `Research`가 기본값입니다
(볼트가 번호 접두사 컨벤션을 쓰면 `"50-Research"` 등으로 지정).

## 구조

```
claude-research-prompter/
├── .claude-plugin/marketplace.json
└── plugins/research-prompter/
    ├── .claude-plugin/plugin.json
    ├── commands/{design,run,verify}.md
    ├── agents/{cell-researcher,synthesizer,red-teamer}.md
    └── skills/research-design/
        ├── SKILL.md                    # 라우터 + 실행 적합도 표
        └── references/01~08-*.md       # 유형별 템플릿 (progressive disclosure)
```

## 라이선스

MIT · 투자 유형의 산출물은 정보 정리이며 투자 권유가 아닙니다.
학술 유형은 인용 실존 검증 절차를 포함하나 최종 확인 책임은 사용자에게 있습니다.

---

## English

A **research design & execution plugin for Claude Code**. It classifies your research
into one of 8 types (tech due diligence, market landscape, feasibility, investment thesis,
executive brief, exploratory learning, academic literature review, K-12 parenting support),
designs a MECE research brief, then optionally executes it: parallel cell-researcher
subagents → synthesis → red-team verification (counter-evidence search, source grading,
hallucinated-citation detection).

```
/plugin marketplace add KORThomasJeong/claude-research-prompter
/plugin install research-prompter@claude-research-prompter
```

Outputs: Korean report + machine-readable `research.json` under `docs/research/`,
optionally mirrored into an Obsidian vault (`~/.research-prompter.json`).
Execution routing is type-aware: some types run in-plugin, some are better served by
handing the generated brief to an external Deep Research tool, and the K-12 type
stops at brief generation by design. MIT licensed.
