# 2nd 레이어 격리 패턴

업무 확장에 따른 변경 리스크를 **후선처리(2nd) 흐름으로 제한·격리**하여 메인시세 흐름에 영향을 주지 않는 표준 확장 패턴입니다.

## 패턴 구성 — KOSPI 체결 확장 사례

<figure markdown="span">
<svg viewBox="0 0 880 300" xmlns="http://www.w3.org/2000/svg" font-family="sans-serif">
  <defs>
    <marker id="b1" markerWidth="8" markerHeight="8" refX="7" refY="4" orient="auto"><path d="M0,0 L8,4 L0,8 z" fill="#c2402a"/></marker>
    <marker id="b2" markerWidth="8" markerHeight="8" refX="7" refY="4" orient="auto"><path d="M0,0 L8,4 L0,8 z" fill="#0a4fa8"/></marker>
    <marker id="b3" markerWidth="8" markerHeight="8" refX="7" refY="4" orient="auto"><path d="M0,0 L8,4 L0,8 z" fill="#177245"/></marker>
    <marker id="b4" markerWidth="8" markerHeight="8" refX="7" refY="4" orient="auto"><path d="M0,0 L8,4 L0,8 z" fill="#8a6d1f"/></marker>
  </defs>
  <rect x="20" y="40" width="840" height="110" rx="10" fill="#fdf6f4" stroke="#e6c4bb" stroke-dasharray="6 4"/>
  <text x="40" y="64" font-size="12" font-weight="bold" fill="#c2402a">PRIMARY FLOW — 무변경 보호 구간</text>
  <rect x="60" y="80" width="130" height="54" rx="8" fill="#fff" stroke="#141d29" stroke-width="1.6"/>
  <text x="125" y="102" text-anchor="middle" font-size="11" fill="#69788c">PMR</text><text x="125" y="120" text-anchor="middle" font-size="13" font-weight="bold">KS_f</text>
  <rect x="330" y="80" width="130" height="54" rx="8" fill="#fff" stroke="#141d29" stroke-width="1.6"/>
  <text x="395" y="102" text-anchor="middle" font-size="11" fill="#69788c">PMR</text><text x="395" y="120" text-anchor="middle" font-size="13" font-weight="bold">KS_f_emit</text>
  <rect x="640" y="80" width="180" height="54" rx="8" fill="#fdf3ec" stroke="#c2402a"/>
  <text x="730" y="112" text-anchor="middle" font-size="12.5" font-weight="bold" fill="#c2402a">UDP 메인시세 송출</text>
  <line x1="190" y1="107" x2="328" y2="107" stroke="#c2402a" stroke-width="2.4" marker-end="url(#b1)"/>
  <line x1="460" y1="107" x2="638" y2="107" stroke="#c2402a" stroke-width="2.4" marker-end="url(#b1)"/>
  <rect x="20" y="168" width="840" height="120" rx="10" fill="#f4f8fc" stroke="#bcd0e6" stroke-dasharray="6 4"/>
  <text x="40" y="192" font-size="12" font-weight="bold" fill="#0a4fa8">SECONDARY FLOW — 확장에 따른 변경 지점</text>
  <g font-size="12">
  <rect x="60" y="210" width="120" height="54" rx="8" fill="#fff" stroke="#0a4fa8"/>
  <text x="120" y="230" text-anchor="middle" fill="#69788c" font-size="10.5">KQP</text><text x="120" y="248" text-anchor="middle" font-weight="bold" fill="#0a4fa8">ksp_f</text>
  <rect x="230" y="210" width="120" height="54" rx="8" fill="#fff" stroke="#8a6d1f"/>
  <text x="290" y="230" text-anchor="middle" fill="#69788c" font-size="10.5">PMR</text><text x="290" y="248" text-anchor="middle" font-weight="bold" fill="#8a6d1f">KS_f_qos</text>
  <rect x="400" y="210" width="120" height="54" rx="8" fill="#fff" stroke="#177245"/>
  <text x="460" y="230" text-anchor="middle" fill="#69788c" font-size="10.5">PMR</text><text x="460" y="248" text-anchor="middle" font-weight="bold" fill="#177245">KS_f_2nd</text>
  <rect x="570" y="210" width="120" height="54" rx="8" fill="#fff" stroke="#177245"/>
  <text x="630" y="230" text-anchor="middle" fill="#69788c" font-size="10.5">BROKER</text><text x="630" y="248" text-anchor="middle" font-weight="bold" fill="#177245">KS_f</text>
  <rect x="740" y="210" width="110" height="54" rx="8" fill="#eaf5ef" stroke="#177245" stroke-dasharray="5 3"/>
  <text x="795" y="235" text-anchor="middle" font-size="11" fill="#69788c">PMR</text><text x="795" y="252" text-anchor="middle" font-weight="bold" fill="#177245" font-size="12">custom01</text>
  </g>
  <line x1="125" y1="134" x2="122" y2="208" stroke="#0a4fa8" stroke-width="1.8" marker-end="url(#b2)"/>
  <text x="150" y="176" font-size="10.5" fill="#0a4fa8">통합시세 분기</text>
  <line x1="180" y1="237" x2="228" y2="237" stroke="#8a6d1f" stroke-width="1.8" marker-end="url(#b4)"/>
  <line x1="350" y1="237" x2="398" y2="237" stroke="#177245" stroke-width="1.8" marker-end="url(#b3)"/>
  <line x1="520" y1="237" x2="568" y2="237" stroke="#177245" stroke-width="1.8" marker-end="url(#b3)"/>
  <text x="544" y="228" text-anchor="middle" font-size="10.5" fill="#177245">TCP 송출</text>
  <line x1="690" y1="237" x2="738" y2="237" stroke="#177245" stroke-width="1.8" stroke-dasharray="5 3" marker-end="url(#b3)"/>
  <text x="714" y="228" text-anchor="middle" font-size="10.5" fill="#177245">신규 업무</text>
</svg>
<figcaption>그림 9. Primary/Secondary 이중 흐름 — 신규 업무(custom01)는 2nd 체인 끝에만 추가</figcaption>
</figure>

*UDP 메인시세 · UDP 통합시세 · UDP 유량제어 · TCP 송출 / 신규 업무*

## 패턴의 원칙

- 후선처리(2nd)용 PMR을 **추가로 구성**합니다 — 기존 노드를 고치지 않습니다.
- 업무 확장에 따른 주요 변경 리스크를 **2nd 흐름으로 제한·격리**합니다.
- **"메인시세 흐름에 영향을 주지 않는다"**가 최우선 규칙입니다.
- 레이아웃(서비스) 구성을 직접 변경·적용하면서 **업무를 숙달**합니다 — 낮은 리스크 구간이 곧 학습 구간.
- TCP 송출 등 신규 프로토콜도 2nd 체인에서 먼저 적용합니다.

## 신규 업무 추가 예 — custom01 노드

"체결 전문 중 특정 보드만, 가격 변동분이 있을 때만 기간계로 송출"이라는 요구는 2nd 체인 뒤에 custom01 노드를 붙여 해결합니다.

```lua
-- custom01 노드 파이프라인
inbound: {
    act 'filter', {offset:1,  length:2, filters:{'A3'}}   -- 체결 전문만
    act 'filter', {offset:14, length:2, filters:{'G1'}}   -- 대상 보드만
    act 'modify', {rule:'diff_a3_only'}                   -- 변동분만 통과·가공
    act 'emit'
}
```
