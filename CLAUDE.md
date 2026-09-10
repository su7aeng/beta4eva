# beta4eva

개인 실험실. "영원한 베타" — 만들다 만 것들과 만드는 중인 것들을 모아두는 정적 사이트.

## 구조

빌드 도구 없음. **순수 정적 HTML**이고, 각 페이지는 CSS·JS를 자기 파일 안에 인라인으로 갖는다.

```
index.html      허브 (하위 실험들 목록)
coin/index.html
seren/index.html
taut/index.html
```

새 실험을 추가할 때는 `<이름>/index.html` 하나를 만들고 루트 `index.html`의 목록에 항목을 더한다.
루트의 상태 뱃지는 `.st.live` / `.st.beta` / `.st.wip` 세 가지를 쓴다.

## 배포

**GitHub `main`에 push하면 Cloudflare Pages가 자동 배포한다.** → https://beta4eva.pages.dev

- Git 연동 방식이라 wrangler도, 별도 배포 명령도 필요 없다. push가 곧 배포다
- 배포는 `/ship` 으로 한다 — 변경 확인 → 커밋 → push → 라이브 반영까지 검증
- 반영까지 보통 10~60초. 확인 없이 "배포됐다"고 말하지 말 것

## 주의

- **public 리포다.** 커밋하는 순간 공개되고, 지워도 히스토리에 남는다
- 원래 GitHub 웹 UI로 작업하던 리포라 초기 커밋 메시지가 전부 `Update index.html`이다. 앞으로는 무엇이 달라졌는지 쓴다
- 여러 컴퓨터에서 작업한다. 시작 전에 `git pull` 할 것
