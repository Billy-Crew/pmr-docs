# PMR Control 관제

**Kuanta Control**은 다수의 PMR 노드를 한 화면에서 관제하는 시스템입니다. 각 노드는 **RESTful API를 기본 제공**하며, Control은 이 API 기반으로 동작합니다.

<figure>
<svg width="800" height="190" viewBox="0 0 800 190" xmlns="http://www.w3.org/2000/svg" font-family="sans-serif">
  <defs><marker id="a6" markerWidth="8" markerHeight="8" refX="7" refY="4" orient="auto"><path class="dg-arrow" d="M0,0 L8,4 L0,8 z" fill="#3d4a5c"/></marker></defs>
  <rect class="dg-box-loggerbar" x="300" y="20" width="200" height="56" rx="9" fill="#101720"/>
  <text class="dg-tx-output" x="400" y="44" text-anchor="middle" font-size="14" font-weight="bold" fill="#7fd1b9">CONTROL 관제</text>
  <text class="dg-tx-sub" x="400" y="63" text-anchor="middle" font-size="11" fill="#9fb0c3">대시보드 · 시각화 · 조회</text>
  <g font-size="12" fill="#141d29">
  <rect class="dg-box-node" x="30" y="126" width="100" height="44" rx="6" fill="#fff" stroke="#cdd5de"/><text class="dg-tx-strong" x="80" y="152" text-anchor="middle">서버 1</text>
  <rect class="dg-box-node" x="155" y="126" width="100" height="44" rx="6" fill="#fff" stroke="#cdd5de"/><text class="dg-tx-strong" x="205" y="152" text-anchor="middle">서버 2</text>
  <rect class="dg-box-node" x="280" y="126" width="100" height="44" rx="6" fill="#fff" stroke="#cdd5de"/><text class="dg-tx-strong" x="330" y="152" text-anchor="middle">서버 3</text>
  <rect class="dg-box-node" x="405" y="126" width="100" height="44" rx="6" fill="#fff" stroke="#cdd5de"/><text class="dg-tx-strong" x="455" y="152" text-anchor="middle">서버 4</text>
  <rect class="dg-box-node" x="530" y="126" width="100" height="44" rx="6" fill="#fff" stroke="#cdd5de"/><text class="dg-tx-strong" x="580" y="152" text-anchor="middle">서버 5</text>
  <rect class="dg-box-node" x="655" y="126" width="100" height="44" rx="6" fill="#fff" stroke="#cdd5de"/><text class="dg-tx-strong" x="705" y="152" text-anchor="middle">서버 6 …</text>
  </g>
  <line class="dg-arrow-stage" x1="340" y1="78" x2="90" y2="124" stroke="#0a4fa8" stroke-width="1.4" marker-end="url(#a6)"/>
  <line class="dg-arrow-stage" x1="365" y1="78" x2="212" y2="124" stroke="#0a4fa8" stroke-width="1.4" marker-end="url(#a6)"/>
  <line class="dg-arrow-stage" x1="395" y1="78" x2="333" y2="124" stroke="#0a4fa8" stroke-width="1.4" marker-end="url(#a6)"/>
  <line class="dg-arrow-stage" x1="415" y1="78" x2="452" y2="124" stroke="#0a4fa8" stroke-width="1.4" marker-end="url(#a6)"/>
  <line class="dg-arrow-stage" x1="440" y1="78" x2="575" y2="124" stroke="#0a4fa8" stroke-width="1.4" marker-end="url(#a6)"/>
  <line class="dg-arrow-stage" x1="465" y1="78" x2="698" y2="124" stroke="#0a4fa8" stroke-width="1.4" marker-end="url(#a6)"/>
  <text class="dg-tx-stage" x="510" y="105" font-size="11" fill="#0a4fa8">RESTful API</text>
</svg>
<figcaption>그림 7. 각 PMR 노드의 RESTful API를 통한 멀티 서버 관제</figcaption>
</figure>

## 주요 화면과 기능

### 1. 대시보드 & 설정 항목 조회

- 노드별 자원 현황 — CPU, Memory, Traffic 등 실시간 모니터링
- 각 설정 항목별 리스트 및 개별 조회

### 2. 메시지 흐름 시각화

- 각 채널별 메시지 흐름 제어와 구성 조회
- 메시지 가공/라우팅 시각화
- **설정 변경 시 메시지 흐름 미리보기(Preview)** — 반영 전에 결과 흐름을 시각적으로 검증
- 각 프로세스별 액션 항목 조회

### 3. 종목 메모리 조회

- 종목별 공유메모리(SHM) 조회
- 통합/가공 이상 여부 또는 메모리 이상 여부 조회

### 4. 증적로그 메시지 조회 (전문 조회)

- 수신·송출 등 각 증적로그 파일의 로그 검색 및 조회
- 검색 조건을 스크립트로 설정
- 시간·일련번호 기준 조회
- 정의되지 않은 Raw 메시지 조회 (Not Defined)

!!! note "API-first"
    Control의 모든 기능은 노드의 RESTful API 위에 구현되어 있으므로, 동일 API로 사내 관제·자동화 도구를 직접 연동할 수 있습니다.
