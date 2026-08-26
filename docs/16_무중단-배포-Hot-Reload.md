# 무중단 배포 (Hot-Reload)

PMR의 변경 적용은 **Stop-and-Go가 아니라 Hot-Reloading**입니다. 레이아웃·룰·스크립트 변경이 메모리 상에서 즉시 반영되며, 세션은 유지됩니다.

## 레거시 대비 배포 흐름

<figure>
<svg width="820" height="200" viewBox="0 0 820 200" xmlns="http://www.w3.org/2000/svg" font-family="sans-serif">
  <defs><marker id="a5" markerWidth="8" markerHeight="8" refX="7" refY="4" orient="auto"><path d="M0,0 L8,4 L0,8 z" fill="#3d4a5c"/></marker></defs>
  <text x="20" y="34" font-size="13" font-weight="bold" fill="#c2402a">C 레거시 (Stop-and-Go)</text>
  <rect x="20" y="48" width="130" height="40" rx="6" fill="#fdf3ec" stroke="#c2402a"/><text x="85" y="73" text-anchor="middle" font-size="12" fill="#8c5040">소스 수정·컴파일</text>
  <rect x="200" y="48" width="130" height="40" rx="6" fill="#fdf3ec" stroke="#c2402a"/><text x="265" y="73" text-anchor="middle" font-size="12" fill="#8c5040">프로세스 종료</text>
  <rect x="380" y="48" width="130" height="40" rx="6" fill="#fdf3ec" stroke="#c2402a"/><text x="445" y="73" text-anchor="middle" font-size="12" fill="#8c5040">바이너리 교체</text>
  <rect x="560" y="48" width="130" height="40" rx="6" fill="#fdf3ec" stroke="#c2402a"/><text x="625" y="73" text-anchor="middle" font-size="12" fill="#8c5040">재시작·세션 재연결</text>
  <line x1="150" y1="68" x2="198" y2="68" stroke="#3d4a5c" stroke-width="1.5" marker-end="url(#a5)"/>
  <line x1="330" y1="68" x2="378" y2="68" stroke="#3d4a5c" stroke-width="1.5" marker-end="url(#a5)"/>
  <line x1="510" y1="68" x2="558" y2="68" stroke="#3d4a5c" stroke-width="1.5" marker-end="url(#a5)"/>
  <text x="740" y="73" font-size="11" fill="#c2402a">중단 발생</text>
  <text x="20" y="136" font-size="13" font-weight="bold" fill="#177245">PMR (Hot-Reloading)</text>
  <rect x="20" y="150" width="160" height="40" rx="6" fill="#eaf5ef" stroke="#177245"/><text x="100" y="175" text-anchor="middle" font-size="12" fill="#0e4d2f">스크립트·설정 수정</text>
  <rect x="230" y="150" width="160" height="40" rx="6" fill="#eaf5ef" stroke="#177245"/><text x="310" y="175" text-anchor="middle" font-size="12" fill="#0e4d2f">Preview 검증 (선택)</text>
  <rect x="440" y="150" width="160" height="40" rx="6" fill="#eaf5ef" stroke="#177245"/><text x="520" y="175" text-anchor="middle" font-size="12" fill="#0e4d2f">메모리 상 즉시 반영</text>
  <line x1="180" y1="170" x2="228" y2="170" stroke="#3d4a5c" stroke-width="1.5" marker-end="url(#a5)"/>
  <line x1="390" y1="170" x2="438" y2="170" stroke="#3d4a5c" stroke-width="1.5" marker-end="url(#a5)"/>
  <text x="650" y="175" font-size="11" fill="#177245">Zero-Downtime · 세션 유지</text>
</svg>
<figcaption>그림 6. 배포 절차 비교 — 코딩·컴파일·재시작 단계 자체가 사라짐</figcaption>
</figure>

## 운영 원칙

- **Intra-day 대응** — 장중에도 실시간 변경이 가능하므로 새벽·장후 작업에 묶이지 않습니다.
- **초 단위 롤백** — 배포 실패 시 설정을 이전 상태로 복구하는 것만으로 롤백이 완료됩니다.
- **Preview 선행** — 변경 전 [PMR Control](17_PMR-Control-관제.md)의 메시지 흐름 미리보기로 영향 범위를 확인합니다.
- **Secondary 우선 적용** — 리스크 있는 변경은 [2nd 레이어](21_2nd-레이어-격리-패턴.md)에 먼저 적용해 Primary를 보호합니다.
