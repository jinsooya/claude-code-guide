# Claude Code 입문 가이드

## Claude Code란?
터미널에서 실행하는 AI 코딩 에이전트다. 단순한 코드 자동완성이 아니라, 코드베이스 전체를 파악하고 파일을 직접 읽고 수정하며 명령어도 실행한다. 새 프로젝트를 처음부터 만들 때도, 기존 코드베이스를 파악할 때도 모두 활용할 수 있다.

---

## 1. 설치

### 사전 조건
- Claude Pro, Max, Teams, Enterprise, 또는 Console(API) 계정 필요
- 무료 플랜은 Claude Code를 사용할 수 없다

### 네이티브 인스톨러 (권장)
Node.js 등 별도 의존성 없이 설치되며, 백그라운드에서 자동 업데이트된다.

**macOS / Linux**
```bash
curl -fsSL https://claude.ai/install.sh | bash
```

**Windows**
```powershell
# PowerShell (권장)
winget install Anthropic.ClaudeCode
```
> Windows는 Git for Windows를 먼저 설치해야 한다.

### 기존에 npm으로 설치한 경우 (마이그레이션)
```bash
# 네이티브 바이너리 설치
curl -fsSL https://claude.ai/install.sh | bash

# 기존 npm 버전 제거
npm uninstall -g @anthropic-ai/claude-code
```

설치 후 `claude`를 처음 실행하면 브라우저가 열리며 Anthropic 계정 인증을 요구한다. 안내에 따라 로그인하면 된다.

---

## 2. 시작하기

> 📌 **이 가이드에 대해:** 터미널(CLI) 기준으로 작성되어 있다. 데스크톱 앱에서는 일부 기능이 다르게 동작하거나 지원되지 않을 수 있다. 터미널과 데스크톱의 차이는 섹션 17에서 정리한다.

### 새 프로젝트를 시작할 때
빈 폴더를 만들고 그 안에서 `claude`를 실행한 뒤, 만들고 싶은 것을 설명하면 된다:

```bash
mkdir my-project
cd my-project
claude
```
```
> '간단한 할 일 관리 웹앱을 만들어주세요. Python Flask 백엔드에 HTML 프론트엔드로요.'
```

### 기존 프로젝트를 파악할 때
프로젝트 루트 디렉토리로 이동한 뒤 실행하면, Claude가 코드베이스 전체를 분석할 수 있다:

```bash
cd my-existing-project
claude
```
```
> '이 프로젝트 전체 구조를 설명해주세요.'
> '유저 요청이 프론트에서 백엔드까지 어떻게 흐르는지 추적해주세요.'
> '이 앱을 어떻게 실행하나요?'
```

> 💡 **팁:** 코드를 짜달라고 하기 전에, 먼저 구조를 설명해달라고 하면 훨씬 좋은 결과를 얻을 수 있다. Claude Code는 코드 작성 도구이기 전에 뛰어난 설명 도구다.

---

## 3. CLAUDE.md — 프로젝트 메모리

Claude Code는 실행할 때마다 `CLAUDE.md` 파일을 읽어 프로젝트 맥락을 기억한다. 팀원들과 공유하는 일종의 'AI를 위한 프로젝트 설명서'라고 생각하면 된다.

### `/init` 으로 자동 생성하기

Claude Code 안에서 아래 명령어를 실행하면 코드베이스를 분석해 자동으로 작성해준다:

```
/init
```

### 파일 종류 3가지

| 파일 | 위치 | 용도 | Git 포함 |
|------|------|------|----------|
| `CLAUDE.md` | 프로젝트 루트 | 팀 전체 공유 설정 | ✅ |
| `CLAUDE.local.md` | 프로젝트 루트 | 나만의 개인 설정 | ❌ gitignore |
| `~/.claude/CLAUDE.md` | 홈 디렉토리 | 모든 프로젝트에 전역 적용 | ❌ |

### 메모리에 빠르게 추가하기

