# VNT 런타임 엔진

**VNT(eVeNT)**는 PMR의 코어 엔진입니다. libevent/libuv 계열의 검증된 비동기 이벤트 루프에 LuaJIT 2.1 JIT 컴파일러를 결합한 구조로, 한 문장으로 **"Node.js like, but Lua"**입니다.

## 비동기 이벤트 루프

- **Libevent / Libuv battle-tested core** 기반
- **Non-Blocking I/O** — epoll, kqueue 등 OS 네이티브 I/O 폴링
- **C/C++ Native Functions** 직접 호출
- **Single-Standalone Executable** — 단일 실행 파일 배포

<figure markdown="span">
<svg viewBox="0 0 840 260" xmlns="http://www.w3.org/2000/svg" font-family="sans-serif">
  <defs><marker id="a2" markerWidth="8" markerHeight="8" refX="7" refY="4" orient="auto"><path d="M0,0 L8,4 L0,8 z" fill="#3d4a5c"/></marker></defs>
  <rect x="30" y="95" width="130" height="66" rx="8" fill="#f5f7f9" stroke="#cdd5de"/>
  <text x="95" y="123" text-anchor="middle" font-size="13" font-weight="bold" fill="#141d29">Event Queue</text>
  <text x="95" y="142" text-anchor="middle" font-size="11" fill="#69788c">수신 이벤트 적재</text>
  <circle cx="330" cy="128" r="52" fill="#eef4fb" stroke="#0a4fa8" stroke-width="2"/>
  <text x="330" y="123" text-anchor="middle" font-size="13" font-weight="bold" fill="#0a4fa8">Event</text>
  <text x="330" y="141" text-anchor="middle" font-size="13" font-weight="bold" fill="#0a4fa8">Loop</text>
  <rect x="530" y="20" width="180" height="58" rx="8" fill="#fff" stroke="#8a6d1f"/>
  <text x="620" y="44" text-anchor="middle" font-size="12.5" font-weight="bold" fill="#8a6d1f">Task Queue</text>
  <text x="620" y="63" text-anchor="middle" font-size="11" fill="#69788c">Blocking ops → Worker Pool</text>
  <rect x="530" y="100" width="180" height="58" rx="8" fill="#fff" stroke="#177245"/>
  <text x="620" y="124" text-anchor="middle" font-size="12.5" font-weight="bold" fill="#177245">Non-blocking ops</text>
  <text x="620" y="143" text-anchor="middle" font-size="11" fill="#69788c">즉시 콜백 처리</text>
  <rect x="530" y="180" width="180" height="58" rx="8" fill="#fff" stroke="#0a4fa8"/>
  <text x="620" y="204" text-anchor="middle" font-size="12.5" font-weight="bold" fill="#0a4fa8">I/O Polling</text>
  <text x="620" y="223" text-anchor="middle" font-size="11" fill="#69788c">epoll · kqueue (libevent)</text>
  <rect x="742" y="30" width="80" height="38" rx="6" fill="#f5f7f9" stroke="#cdd5de"/>
  <text x="782" y="49" text-anchor="middle" font-size="10.5" fill="#3d4a5c">Worker</text>
  <text x="782" y="61" text-anchor="middle" font-size="10.5" fill="#3d4a5c">Thread Pool</text>
  <line x1="160" y1="128" x2="276" y2="128" stroke="#3d4a5c" stroke-width="1.8" marker-end="url(#a2)"/>
  <line x1="372" y1="103" x2="528" y2="52" stroke="#3d4a5c" stroke-width="1.6" marker-end="url(#a2)"/>
  <line x1="382" y1="128" x2="528" y2="128" stroke="#3d4a5c" stroke-width="1.6" marker-end="url(#a2)"/>
  <line x1="372" y1="153" x2="528" y2="206" stroke="#3d4a5c" stroke-width="1.6" marker-end="url(#a2)"/>
  <line x1="710" y1="49" x2="740" y2="49" stroke="#3d4a5c" stroke-width="1.4" marker-end="url(#a2)"/>
</svg>
<figcaption>그림 3. VNT 비동기 이벤트 루프 — Blocking 작업은 워커 풀로, I/O는 OS 네이티브 폴링으로 위임</figcaption>
</figure>

## JIT Powered

- **LuaJIT 2.1** 기반 Trace JIT
- **FFI** (Foreign Function Interface) — C 구조체·함수를 제로 오버헤드로 바인딩
- **Extension: Native Function Slots** — C/C++ 네이티브 함수 슬롯 확장

