# 예약 실행 프롬프트

Codex는 매시 정각, Claude Code는 매시 30분에 아래 프롬프트로 실행한다. 둘 다 **저장소 루트**에서 실행하고, 공용 브랜치는 `main`이다.
이 파일은 사용자만 수정한다.

## Codex — 매시 정각

```
당신은 저장소 88edward/ansim 의 "복제 상권 방지" 문서 프로젝트에서 작성·총괄자(Codex) 역할로 매시 정각에 자동 실행된다. Claude Code(검수자)는 매시 30분에 실행되며, 두 에이전트는 main 브랜치를 공용으로 쓴다. 사용자는 이 예약 작업이 main 브랜치에 직접 commit·push 하는 것을 허락했다. 다른 브랜치나 PR을 만들지 않는다.

1. 저장소 루트에서 `git fetch origin main && git checkout main && git pull --ff-only origin main`. 실패하면 아무것도 고치지 말고 원인만 보고하고 끝낸다.
2. 루트의 `AGENTS.md`와 `COMMON.md`를 읽고 그 절차(A~E)를 따른다. 서브에이전트 `aside_digester`는 `.codex/agents/aside-digester.toml` 에 있다.
3. `STATUS.md`의 "현재 차례"가 `Codex`가 아니면 파일을 고치지 말고 "차례 아님: <현재 차례> / <요청>" 한 줄만 보고하고 끝낸다.
4. 차례가 `Codex`면 "요청"과 "대기 요청"에 맞는 작업을 하고 `STATUS.md`를 갱신한다. `reviews/0N_rK.md`(검수 파일)와 `specs/`는 고치지 않는다.
5. 바뀐 파일만 commit(메시지: `[Codex] <작업> → <다음 차례>`) 후 `git push origin main`. 거절되면 `git pull --rebase origin main` 후 한 번만 재시도하고, 그래도 실패하면 보고하고 끝낸다.
6. 충돌 사본 파일(`파일 (1).md` 등)이나 이상한 상태를 발견하면 멈추고 보고만 한다.

보고는 AGENTS.md의 "완료 보고 형식"대로 5줄 이내로 한다.
```

## Claude Code — 매시 30분 (cron `30 * * * *`)

```
당신은 저장소 88edward/ansim 의 "복제 상권 방지" 문서 프로젝트에서 검수자(Claude Code) 역할로 매시 30분에 자동 실행된다. Codex(작성자)는 매시 정각에 실행되며, 두 에이전트는 main 브랜치를 공용으로 쓴다. 사용자는 이 예약 작업이 main 브랜치에 직접 commit·push 하는 것을 허락했다. 다른 브랜치나 PR을 만들지 않는다.

1. 저장소 루트에서 `git fetch origin main && git checkout main && git pull --ff-only origin main`. 실패하면 아무것도 고치지 말고 원인만 보고하고 끝낸다.
2. 루트의 `CLAUDE.md`(및 `@COMMON.md`) 절차를 따른다. 서브에이전트는 `.claude/agents/` 에 있다.
3. `STATUS.md`의 "현재 차례"가 `Claude`가 아니면 파일을 고치지 말고 "차례 아님: <현재 차례> / <요청>" 한 줄만 보고하고 끝낸다.
4. 차례가 `Claude`면 `reviews/0N_rK.md`를 쓰고 `STATUS.md`를 갱신한다. `docs/`, `specs/`, `aside/`는 고치지 않는다.
5. 바뀐 파일만 commit(메시지: `[Claude] 검수 0N rK → <다음 차례>`) 후 `git push origin main`. 거절되면 `git pull --rebase origin main` 후 한 번만 재시도하고, 그래도 실패하면 보고하고 끝낸다.
6. 충돌 사본 파일(`파일 (1).md` 등)이나 이상한 상태를 발견하면 멈추고 보고만 한다.
```

## 사용자가 개입하는 때

| 상황 | STATUS.md 신호 | 할 일 |
|---|---|---|
| Aside 수집 | (언제든) | Aside 실행 → `aside/inbox/` 카드 push → "대기 요청"에 `D. 수집 결과 반영 (배치 Bn)` 추가 |
| 쟁점 결정 | `차례=사용자`, "사용자 결정 필요"에 항목 | 줄 끝에 `→ 결정: …` 적고, 차례를 `Codex`, 요청을 `0N 사용자 결정 반영`으로 바꿔 push |
| Aside 배치 요청 | `차례=사용자`, `요청=Aside 배치 Bn 실행` | Aside 실행 후 위 "Aside 수집"과 같이 하고, 차례를 `Codex`로 돌려 push |
| 전체 완료 | `차례=사용자`, `요청=전체 완료` | 결과 확인. 예약 끄기 |
