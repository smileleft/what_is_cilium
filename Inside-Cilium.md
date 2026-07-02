# Inside Cilium

## 📘 Chapter 2: *Inside Cilium* 요약

Chapter 2는 “Cilium이 내부적으로 어떻게 구성되고 동작하는가?”를 이해하기 위한 구조적 개요를 제공.
Cilium을 이루는 주요 컴포넌트들이 어떤 역할을 하고 서로 어떻게 상호작용하는지 설명하고 있슴.

## 🧩 1. Cilium at a Glance

Cilium은 Kubernetes 클러스터에서 **네트워킹, 보안, 관찰성(Observability)** 을 제공하는 eBPF 기반 플랫폼.
이 챕터에서는 Cilium을 구성하는 핵심 요소들을 소개하며, 각 요소가 전체 아키텍처에서 어떤 기능을 수행하는지 설명함.

## 🧠 2. The Cilium Agent

- 각 노드에서 실행되는 데몬
- 주요 역할:
    - eBPF 프로그램을 커널에 로드
    - 네트워크 정책 적용
    - 서비스 로드밸런싱 구현
    - 엔드포인트 상태 관리
- Kubernetes API 서버와 지속적으로 통신하며 Pod/Service 변경 사항을 반영함

## 🔌 3. The Cilium CNI Plugin

- Pod가 생성될 때 네트워크 인터페이스를 설정하는 CNI 플러그인
- Pod에 IP를 할당하고, 필요한 eBPF hook을 설치
- Pod 네트워크 초기화의 핵심 구성 요소

## 🛠️ 4. The Cilium Operator

- 클러스터 전체에서 동작하는 컨트롤 플레인 컴포넌트
- 주요 기능:
    - IPAM(주소 관리)
    - ENI 관리(AWS 등 클라우드 환경)
    - Cluster Mesh 구성 요소 관리
- Agent가 처리하기 어려운 클러스터 범위 작업을 담당

## 🧬 5. eBPF Programs and Maps

- Cilium의 핵심 기술
- eBPF 프로그램: 패킷 처리, 정책 적용, 로드밸런싱 등을 커널 레벨에서 수행
- eBPF Maps: 프로그램 간 공유 데이터 저장소 (예: 서비스 엔드포인트 목록, 정책 정보)
- 기존 iptables 기반보다 훨씬 빠르고 유연한 데이터패스 제공

## 💻 6. The Cilium CLI

- `cilium status`, `cilium connectivity test` 등 운영에 필요한 명령 제공
- 설치, 디버깅, 상태 점검에 필수적인 도구

## 🔭 7. Hubble (vHubble 포함)

- Cilium의 Observability 플랫폼
- 네트워크 흐름을 실시간으로 시각화
- L3/L4/L7 레벨에서 트래픽을 추적
- vHubble은 경량화된 버전으로 Agent 내부에서 동작

## 🧱 8. Envoy Proxy

- L7 기능(HTTP, gRPC 등)을 제공하기 위해 Envoy를 통합
- Cilium의 “sidecar-less service mesh” 구현의 핵심
- 트래픽 라우팅, L7 정책, TLS termination 등을 수행

## 🌐 9. DNS Proxy

- DNS 요청을 가로채고 정책을 적용
- FQDN 기반 정책(FQDN Policy)을 구현하기 위한 필수 요소

## 📝 Summary

Chapter 2는 Cilium의 전체 아키텍처를 구성하는 주요 컴포넌트들을 소개하며,
각 요소가 **eBPF 기반 네트워크 데이터패스**, **정책 적용**, **서비스 네트워킹**, **관찰성**, **클러스터 운영** 등에서 어떤 역할을 하는지 설명하고 있슴