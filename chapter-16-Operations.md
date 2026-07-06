# Operations

## 📘 Chapter 16 — Operations

### 🎯 핵심 메시지

**Cilium을 실제 프로덕션 환경에서 안정적으로 운영하기 위한 실전 운영 전략**을 다룬다.
즉, 설치·업그레이드·장애 대응·관측성·성능 튜닝·운영 자동화 같은 “운영자 관점의 Cilium”을 설명

## 🧩 1. 설치 및 업그레이드 전략

운영에서 가장 중요한 것은 **중단 없이 업그레이드**하는 것.

### 핵심 포인트

- **롤링 업그레이드**
    - Cilium DaemonSet을 하나씩 교체
    - 데이터패스(eBPF)와 컨트롤플레인을 순차적으로 업데이트
- **버전 호환성 확인**
    - 커널 버전
    - kube-proxy replacement 여부
    - eBPF 기능 지원 여부
- **업그레이드 전후 Hubble로 흐름 확인**
    - 정책이 깨지지 않았는지
    - L7 프록시(Envoy) 정상 동작 여부

“업그레이드가 네트워크 중단 없이 진행되는지”가 가장 중요

## 🚀 2. Cilium 구성 요소 운영

### 주요 구성 요소

- **Cilium Agent**
    - 각 노드에서 eBPF 데이터패스를 관리
- **Cilium Operator**
    - IPAM, ENI 관리, Cluster Mesh 등 제어
- **Hubble Relay / UI**
    - 관측성 제공
- **Envoy L7 프록시**
    - L7 정책 처리

운영자는 이 구성 요소들의 **상태·리소스·로그**를 지속적으로 모니터링해야 함

## 🔍 3. 장애 대응(Troubleshooting)

운영 챕터에서 가장 비중이 큰 부분.

### 주요 장애 유형

1. **정책 관련 문제**
    - 트래픽이 DROP되는 경우
    - 정책 충돌
    - L7 정책이 예상과 다르게 동작
2. **데이터패스 문제**
    - eBPF map 부족
    - 커널 기능 미지원
    - XDP 충돌
3. **네트워크 성능 문제**
    - 지연 증가
    - 패킷 드랍
    - L7 프록시 병목

### 해결 도구

- `cilium status`
- `cilium sysdump`
- `hubble observe`
- `cilium monitor`
- `cilium bpf` 명령어

운영자는 **sysdump**를 가장 많이 사용한다.
클러스터 전체 상태를 한 번에 수집해 분석할 수 있기 때문.

## 🛡️ 4. 보안 운영(Security Operations)

### 핵심 포인트

- **정책 변경 시 Hubble로 검증**
- **Clusterwide 정책으로 기본 차단 유지**
- **암호화(IPsec/WireGuard) 상태 모니터링**
- **Egress 정책으로 외부 접근 통제**

운영자는 “정책이 의도대로 적용되는지”를 항상 확인해야 함

## 📡 5. 성능 운영(Performance Operations)

### 주요 성능 튜닝 포인트

- eBPF map 크기 조정
- BIG TCP 활성화
- Bandwidth Manager + BBR
- kube-proxy replacement 모드 최적화
- Envoy 리소스 조정

운영자는 **노드별 Cilium Agent CPU 사용량**을 특히 주의 깊게 봐야 함.

## 🧭 6. 운영 자동화

### 자동화 포인트

- Cilium 설치/업그레이드 GitOps 관리
- 정책 변경 자동화
- Hubble 기반 자동 경고(Alerting)
- sysdump 자동 수집
- 멀티클러스터 운영 자동화

Cilium을 “수동 운영”하는 것이 아니라 **GitOps + 자동화 기반으로 운영하는 것을 권장**.

## 📝 Chapter 16 핵심 요약

- Cilium을 프로덕션에서 안정적으로 운영하기 위한 실전 운영 전략
- 롤링 업그레이드, 버전 호환성, 구성 요소 모니터링이 핵심
- 장애 대응은 정책·데이터패스·성능 문제 중심
- 보안 운영은 Zero Trust 정책 검증이 핵심
- 성능 운영은 eBPF·Envoy·BIG TCP·BBR 등 튜닝
- GitOps 기반 자동화 운영을 강하게 권장

원하면 다음으로

- **Chapter 16에서 소개된 운영 체크리스트**
- **ML/AI 워크로드 운영에 특화된 Cilium 운영 전략**
- **운영 자동화 템플릿(GitOps 기반)**