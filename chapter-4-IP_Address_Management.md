# IP Address Management

## 📘 Chapter 4: IP Address Management — 핵심 요약

### 🎯 **챕터 목적**

Kubernetes에서 Pod와 Service에 IP를 어떻게 할당하고 관리하는지, 그리고 Cilium이 기존 CNI와 다른 방식으로 IPAM을 구현하는지 설명하는 챕터.
특히 **eBPF 기반의 IPAM**, **kube-proxy 제거 환경**, **IPv4/IPv6 듀얼 스택**, **Cluster Mesh 환경에서의 IP 관리** 같은 실전 요소들을 다룸.

## 🧩 주요 내용 요약

### 1) **Kubernetes에서의 기본 IPAM 개념**

- Pod는 고유한 IP를 갖고, 클러스터 내부에서 라우팅 가능해야 한다.
- 대부분의 CNI는 IPAM을 위해 별도의 IPAM 플러그인을 사용한다.
- 전통적 방식은 IP 충돌 방지, CIDR 관리, 노드별 IP 블록 할당 등을 포함한다.

### 2) **Cilium의 IPAM 아키텍처**

Cilium은 두 가지 주요 IPAM 모드를 제공한다

#### **① Cluster Scope IPAM**

- 전체 클러스터에서 단일 CIDR을 사용.
- 각 노드가 IP 블록을 자동으로 가져가서 Pod에 할당.
- IP 충돌을 방지하기 위해 Cilium이 중앙에서 상태를 관리.

#### **② Kubernetes Node Scope IPAM**

- Kubernetes가 노드별 Pod CIDR을 할당하고 Cilium은 그 범위 내에서 IP를 관리.
- kubeadm, GKE, EKS 등에서 흔히 사용되는 방식.

### 3) **Cilium의 IPAM 동작 방식**

- Cilium Agent가 각 노드에서 IP 블록을 관리.
- eBPF 기반으로 라우팅 테이블을 최소화하고, IP 할당 정보를 효율적으로 유지.
- IPAM 상태는 Kubernetes CRD로 저장되며, 분산 환경에서도 안정적.

### 4) **IPv4 / IPv6 / Dual-stack 지원**

챕터에서는 다음을 설명해:

- IPv4-only 환경 구성
- IPv6-only 환경 구성
- Dual-stack 환경에서 Pod가 두 개의 IP를 갖는 방식
- eBPF 기반 로드밸런싱이 IPv6에서도 동일하게 동작함

### 5) **Cluster Mesh 환경에서의 IPAM**

여러 클러스터를 연결할 때 IPAM이 복잡해지는데, Cilium은 이를 다음 방식으로 해결해:

- 각 클러스터는 고유한 CIDR을 사용
- Cilium은 클러스터 간 라우팅을 자동 구성
- eBPF 기반으로 NAT 없이 통신 가능

### 6) **IPAM 설정 실습**

챕터에서는 보통 다음 실습이 포함돼:

- `cilium install --set ipam.mode=cluster-pool`
- Pod CIDR 변경
- IPv6 활성화
- IP 블록 크기 조정
- IPAM 상태 확인 (`cilium ip list`, `cilium node list`)

### 7) **Troubleshooting**

- IP 부족 문제 해결
- 노드 간 CIDR 충돌
- Dual-stack 환경에서의 DNS 문제
- IPAM CRD 손상 시 복구 방법

## 🧠 이 챕터가 중요한 이유

- Cilium을 실제 운영 환경에서 사용하려면 IPAM을 제대로 이해해야 함.
- 특히 **대규모 클러스터**, **멀티 클러스터**, **IPv6**, **kube-proxy 제거 환경**에서는 IPAM이 핵심 요소.
- 이후 챕터에서 다루는 Load Balancing, Network Policy, Cluster Mesh의 기반이 되는 부분.