대화 중 `#`으로 시작하면 즉시 메모리에 추가할 수 있다. 저장 위치(프로젝트 / 로컬 / 전역)를 선택할 수 있다:

```
# UV를 사용해서 서버를 실행해주세요. pip는 직접 사용하지 마세요.
```

---

## 4. 커스텀 명령어

`.claude/commands/` 폴더에 마크다운 파일을 만들면 슬래시 명령어로 등록된다. `$ARGUMENTS` 변수로 인자도 받을 수 있다. 자주 쓰는 프롬프트를 명령어로 만들어두면 매번 긴 프롬프트를 입력할 필요가 없다.

```
.claude/
  commands/
    summarize.md      ->  /summarize 로 사용 가능
    code-review.md    ->  /code-review 로 사용 가능
    write-tests.md    ->  /write-tests 로 사용 가능
```

CLAUDE.md와 다르게 매 대화에 자동으로 포함되지 않고, 명시적으로 호출할 때만 사용된다는 점이 핵심 차이다. `.claude/commands/` 폴더는 git에 포함되므로 팀원과 공유된다.

### 만드는 방법

먼저 폴더를 만든다:

```bash
mkdir -p .claude/commands
```

그 안에 명령어 이름으로 마크다운 파일을 만들고 내용을 작성한다:

`summarize.md`
```
$ARGUMENTS 파일의 코드를 읽고 무슨 역할을 하는지 한국어로 쉽게 설명해주세요.
함수별로 정리하고 전체 흐름도 요약해주세요.
```

`code-review.md`
```
$ARGUMENTS 파일을 리뷰하고 개선할 점을 한국어로 정리해주세요.
버그 가능성, 가독성, 성능 순서로 작성해주세요.
```

`write-tests.md`
```
$ARGUMENTS 파일에 대한 테스트 코드를 작성하고 실행해주세요.
어떤 케이스를 테스트하는지 간단히 설명도 달아주세요.
```

### 사용 방법

```
/summarize @src/app.py
/code-review @src/app.py
/write-tests @src/app.py
```

`$ARGUMENTS` 자리에 전달한 값이 자동으로 채워진다. 예를 들어 `/summarize @src/app.py`를 실행하면 내부적으로 이렇게 동작한다:

```
@src/app.py 파일의 코드를 읽고 무슨 역할을 하는지 한국어로 쉽게 설명해주세요.
함수별로 정리하고 전체 흐름도 요약해주세요.
```

---

## 5. 파일 참조 — `@` 태그

Claude Code는 컨텍스트로 제공된 정보만큼만 정확하게 동작한다. 변경이 필요한 파일을 미리 알고 있다면, `@`로 직접 참조해 주면 훨씬 효율적으로 작동한다.

```
> '@src/app.py 이 파일에서 에러 처리 로직을 개선해주세요.'
> '@src/ 이 폴더 구조를 설명해주세요.'
```

파일명 입력 시 Tab으로 자동완성할 수 있다. 폴더를 참조하면 그 안의 파일들을 모두 컨텍스트로 포함할 수 있다.

---

## 6. 기능 추가 및 변경 — Plan Mode

규모가 큰 변경 작업을 할 때는 코드를 바로 작성하게 하지 말고, 먼저 계획을 수립하게 하는 것을 권장한다.

### Plan Mode 활성화
`Shift + Tab`을 **두 번** 누르면 Plan Mode가 켜진다. 이 상태에서는 Claude가 코드를 직접 수정하지 않고 변경 계획만 제시한다.

> 💡 **참고:** `Shift + Tab`을 **한 번** 누르면 Auto-accept Edits가 켜진다. 파일 변경마다 매번 권한을 요청하지 않고 자동으로 승인한다. 권한 처리 방식 전체는 섹션 15에서 정리한다.

```
> '@src/index.html @src/app.py 소스 인용 링크를 렌더링하는 인터페이스를 만들어주세요.'
```

계획을 검토한 뒤 세 가지 중 하나를 선택할 수 있다:
- **승인** -> 계획대로 코드 작성 시작
- **피드백** -> 계획 수정 요청
- **Esc** -> 작업 중단

