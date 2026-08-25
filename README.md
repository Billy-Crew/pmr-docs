# PMR Developer Documentation

Kuanta **PMR (Programmable Message Router)** 개발자 문서입니다.
전체 문서는 의존성 없는 단일 HTML 파일(`index.html`)로 되어 있습니다. (인라인 CSS·SVG·JS)

## 문서 보기 (로컬)

저장소를 클론한 뒤 `index.html`을 브라우저로 열면 됩니다.

```bash
git clone https://github.com/Billy-Crew/pmr-docs.git
cd pmr-docs
# 브라우저로 열기 (Windows)
start index.html
# macOS: open index.html / Linux: xdg-open index.html
```

## 나중에 웹으로 배포하기 (GitHub Pages)

현재는 **Private 저장소**라 보관만 하고 있습니다. 웹사이트로 공개하려면:

### 방법 A — Public으로 전환 후 배포 (무료)
1. GitHub 저장소 → **Settings → General → Danger Zone → Change visibility → Public**
2. **Settings → Pages → Build and deployment**
   - Source: **Deploy from a branch**
   - Branch: **main** / 폴더: **/ (root)** → Save
3. 1~2분 뒤 `https://billy-crew.github.io/pmr-docs/` 에서 확인

   > `index.html`이 루트에 있으므로 추가 설정 없이 바로 렌더링됩니다.

### 방법 B — Private 유지한 채 배포 (유료)
- Private 저장소로 Pages를 쓰려면 조직을 **GitHub Team** 이상으로 업그레이드해야 합니다.
- 접속 URL에 **인증/접근 제어**까지 걸려면 **GitHub Enterprise Cloud**가 필요합니다.
- 사내용 접근 제어만 원하면 Cloudflare Access, Netlify 암호보호 등의 대안도 있습니다.

## 문서 수정

`index.html`을 직접 편집한 뒤 커밋/푸시하면 됩니다. Pages 배포 중이라면 푸시 즉시 자동 재빌드됩니다.