<figure markdown="span">
<svg viewBox="0 0 760 120" xmlns="http://www.w3.org/2000/svg" font-family="sans-serif">
  <defs><marker id="a3" markerWidth="8" markerHeight="8" refX="7" refY="4" orient="auto"><path d="M0,0 L8,4 L0,8 z" fill="#3d4a5c"/></marker></defs>
  <rect x="20" y="34" width="130" height="54" rx="7" fill="#fff" stroke="#cdd5de"/>
  <text x="85" y="57" text-anchor="middle" font-size="13" font-weight="bold" fill="#141d29">.lua / .moon</text>
  <text x="85" y="76" text-anchor="middle" font-size="11" fill="#69788c">스크립트 소스</text>
  <rect x="230" y="34" width="130" height="54" rx="7" fill="#fff" stroke="#cdd5de"/>
  <text x="295" y="57" text-anchor="middle" font-size="13" font-weight="bold" fill="#141d29">.bc</text>
  <text x="295" y="76" text-anchor="middle" font-size="11" fill="#69788c">바이트코드</text>
  <rect x="440" y="34" width="130" height="54" rx="7" fill="#eef4fb" stroke="#0a4fa8"/>
  <text x="505" y="57" text-anchor="middle" font-size="13" font-weight="bold" fill="#0a4fa8">JIT Compiler</text>
  <text x="505" y="76" text-anchor="middle" font-size="11" fill="#69788c">Trace JIT (런타임)</text>
  <rect x="630" y="34" width="115" height="54" rx="7" fill="#eaf5ef" stroke="#177245"/>
  <text x="687" y="57" text-anchor="middle" font-size="13" font-weight="bold" fill="#177245">Native Code</text>
  <text x="687" y="76" text-anchor="middle" font-size="11" fill="#69788c">C/C++ 급 속도</text>
  <line x1="150" y1="61" x2="228" y2="61" stroke="#3d4a5c" stroke-width="1.6" marker-end="url(#a3)"/>
  <text x="189" y="52" text-anchor="middle" font-size="10.5" fill="#69788c">Compiler</text>
  <line x1="360" y1="61" x2="438" y2="61" stroke="#3d4a5c" stroke-width="1.6" marker-end="url(#a3)"/>
  <line x1="570" y1="61" x2="628" y2="61" stroke="#3d4a5c" stroke-width="1.6" marker-end="url(#a3)"/>
</svg>
<figcaption>그림 4. 스크립트 → 바이트코드 → 런타임 JIT 네이티브 코드 변환 흐름</figcaption>
</figure>

## VNT vs Node.js

VNT는 Node.js와 같은 "이벤트 루프 + 웹 스크립트" 패러다임을 따르되, C/C++ 네트워크 애플리케이션을 대체하기 위한 경량·저지연 특성에 최적화되어 있습니다.

| 항목 | Node.js | VNT | 비고 |
|---|---|---|---|
| **방식** | Event Loop with Web-Script | Event Loop with Web-Script | 경량 이벤트 루프 |
| **Script** | JavaScript | Lua (Moonscript) | 웹 스크립트 |
| **JIT Core** | V8 | LuaJIT | 경량 메모리, C/C++에 준하는 실행 속도 |
| **Biz. Section** | Web Application | Network Application | C/C++ 네트워크 앱 대체 용도 |
| **Performance** | Method JIT | Trace JIT |  |
| **Memory Overhead** | 수십 ~ 100 MB 이상 | **10 MB 이하** | 경량 메모리 풋프린트 |
| **C Native Binding** | N-API, Wrapping | **FFI, Zero-Overhead** | C/C++ 리소스와 동일 수준의 바인딩 |
| **Runtime Size** | 수십 MB 이상 | **1 MB 이하** | C/C++ 수준의 경량 런타임 |
| **Raw C Data Access** | JS 추상화 필요 | **Native** | C Structure에 직접 액세스 |

!!! note "왜 Lua인가?"
    시세 전문은 고정 오프셋의 C 구조체 바이너리입니다. LuaJIT FFI는 이 구조체에 추상화 계층 없이 직접 접근하므로, 스크립트 언어의 생산성과 C의 처리 속도를 동시에 얻습니다. → [C 데이터 타입 다루기](cdata.md)

## 제품군에서의 위치

VNT는 PMR뿐 아니라 통합시세 엔진 **KQP**, 프로그래머블 OMS **POMS**, AI 기반 슬리피지 최적화 **DeepVWAP(AI-OMS)**의 공통 기반 기술입니다.
