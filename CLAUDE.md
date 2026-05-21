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
  cd skin_data && zip -r ../gap-tistory-skin.zip skin.html style.css index.xml images/ preview256.jpg && cd ..
  ```

### PR & 릴리스 워크플로

변경 사항을 main에 반영할 때 아래 순서를 따른다:

1. **새 브랜치 생성** — 작업 단위에 맞는 브랜치명 사용
   ```bash
   git checkout -b <type>/<description>
   ```
2. **커밋** — Conventional Commits 형식 (`feat:`, `fix:`, `docs:` 등)
   ```bash
   git add <files> && git commit -m "<type>: <description>"
   ```
3. **PR 생성** — main을 base로, CI 통과 전까지 merge 금지
   ```bash
   gh pr create --base main --title "..." --body "..."
   ```
4. **CI 해결** — lint.yml 등 실패한 워크플로 수정 후 재푸시
   ```bash
   gh pr checks <PR번호> --watch
   ```
5. **main 병합** — CI 전체 통과 후 squash merge
   ```bash
   gh pr merge <PR번호> --squash --delete-branch
   ```
6. **릴리스 재생성** — 기존 릴리스 삭제 후 최신 태그로 재생성 (zip 에셋 첨부)
   ```bash
   gh release delete <tag> --yes
   git tag -d <tag> && git push origin :refs/tags/<tag>
   git tag <tag> && git push origin <tag>
   cd skin_data && zip -r ../gap-tistory-skin.zip skin.html style.css index.xml images/ preview256.jpg && cd ..
   gh release create <tag> gap-tistory-skin.zip --title "..." --notes "..."
   ```