> 💡 **팁:** 큰 변경 작업일수록 Plan Mode로 먼저 계획을 확인하는 습관을 들이면 예상치 못한 수정을 방지할 수 있다.

### 프롬프트에서 줄바꿈
프롬프트 입력 중 `\` + `Enter`를 누르면 줄바꿈할 수 있다. 컨텍스트를 여러 줄에 걸쳐 추가할 때 유용하다.

---

## 7. 복잡한 작업을 위한 고급 기능

### think 키워드 — Extended Thinking 활성화

프롬프트에 `think`, `think hard`, `think a lot` 같은 표현을 넣으면 Claude의 Extended Thinking이 활성화된다. 더 많은 토큰을 사고 과정에 할당해 복잡한 문제를 더 깊이 분석한다. 디버깅이나 설계처럼 판단이 중요한 작업에 특히 유용하다.

```
> '@src/rag_system.py @src/search_tools.py 쿼리 처리 중 오류가 발생하고 있어요.
  think hard해서 원인을 찾고 테스트를 먼저 작성해주세요.'
```

> ⚠️ **주의:** Extended Thinking은 토큰 사용량과 응답 시간이 늘어난다. 간단한 작업에는 사용하지 않는 것이 좋다.

### 서브에이전트 — 병렬 탐색

복잡한 리팩토링이나 설계처럼 최적의 방향을 결정하기 어려울 때, 여러 서브에이전트를 병렬로 실행해 각각 다른 접근 방식을 동시에 탐색하게 할 수 있다. 내부적으로 `task` 도구를 사용한다.

```
> '코드를 직접 수정하지 말고, 두 개의 서브에이전트를 실행해서
  각각 다른 리팩토링 방안을 브레인스토밍해주세요.'
```

각 서브에이전트가 독립적으로 계획을 수립한 뒤 결과를 보고하면, 그중 마음에 드는 방안을 선택해 Plan Mode로 구체화할 수 있다.

### 복잡한 프롬프트는 md 파일로 분리

요구사항이 길고 복잡할 때는 프롬프트를 별도 `.md` 파일로 작성한 뒤 `@`로 참조하는 패턴을 활용한다. 현재 동작, 원하는 동작, 예시 플로우, 요구사항을 구조적으로 정리해서 전달할 수 있다.

```bash
# 예시: backend-refactor.md 파일 작성 후 참조
> '@backend-refactor.md 이 내용대로 리팩토링 계획을 세워주세요.'
```

### Hooks — 도구 실행 생명주기에 코드 삽입

Claude Code가 도구를 실행하는 각 시점에 셸 명령어를 자동으로 실행할 수 있다. `/hooks` 명령어로 설정하거나 `.claude/settings.local.json` 파일을 직접 편집해서 구성할 수 있다.

지원 이벤트:

| 이벤트 | 시점 |
|--------|------|
| `PreToolUse` | 도구 실행 전 (실행 자체를 막을 수도 있음) |
| `PostToolUse` | 도구 실행 후 |
| `Notification` | 알림 발생 시 |
| `Stop` | 작업 종료 시 |

활용 예시 — 파일 수정 후 자동으로 린터 실행:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write",
        "command": "npm run lint"
      }
    ]
  }
}
```

> 💡 **가벼운 활용 예시:** 음성 알림 명령어를 연결하면 웹 요청처럼 오래 걸리는 작업이 끝날 때 소리로 알림을 받을 수 있다. `WebFetch` 도구 실행 후 아래 명령어를 걸어두면 작업 완료 시 알림이 울린다. 장시간 작업을 실행해놓고 다른 일을 할 때 유용하다.

| OS | 명령어 |
|----|--------|
| macOS | `say 'all done'` |
| Windows | `powershell -c "Add-Type -AssemblyName System.Speech; (New-Object System.Speech.Synthesis.SpeechSynthesizer).Speak('all done')"` |
| Linux | `espeak 'all done'` (espeak 설치 필요) |

