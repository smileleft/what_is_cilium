# What is Cilium?

이 저장소는 **《Cilium: Up and Running》** (Bill Mulligan, Nico Vibert 저, O'Reilly Media 출판)의 각 챕터 내용을 학습 목적으로 요약·정리한 자료입니다.

## 📖 저작권 안내 (Copyright Notice)

- 본 저장소의 모든 요약 문서는 **《Cilium: Up and Running》**의 내용을 참고하여 개인 학습 목적으로 정리한 것입니다.
- 원저작물의 저작권은 **O'Reilly Media, Inc.**에 있으며, 본 저장소는 원문의 상업적 재배포나 전재를 목적으로 하지 않습니다.
- 각 챕터 요약은 원문을 그대로 옮긴 것이 아니라 정리자의 이해를 바탕으로 재구성한 내용이므로, 원문과 차이가 있을 수 있습니다. 정확하고 완전한 내용은 반드시 원서를 참고하시기 바랍니다.
- 도서 정보: *Cilium: Up and Running — Coud Native Networking Powered by eBPF*, O'Reilly Media.

## 📚 챕터별 목차 및 주요 내용

| 챕터 | 파일 | 주요 내용 |
|---|---|---|
| 1 | [chapter-1-Why-Cilium.md](chapter-1-Why-Cilium.md) | Kubernetes 네트워킹의 한계, eBPF의 등장 배경, Cilium 탄생과 CNCF Graduated 프로젝트로서의 위상, Cilium이 제공하는 핵심 기능 개요 |
| 2 | [chapter-2-Inside-Cilium.md](chapter-2-Inside-Cilium.md) | Cilium의 내부 아키텍처, 주요 컴포넌트(Agent, Operator, CNI 플러그인 등) 구조와 동작 원리 |
| 3 | [chapter-3-Getting_Started_with_Cilium.md](chapter-3-Getting_Started_with_Cilium.md) | Cilium 설치 및 초기 설정, 클러스터에 배포하는 방법과 기본 동작 확인 |
| 4 | [chapter-4-IP_Address_Management.md](chapter-4-IP_Address_Management.md) | Cilium의 IPAM(IP Address Management) 동작 방식과 다양한 IPAM 모드 |
| 5 | [chapter-5-Cilium_Datapath.md](chapter-5-Cilium_Datapath.md) | eBPF 기반 데이터패스 구조, 패킷 처리 흐름과 성능 최적화 원리 |
| 6 | [chapter-6-Service_Networking.md](chapter-6-Service_Networking.md) | Kubernetes 서비스(Service) 구현 방식, 로드밸런싱과 kube-proxy 대체 메커니즘 |
| 7 | [chapter-7-Ingress_and_Gateway_API.md](chapter-7-Ingress_and_Gateway_API.md) | Ingress 및 Gateway API를 통한 외부 트래픽 유입 처리 방법 |
| 8 | [chapter-8_Performance_Networking_and_Traffic_Optimization.md](chapter-8_Performance_Networking_and_Traffic_Optimization.md) | 네트워크 성능 및 트래픽 최적화 기법, 대역폭 관리와 성능 튜닝 |
| 9 | [chapter-9_Multicluster_Networking.md](chapter-9_Multicluster_Networking.md) | Cluster Mesh를 이용한 멀티클러스터 네트워킹 및 서비스 연결 |
| 10 | [chapter-10-Cluster_Access.md](chapter-10-Cluster_Access.md) | 클러스터 접근 제어와 관련된 네트워킹 및 보안 구성 |
| 11 | [chapter-11-Cluster_Egress.md](chapter-11-Cluster_Egress.md) | 클러스터 외부로 나가는 트래픽(Egress) 제어 방법 |
| 12 | [chapter-12-Network_Policy.md](chapter-12-Network_Policy.md) | Cilium Network Policy를 통한 트래픽 접근 제어와 ID 기반 보안 모델 |
| 13 | [chapter-13-Layer7_and_FQDN_Policy.md](chapter-13-Layer7_and_FQDN_Policy.md) | L7(애플리케이션 계층) 정책 및 FQDN 기반 정책을 통한 세밀한 트래픽 제어 |
| 14 | [chapter-14-Transparent_Encryption.md](chapter-14-Transparent_Encryption.md) | IPsec, WireGuard 등을 활용한 투명한 트래픽 암호화 방식 |
| 15 | [chapter-15-Observability_with_Hubble.md](chapter-15-Observability_with_Hubble.md) | Hubble을 이용한 네트워크 가시성(Observability) 확보와 트래픽 모니터링 |
| 16 | [chapter-16-Operations.md](chapter-16-Operations.md) | Cilium 운영, 트러블슈팅, 업그레이드 등 실무 운영 관련 내용 |

## ⚠️ 참고 사항

- 본 요약은 학습과 참고를 위한 개인 정리 자료이며, 원서의 정확한 표현이나 최신 내용을 완전히 대체하지 않습니다.
- 실무에 적용하거나 정확한 인용이 필요한 경우 반드시 원서 및 Cilium 공식 문서(https://docs.cilium.io)를 확인하시기 바랍니다.

