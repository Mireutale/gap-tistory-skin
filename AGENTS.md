# Gap 티스토리 스킨 — AGENTS.md

## 프로젝트 개요

크림/차콜 팔레트 기반 에디토리얼 티스토리 스킨.
실제 파일은 `skin_data/` 폴더 안에 있고, 티스토리 업로드용 패키지는 `gap-tistory-skin.zip`.

## 주요 파일

| 파일 | 역할 |
|------|------|
| `skin_data/skin.html` | 티스토리 스킨 템플릿 (실제 배포용) |
| `skin_data/style.css` | 전체 스타일 |
| `skin_data/demo.html` | 로컬 브라우저 확인용 데모 |
| `skin_data/index.xml` | 스킨 메타데이터 |
| `DESIGN.md` | 디자인 토큰 및 컴포넌트 명세 |

## 프로젝트 작업 규칙

### 답변
- 항상 한국어로, 간결하게

### 브라우저 확인
- 내부적으로 스크린샷을 찍거나 자동 테스트하지 않는다
- UI 변경 후에는 항상 아래 명령으로 사용자가 직접 브라우저에서 확인하도록 안내한다:
  ```bash
  open skin_data/demo.html
  ```

### 배포 패키지 업데이트
- `skin_data/` 파일 수정 후 zip을 다시 생성해야 할 때:
  ```bash
  rm -f gap-tistory-skin.zip && cd skin_data && zip -r ../gap-tistory-skin.zip skin.html style.css index.xml preview256.jpg && cd ..
  ```

### PR & 릴리스 워크플로

기본 브랜치는 `main`과 `dev`만 유지한다. 모든 작업은 `dev`에서 분기한 작업 브랜치에서 진행하고, `main`에는 사용자의 명시적인 허락을 받은 뒤에만 `dev`를 반영한다.

1. **dev 최신화**
   ```bash
   git checkout dev
   git fetch origin
   git pull --ff-only origin dev
   ```
2. **작업 브랜치 생성** — 작업 단위에 맞는 브랜치명 사용
   ```bash
   git checkout -b <type>/<description>
   ```
3. **커밋** — Conventional Commits 형식 (`feat:`, `fix:`, `docs:` 등)
   ```bash
   git add <files> && git commit -m "<type>: <description>"
   ```
4. **dev 대상 PR 생성** — `main`이 아니라 `dev`를 base로 둔다
   ```bash
   gh pr create --base dev --title "..." --body "..."
   ```
5. **CI 해결** — lint.yml 등 실패한 워크플로 수정 후 재푸시
   ```bash
   gh pr checks <PR번호> --watch
   ```
6. **dev 병합** — CI 전체 통과 후 squash merge
   ```bash
   gh pr merge <PR번호> --squash --delete-branch
   ```
7. **main 반영 승인 요청** — `dev`를 `main`에 올리기 전 반드시 사용자에게 허락을 받는다. 허락 없이 `main` 대상 PR 생성, merge, release를 진행하지 않는다.
8. **승인 후 main 대상 PR 생성**
   ```bash
   gh pr create --base main --head dev --title "..." --body "..."
   ```
9. **승인 후 main 병합** — CI 전체 통과 후 squash merge
   ```bash
   gh pr merge <PR번호> --squash
   ```
10. **릴리스 재생성 또는 생성** — main 반영 후에만 실행한다
    ```bash
    gh release delete <tag> --yes
    git tag -d <tag> && git push origin :refs/tags/<tag>
    git tag <tag> && git push origin <tag>
    rm -f gap-tistory-skin.zip && cd skin_data && zip -r ../gap-tistory-skin.zip skin.html style.css index.xml preview256.jpg && cd ..
    gh release create <tag> gap-tistory-skin.zip --title "..." --notes "..."
    ```

## 공통 행동 지침

Behavioral guidelines to reduce common LLM coding mistakes. Merge with project-specific instructions as needed.

**Tradeoff:** These guidelines bias toward caution over speed. For trivial tasks, use judgment.

## 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:
- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them - don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

## 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.
- Every answer must korean

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.

## 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it - don't delete it.

When your changes create orphans:
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: Every changed line should trace directly to the user's request.

## 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Strong success criteria let you loop independently. Weak criteria ("make it work") require constant clarification.

---

**These guidelines are working if:** fewer unnecessary changes in diffs, fewer rewrites due to overcomplication, and clarifying questions come before implementation rather than after mistakes.
