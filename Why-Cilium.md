# Why Cilium? (왜 Cilium인가?)

## 🧭 Chapter 1 요약: 왜 Cilium인가?

### 🌐 Kubernetes 시대의 새로운 요구

Kubernetes는 현대 애플리케이션 운영의 표준 플랫폼이 되었고, 어떤 환경에서든 **네트워크 연결성, 보안, 가시성(Observability)**은 필수 요구사항이다.
하지만 기존 네트워크 기술(iptables 기반)은 **빠르게 변하는 대규모 컨테이너 환경**을 감당하기 어렵다.

## ⚙️ 초기 CNI의 한계

Flannel, Weave Net, Calico 같은 초기 CNI들은 기본적인 연결성은 제공했지만:

- iptables 기반이라 **규모가 커질수록 성능 저하**
- 지속적인 규칙 업데이트 필요 → **비효율적**
- Kubernetes의 동적 특성에 대응하기 어려움

이런 한계는 새로운 접근 방식의 필요성을 만들었다.

## 🚀 eBPF의 등장과 Cilium의 탄생

2014년 등장한 **eBPF**는 커널 내부에서 안전하게 프로그램을 실행할 수 있게 해주는 기술로,
리눅스 커널에 “슈퍼파워”를 부여했다고 평가된다.

eBPF는 다음을 가능하게 한다:

- 고성능 패킷 처리
- 커널 동작의 실시간 관찰 및 수정
- 안전한 확장성 (커널 모듈 없이 기능 추가)

Cilium은 바로 이 **eBPF 기반으로 설계된 네트워크·보안 플랫폼**이다.

## 📈 Cilium의 성장과 산업 채택

Cilium은 빠르게 발전하며 다음과 같은 기능을 추가했다:

- 멀티클러스터 네트워킹(Cluster Mesh)
- 투명한 암호화(Transparent Encryption)
- Hubble 기반 Observability
- 사이드카 없는 서비스 메시

그리고 다음과 같은 주요 클라우드에서 **기본 네트워크 스택으로 채택**되며 사실상 표준이 되었다:

- Google GKE Dataplane V2
- AWS EKS Anywhere
- Microsoft Azure CNI powered by Cilium

이로써 Cilium은 **대규모·고성능·멀티테넌트 환경에서도 검증된 선택지**가 되었다.

## 🏅 CNCF Graduated Project

2023년 Cilium은 CNCF에서 **Graduated** 단계로 승격되었다.
이는 다음을 의미한다:

- 높은 기술 성숙도
- 폭넓은 산업 채택
- 활발한 커뮤니티 기여
- 안정적인 거버넌스

현재 CNCF 네트워킹 분야에서 **유일한 Graduated 프로젝트**이다.

## 🔧 Cilium이 제공하는 주요 기능(Use Cases)

### 1) **고성능 CNI**

- 노드 간/노드 내 Pod 연결
- Overlay 또는 네이티브 라우팅 지원
- eBPF 기반 고성능 datapath

### 2) **Ingress & Gateway API**

- 외부 트래픽을 클러스터로 안전하게 유입
- Gateway API 완전 지원 → 별도 Ingress Controller 불필요

### 3) **사이드카 없는 서비스 메시**

- eBPF + Envoy 기반 L7 기능
- 사이드카 오버헤드 제거

### 4) **멀티클러스터 네트워킹**

- Cluster Mesh로 클러스터 간 서비스 연결 및 로드밸런싱

### 5) **보안 기능**

- 네트워크 정책
- 암호화
- ID 기반 보안 모델

### 6) **Observability (Hubble)**

- 실시간 네트워크 흐름 추적
- L3~L7 가시성 제공

## 📌 결론: 왜 Cilium인가?

Cilium은 Kubernetes 시대의 네트워킹·보안·관찰성 요구를 충족시키는 **현대적이고 확장 가능한 플랫폼**이다.
eBPF 기반의 고성능, 풍부한 기능, 대규모 운영 환경에서의 검증, 그리고 강력한 커뮤니티 덕분에
Cilium은 **클라우드 네이티브 네트워킹의 사실상 표준**으로 자리 잡았다.

필요하시면 **더 짧은 요약**, **핵심 포인트만 정리**, 또는 **그림으로 설명**도 만들어 드릴게요.