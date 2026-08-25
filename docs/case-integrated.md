# 통합시세 시스템 구성

증권사 시세 FEP·통합시세에 PMR을 적용한 실제 구성입니다. 수신부터 리테일·투자정보·Prop·관제까지 하나의 프로그래머블 파이프라인으로 통합됩니다.

## 전체 구성 (WRSEC 사례)

<figure markdown="span">
<svg viewBox="0 0 880 320" xmlns="http://www.w3.org/2000/svg" font-family="sans-serif">
  <defs><marker id="a7" markerWidth="8" markerHeight="8" refX="7" refY="4" orient="auto"><path d="M0,0 L8,4 L0,8 z" fill="#3d4a5c"/></marker></defs>
  <rect x="14" y="120" width="96" height="60" rx="7" fill="#fdf3ec" stroke="#c2402a"/>
  <text x="62" y="146" text-anchor="middle" font-size="12.5" font-weight="bold" fill="#c2402a">KRX/NXT</text>
  <text x="62" y="164" text-anchor="middle" font-size="11" fill="#8c5040">시세 수신</text>
  <rect x="150" y="110" width="120" height="80" rx="8" fill="#fff" stroke="#141d29" stroke-width="1.6"/>
  <text x="210" y="140" text-anchor="middle" font-size="13" font-weight="bold" fill="#141d29">시세 FEP</text>
  <text x="210" y="160" text-anchor="middle" font-size="11" fill="#69788c">PMR</text>
  <rect x="330" y="60" width="140" height="70" rx="8" fill="#fff" stroke="#0a4fa8" stroke-width="1.6"/>
  <text x="400" y="88" text-anchor="middle" font-size="13" font-weight="bold" fill="#0a4fa8">통합시세</text>
  <text x="400" y="107" text-anchor="middle" font-size="11" fill="#69788c">KQP · PMR</text>
  <rect x="330" y="170" width="140" height="70" rx="8" fill="#fff" stroke="#8a6d1f" stroke-width="1.6"/>
  <text x="400" y="196" text-anchor="middle" font-size="12.5" font-weight="bold" fill="#8a6d1f">2nd 가공/라우팅</text>
  <text x="400" y="215" text-anchor="middle" font-size="11" fill="#69788c">Bypass · Modified</text>
  <g font-size="11.5" fill="#141d29">
  <rect x="560" y="10"  width="118" height="38" rx="6" fill="#f5f7f9" stroke="#cdd5de"/><text x="619" y="34" text-anchor="middle">원장</text>
  <rect x="560" y="56"  width="118" height="38" rx="6" fill="#f5f7f9" stroke="#cdd5de"/><text x="619" y="80" text-anchor="middle">Retail 접속서버</text>
  <rect x="560" y="102" width="118" height="38" rx="6" fill="#f5f7f9" stroke="#cdd5de"/><text x="619" y="126" text-anchor="middle">Retail 투자정보</text>
  <rect x="560" y="148" width="118" height="38" rx="6" fill="#f5f7f9" stroke="#cdd5de"/><text x="619" y="172" text-anchor="middle">AI 뉴스</text>
  <rect x="560" y="194" width="118" height="38" rx="6" fill="#f5f7f9" stroke="#cdd5de"/><text x="619" y="218" text-anchor="middle">Prop.1</text>
  <rect x="560" y="240" width="118" height="38" rx="6" fill="#f5f7f9" stroke="#cdd5de"/><text x="619" y="264" text-anchor="middle">Prop.2</text>
  </g>
  <rect x="732" y="120" width="130" height="60" rx="8" fill="#101720"/>
  <text x="797" y="146" text-anchor="middle" font-size="12.5" font-weight="bold" fill="#7fd1b9">Kuanta 관제</text>
  <text x="797" y="164" text-anchor="middle" font-size="10.5" fill="#9fb0c3">CONTROL</text>
  <line x1="110" y1="150" x2="148" y2="150" stroke="#c2402a" stroke-width="2.2" marker-end="url(#a7)"/>
  <line x1="270" y1="132" x2="328" y2="102" stroke="#0a4fa8" stroke-width="2" marker-end="url(#a7)"/>
  <line x1="270" y1="168" x2="328" y2="198" stroke="#8a6d1f" stroke-width="2" marker-end="url(#a7)"/>
  <line x1="470" y1="80" x2="558" y2="34" stroke="#0a4fa8" stroke-width="1.5" marker-end="url(#a7)"/>
  <line x1="470" y1="90" x2="558" y2="76" stroke="#0a4fa8" stroke-width="1.5" marker-end="url(#a7)"/>
  <line x1="470" y1="100" x2="558" y2="120" stroke="#0a4fa8" stroke-width="1.5" marker-end="url(#a7)"/>
  <line x1="470" y1="192" x2="558" y2="168" stroke="#8a6d1f" stroke-width="1.5" marker-end="url(#a7)"/>
  <line x1="470" y1="208" x2="558" y2="212" stroke="#8a6d1f" stroke-width="1.5" marker-end="url(#a7)"/>
  <line x1="470" y1="222" x2="558" y2="258" stroke="#8a6d1f" stroke-width="1.5" marker-end="url(#a7)"/>
  <line x1="680" y1="150" x2="730" y2="150" stroke="#3d4a5c" stroke-width="1.4" stroke-dasharray="4 3" marker-end="url(#a7)"/>
</svg>
<figcaption>그림 8. WRSEC 구성 사례 — 시세 FEP 뒤에 통합시세와 2nd 가공/라우팅 레이어를 병렬 배치</figcaption>
</figure>

## 통합시세 노드 내부

통합시세 노드는 [아키텍처 개요](architecture.md)의 3단 파이프라인을 그대로 따릅니다: 수신 → 식별/가공 → 분배, 전 구간 Audit Logger, MMF 버퍼 페이징(4M 바이트 프레임 I/O) 로깅.

프로그래머블 요소는 4종으로 분리 관리됩니다:

- **식별자** — `dst_fill_kospi.moon` 등 (→ [전문 정의와 식별자](spec.md))
- **전문 정의** — 시장·전문별 spec 파일
- **가공함수** — `lfn_krx_fill.moon`, `gen_vi.moon` 등 (→ [가공·분배 함수](procdist.md))
- **분배함수** — `route_map` 등 destinate 룰

## Bypass와 Modified의 공존

2nd 레이어는 하위 시스템별 요구에 따라 **Bypass**(원본 그대로)와 **Modified**(가공 전문)를 동시에 서비스합니다. 동일 수신 스트림에서 `route raw:true` 분기와 `modify` 파이프라인을 병렬 구성하면 됩니다.
