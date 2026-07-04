---
description: 브리프 기반 병렬 리서치 실행 (--light 핵심 셀만 / --full 전체) → 종합 → 보고서·JSON 저장
argument-hint: "[--light|--full] [브리프 파일 경로 (생략 시 최신 브리프)]"
---

# /research-prompter:run

인자: $ARGUMENTS

당신은 리서치 실행 오케스트레이터다. 아래 파이프라인을 수행한다.

## 0단계 — 브리프 로드 및 게이트

1. 인자에 경로가 있으면 그 브리프를, 없으면 `docs/research/briefs/`에서 최신 파일을 읽는다.
   브리프가 없으면 실행을 중단하고 `/research-prompter:design`을 먼저 안내한다.
2. **유형 게이트**: 브리프의 유형이 자녀 교육형(8)이면 자동 실행하지 않는다.
   이유(대화형 지도가 적합, 자동 조사 가치 낮음)를 설명하고 claude.ai 사용을 안내한 뒤 종료.
3. **비용 고지**: 모드(--light 기본 아님, 미지정 시 사용자에게 선택 요청)와
   예상 셀 수, 병렬 조사 횟수를 알리고 진행 확인을 받는다.
   - `--light`: 사용자와 함께 핵심 셀 3~4개를 선정 (연구 질문 답변에 필수적인 셀 우선)
   - `--full`: 매트릭스 전체 셀

## 1단계 — 셀 단위 병렬 조사

각 대상 셀에 대해 `cell-researcher` 서브에이전트를 병렬로 실행한다.
각 에이전트에 전달할 것: 브리프의 배경·핵심 질문 요약, 담당 셀([차원]×[관점]),
해당 유형의 요구 분석 방식, 출처 요건.

유형별 추가 지시:
- 투자 판단형(4): 강세·약세 논거를 같은 분량으로. 모든 시세·실적 데이터에 기준 시점 명기
- 학술 연구형(7): 모든 인용에 검색 경로(저자, 연도, 저널, 가능하면 DOI) 필수.
  검색으로 실존 확인 안 되는 문헌은 "미확인" 표기
- 시장·경쟁 분석형(2): 시장 규모 수치는 복수 출처 교차, 방법론 차이 기록

## 2단계 — 종합

`synthesizer` 서브에이전트에 전체 셀 결과를 전달해 유형별 출력 규격
(references/ 템플릿의 "출력 규격" 절)에 맞는 보고서를 합성한다.
출처는 신뢰도 등급(1차/2차/미확인)으로 분류한다.

## 3단계 — 저장

1. `docs/research/RESEARCH_<주제슬러그>_<YYYYMMDD>.md` — 최종 보고서 (한국어)
2. `docs/research/research_<주제슬러그>_<YYYYMMDD>.json` — 머신리더블 결과:
```json
{
  "topic": "", "type": "1-8", "mode": "light|full", "date": "",
  "research_questions": [],
  "findings": [
    {"cell": "차원×관점", "summary": "", "confidence": "high|medium|low",
     "sources": [{"title": "", "url": "", "grade": "primary|secondary|unverified", "as_of": ""}]}
  ],
  "hypotheses_verdict": [], "open_questions": [], "verify_status": "pending"
}
```
3. **Obsidian 저장** (설정 시): 프로젝트 루트 또는 홈의 `.research-prompter.json`에서
   `obsidian_vault_path`를 읽는다. 설정이 없으면 한 번 물어보고, 원하면
   `~/.research-prompter.json`에 저장해 재사용한다.
   저장 하위 폴더는 같은 파일의 `obsidian_research_folder`를 따르며, 없으면 `Research`가 기본값이다.
   볼트의 `<obsidian_research_folder>/` 폴더에 보고서를 복사하되 frontmatter(tags: [research, 유형태그],
   date, source: research-prompter)를 추가하고, 보고서 내 핵심 개념에 [[위키링크]]를 건다.

## 4단계 — 검증 안내

저장 후 반드시 안내한다:
- 투자 판단형(4)·학술 연구형(7): "`/research-prompter:verify`가 **필수**입니다.
  (4: 확증편향·시점 검증 / 7: 환각 인용 검증)"
- 그 외 유형: verify 권장 + 이 리서치에서 특히 검증 가치가 높은 지점 1~2개 제시
