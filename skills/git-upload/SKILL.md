---
name: git-upload
description: >
  로컬 작업물을 GitHub에 업로드한다. 저장소 스캐폴딩, 첫 커밋, remote 연결,
  deploy key 방식 push까지 한 번에 처리한다. 사용자가 "깃에 올려줘", "깃헙에 올려줘",
  "publish해줘", "업로드해줘"라고 하거나 완성된 작업물의 공개를 요청하면 이 순서로
  진행한다. 저장소 생성·deploy key 등록은 git-create가, 브라우저 로그인은 git-login이
  담당한다.
license: MIT
---

# Git Upload

## 인증: GitHub 커넥터 우선, deploy key가 실제 운반 수단

`github status`로 연결 상태를 먼저 확인한다.

- **연결됨**: 커넥터로 저장소·파일 *조회*는 된다. 단, **쓰기(create_or_update_file)는
  App 권한에 따라 403으로 막힐 수 있다** (실측: 읽기 OK, 쓰기 403).
  쓰기가 막히면 deploy key 방식으로 push한다.
- **미연결**: `github authorize-url`의 `connect_url`을 사용자에게 전달하고 연결을
  요청한다.
- **deploy key 방식** (쓰기가 막힐 때의 실제 push 경로):
  1. 사용자가 저장소 Settings → Deploy keys → Add deploy key에 에이전트의 공개키
     (`~/.ssh/id_ed25519.pub`) 등록 + "Allow write access" 체크.
  2. 에이전트가 로컬에서 `git init -b main` → `add -A` → 커밋 →
     `git push git@github.com:USER/REPO.git main` 으로 직접 push한다.

에이전트는 토큰·비밀번호를 채팅으로 요구하지 않는다.

## 관련 스킬

- `git-login`: 브라우저 GitHub 로그인 (세션이 없을 때 먼저 실행)
- `git-create`: 브라우저로 저장소 생성 + deploy key 등록

## 절차

1. **스캐폴딩 확인**: `README.md`, `LICENSE`, `.gitignore`가 있는지 확인. 없으면 생성한다.
   - README: 무엇을 하는지, 설치법, 사용법을 한 화면에.
   - LICENSE: MIT 기본. 저작권자 이름은 사용자에게 확인한다.
2. **비밀 검사**: `git status`로 포함될 파일을 확인. 토큰·API 키·비밀번호·개인정보가
   들어가지 않았는지 검사한다. 의심스러우면 push 전에 사용자에게 확인한다.
3. **첫 커밋**: `git init -b main` → `git add -A` →
   conventional commits 형식 (`feat:`, `docs:`, `fix:` 등)으로 커밋.
4. **연결 및 push**: `git remote add origin <SSH 주소>` → `git push -u origin main`.
5. **정리 제안**: push 완료 후 deploy key 제거 여부를 사용자에게 확인한다.
   (다음 publish 때 다시 등록하면 됨.)

## 생략 규칙

- 이미 git 저장소인 경우: 3단계의 `init` 생략, 변경분만 커밋 후 push.
- 비공개 저장소: 절차 동일. README에 공개 범위만 맞게 기술.

## 주의

- 토큰·비밀번호를 채팅으로 요구하거나 평문으로 저장하지 않는다. 인증은 deploy key 방식만.
- force push 금지. 첫 push 충돌 시 사용자에게 보고하고 멈춘다.
