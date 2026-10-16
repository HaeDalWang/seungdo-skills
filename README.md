# seungdo-skills

AI 코딩 도우미(Claude Code)에게 **"일 잘하는 습관"을 심어 주는 설정 모음**입니다.

AWS·Kubernetes 운영과 고객 응대를 하면서 매번 똑같이 하던 판단과 말투가 있었어요.
그걸 AI가 알아서 따라 하도록 파일로 굳혀 둔 게 이 저장소입니다.

> **한 줄 규칙:** 실제 업무에서 **여러 번 써 보고 효과를 본 것만** 올립니다.
> 한두 번 써서 괜찮았던 건 아직 안 올려요.

---

## 먼저, 용어 4개

이 README에는 네 가지가 나와요. 스마트폰에 비유하면 쉽습니다.

| 용어 | 비유 | 실제로는 |
|---|---|---|
| **스킬** | 필요할 때만 여는 앱 | 특정 상황(예: "근거 있어?")이 오면 AI가 꺼내 읽는 작업 설명서 |
| **룰** | 폰의 기본 설정 | 대화를 시작할 때마다 **항상** 켜져 있는 지침. 기다리지 않고 늘 적용 |
| **에이전트** | 심부름 보낸 친구 | 따로 나가서 일을 하고 **결과 요약만** 들고 오는 AI. 내 대화창이 지저분해지지 않음 |
| **플러그인** | 앱스토어에서 받는 앱 묶음 | 스킬·에이전트를 한 번에 설치·업데이트하는 꾸러미. 이 저장소가 그 앱스토어(마켓플레이스) 역할도 함 |

---

## 들어 있는 것

### 스킬

