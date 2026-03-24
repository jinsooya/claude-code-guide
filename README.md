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

Claude Code는 실행할 때마다 `CLAUDE.md` 파일을 읽어 프로젝트 맥락을 기억한다. 팀원들과 공유하는 일종의 "AI를 위한 프로젝트 설명서"라고 생각하면 된다.

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

## 4. IDE 연동 — `/ide`

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

## 5. 주요 명령어 정리

| 명령어 | 설명 |
|--------|------|
| `/init` | 코드베이스 분석 후 CLAUDE.md 자동 생성 |
| `/ide` | IDE 연동 (VS Code 계열 / JetBrains 계열 지원) |
| `/help` | 전체 명령어 목록 확인 |
| `/clear` | 대화 기록 초기화 — 새 작업 시작 시 권장 |
| `/compact` | 요약을 유지하면서 컨텍스트 정리 |
| `Esc` | 진행 중인 작업 즉시 중단 |
| `#` | 메모리에 즉시 추가 |

---

## 6. Git 통합

변경 사항을 커밋할 때 Claude Code에게 맡길 수 있다. 적절한 git 명령어와 설명적인 커밋 메시지를 자동으로 작성해준다:

```
> '변경한 파일들 추가하고 커밋 메시지 작성해서 커밋해주세요.'
```

---

## 7. 권한 처리

Claude Code는 파일 수정이나 명령어 실행 전에 매번 권한을 요청한다. 익숙해지면 "항상 허용" 옵션을 선택해 자동 승인할 수 있다.

---

## 8. 활용 흐름 요약

```
1. 프로젝트 폴더로 이동 (또는 새 폴더 생성)
2. claude 실행
3. /init -> CLAUDE.md 생성
4. /ide -> IDE 연동
5. 자연어로 질문하며 코드베이스 파악
6. 기능 추가 / 수정 요청
7. 변경사항 확인 후 git 커밋
```
