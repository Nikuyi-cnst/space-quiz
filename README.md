# 오르빗 — 우주 탐험 퀴즈

우주에 관한 10문제를 풀며 탐험하는 한국어 퀴즈입니다. 문항마다 힌트와
해설을 제공하며, 정답은 10점씩 총 100점입니다. 탐험이 끝나면 점수와
정답률을 확인하고 틀린 문제를 복습할 수 있습니다.

**웹사이트:** https://nikuyi-cnst.github.io/space-quiz/

## 로컬 실행

저장소를 내려받은 뒤 루트의 `index.html`을 브라우저에서 열면 됩니다.
Windows PowerShell에서는 프로젝트 루트에서 다음 명령을 실행하세요.

```powershell
Start-Process .\index.html
```

HTML, CSS, SVG, 일반 JavaScript가 `index.html` 하나에 들어 있습니다.
설치, 빌드, 서버, 외부 의존성 또는 네트워크 연결 없이 `file://`로 실행할 수
있습니다. 변경 사항은 브라우저를 새로고침하면 반영됩니다. 응답 기록과
점수는 메모리에만 저장되므로 새로고침하면 초기화됩니다.

## GitHub Pages 배포

저장소의 **Settings → Pages → Build and deployment → Source**는
**GitHub Actions**로 설정합니다.

`.github/workflows/pages.yml`은 PR에서 비어 있지 않은 `index.html`을 확인하고
해당 파일만 게시 아티팩트로 패키징합니다. 소스 코드 빌드는 필요하지 않습니다.
`main`에 변경 사항이 푸시되면 같은 아티팩트를 `github-pages` 환경에 배포합니다.
README나 저장소 설정 파일은 사이트에 포함하지 않습니다.

필요하면 **Actions → Deploy GitHub Pages → Run workflow**에서 `main`을
선택해 수동으로 다시 배포할 수 있습니다. 배포 결과와 게시 주소는 워크플로의
`Deploy site` 작업에서 확인할 수 있습니다. PR에서는 배포하지 않습니다.