> ⚠️ **주의:** Hooks는 임의의 셸 명령어를 실행하므로 신뢰할 수 있는 환경에서만 사용해야 한다.

---

## 8. 이미지 / 스크린샷 입력

UI 변경이 필요할 때 스크린샷을 붙여넣으면 Claude Code가 시각적으로 분석해 수정 사항을 직접 파악한다. 텍스트로 설명하기 어려운 레이아웃 문제나 스타일 변경에 특히 효과적이다.

```
> [스크린샷 붙여넣기]
  '링크 색상이 배경과 구분이 안 돼서 읽기 어려워요. 더 보기 좋게 바꿔주세요.'
```

---

## 9. IDE 연동 — `/ide`

터미널과 에디터를 오가는 번거로움을 없애준다. 연동하면 현재 열려 있는 파일의 컨텍스트가 Claude와 자동으로 공유되고, 코드 변경사항을 에디터 안에서 바로 diff로 확인할 수 있다.

지원 IDE:

| 계열 | 지원 에디터 |
|------|------------|
| VS Code 계열 | VS Code, Cursor, Windsurf |
| JetBrains 계열 | IntelliJ, PyCharm, WebStorm 등 |

VS Code 계열은 통합 터미널에서 `claude`를 실행하면 자동으로 연동을 제안한다. JetBrains는 마켓플레이스에서 Claude Code 플러그인을 먼저 설치해야 한다.

외부 터미널을 사용하는 경우 `/ide` 명령어로 수동 연결한다:

```
/ide
```

---

## 10. MCP 서버 연동

MCP(Model Context Protocol)는 Claude Code에 외부 도구와 데이터 소스를 연결하는 프로토콜이다. MCP 서버를 추가하면 Claude Code가 직접 브라우저를 열거나, 데이터베이스를 조회하는 등 기본 기능 이상의 작업을 수행할 수 있다.

### MCP 서버 추가

MCP 서버는 설치 범위를 지정할 수 있다. 범위를 지정하지 않으면 현재 프로젝트에만 적용된다:

| 범위 | 명령어 | 적용 범위 | 저장 위치 |
|------|--------|----------|----------|
| 기본값 (로컬) | `claude mcp add` | 현재 프로젝트만 | `.claude/settings.local.json` |
| `project` | `claude mcp add --scope project` | 팀 전체 공유 | `.mcp.json` |
| `user` | `claude mcp add --scope user` | 내 모든 프로젝트 | `~/.claude.json` |

Playwright처럼 여러 프로젝트에서 자주 쓰는 서버는 `--scope user`로 한 번만 설치하면 된다:

```bash
# 모든 프로젝트에서 사용 가능하게 설치 (한 번만)
claude mcp add --scope user playwright npx @playwright/mcp@latest
claude mcp add --scope user --transport http figma https://mcp.figma.com/mcp

# 현재 프로젝트에서만 사용 (기본값)
claude mcp add playwright npx @playwright/mcp@latest
```

### 연결된 MCP 서버 확인

Claude Code 안에서 아래 명령어로 연결된 서버와 사용 가능한 도구 목록을 확인할 수 있다:

```
/mcp
```

### 활용 예시 — Playwright

Playwright MCP를 연결하면 Claude Code가 스스로 브라우저를 열어 스크린샷을 찍고, 결과를 분석해 UI를 수정하는 작업을 자동으로 반복할 수 있다. 수동으로 스크린샷을 찍어 붙여넣는 과정 없이 시각적 피드백 루프를 자동화할 수 있다.

### 활용 예시 — Figma

Figma MCP를 연결하면 Figma 목업 링크를 붙여넣는 것만으로 Claude Code가 디자인을 분석해 코드를 생성한다. 설치 후 `/mcp`에서 Figma 서버를 선택해 OAuth 인증을 완료하면 된다.

```
> 'https://www.figma.com/design/xxxx 이 목업을 Next.js 컴포넌트로 구현해주세요.'
```

