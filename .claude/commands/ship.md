---
description: 변경사항을 커밋하고 main에 push한 뒤, Cloudflare Pages 라이브 반영까지 확인
allowed-tools: Bash(git status:*), Bash(git diff:*), Bash(git add:*), Bash(git commit:*), Bash(git push:*), Bash(git log:*), Bash(curl:*), Bash(shasum:*), Bash(sleep:*)
---

beta4eva를 배포한다. 순서대로 진행하고, 각 단계 결과를 짧게 보고할 것.

## 1. 변경 확인

```
git status --short
git diff --stat
```

변경이 없으면 "올릴 변경 없음"이라고 말하고 **여기서 중단**. 이후 단계 진행 금지.

## 2. 올리면 안 되는 것 걸러내기

`git status --short` 결과에 아래가 있으면 커밋에서 제외하고 사용자에게 알릴 것:

- `.env`, `.env.*` (`.env.example` 제외), `*.key`, `*.pem`, 자격증명·토큰이 든 파일
- `.DS_Store`, 에디터 임시파일
- 실수로 들어온 대용량 바이너리 (1MB 초과 파일은 언급하고 확인받을 것)

**이 리포는 public이다.** 커밋하는 순간 전 세계에 공개되고, 지워도 git 히스토리와 캐시에 남는다. 애매하면 올리지 말고 물어볼 것.

## 3. 커밋

`git diff` 를 실제로 읽고, **무엇이 어떻게 달라졌는지** 한국어로 쓴다.

- `Update index.html` 같은 무의미한 메시지 금지 (기존 히스토리가 전부 이 모양이라 되돌아보기 어렵다)
- 형식: 한 줄 요약 + 필요하면 빈 줄 뒤 상세
- 좋은 예: `coin: 시세 카드 모바일에서 잘리던 문제 수정`, `seren: 소개 문단 추가 + 폰트 크기 조정`
- 여러 하위 페이지를 동시에 고쳤으면 각각 한 줄씩

메시지 끝에 다음 줄을 붙인다:

```
Co-Authored-By: Claude Opus 5 (1M context) <noreply@anthropic.com>
```

## 4. push

```
git push origin main
```

Cloudflare Pages가 이 리포에 Git 연동되어 있어 **push 자체가 배포 트리거**다. wrangler나 별도 배포 명령은 필요 없고 실행하지도 말 것.

## 5. 라이브 반영 확인 (건너뛰지 말 것)

이번에 바꾼 HTML 파일마다 실제 배포본을 받아 로컬과 대조한다.

- 루트 `index.html` → `https://beta4eva.pages.dev/`
- `coin/index.html` → `https://beta4eva.pages.dev/coin/`
- `seren/index.html`, `taut/index.html` 도 같은 규칙

```
curl -s --max-time 15 "https://beta4eva.pages.dev/<경로>" | shasum -a 256
shasum -a 256 <로컬 파일>
```

**두 해시 중 하나라도 비어 있으면 "일치"가 아니라 오류다.** curl이 실패하거나 명령이 깨지면 빈 문자열끼리 비교돼 거짓 통과가 나온다. 비교 전에 두 값이 모두 64자인지 확인할 것. 쉘 함수로 감싸지 말고 평범한 루프로 쓸 것 (zsh에서 함수 본문이 깨져 조용히 실패한 적 있음).

해시가 다르면 아직 빌드 중이다. **10초 간격으로 최대 9번** 다시 확인한다. 즉, 최대 약 90초까지 기다린다.

- 일치하면 → 배포 완료. URL을 알려준다
- 90초가 지나도 다르면 → "push는 됐지만 라이브 반영이 90초 안에 안 끝났다"고 **사실대로** 보고하고, Cloudflare 대시보드에서 빌드 로그를 확인하라고 안내한다. 반영됐다고 추측해서 말하지 말 것

## 6. 보고

- 커밋 해시와 메시지 한 줄
- 확인한 URL과 최종 상태 (일치 / 대기 중)

배포가 확인되지 않았으면 완료라고 말하지 않는다.
