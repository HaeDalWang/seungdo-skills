# seungdo-skills

실무에서 **반복해서 쓴 것만** 올리는 스킬 저장소.

AWS/Kubernetes 운영과 고객 대응을 하면서, 매번 똑같이 하던 판단과 말투를 스킬로 굳혔습니다.
새로 만든 스킬이 아니라 **이미 쓰고 있던 스킬**만 여기 올라옵니다.

## 수록 기준

- 실제 업무에서 **반복 사용**했고, 결과가 검증된 것
- 한두 번 써보고 괜찮았던 것은 올리지 않는다
- 스킬이 없을 때와 있을 때의 차이를 말로 설명할 수 있는 것

---

## 수록 스킬

| 스킬 | 한 줄 | 언제 켜지나 |
|---|---|---|
| [`seonbi`](skills/seonbi/SKILL.md) | 답변 품질 게이트 — 검증한 것과 기억한 것을 구분시킨다 | 사실·기술·버전 질문, `확실해?`, `근거`, `출처`, 장애 대응 |
| [`katalk`](skills/katalk/SKILL.md) | 고객 메신저 회신을 내 말투로 쓰거나, 내 초안을 검토한다 | `카톡`, `뭐라고 보내`, `회신`, `너라면 뭐라고 적을래` |
| [`k8s-upgrade-skills`](https://github.com/HaeDalWang/k8s-upgrade-skills) ↗ | Kubernetes 버전 업그레이드를 phase-gated 방식으로 수행 | `EKS 업그레이드`, `K8s 버전 업그레이드` |

> `k8s-upgrade-skills`는 규모가 커서 [별도 레포](https://github.com/HaeDalWang/k8s-upgrade-skills)에 있습니다.
> 파일은 여기 없지만 **같은 마켓플레이스에서 설치**됩니다 (아래 참고).

---

## 설치

### 플러그인으로 설치 (권장)

Claude Code에서:

```
/plugin marketplace add HaeDalWang/seungdo-skills
/plugin install seungdo-skills@seungdo-skills
```

Kubernetes 업그레이드 스킬까지 쓸 경우 (별도 레포에서 자동으로 받아옵니다):

```
/plugin install k8s-upgrade-skills@seungdo-skills
```

설치 확인:

```
/plugin list
```

업데이트는 `/plugin update seungdo-skills`.

### 수동 설치

플러그인을 쓰지 않거나, Claude Code 외 다른 에이전트 도구에 넣을 때:

```bash
git clone https://github.com/HaeDalWang/seungdo-skills.git
cd seungdo-skills
cp -R skills/seonbi skills/katalk ~/.claude/skills/
```

`k8s-upgrade-skills`는 해당 레포의 `install.sh`를 쓰세요.

> 플러그인 설치와 수동 설치를 **둘 다 하지는 마세요.** 같은 스킬이 두 벌 로드됩니다.
> 플러그인으로 옮겼다면 `~/.claude/skills/`의 같은 이름 디렉터리는 지우세요.

---

## 스킬 상세

### `seonbi` — 선비

> 답변이 **틀렸다는 사실이 배포되기 전에 눈에 보이게** 만드는 게 목적입니다.

LLM의 가장 비싼 출력은 "자신 있게 말한 틀린 답"입니다.
특히 **기억한 문서를 출처가 있는 것처럼 인용하는 것** — 게이트가 만들려던 신뢰성을 위조하는 행위입니다.

이 스킬은 답변 전에 질문 범위를 고정(scope lock)하고, 다섯 개 게이트를 통과시킵니다.

| 게이트 | 검사 |
|---|---|
| 1. 시점 적합성 | 이 근거가 **이 버전**에 적용되나. 버전을 모르면 먼저 찾거나 물어본다 |
| 2. 출처 사다리 | "실제로 어떻게 동작하나" → **실행 중인 시스템이 문서보다 위**. "어떻게 동작해야 하나" → 공식 문서가 위 |
| 3. 설계 의도 | 만들어진 용법을 거스르는 우회인가. 그렇다면 그렇다고 말하고 정석 경로를 같이 제시 |
| 4. 보정된 확신 | `아마`, `~인 것 같다` 금지. **confirmed / hypothesis / unknown** 중 하나를 명시 |
| 5. 최적성 | 눈앞의 문제만 보지 않았나. 트레이드오프를 말했나 |

**모호함은 금지, 불확실은 필수.** "모르겠다"는 통과하고, 근거 없이 단정하면 실패합니다.

모든 기술적 답변 끝에 이 한 줄이 붙습니다:

```
Source: <URL 또는 명령> (<사다리 단계>, <버전/날짜>) | Certainty: confirmed | Verify: <확인 명령>
```

실행 중인 시스템과 문서가 어긋나면 **행위 주장에서는 시스템이 이기고, 그 차이 자체를 보고**합니다. 보통 그 차이가 진짜 발견입니다.

검색 경로는 `exa` → 내장 `WebSearch`/`WebFetch` 순으로 내려갑니다. **exa MCP가 없어도 동작하고**, 검색 경로가 전부 막혔을 때만 기억으로 답하되 그 사실이 footer에 `recall, unverified`로 남습니다. 조용한 폴백은 금지입니다.

**하드 스톱 하나** — 검증되지 않은 근거로 되돌릴 수 없는 작업(데이터 손실, 롤백 없는 프로덕션 변경)을 제안하려는 경우. 가설도 제안도 내지 않고, 근거를 세울 확인 절차만 내고 멈춥니다.

<details>
<summary>왜 만들었나</summary>

팀 리드의 리뷰 기준을 스킬로 옮긴 것입니다. 목적은 엄밀해 **보이는** 게 아니라,
틀린 답이 나갔을 때 **틀렸다는 게 먼저 보이는 것**입니다.
</details>

---

### `katalk` — 카톡

고객사 담당자에게 메신저로 **그대로 복사해서 보낼** 한 덩어리 메시지를 씁니다.
티켓 회신이나 첨부 문서(긴 글)는 대상이 아닙니다.

```
[관찰한 사실] + [되묻기 ~인가요??]
[이유 한 줄] + [해결책 한 줄 ~하시면 됩니다!]
```

- **2~3줄.** 줄 수는 늘리지 않는다
- **사실 먼저** — 우리가 실제로 확인한 것. 이번 세션에서 직접 본 것만
- **되묻기** — 단정하지 않는다. 고객 리소스는 고객이 더 잘 안다
- **해결책은 복붙 가능하게** — 리소스 이름, DNS, 설정값을 그대로

실제로 한 번에 원인이 확인된 메시지:

```
혹시 apply 출력에 aws_lb_target_group.traefik_alb 에러 나있나요?? 지금 NLB는 active인데 NLB→ALB 타겟그룹이 prod VPC에 안보여서요
dev 쪽에 같은 이름(traefik-alb-tg-443/80/8000)이 이미 있어서 이름 겹치면 DuplicateTargetGroupName 날 수 있어서 그때는 name에 ${local.env} 넣으시면 됩니다!
```

같은 내용을 해설로 쓰면 이렇게 됩니다 — 단정하고, 길고, 고객이 아는 걸 설명합니다:

```
검토해본 결과 타겟그룹 생성 단계에서 문제가 발생한 것으로 판단됩니다.
AWS 에서 타겟그룹 이름은 계정과 리전 단위로 유일해야 하는데 ...
```

초안을 주고 "검토"라고 하면 **파일을 고치지 않고** 🔴 사실 오류 / 🟡 오해 소지 / 🟢 오타 순으로만 짚습니다.

---

### `k8s-upgrade-skills` ↗

[HaeDalWang/k8s-upgrade-skills](https://github.com/HaeDalWang/k8s-upgrade-skills)

`recipe.md`에 클러스터 정보를 정의하면, 사전 검증 → 업그레이드 실행 → 사후 검증까지 phase-gated로 수행합니다.
게이트 판정은 LLM이 아니라 **스크립트**가 하고, LLM은 종료 코드를 재해석할 수 없습니다.

- 마이너 버전 **+1만** — 1.33 → 1.35 같은 스킵 거부
- Control Plane 먼저, Data Plane이 절대 앞서지 않음
- PDB를 force로 뚫지 않음
- 계획서 승인 게이트 — `승인`/`확인`/`시작` **정확히 그 단어만** 통과 ("진행해줘", "ㅇㅇ"는 승인이 아님)
- 어떤 단계든 예기치 않은 오류 → 중단하고 사용자 판단 대기

함께 들어있는 `helm-k8s-compat`은 업그레이드 전에 Helm 차트 ↔ K8s 버전 호환성을 사전 점검합니다.

> 무중단을 **보장**하지는 않습니다. 위험을 사전에 탐지(disruption-aware)할 뿐이고,
> 인프라 변경의 최종 책임은 실행자에게 있습니다.

---

## 함께 쓰기

`seonbi`와 `katalk`은 짝으로 씁니다.

고객에게 메시지를 보내기 전에 **그 메시지에 들어갈 사실이 이번 세션에서 확인된 것인지**를 `seonbi`가 먼저 거릅니다.
검증 안 된 추측을 고객에게 단정해서 보내는 게 실무에서 가장 비싼 실수라서요.

K8s 업그레이드 중에도 같습니다 — 게이트가 멈추면 `seonbi`로 원인을 확인하고, `katalk`으로 고객에게 상황을 알립니다.

---

## 레포 구조

```
seungdo-skills/
├── .claude-plugin/
│   ├── plugin.json        # 이 레포가 하나의 플러그인
│   └── marketplace.json   # 마켓플레이스 — 외부 레포 스킬도 여기 등록
├── skills/
│   ├── seonbi/SKILL.md
│   └── katalk/SKILL.md
└── README.md
```

매니페스트를 고쳤다면 커밋 전에:

```bash
claude plugin validate . --strict
claude plugin validate skills --strict
```

---

## 라이선스

[PolyForm Noncommercial License 1.0.0](LICENSE)

개인·학습·사내 업무 등 **비상업 목적**은 자유롭게 쓰세요. 상업적 이용은 [저작자](https://github.com/HaeDalWang)에게 문의해 주세요.