Playwright MCP와 함께 사용하면 생성된 코드를 브라우저에서 바로 확인하고 목업과 비교하는 작업까지 자동화할 수 있다.

---

## 11. 컨텍스트 관리 — `/clear` vs `/compact`

Claude Code는 대화가 길어질수록 컨텍스트 윈도우가 쌓여 응답 품질이 떨어지거나 이전 내용과 혼선이 생길 수 있다. 작업 전환 시 아래 두 명령어로 컨텍스트를 정리하는 것을 권장한다.

| 명령어 | 동작 | 언제 쓸지 |
|--------|------|----------|
| `/clear` | 대화 기록 완전 초기화 | 전혀 다른 새 작업을 시작할 때 |
| `/compact` | 요약을 유지하면서 기록 정리 | 같은 작업을 이어가되 컨텍스트가 너무 길어졌을 때 |

> 💡 **팁:** 기능 하나를 완성한 뒤 새 기능을 시작할 때는 `/clear`로 깔끔하게 초기화하는 것이 좋다. 이전 작업의 맥락이 새 작업에 혼선을 줄 수 있기 때문이다.

### 세션 재개 — `--continue` / `--resume`

Claude Code는 모든 대화를 자동으로 저장한다. 터미널이 꺼지거나 작업이 중단된 경우에도 이전 세션을 그대로 이어받을 수 있다.

| 명령어 | 단축키 | 동작 |
|--------|--------|------|
| `claude --continue` | `claude -c` | 가장 최근 세션 즉시 재개 |
| `claude --resume` | `claude -r` | 세션 목록 피커에서 선택해서 재개 |
| `claude --resume [세션명]` | `claude -r [세션명]` | 특정 세션 이름으로 바로 재개 |

세션 내부에서 `/resume`을 실행하면 Claude Code를 종료하지 않고도 다른 세션으로 전환할 수 있다.

```bash
# 터미널이 꺼진 후 바로 이전 작업으로 복귀
claude -c

# 특정 세션 이름으로 재개
claude -r 'auth-refactor'
```

---

## 12. Git 통합

변경 사항을 커밋할 때 Claude Code에게 맡길 수 있다. 적절한 git 명령어와 설명적인 커밋 메시지를 자동으로 작성해준다:

```
> '변경한 파일들 추가하고 커밋 메시지 작성해서 커밋해주세요.'
```

---

## 13. GitHub 연동

Claude Code를 GitHub에 연동하면 터미널 밖에서도 PR 리뷰와 이슈 처리를 자동화할 수 있다.

### 설치

```
/install-github-app
```

브라우저가 열리며 GitHub 앱 설치 화면으로 이동한다. 설치 후 `.github/workflows/`에 아래 두 가지 워크플로우 YAML 파일이 자동 생성된다:

| 파일 | 기능 |
|------|------|
| `claude-code-review.yml` | PR 생성 시 자동 코드 리뷰 |
| `claude-code-fix.yml` | 이슈에서 `@claude` 태그 시 자동 수정 및 PR 생성 |

프롬프트는 YAML 파일을 직접 수정해서 커스터마이징할 수 있다.

### 사용 방법

**PR 자동 리뷰:** PR을 생성하면 Claude가 자동으로 코드를 분석해 리뷰 코멘트를 남긴다.

**이슈 자동 처리:** 이슈에 `@claude`를 태그하면 Claude가 코드를 수정하고 PR을 자동으로 생성한다.

```
# 이슈 코멘트 예시
@claude 헤더를 이전 디자인으로 되돌려주세요.
서브헤더와 구분선도 제거해주세요.
```

> 💡 **팁:** Claude가 작성한 PR도 자동으로 코드 리뷰가 실행된다. Claude가 스스로 작성한 코드를 다른 Claude가 검토하는 이중 확인 구조가 만들어진다.

---

## 14. Git Worktree로 병렬 작업

