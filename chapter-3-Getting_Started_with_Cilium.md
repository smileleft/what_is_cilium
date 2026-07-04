# Getting Started with Cilium

## 📘 Chapter 3: Getting Started with Cilium — 핵심 요약

### 🎯 **챕터 목적**

Cilium을 실제 Kubernetes 클러스터에 설치하고 기본 기능을 체험할 수 있도록 안내하는 **첫 실전 챕터**.
이전 챕터에서 배운 eBPF 개념과 Cilium의 아키텍처를 바탕으로, 실제 환경에서 Cilium이 어떻게 동작하는지 확인하는 단계.

## 🧩 **주요 내용 요약**

### 1) **Cilium 설치 준비**

- Kubernetes 클러스터가 필요하며, 최소 요구사항을 확인한다.
- Cilium은 다양한 환경에서 설치 가능:
    - Kind
    - Minikube
    - Kubeadm
    - Cloud-managed Kubernetes (EKS, GKE, AKS)
- 커널 버전과 eBPF 지원 여부 확인이 중요하다.

### 2) **Cilium 설치 방법**

일반적으로 다음 두 가지 방식이 소개된다:

#### **① Cilium CLI 설치**

- `cilium install` 명령으로 간단히 설치 가능.
- 설치 후 `cilium status`로 상태 확인.

#### **② Helm Chart 설치**

- 더 세밀한 설정이 필요한 경우 Helm 사용.
- 예: L7 proxy, Hubble, BGP, IPAM 설정 등.

### 3) **설치 후 기본 확인**

설치가 끝나면 다음을 점검한다:

- Cilium DaemonSet이 정상적으로 실행되는지
- Cilium이 kube-proxy를 대체하는지 여부
- eBPF map, program이 정상 로드되었는지
- Pod 간 통신이 정상인지

### 4) **기본 기능 테스트**

챕터에서는 보통 다음 기능을 실습한다:

#### **① 네트워크 정책 테스트**

- 기본적으로 모든 Pod 간 통신 허용
- CiliumNetworkPolicy 적용 후 트래픽 차단/허용 확인

#### **② Hubble 설치 및 관찰**

- Hubble UI 또는 CLI로 트래픽 흐름을 시각화
- eBPF 기반으로 L3/L4/L7 흐름을 실시간 관찰

#### **③ 서비스 로드밸런싱 확인**

- kube-proxy 없이 eBPF 기반으로 구현되는 로드밸런싱 확인

### 5) **Troubleshooting 기본**

- `cilium status`, `cilium sysdump` 사용법
- eBPF 프로그램이 로드되지 않을 때 확인해야 할 커널 설정
- CNI 충돌 문제 해결

## 🧠 **이 챕터가 중요한 이유**

- Cilium을 실제로 설치해 보면서 eBPF 기반 네트워킹이 어떻게 동작하는지 체감할 수 있다.
- 이후 챕터에서 다룰 고급 기능(Observability, Security, Mesh, Gateway)을 이해하기 위한 **기초 실습 단계**다.
- Kubernetes 네트워킹을 eBPF로 대체하는 구조를 직접 확인할 수 있다.

### 상세 실습 내용

- Cilium 설치 명령어 전체 흐름
- Hubble 사용법
- CiliumNetworkPolicy 예제
- kube-proxy replacement 예제