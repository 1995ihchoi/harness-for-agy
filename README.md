# harness-for-agy

> Google **Antigravity CLI(`agy`)** 를 위한 하네스(Harness) 스킬.
> 도메인에 특화된 다중 에이전트 팀과 그들이 사용할 커스텀 스킬 구조를 한 번에 스캐폴딩합니다.

이 프로젝트는 [`revfactory/harness`](https://github.com/revfactory/harness)(Claude Code 용 메타 스킬)를 **Antigravity CLI(`agy`)** 환경에 맞게 재구성한 포팅입니다. Claude Code 의 `~/.claude/skills/` 규약 대신, `agy` 가 인식하는 스킬 디렉토리 규약(`SKILL.md` + YAML Frontmatter)을 따르도록 다시 작성했습니다.

---

## 무엇을 하는 스킬인가

`/harness` 한 줄로 다음 산출물을 자동 생성합니다.

1. **프로젝트 비전 & 행동 강령** — 루트의 `AGENTS.md`
2. **전문 에이전트 페르소나 3종** — `.agents/agents/<AgentName>.md`
3. **에이전트가 협업할 때 사용할 커스텀 스킬 3~4종** — `.agents/skills/<skill-folder>/SKILL.md`

즉, "React 테스트 자동화 팀 만들어줘", "데이터 분석 파이프라인 전용 에이전트 짜줘" 같은 한 문장 요청을, 곧바로 작업 가능한 폴더 트리로 변환합니다.

---

## 설치

### 1) `agy` CLI 설치

```bash
# Google Antigravity CLI 가 이미 설치되어 있어야 합니다.
agy --version
```

설치되어 있지 않다면 [Antigravity 공식 사이트](https://antigravity.google/cli)의 안내를 따르세요.

### 2) 스킬 배치

`agy` 가 인식하는 사용자 스킬 경로(예: `~/.agy/skills/`) 또는 프로젝트 로컬 스킬 경로(`./.agents/skills/`)에 `skills/harness/` 디렉토리를 그대로 복사합니다.

```bash
git clone https://github.com/revfactory/harness-for-agy.git
cp -R harness-for-agy/skills/harness ~/.agy/skills/harness
```

또는 프로젝트 단위로 두려면:

```bash
mkdir -p ./.agents/skills
cp -R harness-for-agy/skills/harness ./.agents/skills/harness
```

설치 후 `agy` 세션에서 `/harness` 가 슬래시 커맨드로 노출됩니다.

---

## 사용법

```text
$ agy
> /harness React + Vitest 테스트 자동화 팀을 구성해줘
```

스킬이 활성화되면 에이전트는 아래 순서로 동작합니다.

1. **요구사항 도출** — 도메인, 기술 스택, 협업 형태를 질의/추론
2. **에이전트 역할 설계** — 일반적으로 `PM/Writer`, `Developer`, `QA/Expert` 3종
3. **스킬 명세 기획** — 3~4개의 보조 스킬 이름·트리거·workflow 설계
4. **파일 스캐폴딩**
   - `./AGENTS.md`
   - `./.agents/agents/<AgentName>.md`
   - `./.agents/skills/<skill-folder>/SKILL.md`
5. **검증 안내** — 생성 결과를 트리로 출력하고 `agy inspect` 로 확인하도록 안내

> 💡 같은 디렉토리에서 `/harness` 를 다시 호출하면 기존 구조를 점검·확장하는 모드로 동작시킬 수 있습니다.

---

## 스킬 구성 (Skill Anatomy)

이 레포는 단 하나의 메타 스킬을 정의하며, 그 스킬이 다른 스킬을 만들어내는 구조입니다.

### 디렉토리

```
harness-for-agy/
├── README.md
├── LICENSE
└── skills/
    └── harness/
        └── SKILL.md      # /harness 메타 스킬 본체
```

### `SKILL.md` Frontmatter 규약

`agy` 의 스킬은 모두 다음 형태의 YAML Frontmatter 로 시작합니다.

```yaml
---
name: <kebab-case-name>
description: <3인칭 시점의 기능 설명 + 트리거 조건>
allowed-tools:
  - <허용할 도구 1>
  - <허용할 도구 2>
---
```

본문에는 다음 3대 헤더가 **반드시** 포함되어야 합니다.

- `## When to use this skill` — 언제 호출되어야 하는가 (트리거 조건)
- `## Instructions` — 동작 시 따라야 할 세부 지침
- `## Workflow` — 순차적으로 실행할 단계

`/harness` 가 생성하는 모든 하위 스킬도 이 규약을 그대로 따릅니다. 즉, 이 메타 스킬은 동시에 **스킬 작성 표준의 참조 구현(reference implementation)** 역할을 합니다.

### 생성되는 산출물 구조

```
<your-project>/
├── AGENTS.md                              # 프로젝트 비전 + 에이전트 행동 강령
└── .agents/
    ├── agents/
    │   ├── ProductManager.md              # name/role/description Frontmatter
    │   ├── Developer.md
    │   └── QualityExpert.md
    └── skills/
        ├── <skill-1>/SKILL.md
        ├── <skill-2>/SKILL.md
        └── <skill-3>/SKILL.md
```

---

## `revfactory/harness` 와의 차이점

| 항목 | `revfactory/harness` (원본, Claude Code) | `harness-for-agy` (이 레포) |
| --- | --- | --- |
| 대상 CLI | Claude Code | Google Antigravity CLI (`agy`) |
| 설치 경로 | `~/.claude/skills/` | `~/.agy/skills/` 또는 `./.agents/skills/` |
| 스킬 본문 규약 | `SKILL.md` + Claude Code Frontmatter | `SKILL.md` + `agy` Frontmatter (`name`, `description`, `allowed-tools`) |
| 산출물 위치 | `.claude/agents`, `.claude/skills` | `.agents/agents`, `.agents/skills` |
| 슬래시 호출 | `/harness` (Claude Code) | `/harness` (`agy`) |

핵심 사상 — *"메타 스킬이 도메인별 에이전트 팀과 보조 스킬을 한꺼번에 만들어낸다"* — 은 동일하게 유지했습니다.

---

## 기여

이슈 및 PR 환영합니다. 새로운 도메인 템플릿(예: 모바일/게임/데이터 엔지니어링)을 제안하실 때는 `/harness` 가 생성한 결과 트리를 함께 첨부해 주세요.

---

## License

Apache License 2.0. 자세한 내용은 [`LICENSE`](./LICENSE) 파일을 참고하세요.

원본 메타 스킬의 아이디어와 구조에 대한 크레딧은 [`revfactory/harness`](https://github.com/revfactory/harness) 에 있습니다.
