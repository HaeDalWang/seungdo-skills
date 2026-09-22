# 출처

이 플러그인의 스킬 2종은 [ECC (Everything Claude Code)](https://github.com/affaan-m/ECC)에서
가져와 수정한 것이다. 원본은 MIT 라이선스이며, 저작권 고지는 `LICENSE`에 그대로 포함했다.

| 스킬 | 원본 경로 | 변경 내용 |
|---|---|---|
| `verification-loop` | `skills/verification-loop` | frontmatter에 출처 표기 추가. 본문 변경 없음 |
| `github-ops` | `skills/github-ops` | frontmatter에 출처 표기 추가. ECC 레포 유지보수자 전용 릴리스 문단(7줄)과 `references/ecc-release-checklist.md` 제거 |

원본 저작권: Copyright (c) 2026 Affaan Mustafa (MIT)

상위 레포(`seungdo-skills`)는 PolyForm-Noncommercial-1.0.0이지만,
이 디렉토리(`plugins/seungdo-devops/`)는 원본을 따라 **MIT**로 유지한다.

## 채택 근거

2026-08-18 ~ 09-22, 91개 세션 실측에서 ECC 스킬 호출은 0회였다.
그럼에도 이 2종만 가져온 이유:

- **verification-loop** — ECC 스킬 중 유일하게 다른 스킬을 참조하지 않는 자립형이다.
- **github-ops** — 이슈 본문·PR 설명·CI 로그를 untrusted 입력으로 다루는
  prompt injection 방어 섹션이 있다. github MCP(툴)와 역할이 겹치지 않는다.

제외한 4종과 그 이유:

| 스킬 | 제외 이유 |
|---|---|
| `tdd-workflow` | `~/.claude/rules/common/testing.md`와 내용 중복 (80% 커버리지, RED/GREEN) |
| `security-review` | `~/.claude/rules/common/security.md`와 체크리스트 중복 + 내장 `security-review` 스킬과 이름 충돌 |
| `terminal-ops` | 나머지 5종을 "Skill Stack"으로 호출하는 허브. 단독으로는 반쪽 |
| `knowledge-ops` | Layer 3이 MCP memory server 전제인데 미설치 |