| 스킬 | 하는 일 | 이럴 때 켜져요 |
|---|---|---|
| [`seonbi`](skills/seonbi/SKILL.md) | 깐깐한 선생님처럼 **"그거 확인한 거야, 기억한 거야?"** 를 구분시킴 | 사실·기술·버전 질문, `확실해?`, `근거`, `출처`, 장애 대응 |
| [`katalk`](skills/katalk/SKILL.md) | 고객에게 보낼 메신저 답장을 내 말투로 쓰거나, 내가 쓴 초안을 검토 | `카톡`, `뭐라고 보내`, `회신`, `너라면 뭐라고 적을래` |
| [`k8s-upgrade-skills`](https://github.com/HaeDalWang/k8s-upgrade-skills) ↗ | Kubernetes 버전 업그레이드를 **단계마다 검문을 통과해야 다음으로** 가는 방식으로 진행 | `EKS 업그레이드`, `K8s 버전 업그레이드` |

`seungdo-devops` 플러그인 (따로 설치):

| 스킬 | 하는 일 | 이럴 때 켜져요 |
|---|---|---|
| [`verification-loop`](plugins/seungdo-devops/skills/verification-loop/SKILL.md) | "다 됐어요" 말하기 전에 프로젝트의 빌드·타입·린트·테스트를 **실제로 돌려서** 증거를 보여 줌 | 기능 완성, PR 직전, 리팩터링 후 |
| [`github-ops`](plugins/seungdo-devops/skills/github-ops/SKILL.md) | `gh` 명령으로 이슈·PR·CI를 관리. 남이 쓴 PR 내용은 **믿지 않고** 다룸 | `이슈 정리`, `PR 확인`, `CI 깨졌다`, 릴리스 |

> `k8s-upgrade-skills`는 덩치가 커서 [별도 저장소](https://github.com/HaeDalWang/k8s-upgrade-skills)에 있어요.
> 파일은 여기 없지만 **같은 앱스토어(마켓플레이스)에서 설치**됩니다.

### 에이전트

| 에이전트 | 하는 일 | 권한 |
|---|---|---|
| [`leak-scan`](agents/leak-scan.md) | 저장소를 공개하거나 push하기 전에 비밀번호·개인정보·회사 내부 이름이 섞여 있는지 검사. 지금 파일**과 git 기록** 둘 다 봄 | 읽기만 |
| [`shell-diagnose`](agents/shell-diagnose.md) | 빌드·테스트·로그가 실패한 원인을 조사해서 **원인과 고칠 방법만** 짧게 보고 | 읽기만 |

플러그인을 설치하면 에이전트도 같이 들어와요.

### 룰

`~/.claude/rules/`에 넣어 두면 **모든 프로젝트에서 항상** 적용됩니다.

| 룰 | 한마디로 |
|---|---|
| [`simplicity.md`](rules/common/simplicity.md) | **최소한으로 만들기.** 꼭 필요한가 → 이미 있나 → 기본 기능으로 되나 → 한 줄로 되나, 순서로 따져 봄. 버그는 증상 말고 원인을 고침. 요청과 상관없는 코드는 건드리지 않음 |
| [`coding-style.md`](rules/common/coding-style.md) | 데이터를 직접 고치지 말고 새로 만들기(불변성), 에러 처리·입력 검증, 이름 짓기, 파일은 800줄까지, 리뷰 심각도 4단계 |
| [`development-workflow.md`](rules/common/development-workflow.md) | 새로 짜기 전에 남이 만든 것부터 찾기 → 계획 → 테스트(맞을 때만) → 검증 |
| [`git-workflow.md`](rules/common/git-workflow.md) | 커밋 메시지 형식, PR 쓸 때 전체 커밋 기록 보기 |
| [`security.md`](rules/common/security.md) | 커밋 전 보안 체크리스트, 비밀값 관리, 사고 났을 때 순서 |
| [`testing.md`](rules/common/testing.md) | 복잡한 로직엔 **실행 가능한 확인 하나**만. 테스트가 실패하면 테스트 말고 코드를 고침 |

> **룰은 플러그인으로 설치되지 않아요.** Claude Code 플러그인에는 "룰" 칸이 없거든요
> ([plugins reference](https://code.claude.com/docs/en/plugins-reference)). 그래서 룰만 직접 복사합니다.

> `simplicity.md`는 [ponytail](https://github.com/DietrichGebert/ponytail)(MIT) 4.10.0의 핵심 규칙을
> 가져와 다듬은 거예요. 플러그인 없이 파일 하나로 같은 효과를 냅니다. 출처는 [`rules/NOTICE.md`](rules/NOTICE.md).

---

## 설치

### 플러그인으로 설치 (추천)

Claude Code에서:

```
/plugin marketplace add HaeDalWang/seungdo-skills
/plugin install seungdo-skills@seungdo-skills
```

Kubernetes 업그레이드 스킬도 쓰려면 (별도 저장소에서 자동으로 받아와요):

```
/plugin install k8s-upgrade-skills@seungdo-skills
```

검증·GitHub 관리 스킬도 쓰려면:

```
/plugin install seungdo-devops@seungdo-skills
```

설치 확인은 `/plugin list`, 업데이트는 `/plugin update seungdo-skills`.

### 직접 복사해서 설치

플러그인을 안 쓰거나, Claude Code 말고 다른 AI 도구에 넣을 때:

```bash
git clone https://github.com/HaeDalWang/seungdo-skills.git
cd seungdo-skills
cp -R skills/seonbi skills/katalk ~/.claude/skills/
```

`k8s-upgrade-skills`는 그 저장소의 `install.sh`를 쓰세요.

> ⚠️ 플러그인 설치와 직접 복사를 **둘 다 하지 마세요.** 같은 스킬이 두 번 들어가요.
> 플러그인으로 옮겼다면 `~/.claude/skills/`의 같은 이름 폴더는 지우세요.

### 룰 설치 (항상 직접 복사)

복사만 하면 끝이에요. `~/.claude/rules/` 아래에 있는 `.md` 파일은 하위 폴더까지 자동으로 읽힙니다.

```bash
git clone https://github.com/HaeDalWang/seungdo-skills.git
cp -R seungdo-skills/rules/common ~/.claude/rules/
```

몇 개만 고르려면:

```bash
mkdir -p ~/.claude/rules/common
cp seungdo-skills/rules/common/{security,testing}.md ~/.claude/rules/common/
```

- 한 프로젝트에만 쓰고 싶으면 → 그 프로젝트의 `.claude/rules/`에 넣기
- 특정 파일을 만질 때만 켜지게 하려면 → 파일 맨 위에 `paths`를 적기

```markdown
---
paths:
  - "src/**/*.{ts,tsx}"
---
```

`paths`가 없으면 항상 켜지고, 있으면 그 경로의 파일을 읽을 때만 켜져요.

### Claude Code 말고 다른 AI 도구에서도

룰과 스킬은 그냥 Markdown 파일이라 다른 도구에서도 쓸 수 있어요. **복사본을 만들지 말고 원본을 가리키게** 하면, 원본 하나만 고쳐도 전부 같이 바뀝니다.

| 도구 | 방법 |
|---|---|
| Kiro CLI | `~/.kiro/steering/`에 룰 파일 **symlink**, `~/.kiro/skills/`에 스킬 폴더 symlink |
| Codex CLI | 스킬은 `~/.agents/skills/`에 symlink. 전역 지시는 `~/.codex/AGENTS.md` **파일 하나**만 읽어서 symlink로 묶을 수 없음. 룰을 이어 붙여 그 파일을 만들고, 원본을 고치면 다시 만들어야 함 |

### 다른 플러그인과 겹치지 않게

이 저장소의 일부는 아래 프로젝트에서 가져오거나 아이디어를 얻었어요. **같은 일을 하는 건 하나만 설치하세요.**
둘 다 깔면 같은 지시가 두 번 들어가거나, 같은 상황에서 서로 다른 방법끼리 부딪혀요.

| 이 저장소 | 겹치는 외부 프로젝트 | 관계 | 둘 다 깔면 |
|---|---|---|---|
| `rules/common/simplicity.md` | [ponytail](https://github.com/DietrichGebert/ponytail) 플러그인 | 핵심 규칙을 가져와 다듬음 (MIT) | 같은 규칙이 매번 두 번 들어감. ponytail을 쓸 거면 `simplicity.md`만 빼고 복사 |
| `seonbi`의 진단 순서 (증상 확인·가설 순위·고친 뒤 재확인) | [mattpocock/skills](https://github.com/mattpocock/skills)의 `diagnosing-bugs` | 아이디어만 얻음, 문장은 새로 씀 | "디버그해 줘"에 진단 방법 두 개가 같이 켜짐. 하나만 쓰세요 |
| `seungdo-devops`의 `verification-loop`, `github-ops` | [ECC](https://github.com/affaan-m/ECC) | 가져와 수정 (MIT) | 같은 이름 스킬이 두 개 생김 |

`mattpocock/skills`의 나머지(`grilling`, `tdd`, `to-spec` 등)는 여기 없어서 겹치지 않아요.

---

## 같이 쓰면 더 좋은 조합

### 핵심: `seonbi`가 항상 먼저

고객에게 메시지를 보내기 전에 `seonbi`가 **"이 내용, 이번에 직접 확인한 거 맞아?"** 를 먼저 걸러요.
확인 안 된 추측을 고객에게 확실한 것처럼 보내는 게, 실무에서 제일 비싼 실수거든요.

### 저장소 밖에서 같이 쓰는 도구

`seonbi`는 검색 도구가 있어야 제 실력이 나와요. 숙제 검사하는 선생님한테 교과서가 있어야 하는 것처럼요.

| 도구 | 종류 | 원본 | 왜 같이 쓰나 |
|---|---|---|---|
| **exa** | MCP | [exa-labs/exa-mcp-server](https://github.com/exa-labs/exa-mcp-server) | `seonbi`의 1순위 검색. 없으면 기본 `WebSearch`로 대신하지만, AI용으로 정리된 결과라 품질이 달라요 |
| **context7** | MCP | [upstash/context7](https://github.com/upstash/context7) | 라이브러리 문서를 **그 버전 기준으로** 가져옴. 기억이 아니라 문서에서 |
| **AWS MCP** | 플러그인 | [aws/agent-toolkit-for-aws](https://github.com/aws/agent-toolkit-for-aws) | 지금 돌아가는 AWS 상태를 직접 조회. "실제 시스템이 문서보다 우선"의 그 실제 시스템 |
| **eli5** | 스킬 | [DreambigOu/ELI5](https://github.com/DreambigOu/ELI5) | 같은 내용을 듣는 사람 수준에 맞춰 다시 설명. 비개발자 담당자·임원 보고용 |

넷 다 **따로 있는 프로젝트**예요. 각자 설치하면 돼요.

```bash
claude mcp add --transport http exa https://mcp.exa.ai/mcp
claude mcp add context7 -- npx -y @upstash/context7-mcp
```

```
/plugin marketplace add aws/agent-toolkit-for-aws
/plugin install aws-core@agent-toolkit-for-aws
```

### 이렇게 씁니다

**장애가 났을 때** (제일 자주 씀)

```
seonbi          에러 메시지부터 정확히 적어 둠
  ├ exa         남들도 똑같이 겪었나? (이미 "fixed in X"로 해결된 경우가 많음)
  ├ AWS MCP     내 환경은 실제로 어떤 상태지? — 추측 말고 조회
  └ context7    그 버전 공식 문서는 뭐라고 하지?
      ↓
    확인된 것만 결론으로 → 답 끝에 출처 / 확신도 / 확인 방법 한 줄
      ↓
katalk          고객에게 보낼 2~3줄. 확인된 사실만
```

**EKS 업그레이드**

```
k8s-upgrade-skills   준비물 확인 → 계획서 → 단계별 검문
  검문에서 멈추면 → seonbi (왜 멈췄지? 비슷한 사례부터 검색)
  고객에게 알려야 하면 → katalk
```

**비개발자에게 설명할 때**

```
seonbi   먼저 사실 확정 (틀린 걸 쉽게 설명하면 더 나쁨)
  → eli5     듣는 사람 수준으로 다시 씀
  → katalk   메신저 길이로 줄임
```

순서가 중요해요. **틀린 내용을 쉽게 풀어 쓰는 것**이 어렵게 쓴 틀린 답보다 더 큰 문제를 만들거든요.

---

## 하나씩 자세히

### `seonbi` — 선비

> 목표: AI 답이 틀렸다면, **고객에게 나가기 전에 틀렸다는 게 보이게** 하기.

AI가 내는 답 중에 제일 위험한 건 **"자신 있게 말한 틀린 답"** 이에요.
특히 기억에만 있는 내용을 "공식 문서에 따르면~"처럼 출처가 있는 척 말하는 것. 이건 신뢰를 위조하는 거예요.

그래서 답하기 전에 **질문이 정확히 뭔지 먼저 고정**하고, 다섯 관문을 통과시킵니다.

| 관문 | 확인하는 것 |
|---|---|
| 1. 시점 | 이 근거가 **지금 이 버전**에 맞나? 버전을 모르면 먼저 찾거나 물어봄 |
| 2. 출처 순서 | "실제로 어떻게 돌아가나" → **실제 시스템을 직접 확인한 게** 문서보다 위. "원래 어떻게 돼야 하나" → 공식 문서. **"왜 고장 났나" → 남의 사례부터 검색** |
| 3. 원래 용도 | 만든 사람 의도를 거스르는 꼼수인가? 꼼수면 꼼수라고 말하고 정석 방법도 같이 보여 줌 |
| 4. 확신도 | "아마", "~인 것 같아요" 금지. 대신 **확인됨(confirmed) / 추정(hypothesis) / 모름(unknown)** 중 하나를 밝힘 |
| 5. 최선인가 | 눈앞의 문제만 봤나? 다른 선택지와 장단점을 말했나? |

**애매하게 말하는 건 금지, 모른다고 말하는 건 환영.** "모르겠어요"는 통과고, 근거 없이 단정하면 탈락이에요.

모든 기술 답변 끝에는 이런 한 줄이 붙어요:

```
Source: <URL 또는 명령> (<출처 단계>, <버전/날짜>) | Certainty: confirmed | Verify: <확인 명령>
```

실제 시스템과 문서 내용이 다르면 **실제 시스템을 믿고, 그 차이를 따로 알려 줘요.** 보통은 그 차이가 진짜 원인이에요.

**장애 진단은 순서가 달라요.**
1. **증상 확인:** 증상을 보여 주는 명령 하나를 먼저 돌려서 기록해요. 이 명령은 읽기만 하고 아무것도 바꾸지 않아요.
2. **사례 검색:** 에러 메시지에서 매번 바뀌는 부분(ID·경로·시간)을 지우고 검색해요. 이미 누가 겪고 해결한 경우가 문서에 답이 있는 경우보다 많아요. **이 단계는 건너뛸 수 없어요.**
3. **가설 세우기:** 가설은 **최소 2개**예요. 각각에 *"X가 원인이면 이걸 확인했을 때 Y가 보일 것"* 이라는 예측을 붙여요. 예측이 없으면 아직 가설이 아니에요.
4. **확인하기:** 검색으로 찾은 건 **단서**지 **증거**가 아니에요. 실제 시스템이나 공식 문서로 확인한 뒤에야 근거로 써요.
5. **고친 뒤 재확인:** 1번 명령을 다시 돌려서 증상이 사라진 걸 봐야 "고쳤다"고 말해요.

(1·3·5번은 [mattpocock/skills](https://github.com/mattpocock/skills)의 `diagnosing-bugs`에서 아이디어를 얻었어요.)

검색은 `exa` → 기본 `WebSearch`/`WebFetch` 순으로 해요. **exa가 없어도 돌아가고**, 검색 수단이 전부 막혔을 때만 기억으로 답해요. 그때는 답 끝에 `recall, unverified`(기억, 확인 안 됨)라고 꼭 남겨요.

**딱 하나 무조건 멈추는 경우:** 확인 안 된 근거로 **되돌릴 수 없는 작업**(데이터 삭제, 되돌릴 방법 없는 운영 변경)을 하려고 할 때. 이때는 추측도 제안도 안 하고, 확인 방법만 알려 주고 멈춰요.

<details>
<summary>왜 만들었나</summary>

팀장님의 리뷰 기준을 스킬로 옮긴 거예요. 목적은 똑똑해 **보이는** 게 아니라,
틀린 답이 나갔을 때 **틀렸다는 게 먼저 보이는 것**이에요.
</details>

---

### `katalk` — 카톡

고객 담당자에게 메신저로 **그대로 복사해서 보낼** 짧은 메시지를 써요.
티켓 답장이나 첨부 문서처럼 긴 글은 대상이 아니에요.

```
[내가 확인한 사실] + [되묻기 ~인가요??]
[이유 한 줄] + [해결책 한 줄 ~하시면 됩니다!]
```

- **2~3줄.** 더 늘리지 않아요
- **사실 먼저.** 이번에 직접 본 것만
- **되묻기.** 단정하지 않아요. 고객 시스템은 고객이 더 잘 아니까요
- **해결책은 복붙 가능하게.** 리소스 이름, 주소, 설정값을 그대로

실제로 한 번에 원인을 찾아낸 메시지:

```
혹시 apply 출력에 aws_lb_target_group.traefik_alb 에러 나있나요?? 지금 NLB는 active인데 NLB→ALB 타겟그룹이 prod VPC에 안보여서요
dev 쪽에 같은 이름(traefik-alb-tg-443/80/8000)이 이미 있어서 이름 겹치면 DuplicateTargetGroupName 날 수 있어서 그때는 name에 ${local.env} 넣으시면 됩니다!
```

같은 내용을 보고서처럼 쓰면 이렇게 돼요. 단정하고, 길고, 고객이 이미 아는 걸 설명하죠:

```
검토해본 결과 타겟그룹 생성 단계에서 문제가 발생한 것으로 판단됩니다.
AWS 에서 타겟그룹 이름은 계정과 리전 단위로 유일해야 하는데 ...
```

초안을 주고 "검토해 줘"라고 하면 **파일은 안 고치고** 🔴 사실 오류 / 🟡 오해할 만한 부분 / 🟢 오타 순서로 짚어만 줘요.

---

### `k8s-upgrade-skills` ↗

[HaeDalWang/k8s-upgrade-skills](https://github.com/HaeDalWang/k8s-upgrade-skills)

`recipe.md`에 클러스터 정보를 적으면 사전 점검 → 업그레이드 → 사후 점검을 단계별로 진행해요.
각 단계 통과 여부는 AI가 아니라 **스크립트**가 판정해요. AI가 결과를 마음대로 해석할 수 없게요.

- 버전은 **한 칸씩만** (1.33 → 1.35처럼 건너뛰기 거부)
- 컨트롤 플레인 먼저. 노드가 절대 앞서지 않음
- 파드 보호 규칙(PDB)을 강제로 뚫지 않음
- 계획서 승인은 `승인`/`확인`/`시작` **이 단어만** 통과 ("진행해줘", "ㅇㅇ"는 승인 아님)
- 예상 못 한 오류가 나면 → 멈추고 사람 판단을 기다림

같이 들어 있는 `helm-k8s-compat`은 업그레이드 전에 Helm 차트가 새 버전과 맞는지 미리 확인해요.

> 무중단을 **보장**하진 않아요. 위험을 미리 찾아 줄 뿐이고, 최종 책임은 실행하는 사람에게 있어요.

---

### `verification-loop` · `github-ops` — ECC에서 가져온 것

[affaan-m/ECC](https://github.com/affaan-m/ECC) (MIT)에서 두 개만 골라 왔어요.

이 둘은 **"여러 번 써 본 것"이 아니라는 점**이 다른 스킬과 달라요. 91개 대화 기록을 세어 보니
ECC 스킬은 5주 동안 한 번도 안 쓰였거든요. 그래도 이 둘을 남긴 이유:

| 스킬 | 남긴 이유 |
|---|---|
| `verification-loop` | ECC 스킬 중 **유일하게 혼자서 돌아가요.** 나머지는 서로 엮여 있어서 하나만 떼면 반쪽이 돼요. 가져온 뒤 npm만 가정하던 부분을 **프로젝트가 실제로 쓰는 명령을 찾아 돌리도록** 고쳤어요 |
| `github-ops` | 이슈·PR·CI 로그를 **믿지 않고 다루는** 부분이 있어요. "이전 지시 무시해" 같은 문장이나 `curl … \| sh`가 적힌 남의 PR을 그대로 실행하지 않게 막아 줘요 |

안 가져온 것과 이유:

| 스킬 | 이유 |
|---|---|
| `tdd-workflow` | 커버리지 80%·TDD 강제가 `testing.md`(복잡한 로직엔 확인 하나)와 충돌 |
| `security-review` | `security.md`와 겹치고, Claude Code에 같은 이름 기능이 이미 있음 |
| `terminal-ops` | 나머지 5개를 묶어 부르는 허브라 혼자서는 의미 없음 |
| `knowledge-ops` | 설치 안 된 메모리 서버가 있어야 동작 |

**겹치는 게 많아지면 엉망이 돼서** 이렇게 걸렀어요.
원본 저작권: Copyright (c) 2026 Affaan Mustafa (MIT). 바꾼 내용은 [`NOTICE.md`](plugins/seungdo-devops/NOTICE.md)에 있어요.

---

### `leak-scan` · `shell-diagnose` — 에이전트

둘 다 **파일을 고칠 권한이 없어요.** 실수가 아니라 일부러 그렇게 만든 거예요.

**`shell-diagnose`** 는 긴 빌드 로그나 에러 메시지가 내 대화창을 가득 채우는 걸 막아요.
안에서 명령을 몇 개 돌리든 돌려주는 건 **원인 / 근거 1~3줄 / 고칠 방법 / 돌린 명령 목록**뿐이에요.
고칠 줄 알아도 직접 안 고치고 방법만 알려 줘요. 고치는 건 부른 쪽이 해요.

**`leak-scan`** 은 공개 전 검사관이에요. 원칙 두 개:

- **비밀값을 절대 화면에 안 보여 줘요.** 어느 파일 몇 번째 줄인지만 알려 줘요. 값을 보고서에 적으면
  유출을 한 번 더 복사하는 셈이라, 앞 6글자도 안 돼요.
- **이미 공개된 저장소에서 찾았으면 경고가 아니라 사고예요.** 파일을 지워도 이미 본 사람은 봤으니까,
  그 값을 **새로 바꾸는 것(rotate)** 이 필수라고 알려 줘요.

그리고 `.env`는 실패지만 `.env.example`은 통과, `*.tfvars`는 "내용 보고 판단"처럼
**단순한 검사기가 내는 헛경보를 구분**해요.

> `leak-scan`의 Category 2·3은 **일부러 비워 뒀어요.** 회사 도구 이름, 본인 이메일처럼 사람마다 다른 거라
> 처음 쓰기 전에 채워 넣어야 해요. 뭘 넣으면 되는지는 파일 안에 적어 뒀어요.

---

## 저장소 구조

```
seungdo-skills/
├── .claude-plugin/
│   ├── plugin.json        # 이 저장소 = 플러그인 하나
│   └── marketplace.json   # 앱스토어 목록 — 다른 저장소 스킬도 여기 등록
├── skills/                # seungdo-skills 플러그인의 스킬
│   ├── seonbi/SKILL.md
│   └── katalk/SKILL.md
├── agents/                # 플러그인이 자동으로 읽는 에이전트
│   ├── leak-scan.md
│   └── shell-diagnose.md
├── rules/                 # 플러그인이 아님 — 직접 복사
│   ├── NOTICE.md          # simplicity.md 출처 (ponytail, MIT)
│   └── common/
│       ├── simplicity.md
│       ├── coding-style.md
│       ├── development-workflow.md
│       ├── git-workflow.md
│       ├── security.md
│       └── testing.md
├── plugins/
│   └── seungdo-devops/    # 같은 저장소 안의 두 번째 플러그인
│       ├── .claude-plugin/plugin.json
│       ├── LICENSE        # MIT (원본 ECC)
│       ├── NOTICE.md      # 출처·바꾼 내용·가져온 이유
│       └── skills/
│           ├── verification-loop/SKILL.md
│           └── github-ops/SKILL.md
└── README.md
```

플러그인마다 읽는 폴더가 하나씩 정해져 있어요. `seungdo-skills`는 `skills/`만, `seungdo-devops`는 `plugins/seungdo-devops/skills/`만 읽어서 같은 스킬이 두 번 들어가지 않아요.

설정 파일(매니페스트)을 고쳤다면 커밋 전에:

```bash
claude plugin validate . --strict
claude plugin validate skills --strict
claude plugin validate agents --strict
```

---

## 라이선스

[PolyForm Noncommercial License 1.0.0](LICENSE) — 개인·공부·회사 업무처럼 **돈 버는 용도가 아니면** 자유롭게 쓰세요. 상업적으로 쓰려면 [저작자](https://github.com/HaeDalWang)에게 물어봐 주세요.

예외로 MIT인 것들 (원본 라이선스를 따름):

- `plugins/seungdo-devops/` — [ECC](https://github.com/affaan-m/ECC)에서 가져옴. 고지: [`plugins/seungdo-devops/NOTICE.md`](plugins/seungdo-devops/NOTICE.md)
- `rules/common/simplicity.md` — [ponytail](https://github.com/DietrichGebert/ponytail)에서 가져옴. 고지: [`rules/NOTICE.md`](rules/NOTICE.md)
