# Bolt-Log와 리플레이

**Bolt-Log**는 PMR의 고성능 Key-Value 파일 DB입니다. 모든 메시지를 유실 없이 기록하고, 제한 없이 조회하거나, 원하는 구간을 원하는 속도로 재생합니다.

## 특징

- **랜덤-액세스**와 **2차-인덱스**를 제한 없이 제공
- 시간·일련번호 기반 구간 검색
- **배속 재생**: 0.5× 정밀 분석부터 20× 부하 테스트까지
- MMF 버퍼 페이징 기반 무손실 기록 (→ [아키텍처 개요](03_아키텍처-개요.md))

<figure>
<svg width="800" height="220" viewBox="0 0 800 220" xmlns="http://www.w3.org/2000/svg" font-family="sans-serif">
  <defs><marker id="a4" markerWidth="8" markerHeight="8" refX="7" refY="4" orient="auto"><path d="M0,0 L8,4 L0,8 z" fill="#3d4a5c"/></marker></defs>
  <rect x="40" y="80" width="140" height="60" rx="8" fill="#fff" stroke="#141d29" stroke-width="1.6"/>
  <text x="110" y="106" text-anchor="middle" font-size="13" font-weight="bold" fill="#141d29">PMR (운영)</text>
  <text x="110" y="125" text-anchor="middle" font-size="11" fill="#69788c">실시간 시세 기록</text>
  <rect x="300" y="70" width="170" height="80" rx="10" fill="#101720"/>
  <text x="385" y="103" text-anchor="middle" font-size="14" font-weight="bold" fill="#7fd1b9">Bolt-Log</text>
  <text x="385" y="124" text-anchor="middle" font-size="11" fill="#9fb0c3">Custom KV-DB · 2차 인덱스</text>
  <rect x="600" y="14" width="160" height="50" rx="8" fill="#eaf5ef" stroke="#177245"/>
  <text x="680" y="35" text-anchor="middle" font-size="12.5" font-weight="bold" fill="#177245">Recovery</text>
  <text x="680" y="53" text-anchor="middle" font-size="11" fill="#69788c">장애 구간 복구</text>
  <rect x="600" y="85" width="160" height="50" rx="8" fill="#eef4fb" stroke="#0a4fa8"/>
  <text x="680" y="106" text-anchor="middle" font-size="12.5" font-weight="bold" fill="#0a4fa8">Replay</text>
  <text x="680" y="124" text-anchor="middle" font-size="11" fill="#69788c">0.5× ~ 20× 배속 재생</text>
  <rect x="600" y="156" width="160" height="50" rx="8" fill="#fdf3ec" stroke="#c2402a"/>
  <text x="680" y="177" text-anchor="middle" font-size="12.5" font-weight="bold" fill="#c2402a">Tester</text>
  <text x="680" y="195" text-anchor="middle" font-size="11" fill="#69788c">부하·회귀 테스트</text>
  <line x1="180" y1="110" x2="298" y2="110" stroke="#3d4a5c" stroke-width="2" marker-end="url(#a4)"/>
  <line x1="470" y1="92" x2="598" y2="42" stroke="#3d4a5c" stroke-width="1.6" marker-end="url(#a4)"/>
  <line x1="470" y1="110" x2="598" y2="110" stroke="#3d4a5c" stroke-width="1.6" marker-end="url(#a4)"/>
  <line x1="470" y1="128" x2="598" y2="178" stroke="#3d4a5c" stroke-width="1.6" marker-end="url(#a4)"/>
</svg>
<figcaption>그림 5. Bolt-Log를 축으로 한 Recovery · Replay · Tester 구성</figcaption>
</figure>

## Full-Fidelity Replay: 단순 기록을 넘어선 '재현'

실시간 시세 데이터를 자산화하면 시스템 검증의 완결성이 달라집니다.

| 시나리오 | 배속 | 용도 |
| --- | --- | --- |
| **장애 상황 복기** | 1× / 0.5× | 사고 당시 메시지 흐름을 그대로 재현하여 원인 분석 |
| **정밀 분석** | 0.5× | 이벤트 간 타이밍·순서를 저속으로 관찰 |
| **부하 테스트** | 20× | 실데이터 기반 스트레스 테스트, 신규 구성 검증 |
| **마켓 에뮬레이션** | 자유 | 개발·QA 환경에 실장 데이터와 동일한 피드 공급 |

!!! note "개발 환경이 곧 운영 환경"

    Bolt-Log 리플레이를 개발 환경의 피드 소스로 쓰면, 운영과 동일한 데이터로 레이아웃·룰을 검증한 뒤 반영할 수 있습니다.

운영 화면에서의 검색·재생 조작은 [메시지 검색과 리플레이](19_메시지-검색과-리플레이.md)를 참고하세요.