Claude Code는 여러 터미널에서 동시에 실행할 수 있다. 단, 같은 파일을 여러 인스턴스가 동시에 수정하면 덮어쓰기 문제가 생긴다. Git worktree를 사용하면 코드베이스의 독립적인 복사본을 만들어 각각 다른 인스턴스에서 작업할 수 있다.

### 방법 1 — Claude Code 내장 플래그 (`-w`)

`claude -w [이름]` 플래그를 사용하면 worktree 생성과 Claude Code 실행을 한 번에 처리한다. worktree는 `.claude/worktrees/` 폴더 아래에 생성된다:

```bash
claude -w ui_feature
claude -w testing_feature
```

### 방법 2 — Git 명령어로 직접 생성

```bash
# worktree 생성
git worktree add .trees/ui_feature
git worktree add .trees/testing_feature

# 각 폴더에서 독립적으로 Claude Code 실행
cd .trees/ui_feature && claude
cd .trees/testing_feature && claude
```

작업 완료 후 main 브랜치에서 머지를 Claude Code에게 맡길 수 있다:

```
> '.trees 폴더 안의 모든 worktree를 main에 머지하고
  충돌이 있으면 해결해주세요.'
```

> 💡 **팁:** Git에 익숙하지 않다면 worktree보다 먼저 기본 브랜치 워크플로우를 익히는 것을 권장한다.

---

## 15. 권한 처리

Claude Code는 파일 수정이나 명령어 실행 전에 매번 권한을 요청한다. 상황과 신뢰도에 따라 아래 네 가지 방식 중 하나를 선택할 수 있다:

| 방식 | 활성화 방법 | 설명 | 위험도 |
|------|------------|------|--------|
| 기본 모드 | 기본값 | 매번 권한 요청 | 낮음 |
| Auto-accept Edits | `Shift + Tab` 1회 | 파일 변경 자동 승인 | 낮음~중간 |
| `--enable-auto-mode` | CLI 플래그 | AI가 위험도 판단 후 선택적 승인 | 중간 |
| `--dangerously-skip-permissions` | CLI 플래그 | 모든 권한 무조건 승인 | 높음 |

처음에는 기본 모드로 시작하고, Claude Code에 익숙해지면 Auto-accept Edits를 활용하는 것이 일반적인 흐름이다.

### Auto Mode (리서치 프리뷰)

2026년 3월 출시된 기능으로, Claude가 각 작업의 위험도를 스스로 판단해 저위험 작업은 자동 승인하고 고위험 작업만 사람에게 확인을 요청한다. 기존 `--dangerously-skip-permissions`보다 안전한 중간 단계 옵션이다.

```bash
claude --enable-auto-mode
```

> ⚠️ **주의:** 아직 리서치 프리뷰 단계다. 토큰 사용량과 비용이 다소 증가하며, 격리된 환경(샌드박스, 컨테이너)에서 사용하길 권장한다.

---

## 16. 주요 명령어 및 단축키 정리

