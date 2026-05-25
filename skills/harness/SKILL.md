---
name: harness
description: Scaffolds customized agent personas and standards-compliant skill directories based on user request or project type.
allowed-tools:
  - write_to_file
  - run_command
  - list_dir
---

# Project Harness Scaffolder (/harness)

이 스킬은 사용자가 구축하고자 하는 어떠한 프로젝트 형태나 비전에 맞춰, 특화된 **에이전트 페르소나(Personas)**팀과 그들이 사용할 **공식 규격 커스텀 스킬(Skills)** 구조를 일괄 설계하고 프로젝트 폴더 하위에 스캐폴딩(자동 뼈대 생성)합니다.

## When to use this skill
- 새로운 프로젝트를 시작할 때, 그에 특화된 다중 에이전트 협업 팀을 구축하고자 할 때.
- 사용자가 "React 테스트 자동화 팀을 구성해줘" 혹은 "데이터 분석 파이프라인 전용 에이전트와 스킬들을 빌드해줘"라고 도메인별 요청을 할 때.
- 일일이 손으로 하부 폴더를 만들고 `SKILL.md`를 채워 넣는 반복 작업을 자동화하고 싶을 때.

## Instructions
이 스킬이 가동되면 에이전트는 아래 규칙에 맞춰 정교한 설계 및 파일 생성을 처리해야 합니다.

### 1. 사용자 요구사항 도출
- 프로젝트의 도메인(웹 프론트엔드, AI 분석, 서버 인프라 등)과 사용 기술 스택을 파악합니다.
- 이에 맞는 전문적인 **에이전트 역할군 3종**(예: PM/Writer, Developer, QA/Expert)을 도출합니다.
- 에이전트들이 협업 과정에서 유용하게 쓸 수 있는 **핵심 스킬 3~4종**의 이름과 명세를 기획합니다.

### 2. 프로젝트 폴더 하위 스캐폴딩 규칙
1. **통합 에이전트 가이드라인 (`/AGENTS.md`)**:
   - 프로젝트 루트 디렉토리에 생성합니다.
   - 프로젝트의 비전과 3종의 에이전트 역할 명세, 사용 스택, 행동 강령 및 기술 표준을 기술합니다.
2. **개별 에이전트 상세 명세 (`.agents/agents/<AgentName>.md`)**:
   - `.agents/agents/` 디렉토리에 개별 마크다운 파일로 생성합니다.
   - 상단에 YAML Frontmatter(`name`, `role`, `description`)를 포함하고 바디에 구체적인 행동 가이드를 작성합니다.
3. **표준 규격 스킬들 (`.agents/skills/<skill-folder>/SKILL.md`)**:
   - 스킬 이름은 케밥 케이스(kebab-case)로 지정하고 개별 폴더를 생성합니다.
   - 각 폴더 내에 반드시 **`SKILL.md`** 파일명으로 생성합니다.
   - 파일 상단에 필수 **YAML Frontmatter**를 채워 넣습니다:
     ```yaml
     ---
     name: <kebab-case-name>
     description: <3인칭 시점의 기능 설명 및 트리거 조건 포함>
     allowed-tools:
       - <허용할 도구 1>
       - <허용할 도구 2>
     ---
     ```
   - 바디에는 반드시 아래의 3대 필수 헤더를 포함시킵니다:
     - `## When to use this skill` (언제 호출해야 하는지 상황과 트리거 제시)
     - `## Instructions` (동작에 필요한 세부 프롬프트 지침)
     - `## Workflow` (에이전트가 실행해야 할 순차적 수행 절차)

## Workflow
1. 사용자로부터 어떠한 주제의 에이전트/스킬 팀을 구축하고 싶은지 키워드나 명세를 수집합니다.
2. 프로젝트 하위에 `.agents/` 및 필요 하부 디렉토리(`agents/`, `skills/`)를 구조화하여 생성합니다.
3. `/AGENTS.md`, `.agents/agents/*.md`, `.agents/skills/<folder>/SKILL.md` 순서로 뼈대 콘텐츠를 자동 빌드해 채워 넣습니다.
4. 생성이 완료되면 구조 맵(Tree)을 시각적으로 출력하고, 사용자가 `agy inspect` 명령어로 검증할 수 있도록 친절하게 안내합니다.
