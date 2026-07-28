# 부록 1 - ML/AI 워크로드에서의 Cilium 최적화 전략

### 1. ML/AI 워크로드 특성

- **대용량 데이터 전송**: 모델 파라미터, 체크포인트, 데이터 셔플링 등으로 대역폭 요구가 큼
- **GPU 노드 집중 트래픽**: 특정 노드에 트래픽이 몰리기 쉬움
- **짧은 지연에 민감한 서비스**: 실시간 추론, 온라인 추천, 광고 서빙 등은 latency가 곧 품질

그래서 Cilium 튜닝은 **대역폭·지연·안정성** 세 축으로 보는 게 좋음.

### 2. 데이터패스 최적화 (필수 영역)

**1) 네이티브 라우팅 + kube-proxy 대체**

- **routingMode=native**, **kubeProxyReplacement=true** 설정으로
    - iptables 기반 kube-proxy 제거
    - eBPF 기반 L4 로드밸런싱 사용
- ML/AI처럼 노드 간 대량 통신이 많은 환경에서 지연과 CPU 오버헤드가 줄어듦

**2) eBPF host-routing 활성화**

- **bpf.hostLegacyRouting=false**
- 커널 레벨에서 라우팅을 처리해 hop 수와 오버헤드를 줄임

**3) socketLB 사용**

- **socketLB.enabled=true**
- 소켓 레벨에서 LB를 수행해 커넥션 처리 효율을 높임—gRPC, HTTP/2 기반 추론 서비스에 특히 유리

### 3. 대역폭·패킷 처리 최적화

**1) BIG TCP + 대용량 패킷**

- **enableIPv4BIGTCP**, **enableIPv6BIGTCP** 활성화
- MTU를 키워 한 패킷에 더 많은 데이터를 실어 나름
- 대규모 모델 파라미터 전송, 데이터 셔플링에 유리

**2) Bandwidth Manager + BBR**

- **bandwidthManager.enabled=true**, **bandwidthManager.bbr=true**
- BBR 혼잡 제어로 WAN/하이브리드 클라우드 환경에서 대역폭 활용 극대화

**3) XDP 활용 (가능하면)**

- DDoS 방어, 불필요한 트래픽 조기 드롭
- ML/AI 서비스 앞단에 붙여서 “진짜 유효한 요청만” 애플리케이션까지 올리도록 설계

### 4. 대규모 클러스터·세션 수 대비 튜닝

ML/AI 클러스터는 **동시 세션 수·연결 수**가 많아서 BPF 맵 사이즈 튜닝이 중요

- **bpf.ctTcpMax, bpf.ctAnyMax, bpf.natMax, bpf.lbMapMax, bpf.policyMapMax** 등 맵 크기 조정
- 예: 수만 개의 동시 추론 요청, 수천 개의 서비스 엔드포인트를 다루는 환경

추가로:

- **bpf.mapDynamicSizeRatio** 조정해서 메모리 사용과 맵 크기 균형 맞추기

### 5. GPU 노드·리소스 관점 최적화

**1) Cilium 에이전트 리소스 상향**

- GPU 노드나 트래픽 허브 노드에는
    - `resources.requests/limits`를 넉넉하게 설정
    - 에이전트가 CPU 부족으로 병목되지 않게 하기

**2) NUMA·CPU 핀닝**

- 고성능 ML/AI 노드에서는
    - Cilium, Envoy(L7 프록시), 애플리케이션 프로세스를 NUMA-aware하게 배치
    - 네트워크 인터럽트와 애플리케이션 스레드가 같은 NUMA 노드에 있도록 조정

이 부분은 Cilium 밖의 리눅스 튜닝이지만, **네트워크 지연 체감에 직접 영향** 준다.

### 6. 관측성(Hubble) 튜닝: “보되, 너무 많이 보지 말 것”

ML/AI 워크로드는 트래픽이 많아서, Hubble을 기본값으로 켜두면 오히려 성능을 깎을 수 있다.

- **hubble.metrics.enabled**를 필요한 것만 선택
    - 예: `dns,drop,tcp,flow` 정도로 제한
- **bpf.monitorAggregation, bpf.monitorInterval**로 이벤트 집계 수준 조정

즉,

> “디버깅 모드”와 “프로덕션 모드”를 분리해서 운영하는 게 좋음
> 

### 7. ML/AI 워크로드에 특화된 설계 팁

- **모델 서버 앞단에 Gateway API + Envoy**
    - gRPC/HTTP/2 기반 추론 트래픽을 L7에서 최적화
    - connection pooling, keep-alive로 커넥션 재사용 극대화
- **데이터 파이프라인은 L4 중심 설계**
    - 대용량 전송은 L4 LB + BIG TCP + BBR 조합으로 처리
- **실험용 클러스터와 프로덕션 클러스터 분리**
    - 실험 클러스터는 Hubble·로그·추적을 풍부하게
    - 프로덕션은 최소한의 관측성 + 최대 성능 프로파일 적용