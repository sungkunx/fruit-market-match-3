# Fruit Market Match 3

과일 컨셉을 유지한 채 장르를 새로 짜는 세 번째 과일 게임. 장르와 규칙은 아직 정하는 중이다 (`docs/specs/`에 쌓인다).

이전 작품 `Fruit 2 Market`(성장형 매치3 경제 게임)은 `../fruit-market-match-v2`에 있고, GitHub `sungkunx/fruit-market-match-2`에 올라가 있다. 구현 방식이나 연출을 참고할 때 읽기만 하고 고치지 않는다.

## 사용자와 일하는 방식

- 대답은 **한국어**로.
- 사용자는 자주 **폰(Remote Control)으로** 지시한다. 진행 상황은 짧게 알리고, 화면이 바뀌는 작업은 내장 브라우저로 확인한 뒤 **스크린샷을 `SendUserFile`로 보낸다.** "됐다"고 말하기 전에 직접 확인한다.
- **큰 설계(새 시스템, 규칙, 장르)는 논의가 먼저다.** 질문은 한 번에 하나씩, 선택지에 추천안을 앞에 두고(AskUserQuestion), 정해지면 `docs/specs/`에 적고 구현한다. 작은 수정은 바로 한다.
- 난이도·밸런스 이야기는 **감이 아니라 숫자로.** 봇 시뮬레이터로 재서 표로 보여준다.
- 기능 하나가 끝나면 커밋한다. 푸시는 사용자가 요청할 때 한다(자주 요청한다).

## 기술 규칙

- 바닐라 HTML/CSS/JS. 빌드 도구·npm 의존성 없음.
- 규칙 로직은 DOM 없는 모듈로 분리한다: IIFE + 이중 export(`window.X` / `module.exports`). 테스트는 Node 내장 `node:test`(`node --test tests/*.test.js`). 테스트는 밸런스 파일(`tuning.js` 같은 것)을 읽지 않고 고정 값을 쓴다.
- 밸런스 숫자는 한 파일에 모은다. 바꾼 뒤엔 시뮬레이터(`tools/sim.js`)로 확인한다.
- 화면 코드는 `await` 뒤에 세션이 바뀌었는지(홈으로 나갔는지) 확인한다.
- 모든 새 애니메이션·트랜지션은 `@media (prefers-reduced-motion: reduce)`에서 끈다.
- 캔버스는 `devicePixelRatio`를 반영한다. 캔버스는 CSS 변수를 못 읽으니 `:root`에서 읽어 쓴다.
- 커밋 메시지 끝: `Co-Authored-By: Claude Opus 5.5 <noreply@anthropic.com>` (그때그때 시스템이 알려주는 줄을 따른다).

## 모바일 우선

- 기준 화면 375×812. 내장 브라우저에서 `resize_window`의 mobile 프리셋으로 확인한다.
- 브라우저 테스트 중에는 **소리를 끈다**(`GameAudio.setMuted(true)`). 끝나면 뷰포트를 desktop으로 돌려놓는다.
- 개발 서버: `.claude/launch.json`의 `fruit-market-3`(포트 8767).

## 배포

- GitHub Pages(`sungkunx/fruit-market-match-3`, main 브랜치 루트). 푸시 1~2분 뒤 폰에서 바로 플레이할 수 있다.
- **서비스워커를 캐시 우선으로 만들지 않는다.** match-2에서 캐시 이름이 고정된 cache-first 워커가 옛 빌드를 계속 내주는 문제가 있었다. 오프라인이 필요하면 네트워크 우선 + 버전 붙은 캐시로.
- Pages는 파일을 최대 10분 캐시한다. 변경이 안 보이면 새로고침.

## 가져온 것

- `img/fruits/fruit_{apple,banana,grape,kiwi,orange,strawberry,cherry}_{001-004}.png` — 표정 4장. match-2에서 001은 기본, 002는 누를 때, 003은 터질 때, 004는 실패했을 때 썼다.
- `img/reference/` — match-1·2 로고. 새 로고를 만들 때 참고용.
- `audio.js` — Web Audio로 합성하는 음악·효과음 엔진(게임 음악, 타이틀 음악, 팝/성공/실패, 특수 폭발, 팡파르, 음소거 저장). 곡은 match-2 것이라 새 게임에 맞게 바꿔 쓴다. 브라우저는 첫 터치 전에는 소리를 못 낸다: 첫 터치에서 음악을 시작하고, 스피커 버튼의 첫 탭은 "재생"으로 처리한다.
- `tests/audio.test.js` — 위 엔진의 순수 함수 테스트.
