# 프로젝트 개요

오르빗은 한국어 우주 탐험 퀴즈다. 실행 코드는 루트의 `index.html` 하나에
HTML, CSS, SVG, 일반 JavaScript로 들어 있다. 서버나 외부 의존성 없이
`file://`로 실행되는 구성을 유지한다. 외부 폰트, CDN, API 호출이나
번들러를 도입하지 않는다.

## 실행 및 브라우저 검증

Windows PowerShell에서 프로젝트 루트 기준으로 실행한다.

```powershell
Start-Process .\index.html
```

통합 브라우저에서는 `index.html`의 절대 경로를 `file://` URL로 연다.
파일 수정 후 페이지를 새로고침해야 코드와 상태가 초기화된다.

개별 정답 흐름을 빠르게 확인하려면 새로고침한 페이지의 브라우저 콘솔에서
아래 코드를 실행한다. DOM 버튼을 통해 첫 문제의 정답을 선택한다.

```javascript
(() => {
  const answer = document.querySelectorAll(".answer")[2];
  answer.focus();
  answer.click();
  console.assert(document.getElementById("score").textContent === "10", "정답 +10점");
  console.assert(answer.classList.contains("correct"), "정답 표시");
  console.assert(document.activeElement.id === "next", "다음 버튼으로 포커스 이동");
  console.assert(
    document.querySelector('[role="progressbar"]').getAttribute("aria-valuenow") === "1",
    "한 문제 완료"
  );
})();
```

전체 스모크 테스트는 모두 정답, 모두 오답, 정오답 교대의 세 경로를 실행한다.
각각 최종 점수 100·0·50점과 이모지 🧑‍🚀·🌱·🚀를 확인한다.
정답 +10점, 오답 점수 유지, 중복 응답 차단, 해설, 진행률, 결과의 정답 수와
정답률, 재시작 시 점수·진행률 초기화를 확인한다.

실제 키보드 검증은 Tab/Shift+Tab으로 선택지를 이동하고 Enter/Space로 선택한다.
답변 전에는 다음 버튼이 비활성화되어야 하고, 답변 후에는 선택지가
비활성화되고 다음 버튼으로 포커스가 이동해야 한다. 다음 문제와 재시작은
`#question`, 결과 화면은 `#result-title`에 포커스를 둔다.
JavaScript의 `focus()`와 `click()` 검증만으로 실제 키 입력을 검증했다고
보고하지 않는다.

## 화면 및 상태 구조

- `questions`는 고정 순서의 10문항이다. 각 항목은 `category`, `question`,
  4개의 `answers`, 0부터 시작하는 정답 인덱스 `correct`, 해설 `fact`를 가진다.
- `current`는 현재 문제 인덱스, `score`는 누적 점수, `answered`는 중복 응답
  방지 플래그다. `history`는 답변한 순서대로 정오답 불리언을 저장한다.
  진행률은 현재 문제 번호가 아니라 `history.length`로 계산한다.
- `renderQuestion()`은 선택지 DOM을 다시 만들고 답변 상태를 초기화한다.
  `choose()`는 한 번만 채점하고 선택지를 잠그며 정오답 표시와 해설을 만든다.
  `updateProgress()`는 관제 패널, 접근성 진행률 값, 하단 문제별 표시를 함께 갱신한다.
- 다음 버튼은 마지막 응답 이후 `#quiz`를 숨기고 `#results`를 표시한다.
  결과 반응은 80점 이상, 50점 이상, 50점 미만으로 나뉜다.
  재시작은 상태를 초기화하고 `renderQuestion(true)`를 호출한다.
- 상태는 메모리에만 있으며 새로고침하면 초기화된다.

## 변경 시 유지할 규칙

- UI 문구와 접근성 설명은 한국어로 유지한다. 영문 임무 코드와 장식용
  짧은 표제는 기존 디자인처럼 사용할 수 있다.
- 10문항·문항당 10점·총점 100점이 화면 문구, 진행 바 10칸, 결과 계산에
  함께 반영되어 있다. 문항 수나 배점을 변경한다면 이 연결 지점을 함께 수정한다.
- 전체 테마는 `:root`의 CSS 변수와 `prefers-color-scheme: dark`로 관리한다.
  관제 패널의 청록색 계열은 `.mission-control`의 `--console-*` 변수로
  분리되어 있으며 어두운 테마에도 별도 값이 있다.
- 중앙 `.shell`의 최대 너비는 520px이고 본문은 15px 시스템 산세리프다.
  좁은 화면에서 관제 데이터나 한국어 문구가 가로로 넘치지 않게 유지한다.
- 정답은 `.correct`의 초록색·팝 효과, 오답은 `.wrong`의 빨간색·흔들림이다.
  색상만으로 구분하지 않고 기호와 한국어 해설도 유지한다.
- 선택지는 네이티브 `button`을 사용한다. 점수와 해설의 라이브 영역,
  진행 바의 `aria-valuenow`·`aria-valuetext`, 제목 포커스용 `tabindex="-1"`,
  `hidden` 기반 화면 전환을 유지한다.
- 레이더 같은 장식 SVG는 `aria-hidden="true"`로 둔다.
  모든 새 애니메이션과 전환도 기존 `prefers-reduced-motion: reduce` 규칙을 따른다.
