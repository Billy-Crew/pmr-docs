# 핵심 용어

PMR 문서 전반에서 사용되는 기본 용어입니다. Kafka의 Broker·Topic·Producer·Consumer에 대응하는 개념이 무엇인지 함께 표기했습니다.

### 노드 (Node)

하나의 PMR 실행 인스턴스입니다. 단일 스탠드얼론 실행 파일(Single-Standalone Executable)로 배포되며, 각 노드는 [RESTful API](control.md)를 기본 제공합니다. 여러 노드를 조합해 수신·가공·QoS·송출 등 역할별 파이프라인을 구성합니다.

### 레이아웃 (Layout)

노드가 처리할 서비스 전체를 선언하는 최상위 설정 단위입니다. 예: `KOSPI_KOSDAQ 레이아웃`. 레이아웃 안에 여러 토픽이 정의되며, 레이아웃(서비스) 구성은 운영 중에도 직접 변경·적용할 수 있습니다.

### 토픽 (Topic)

메시지 흐름의 논리적 단위입니다. 예: `KS_f`(KOSPI 주식 체결), `KQ_f`(KOSDAQ 주식 체결), `KS_f_emit`(송출 단계). Kafka의 토픽이 "저장되는 이벤트 로그"라면, PMR의 토픽은 **액션 파이프라인이 실행되는 처리 스테이지**입니다. 토픽은 `inbound` 등의 방향별 액션 목록을 갖습니다.

### 채널 (Channel)

메시지가 드나드는 물리 입출력 지점입니다. `recv2r: { 217,218,219,220,221,256 }`처럼 수신 채널 번호 집합으로 선언하며, UDP 멀티캐스트·TCP 등 프로토콜별 송수신 채널을 `dict_channels` 설정으로 추가합니다.

### 액션 (Action)

토픽 안에서 메시지 한 건마다 순서대로 실행되는 처리 명령입니다. `log`, `route`, `proute`, `filter`, `modify`, `destinate`, `qos`, `process`, `emit` 등이 있습니다. → [액션 레퍼런스](actions-reference.md)

### 룰 (Rule)

액션에 주입하는 재사용 가능한 로직 단위입니다. 이름으로 참조하며 (`act 'modify', {rule:'wrsec1'}`), 함수형 룰은 Moonscript로 직접 작성합니다. → [룰](rules.md)

### VNT

PMR의 코어 런타임 엔진. libevent 기반 비동기 이벤트 루프와 LuaJIT 2.1 JIT 컴파일러의 조합으로, "Node.js like, but Lua"로 요약됩니다. → [VNT 런타임 엔진](vnt.md)

### Bolt-Log

모든 메시지를 유실 없이 기록하는 고성능 Key-Value 파일 DB. 랜덤 액세스와 2차 인덱스를 제한 없이 제공하며 리커버리·리플레이·부하 테스트의 기반입니다. → [Bolt-Log와 리플레이](boltlog.md)

### Kafka 용어 대응표

| Kafka | PMR | 차이점 |
|---|---|---|
| Broker (클러스터) | **노드 (파이프라인 조합)** | 저장 중심 클러스터가 아닌, 처리 역할별 경량 노드 체인 |
| Topic (이벤트 로그) | **토픽 (처리 스테이지)** | 저장 단위가 아니라 액션이 실행되는 흐름 단위 |
| Producer / Consumer | **채널 (recv / emit)** | 클라이언트 라이브러리 없이 UDP/TCP 채널 설정으로 연결 |
| Streams / ksqlDB | **액션 + 룰 (Moonscript)** | 별도 처리 클러스터 없이 라우터 내부에서 인라인 가공 |
| Log Retention / Replay | **Bolt-Log** | 2차 인덱스 기반 검색과 배속 재생(0.5×~20×) 내장 |