| 명령어 / 단축키 | 설명 | 환경 |
|----------------|------|------|
| `/init` | 코드베이스 분석 후 CLAUDE.md 자동 생성 | 터미널 + 데스크톱 |
| `/ide` | IDE 연동 (VS Code 계열 / JetBrains 계열 지원) | 터미널 + 데스크톱 |
| `/mcp` | 연결된 MCP 서버 및 도구 목록 확인 | 터미널 + 데스크톱 |
| `/hooks` | 도구 실행 생명주기 이벤트에 셸 명령어 설정 | 터미널 + 데스크톱 |
| `/install-github-app` | GitHub 앱 설치 — PR 리뷰 및 이슈 자동 처리 연동 | 터미널 전용 |
| `/help` | 전체 명령어 목록 확인 | 터미널 + 데스크톱 |
| `/clear` | 대화 기록 초기화 — 새 작업 시작 시 권장 | 터미널 + 데스크톱 |
| `/compact` | 요약을 유지하면서 컨텍스트 정리 | 터미널 + 데스크톱 |
| `/resume` | 세션 내부에서 다른 세션으로 전환 | 터미널 + 데스크톱 |
| `/rename` | 현재 세션 이름 지정 | 터미널 + 데스크톱 |
| `claude -c` | 가장 최근 세션 즉시 재개 | 터미널 전용 |
| `claude -r` | 세션 목록 피커에서 선택해서 재개 | 터미널 전용 |
| `claude -w [이름]` | Git worktree 생성 후 해당 환경에서 Claude 실행 | 터미널 전용 |
| `claude --enable-auto-mode` | Auto Mode 활성화 (AI가 위험도 판단 후 선택적 승인) | 터미널 전용 |
| `claude mcp add [서버명] [명령어]` | MCP 서버 추가 | 터미널 전용 |
| `Shift + Tab` (2회) | Plan Mode 활성화 | 터미널 + 데스크톱 |
| `Shift + Tab` (1회) | Auto-accept Edits 활성화 | 터미널 + 데스크톱 |
| `Esc` | 진행 중인 작업 즉시 중단 | 터미널 + 데스크톱 |
| `#` | 메모리에 즉시 추가 | 터미널 + 데스크톱 |
| `\` + `Enter` | 프롬프트 입력 중 줄바꿈 | 터미널 + 데스크톱 |
| `@파일명` | 특정 파일 또는 폴더를 컨텍스트로 참조 | 터미널 + 데스크톱 |

---

## 17. 터미널 vs 데스크톱 비교

Claude Code는 터미널(CLI)과 데스크톱 앱 두 가지 환경에서 사용할 수 있다. 같은 Claude Code 엔진을 사용하지만 경험과 지원 기능이 다르다.

| 기능 | 터미널 (CLI) | 데스크톱 앱 |
|------|------------|------------|
| 인터페이스 | 텍스트 기반 | GUI (시각적) |
| 코드 변경 확인 | 텍스트 출력 | 실시간 시각적 diff 뷰 |
| 세션 관리 | `-c`, `-r` 플래그 | UI에서 클릭으로 관리 |
| 병렬 세션 | 터미널 탭 여러 개 | 사이드바에서 통합 관리 |
| 실행 환경 | 로컬만 | 로컬 / 클라우드(Remote) / SSH 원격 서버 선택 가능 |
| CLI 플래그 | ✅ 전부 사용 가능 | ❌ (`-c`, `-r`, `--enable-auto-mode` 등 불가) |
| 자동화 / 스크립트 | ✅ 파이프, 배치 처리 가능 | ❌ |
| 장시간 야간 실행 | ✅ 루프 설정으로 가능 | ❌ 짧은 세션 위주 |
| 컴퓨터 직접 제어 | ❌ | ✅ 화면 녹화 + 접근성 권한으로 앱 조작 가능 |
| 진입 장벽 | 터미널 지식 필요 | 낮음 |

### 어떤 환경을 선택할까?

- **터미널** -> 자동화, 스크립트, 세밀한 제어가 필요한 개발자. 장시간 자율 실행이 필요할 때.
- **데스크톱** -> 시각적 확인을 선호하거나, 터미널이 낯선 사용자. 원격 서버나 클라우드 환경에서 작업할 때.

---

## 18. 활용 흐름 요약

```
1.  프로젝트 폴더로 이동 (또는 새 폴더 생성)
2.  claude 실행
3.  /init -> CLAUDE.md 생성
4.  /ide -> IDE 연동
5.  필요한 MCP 서버 설치 (--scope user로 한 번만)
6.  자연어로 질문하며 코드베이스 파악
7.  @태그로 관련 파일 참조
8.  Plan Mode로 변경 계획 수립 및 검토
9.  승인 후 코드 작성 (필요시 Auto-accept Edits 활성화)
10. 스크린샷으로 UI 피드백
11. 자주 쓰는 프롬프트는 커스텀 명령어로 등록
12. 변경사항 확인 후 git 커밋 및 push
13. GitHub 연동 시 PR 자동 리뷰 확인
14. 작업 중단 시 claude -c 로 세션 재개
15. 대규모 병렬 작업 시 Git Worktree 활용
```
