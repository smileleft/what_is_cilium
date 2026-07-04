# Cilium Datapath

## 📘 Chapter 5: Cilium Datapath — 핵심 요약

### 🎯 **챕터 목적**

Cilium이 기존 Linux 네트워킹 스택을 어떻게 확장하고, eBPF 프로그램을 이용해 패킷을 처리하는지 설명.
즉, **Pod → Pod**, **Pod → Service**, **Node → Node** 트래픽이 실제로 어떤 경로를 따라가고 어떤 eBPF 프로그램이 관여하는지를 설명.

## 🧩 주요 내용 요약

### 1) **Datapath란 무엇인가**

- Kubernetes 네트워킹에서 “datapath”는 패킷이 실제로 이동하는 경로와 그 경로에서 수행되는 처리 과정을 의미함.
- 기존 CNI는 iptables, conntrack, routing table을 조합해 datapath를 구성하지만,
- **Cilium은 eBPF 프로그램을 커널에 직접 로드하여 datapath를 구성**.

### 2) **Cilium datapath의 구성 요소**

Cilium datapath는 여러 eBPF hook에 프로그램을 삽입해 동작

#### **① TC(BPF) ingress/egress**

- Pod 네트워크 인터페이스에 붙는 주요 datapath
- 패킷 필터링, NAT, 정책 적용, 로드밸런싱 수행

#### **② XDP**

- NIC 레벨에서 초고속 패킷 처리
- DDoS 방어, 빠른 드롭, 일부 로드밸런싱에 사용

#### **③ Socket-level BPF**

- L7 정책, DNS 필터링, transparent proxying

#### **④ Cgroup BPF**

- 프로세스 기반 정책 적용

### 3) **Pod 간 통신 처리 흐름**

1. Pod에서 패킷 생성
2. veth pair를 통해 host 네임스페이스로 이동
3. TC ingress eBPF 프로그램이 패킷을 처리
4. 정책 검사 (NetworkPolicy)
5. 필요 시 NAT 또는 LB 수행
6. 목적지 Pod로 라우팅

이 과정에서 **iptables는 사용되지 않음**(kube-proxy replacement 모드 기준).

### 4) **Service Load Balancing**

Cilium은 eBPF 기반으로 kube-proxy 없이 서비스 로드밸런싱을 수행

- NodePort, ClusterIP, LoadBalancer 모두 지원
- conntrack 의존도 최소화
- 패킷 재작성(NAT)을 eBPF에서 수행
- DSR(Direct Server Return) 지원

### 5) **Network Policy Enforcement**

Cilium datapath는 L3/L4/L7 정책을 모두 eBPF에서 처리함

- L3/L4 정책은 TC ingress에서 처리
- L7 정책은 Envoy proxy 또는 socket-level BPF에서 처리
- 정책 결과는 eBPF map에 저장되어 빠르게 조회됨

### 6) **Encapsulation & Routing**

Cilium은 다양한 라우팅 모드를 지원

- **Direct routing**
    - eBPF 기반으로 ARP/NDP를 최적화
- **VXLAN**
    - 오버레이 네트워크 구성
- **Geneve**
    - 고급 오버레이 기능 지원

각 모드에서 datapath가 어떻게 달라지는지 설명

### 7) **Datapath Debugging**

datapath 문제를 디버깅하는 방법

- `cilium monitor`로 실시간 패킷 관찰
- `hubble observe`로 L3/L4/L7 흐름 확인
- eBPF map 상태 확인
- sysdump 수집

운영 환경에서 매우 유용하게 사용됨

## 🧠 이 챕터가 중요한 이유

- Cilium의 성능과 안정성의 핵심은 datapath에 있음.
- eBPF가 기존 iptables 기반 네트워킹을 어떻게 대체하는지 이해할 수 있슴.
- 이후 챕터에서 다루는 **security**, **observability**, **service mesh**, **cluster mesh**의 기반이 되는 구조를 설명하는 챕터

### 추가로 학습해야 할 내용

- TC eBPF 프로그램이 실제로 어떤 코드를 실행하는지
- kube-proxy replacement가 내부적으로 어떻게 동작하는지
- XDP를 사용하는 경우와 사용하지 않는 경우의 차이
- datapath 문제를 디버깅하는 실전 절차