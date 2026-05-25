# Gap 티스토리 스킨 — CLAUDE.md

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

## 작업 규칙

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

### README 업데이트
- 릴리스 작업을 할 때마다 반드시 `README.md`의 버전 히스토리에 해당 버전과 주요 변경 내용을 추가한다.
- README 버전 히스토리가 누락된 상태로 릴리스 PR, 태그 생성, GitHub Release 생성을 진행하지 않는다.

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
3. **릴리스 문서 갱신** — 릴리스 작업이면 `README.md` 버전 히스토리에 해당 버전과 변경 내용을 먼저 추가한다.
   ```bash
   $EDITOR README.md
   ```
4. **커밋** — Conventional Commits 형식 (`feat:`, `fix:`, `docs:` 등)
   ```bash
   git add <files> && git commit -m "<type>: <description>"
   ```
5. **dev 대상 PR 생성** — `main`이 아니라 `dev`를 base로 둔다
   ```bash
   gh pr create --base dev --title "..." --body "..."
   ```
6. **CI 해결** — lint.yml 등 실패한 워크플로 수정 후 재푸시
   ```bash
   gh pr checks <PR번호> --watch
   ```
7. **dev 병합** — CI 전체 통과 후 squash merge
   ```bash
   gh pr merge <PR번호> --squash --delete-branch
   ```
8. **main 반영 승인 요청** — `dev`를 `main`에 올리기 전 반드시 사용자에게 허락을 받는다. 허락 없이 `main` 대상 PR 생성, merge, release를 진행하지 않는다.
9. **승인 후 main 대상 PR 생성**
   ```bash
   gh pr create --base main --head dev --title "..." --body "..."
   ```
10. **승인 후 main 병합** — CI 전체 통과 후 squash merge
   ```bash
   gh pr merge <PR번호> --squash
   ```
11. **릴리스 재생성 또는 생성** — main 반영 후에만 실행한다. 실행 전 `README.md` 버전 히스토리에 해당 릴리스 내용이 있는지 다시 확인한다.
    ```bash
    gh release delete <tag> --yes
    git tag -d <tag> && git push origin :refs/tags/<tag>
    git tag <tag> && git push origin <tag>
    rm -f gap-tistory-skin.zip && cd skin_data && zip -r ../gap-tistory-skin.zip skin.html style.css index.xml preview256.jpg && cd ..
    gh release create <tag> gap-tistory-skin.zip --title "..." --notes "..."
    ```
