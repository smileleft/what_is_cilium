# Service Networking

## 📘 Chapter 6: Service Networking — 핵심 요약

### 🎯 **챕터 목적**

Kubernetes의 Service 개념을 복습하고, Cilium이 eBPF 기반으로 **고성능·저지연·고효율** 서비스 로드밸런싱을 어떻게 구현하는지 설명하는 챕터.
특히 kube-proxy를 대체하는 구조가 중심.

## 🧩 주요 내용 요약

### 1) **Kubernetes Service 기본 개념 정리**

- ClusterIP
- NodePort
- LoadBalancer
- ExternalTrafficPolicy
- SessionAffinity

기존 kube-proxy가 iptables 또는 IPVS로 이를 처리하는 구조를 설명

### 2) **Cilium의 eBPF 기반 Service LB**

Cilium은 kube-proxy 없이 서비스 로드밸런싱을 수행할 수 있슴.
핵심은 **eBPF 프로그램이 커널에서 직접 패킷을 재작성(NAT)하고 라우팅**한다는 점

#### Cilium LB의 특징

- iptables 사용 안 함
- conntrack 의존도 최소화
- 빠른 failover
- DSR(Direct Server Return) 지원
- NodePort 성능 향상
- L7 LB와 연계 가능 (Envoy 기반)

### 3) **Service 처리 흐름**

챕터에서는 패킷이 서비스로 들어올 때 어떤 경로를 따라가는지 설명

1. 패킷이 노드에 도착
2. XDP 또는 TC ingress eBPF 프로그램이 패킷을 가로챔
3. 서비스 매핑(eBPF map)에서 백엔드 Pod 선택
4. NAT 수행
5. Pod로 전달
6. 응답 패킷은 DSR 또는 reverse NAT로 처리

이 과정은 기존 iptables 기반보다 훨씬 단순하고 빠름

### 4) **NodePort 구현 방식**

Cilium은 NodePort를 eBPF로 구현

- 모든 노드에서 동일한 NodePort 동작
- 외부 트래픽을 빠르게 처리
- XDP를 사용하면 더 빠른 패킷 드롭/포워딩 가능
- ExternalTrafficPolicy=Local도 지원

### 5) **LoadBalancer 구현**

Cilium은 클라우드 LB 없이도 자체 LB 기능을 제공할 수 있슴

- BGP를 통한 외부 광고
- MetalLB와 통합 가능
- eBPF 기반으로 NAT 처리
- L7 LB는 Envoy proxy와 통합

### 6) **Session Affinity**

Cilium은 session affinity를 eBPF map으로 구현

- 클라이언트 → 백엔드 Pod 매핑을 저장
- 빠른 lookup
- iptables보다 안정적

### 7) **Service Networking 설정 실습**

다음 실습이 포함됨

- kube-proxy 제거 모드로 Cilium 설치
- NodePort 테스트
- ExternalTrafficPolicy 설정
- DSR 모드 활성화
- BGP LB 구성
- Hubble로 서비스 트래픽 관찰

### 8) **Troubleshooting**

- 서비스가 특정 노드에서만 동작하는 문제
- NodePort가 응답하지 않는 문제
- DSR 모드에서 응답이 누락되는 문제
- BGP 라우팅 충돌
- eBPF map 손상 시 복구

## 🧠 이 챕터가 중요한 이유

- Cilium의 가장 큰 장점 중 하나가 **kube-proxy 제거**인데, 그 핵심을 설명
- eBPF 기반 LB는 성능·안정성·운영 편의성 모두에서 기존 CNI보다 우수함.
- 이후 챕터에서 다루는 **Gateway API**, **Service Mesh**, **Cluster Mesh**의 기반이 되는 구조를 이해할 수 있슴.

### 추가로 학슴해야 할 내용

- DSR 모드가 실제로 어떻게 동작하는지
- kube-proxy replacement의 내부 구조
- BGP LoadBalancer 구성 방법
- XDP 기반 LB의 성능 차이