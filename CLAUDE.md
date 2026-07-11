# QDrive — AI 기반 탄소중립 운영 플랫폼 (프로토타입)

대구 시내버스 412대의 OBD·DTG 데이터로 안전운전 → 연료 절감 → 탄소 감축을 관리하는 UI 프로토타입.

**기획 정본은 [기획서.md](기획서.md)** — 수치를 바꿀 때는 그 문서의 §4 정합 사전을 먼저 갱신할 것. 랜딩(index.html)은 제품 스토리 + 권장 시연 동선 5단계를 담은 데모 브리핑 페이지다.

## 아키텍처

- **`.dc.html` 포맷**: `<x-dc>` 안의 HTML 템플릿(`{{ }}` 플레이스홀더, `<sc-if>`/`<sc-for>` 제어 태그) + `<script data-dc-script>`의 `class Component extends DCLogic` 로직을 `support.js` 런타임이 React로 렌더링한다.
- **`support.js`**: dc-runtime 빌드 산출물. **직접 수정 금지** (첫 줄 주석 참조 — dc-runtime/src에서 재빌드).
- **`_ds/toss-design-system-*/`**: 토스 디자인 시스템 토큰(CSS 변수)·Pretendard 폰트·컴포넌트 번들. 화면 코드는 `var(--primary)` 등 토큰만 사용.
- 외부 CDN: Chart.js 4.4.1, Leaflet 1.9.4 (+leaflet.heat), CARTO 타일 — 인터넷 필요.

| 파일 | 내용 |
|---|---|
| `탄소중립 대시보드.dc.html` | 관제 웹 — 대시보드 3종(통합 운영·탄소·연료·안전운행) + 운영관리 3종(차량·운전자·노선) + AI 에이전트 센터 + AI Planning + Reports (총 9페이지, `state.dash` 키: 1/2/3/V/D/R/A/P/T) |
| `QDrive 기사앱.dc.html` | 8인치 태블릿(가로) 기사앱 — 내비형 실시간 코칭 + 운행 리포트 + 게이미피케이션(사내 랭킹·배지 6종) |
| `시민 탄소 대시보드.dc.html` | 시민 공개 페이지 — 히어로 카운터·월별 차트·노선별 등급 조회 (독립 스크롤 페이지, 다크 히어로) |
| `운수사 경영 대시보드.dc.html` | 세운버스(주) 경영진용 — 손익 효과 수식: 연료비 1,065만(18.4tCO₂↔6,870L×1,550원/L) + 예지정비 420만 − 인센티브 310만 = 월 순 +1,175만원. 관제 coRanks(-5.8% 1위)와 정합 |

## 배포

- 저장소: https://github.com/hyegeungim3-git/qdrive-carbon-platform (공개)
- 라이브: https://hyegeungim3-git.github.io/qdrive-carbon-platform/ — Pages는 master 브랜치 루트에서 legacy 빌드, 푸시마다 자동 배포
- `.nojekyll` 필수 (`_ds` 언더스코어 폴더가 Jekyll에서 제외되는 것 방지). `index.html`은 4화면 랜딩 허브
- git identity는 저장소 로컬 설정 (hyegeungim3-git)

## 실행·검증

- `.claude/launch.json`의 `qdrive-static` (python http.server, 포트 8734)으로 실행. `file://`이 아닌 HTTP로 열 것.
- 차트는 250ms 폴링 `_tick()`이 canvas 존재를 감지해 그린다. 페이지·기간 전환 시 `_painted` 키(`dash|period`)가 바뀌면 전체 차트를 destroy 후 재생성.
- 스크린샷 검증은 이 환경에서 타임아웃이 잦음 → **DOM 텍스트/상태 검증 우선** (Chart.getChart(id), innerText 포함 여부, getBoundingClientRect).

## 주요 결정사항

