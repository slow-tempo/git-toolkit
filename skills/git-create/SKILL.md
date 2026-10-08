---
name: git-create
description: >
  GitHub 저장소를 브라우저로 생성한다. Muse GitHub App의 create_repository는
  403으로 막히므로(실측) 브라우저 자동화가 정석이다. 동명 저장소 중복 확인 →
  생성 폼 작성 → 생성 → URL 보고. 필요하면 deploy key까지 등록한다. 사용자가
  "저장소 만들어줘", "깃헙에 리포 만들어줘"라고 하면 사용한다.
license: MIT
---

# Git Create

## 전제

- 브라우저에 GitHub 로그인이 필요하다. 안 되어 있으면 `git-login`을 먼저 실행한다.

## 절차

1. **중복 확인**: `github.com/<사용자명>/<저장소명>`에 접속하거나 저장소 목록에서
   동명 저장소를 확인한다. 이미 있으면 생성하지 않고 기존 URL을 보고한다.
2. **생성 폼**: 브라우저 작업으로 `https://github.com/new`에 접속한다.
   - Repository name: 사용자가 지정한 이름 (필수)
   - Description: 한 줄 설명
   - Visibility: Public/Private — 사용자가 지정한다. 지정이 없으면 묻는다.
   - **"Add a README file" 체크 해제**, .gitignore·license 추가 안 함.
     (스캐폴딩과 파일은 `git-upload`가 push한다.)
3. **"Create repository"** 클릭 → 생성된 저장소 URL을 보고한다.
4. **(선택) deploy key 등록**: SSH push(`git-upload`)를 할 예정이면 이어서 등록한다.
   Settings → Deploy keys → Add deploy key:
   - Title: `muse-vm` (또는 사용자가 지정한 이름)
   - Key: `~/.ssh/id_ed25519.pub`의 내용 그대로
   - **"Allow write access" 체크**
   - 등록 후 키 지문(SHA256)으로 확인한다.

## 주의

- API `create_repository`는 GitHub App 권한으로 403 실측 → 브라우저 경로를 쓴다.
- 저장소 설정의 다른 항목은 건드리지 않는다.
- 비밀번호 재입력이나 2FA가 나타나면 중단하고 사용자에게 보고한다.
- 관련 스킬: `git-login`(사전 로그인), `git-upload`(파일 push).
