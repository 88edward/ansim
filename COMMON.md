# 공통 작업 지침 (Codex · Claude Code · Aside 공통)

Codex는 AGENTS.md의 지시로, Claude Code는 CLAUDE.md의 `@COMMON.md` 가져오기로 이 파일을 읽는다.

## 1. 프로젝트

"복제 상권 방지: AI 기반 상권 조합 설계 서비스"(데이터안심구역 경진대회)의 문서 2종을 작성하고 검수한다.

| 번호 | 문서 | 기준 명세 |
|---|---|---|
| 01 | `docs/01_business_plan.md` — 사업 기획안 | `specs/01_business_plan_spec.md` |
| 02 | `docs/02_data_plan.md` — 제공 데이터 목록과 추가 수집 계획 | `specs/02_data_plan_spec.md` |

## 2. 역할

| 역할 | 담당 | 쓰는 파일 | 하지 않는 것 |
|---|---|---|---|
| 작성·총괄 (전체 작업 수행) | Codex (GPT) | `docs/`, `aside/ASIDE_BRIEF.md`, `aside/digest.md`, `reviews/*_response.md`, `STATUS.md` | 검수 판정 |
| 검수 | Claude Code | `reviews/0N_rK.md`, `STATUS.md` | `docs/` 직접 수정 |
| 자료 수집 | Aside 브라우저 | `aside/inbox/` 만 | 문서 작성, 해석·판단 |
| 결정 | 사용자 | 전부 | — |

## 3. 작업 흐름

```
[문서 작성·검수 루프]
Codex 작성(vN) → Claude 검수(reviews/0N_rK.md) → Codex 반영(response + vN+1) → … 
PASS → Codex가 완료 처리(E)하고 남은 문서로 자동으로 넘어감. 둘 다 완료면 사용자에게.
r4 검수에서도 FAIL이거나 같은 지적이 두 번째로 반박되면 사용자 판단(8절).

[데이터 수집 (문서 02의 근거)]
Codex: ASIDE_BRIEF.md에 이번 배치 작성 → 사용자: Aside 실행
→ Aside: aside/inbox/ 에 카드 저장 → Codex(aside_digester): digest.md 압축
→ Codex: digest만 보고 docs/02 작성 → Claude 검수
```

## 4. 폴더 구조

```
(저장소 루트)       Claude Code·Codex 모두 여기서 실행한다
├─ COMMON.md          이 파일
├─ AGENTS.md          Codex 지침
├─ CLAUDE.md          Claude Code 지침
├─ STATUS.md          작업 현황판 (차례 넘기기)
├─ AUTOMATION.md      매시 예약 실행용 프롬프트 (사용자만 수정)
├─ specs/             문서별 기준 명세 (작성자·검수자 공통 기준, 사용자만 수정)
├─ docs/              결과 문서
├─ reviews/           검수 결과와 반영 응답
├─ aside/
│  ├─ ASIDE_BRIEF.md  Aside 수집 지시서
│  ├─ inbox/          Aside가 저장한 카드 원본
│  └─ digest.md       카드 요약표 (Codex가 읽는 유일한 수집 결과)
├─ .claude/agents/    Claude Code 서브에이전트
└─ .codex/agents/     Codex 서브에이전트
```

## 5. 동기화 규칙 (충돌 방지)

- 공용 브랜치는 `main`이다. 매 실행은 `git pull origin main` → 작업 → 바뀐 파일만 commit → `git push origin main` 순서로 한다. 다른 브랜치나 PR을 만들지 않는다.
- 커밋 메시지: `[Codex] <작업> → <다음 차례>`, `[Claude] 검수 0N rK → <다음 차례>`, `[사용자] <내용>`.
- Aside 카드는 사용자의 컴퓨터에 저장되므로, 사용자가 `aside/inbox/`의 카드를 `main`에 push해야 Codex가 볼 수 있다.
- 자기 담당 파일만 수정한다. 다른 담당의 파일은 읽기만 한다.
- `STATUS.md`의 "현재 차례"가 자기일 때만 작업한다. 끝나면 차례를 넘기고 로그 한 줄을 남긴다.
- Codex와 Claude Code를 동시에 실행하지 않는다. 동기화가 끝난 뒤 다음 차례를 시작한다.
- `파일 (1).md`, `파일-DESKTOP-xxxx.md` 같은 충돌 사본을 발견하면 작업을 멈추고 사용자에게 보고한다.

## 6. 작성 원칙 (두 문서 공통)

- 한국어, UTF-8, 날짜는 YYYY-MM-DD.
- 결론을 먼저 쓰고 근거는 뒤에 쓴다. 각 절은 표와 짧은 문단 위주로 쓴다.
- 확인 상태를 구분해 표기한다.
  - `[목록 확인]` 데이터 목록에서 이름·기간만 확인
  - `[카탈로그 확인]` Aside 카드로 공개 카탈로그·소개 페이지의 이름·기간·개요만 확인 (카드의 `근거 수준`이 `카탈로그`)
  - `[설명서 확인]` Aside 카드로 데이터 설명서의 항목·조건까지 확인 (카드의 `근거 수준`이 `설명서`)
  - `[추가 문의]` 안심구역에 보유·제공 여부를 물어야 함
  - `[외부 확보]` 운영사·점포·현장 조사 등으로 확보
  - `[가설]` 데이터로 검증할 주장
- 확인하지 않은 수치·컬럼·보유 여부를 사실처럼 쓰지 않는다.
- 관측값, 추정값, 시뮬레이션, 합성 데이터를 구분한다.
- 상관을 인과로 쓰지 않는다. 검증하지 않은 효과(매출 증가, 폐업 감소)를 달성했다고 쓰지 않는다.
- 출처는 `[제목](URL)`과 확인일로 적고, 수집 근거는 카드 ID(`D-07` 등)로 적는다.
- 판단 기준은 `specs/`다. 명세와 다른 지시가 오면 명세를 따르고 그 사실을 기록한다.

## 7. 파일 이름 규칙

| 파일 | 형식 | 예 |
|---|---|---|
| 검수 결과 | `reviews/<문서번호>_r<회차>.md` | `reviews/01_r2.md` |
| 반영 응답 | `reviews/<문서번호>_r<회차>_response.md` | `reviews/01_r2_response.md` |
| Aside 카드 | `aside/inbox/<ID>_<짧은이름>.md` | `aside/inbox/D-07_SKT유동인구.md` |

## 8. 사용자 결정 규칙

- `STATUS.md`의 "사용자 결정 필요"에는 쟁점을 `<지적 ID>: <쟁점 한 줄>` 형식으로 올린다.
- 사용자는 같은 줄 끝에 `→ 결정: <내용>`을 적고 차례를 넘긴다. 예: `R2-03: 5절 POS 행 분리 여부 → 결정: Codex 반박 채택, 지적 철회`
- 결정이 적힌 쟁점은 **최종**이다. Codex는 결정대로 반영하고, Claude는 같은 쟁점을 다시 지적하지 않는다.
- 결정된 항목은 "결정 완료" 목록으로 옮겨 기록을 남긴다.