- **기간 토글(일간/월간/연간)은 실동작**: `periodData()`가 기간별 성과·추이 데이터를 반환하고, 탄소·연료 대시보드의 '핵심 성과 흐름' 카드와 c-trend 차트가 연동된다. 수치는 경유 배출계수 2.68 kgCO₂/L 기준으로 상호 정합 (일간 784L↔2.1t, 월간 27,100L↔72.6t, 연간 146.9천L↔393.7t).
- **죽은 컨트롤 금지**: 정식 기능이 아닌 버튼(차량 등록 등)은 `_toast()` 데모 토스트로 반응. 알림 벨은 드롭다운 패널(모두 읽음 → 빨간 점 제거). 차량 검색은 결과 없음 빈 상태 행 표시.
- **AI 에이전트 센터(dash A)**: 승인 대기는 `state.appr`(idx→ok/hold), 코파일럿은 `copilotQA()` 고정 시나리오 3건 + `_ask()`의 750ms 단계 타이머(`_chatTimers`, unmount 시 정리). 자유 입력은 데모 범위 안내로 응답. HITL·설명가능성·정직한 시뮬레이션 3원칙은 기획서 §7.
- 목업 데이터 기준: 412대(경유 272·CNG 72·전기 68), 5개 운수회사, 배출계수는 환경부 고시.
- 기사앱 좌측 레일 하단에 관제 대시보드 복귀 링크(`관제`) 있음 — 데모 왕복 동선. 시민 페이지 푸터에도 관제 복귀 링크.
- **AI Planning 시뮬레이터 수식**: k번째 전환 차량의 연 감축 = 36.5 − 0.3k tCO₂ (AI가 효과순 선정 → 한계 체감). 누적 = `planCalc(n)` = 36.5n − 0.15n(n+1). 대당 실투자 1.1억(차량가 3.9억 − 보조금 2.8억), 연료비 절감 1.148백만원/tCO₂ (기존 'AI 추천' 카드의 6대=214t·ROI 2.7년과 정합). KOC 크레딧 8,900원/t. 슬라이더 변경 시 차트 재생성 — `_painted` 키가 `dash|period|planN`.
- 시민 페이지 소나무 환산: 6.6 kgCO₂/그루·년 → 연 누적 393.7t = 약 59,600그루.

## 검수에서 배운 것 (재발 방지)

- **Leaflet 지도 생명주기**: ①컨테이너 폭 0 시점에 heatLayer를 add하면 IndexSizeError로 초기화가 중단되고 `_leaflet_id`가 남아 이후 `L.map()`이 영구 실패 → `el.clientWidth` 가드 + 재생성 전 `_leaflet_id`/innerHTML 강제 정리 ②지도 정리는 페이지를 **떠나기 전**(컨테이너가 문서에 있을 때) `_go()`에서 수행. 고속 전환 12회당 CORS 마스킹된 "Script error." 1건은 잔존(무해·자가 복구) — 수용
- **브라우저 캐시**: .dc.html 수정 후 같은 URL 재방문은 캐시 렌더될 수 있음 — 검증 시 `?v=N` 쿼리로 강제 리로드 (dc 런타임은 pathname만 사용하므로 쿼리 안전)
- **시민 페이지는 모바일 필수**: 인라인 grid는 미디어쿼리 불가 → helmet `<style>`의 클래스(`pub-grid-main` 등)로. 375/768 검증 완료
- **그리드 + 캔버스 = minmax(0,1fr) 필수**: 1fr 트랙은 캔버스 min-content에 밀려 리사이즈(기기 회전) 시 줄어들지 않는다 → `minmax(0,1fr)` + 자식 `min-width:0`. 시민 페이지에서 실제 발생·수정

## 남은 일 (후보)

- 반응형 대응 — 관제 웹은 1280px+ 기준으로 설계됨 (태블릿 이하 미검증). 시민 페이지도 데스크톱 우선
- 다크 모드 미지원 (토스 토큰은 라이트만 로드)
- GitHub Pages 배포 시 한글 파일명 URL 인코딩 확인 필요
- Reports '운수회사별 성과 정산'은 미생성 상태(빈 상태 화면)로 두었음 — 필요 시 콘텐츠 채우기
- 기사앱 게이미피케이션(주간 랭킹·배지), 운수회사 경영진용 화면 등 이해관계자 확장 여지
