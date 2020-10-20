---
title: Azure Kubernetes Service에 대 한 azure 보안 기준
description: Azure Kubernetes Service 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: container-service
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 303720cf50b5eabf22319e1eeae7423c4f1d94bb
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92201701"
---
# <a name="azure-security-baseline-for-azure-kubernetes-service"></a>Azure Kubernetes Service에 대 한 azure 보안 기준

Azure Kubernetes Service에 대 한 Azure 보안 기준에는 배포의 보안 상태를 개선 하는 데 도움이 되는 권장 사항이 포함 되어 있습니다.

이 서비스의 기준은 [Azure Security Benchmark 버전 1.0](../security/benchmarks/overview.md)에서 가져왔으며, 모범 사례 지침을 통해 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 추천 사항을 제공합니다.

자세한 내용은 [Azure 보안 기준 개요](/azure/security/benchmarks/security-baselines-overview)를 참조하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [Azure 보안 벤치 마크: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조 하세요.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: 기본적으로 네트워크 보안 그룹 및 경로 테이블은 AKS (Microsoft Azure Kubernetes Service) 클러스터를 만들 때 자동으로 만들어집니다. AKS는 부하 분산 장치, 포트 매핑 또는 수신 경로를 사용 하 여 서비스를 만들 때 적절 한 트래픽 흐름에 대 한 네트워크 보안 그룹을 자동으로 수정 합니다. 네트워크 보안 그룹은 고객 노드의 가상 Nic와 가상 네트워크의 서브넷을 포함 하는 경로 테이블에 자동으로 연결 됩니다. 

AKS 네트워크 정책을 사용 하 여 선택 된 네임 스페이스 및 레이블 선택기에 따라 클러스터에서 Linux pod 간의 수신 및 송신 트래픽에 대 한 규칙을 정의 하 여 네트워크 트래픽을 제한 합니다. 네트워크 정책 사용에는 정의 된 가상 네트워크 및 서브넷이 있는 Azure CNI 플러그 인이 필요 하며 클러스터를 만들 때만 사용할 수 있습니다. 기존 AKS 클러스터에 배포할 수 없습니다.

AKS API 서버와 노드 풀 간의 네트워크 트래픽이 개인 네트워크에만 남아 있도록 개인 AKS 클러스터를 구현할 수 있습니다. 제어 평면이 나 API 서버는 AKS로 관리 되는 Azure 구독에 상주 하 고 내부 (RFC1918) IP 주소를 사용 하는 반면, 고객의 클러스터 또는 노드 풀은 자체 구독에 있습니다. 서버와 클러스터 또는 노드 풀은 API 서버 가상 네트워크에서 Azure 개인 링크 서비스를 사용 하 여 서로 통신 하 고, 고객의 AKS 클러스터의 서브넷에 노출 되는 개인 끝점을 사용 합니다.  또는 AKS API 서버에 대 한 공용 끝점을 사용 하지만 AKS API 서버의 권한 있는 IP 범위 기능을 사용 하 여 액세스를 제한 합니다. 

- [AKS(Azure Kubernetes Service)의 애플리케이션 및 클러스터에 대한 보안 개념](concepts-security.md)

- [AKS(Azure Kubernetes Service)에서 네트워크 정책을 사용하여 pod 간 트래픽 보호](use-network-policies.md)

- [프라이빗 Azure Kubernetes Service 클러스터 만들기](private-clusters.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: 가상 네트워크, 서브넷 및 Nic의 구성과 트래픽을 모니터링 하 고 기록 합니다.

**지침**: Security Center을 사용 하 고 네트워크 보호 권장 사항을 따라 AKS (Azure Kubernetes Service) 클러스터에서 사용 되는 네트워크 리소스를 보호 합니다. 

네트워크 보안 그룹 흐름 로그를 사용 하도록 설정 하 고 감사를 위해 로그를 Azure Storage 계정으로 보냅니다. 또한 흐름 로그를 Log Analytics 작업 영역으로 보낸 다음 트래픽 분석를 사용 하 여 Azure 클라우드의 트래픽 패턴에 대 한 통찰력을 제공 하 여 네트워크 활동을 시각화 하 고, 핫 스폿 및 보안 위협을 식별 하 고, 트래픽 흐름 패턴을 이해 하 고, 네트워크 구성을 잘못 파악할 수 있습니다.

- [Azure Security Center에서 제공 하는 네트워크 보안 이해](../security-center/security-center-network-recommendations.md)

- [네트워크 보안 흐름 로그를 사용 하도록 설정 하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [트래픽 분석을 사용하도록 설정하고 사용하는 방법](../network-watcher/traffic-analytics.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: 웹 응용 프로그램에 대 한 들어오는 트래픽을 필터링 하 여 추가 보안 계층을 제공 하기 위해 AKS 클러스터 앞에 Azure 애플리케이션 Gateway를 사용 하도록 설정 된 Waf (웹 응용 프로그램 방화벽)를 사용 합니다. Azure WAF는 OWASP (Open Web Application Security Project)에서 제공 하는 규칙 집합을 사용 하 여 사이트 간 스크립팅 또는이 트래픽에 대 한 쿠키 손상 등의 공격을 수행 합니다. 

AKS 환경에서 사용 되는 Api에 대 한 인증, 권한 부여, 제한, 캐싱, 변환 및 모니터링에 API 게이트웨이를 사용 합니다. API 게이트웨이는 마이크로 서비스에 대 한 전면 도어 역할을 하며, 마이크로 서비스에서 클라이언트를 분리 하 고, 크로스 절삭 문제를 처리 하는 부담을 제거 하 여 마이크로 서비스의 복잡성을 줄입니다.

- [AKS의 네트워크 연결 및 보안에 대 한 모범 사례 이해](operator-best-practices-network.md)

- [Application Gateway 수신 컨트롤러 ](../application-gateway/ingress-controller-overview.md)

- [Azure Kubernetes Service에 배포 된 마이크로 서비스에서 Azure API Management 사용](../api-management/api-management-kubernetes.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: DDoS 공격 으로부터 보호 하기 위해 AKS (Azure Kubernetes service) 구성 요소가 배포 되는 가상 네트워크에서 Microsoft Distributed DDoS (서비스 거부) 표준 보호를 사용 하도록 설정 합니다.
네트워크 정책 엔진을 설치 하 고 Kubernetes 네트워크 정책을 만들어 AKS에서 pod 간의 트래픽 흐름을 제어 합니다. 기본적으로 이러한 pod 간에 모든 트래픽이 허용 됩니다. 네트워크 정책은 Linux 기반 노드와 Azure Kubernetes Service의 pod에만 사용해야 합니다. 보안 향상을 위해 pod 통신을 제한 하는 규칙을 정의 합니다. 

할당 된 레이블, 네임 스페이스, 트래픽 포트 등의 설정에 따라 트래픽을 허용 하거나 거부 하도록 선택 합니다. Pod가 AKS 클러스터에 동적으로 생성 되 면 필요한 네트워크 정책을 자동으로 적용할 수 있습니다. 

- [AKS(Azure Kubernetes Service)에서 네트워크 정책을 사용하여 pod 간 트래픽 보호](use-network-policies.md)

- [DDoS 보호를 구성 하는 방법](../virtual-network/manage-ddos-protection.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷을 기록 합니다.

**지침**: 비정상적인 활동을 조사 하는 데 필요한 대로 Network Watcher 패킷 캡처를 사용 합니다. 

Network Watcher는 구독에서 가상 네트워크를 만들거나 업데이트할 때 가상 네트워크 지역에서 자동으로 사용 하도록 설정 됩니다. PowerShell, Azure CLI, REST API 또는 ARMClient 메서드를 사용 하 여 Network Watcher의 새 인스턴스를 만들 수도 있습니다.

- [Network Watcher를 사용하도록 설정하는 방법](../network-watcher/network-watcher-create.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템 (IDS/IPS) 배포

**지침**: Waf (웹 응용 프로그램 방화벽)를 사용 하 여 Azure 애플리케이션 Gateway를 사용 하 여 AKS (Azure Kubernetes Service) 클러스터를 보호 합니다. 

페이로드 검사 또는 동작 분석을 기반으로 하는 침입 탐지 및/또는 방지가 요구 사항이 아닌 경우 WAF를 사용 하는 Azure 애플리케이션 게이트웨이를 "검색 모드"에서 사용 하 고 구성 하 여 경고 및 위협을 기록 하거나 "방지 모드"를 사용 하 여 검색 된 침입 및 공격을 적극적으로 차단할 수 있습니다.

- [WAF를 사용 하 여 AKS 클러스터를 보호 하기 위한 모범 사례 이해](operator-best-practices-network.md#secure-traffic-with-a-web-application-firewall-waf)

- [Azure 애플리케이션 게이트웨이를 배포 하는 방법 (Azure WAF)](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침**: AKS 클러스터 앞에 Azure 애플리케이션 게이트웨이 사용 웹 응용 프로그램 방화벽 (waf)을 사용 하 여 들어오는 트래픽을 필터링 합니다. OWASP (Open Web Application Security Project)는 Azure WAF에서 사이트 간 스크립팅 또는 쿠키 손상 등의 공격을 감시 하는 데 사용 되는 규칙 집합을 제공 합니다.

네트워크 보안 그룹 내에서 응용 프로그램 규칙을 설정 하는 데 사용 하기 쉽도록 응용 프로그램에 FQDN (정규화 된 도메인 이름) 태그를 적용 합니다. 네트워크 규칙을 설정한 후 FQDN 태그를 사용 하 여 응용 프로그램 규칙을 추가 합니다 (예: AzureKubernetesService). 여기에는 TCP 포트 443 및 포트 80을 통해 액세스할 수 있는 모든 Fqdn이 포함 됩니다. 

- [AKS의 네트워크 연결 및 보안에 대 한 모범 사례 이해](operator-best-practices-network.md)

- [AKS(Azure Kubernetes Service)에서 네트워크 정책을 사용하여 pod 간 트래픽 보호](use-network-policies.md)

- [Azure 애플리케이션 게이트웨이를 배포 하는 방법 (Azure WAF)](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: 가상 네트워크 서비스 태그를 사용 하 여 AKS (Azure Kubernetes service) 인스턴스와 연결 된 네트워크 보안 그룹에 대 한 네트워크 액세스 제어를 정의 합니다. 서비스 태그 이름을 지정 하 여 해당 서비스에 대 한 트래픽을 허용 하거나 거부 하는 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 

Microsoft는 서비스 태그에 포함되는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트합니다.

AKS 클러스터의 노드 풀에 Azure 태그를 적용 합니다. 이러한 서비스는 가상 네트워크 서비스 태그와 다르며 노드 풀 내의 각 노드에 적용 되 고 업그레이드를 통해 지속 됩니다. 

- [서비스 태그 이해 및 사용](../virtual-network/service-tags-overview.md)

- [AKS에 대 한 NSGs 이해](support-policies.md)

- [AKS(Azure Kubernetes Service)에서 클러스터 노드의 송신 트래픽 제어](limit-egress-traffic.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지 관리

**지침**: AKS (Azure Kubernetes Service) 클러스터와 연결 된 네트워크 리소스에 대 한 Azure Policy를 사용 하 여 표준 보안 구성을 정의 하 고 구현 합니다. "ContainerService" 및 "AKS" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 사용자 지정 정책을 만들어 클러스터의 네트워크 구성을 감사 하거나 적용 합니다. 

또한 다음과 같이 AKS와 관련 된 기본 제공 정책 정의를 사용 합니다.

• 권한 있는 IP 범위는 Kubernetes Services에서 정의 해야 합니다.

• Kubernetes 클러스터에서 HTTPS 수신 적용

• 서비스가 Kubernetes 클러스터의 허용 된 포트 에서만 수신 하도록 합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [네트워킹에 대 한 Azure Policy 샘플](../governance/policy/samples/built-in-policies.md#network)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: 트래픽 구성 규칙 문서화

**지침**: Azure Kubernetes 서비스 (AKS) 클러스터 간 트래픽 흐름에 대 한 네트워크 보안 그룹 및 기타 리소스에 대 한 태그를 사용 합니다. 개별 네트워크 보안 그룹 규칙에 대 한 "설명" 필드를 사용 하 여 네트워크에서 들어오고 나가는 트래픽을 허용 하는 모든 규칙에 대 한 비즈니스 요구 및/또는 기간을 지정할 수 있습니다.
모든 리소스를 태그를 사용 하 여 만들고 태그가 지정 되지 않은 기존 리소스에 대 한 알림을 받으려면 기본 제공 Azure Policy 태그 관련 정의 (예: "태그 및 해당 값 필요")를 사용 합니다.

네트워크 정책을 사용 하 여 네임 스페이스 및 레이블 선택기에 따라 클러스터 내의 특정 네트워크 경로를 허용 하거나 거부 하도록 선택 합니다. 이러한 네임 스페이스 및 레이블을 트래픽 구성 규칙에 대 한 설명자로 사용 합니다. Azure PowerShell 또는 Azure CLI (명령줄 인터페이스)를 사용 하 여 태그를 기준으로 리소스에 대 한 작업을 조회 하거나 수행할 수 있습니다.

- [CLI를 사용 하 여 Azure Policy](/cli/azure/policy?view=azure-cli-latest)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

- [보안 구성을 사용하여 NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용 하 여 네트워크 리소스 구성을 모니터링 하 고 AKS (Azure Kubernetes Service) 클러스터와 관련 된 네트워크 리소스에 대 한 변경 내용을 검색 합니다. 

Azure Monitor 내에서 중요한 네트워크 리소스가 변경되면 트리거되는 경고를 만듭니다. 활동 로그에 있는 AzureContainerService 사용자의 모든 항목은 플랫폼 작업으로 기록 됩니다. 

Azure Monitor 로그를 사용 하 여 마스터 구성 요소인 kube-apiserver 및 kube에서 로그를 사용 하도록 설정 하 고 쿼리 합니다. 컨테이너 런타임을 사용 하 여 kubelet를 실행 하 고 관리 되는 Kubernetes API 서버를 통해 응용 프로그램을 배포 하는 노드를 만들고 관리 합니다. 

- [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Azure Monitor에서 경고를 만드는 방법](../azure-monitor/platform/alerts-activity-log.md)

- [AKS(Azure Kubernetes Service)에서 Kubernetes 마스터 노드 로그 활성화 및 검토](view-master-logs.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure 보안 벤치 마크: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조 하세요.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 원본 사용

**지침**: AKS (Azure Kubernetes Service) 노드는 UDP 포트 123 및 Ntp (Network time Protocol)와 함께 시간 동기화에 ntp.ubuntu.com를 사용 합니다. 

사용자 지정 DNS 서버를 사용 하는 경우 클러스터 노드에서 NTP 서버에 액세스할 수 있는지 확인 합니다. 

- [AKS 클러스터 노드에 대 한 NTP 도메인 및 포트 요구 사항 이해](limit-egress-traffic.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: 관리 서비스로 제공 되는 AKS (Azure Kubernetes Services) 마스터 구성 요소, kube-apiserver 및 kube-manager에서 감사 로그를 사용 하도록 설정 합니다. 

• kube-auditaksService: hcpService의 제어 평면 작업에 대 한 감사 로그의 표시 이름 

• masterclient: MasterClientCertificate에 대 한 감사 로그의 표시 이름, az aks get 자격 증명에서 가져온 인증서 

• nodeclient: 에이전트 노드에서 사용 되는 ClientCertificate에 대 한 표시 이름

Kube 등의 다른 감사 로그도 사용 하도록 설정 합니다. 

Log Analytics 또는 다른 저장소 플랫폼으로 이러한 로그를 내보냅니다. Azure Monitor에서 Log Analytics 작업 영역을 사용 하 여 분석을 쿼리하고 수행 하 고 장기 및 보관 저장소에 Azure Storage 계정을 사용할 수 있습니다.

조직의 비즈니스 요구 사항에 따라이 데이터를 Azure 센티널 또는 타사 SIEM에 사용 하도록 설정 합니다.

- [여기에 로그 역할을 포함 하 여 로그 스키마를 검토 합니다.](view-master-logs.md)

- [컨테이너에 대 한 Azure Monitor 이해](../azure-monitor/insights/container-insights-overview.md)

- [컨테이너에 대해 Azure Monitor를 사용 하도록 설정 하는 방법](../azure-monitor/insights/container-insights-onboard.md)

- [AKS(Azure Kubernetes Service)에서 Kubernetes 마스터 노드 로그 활성화 및 검토](view-master-logs.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: 활동 로그를 사용 하 여 AKS (Azure Kubernetes Service) 리소스의 작업을 모니터링 하 여 모든 활동 및 해당 상태를 볼 수 있습니다. 활동 로그를 사용 하 여 구독의 리소스에서 수행 된 작업을 확인 합니다. 작업을 시작한 사람

작업이 발생한 시간

작업의 상태

작업을 조사하는 데 도움이 될 수 있는 기타 속성 값

Azure PowerShell, Azure CLI (명령줄 인터페이스), Azure REST API 또는 Azure Portal를 통해 활동 로그에서 정보를 검색 합니다. 

다음과 같이 AKS 마스터 구성 요소에서 감사 로그를 사용 하도록 설정 합니다. 

• kube-auditaksService: hcpService의 제어 평면 작업에 대 한 감사 로그의 표시 이름 

• masterclient: MasterClientCertificate에 대 한 감사 로그의 표시 이름, az aks get 자격 증명에서 가져온 인증서 

• nodeclient: 에이전트 노드에서 사용 되는 ClientCertificate에 대 한 표시 이름

Kube 등의 다른 감사 로그를 설정 합니다. 

- [AKS에서 Kubernetes 마스터 노드 로그를 사용 하도록 설정 하 고 검토 하는 방법](view-master-logs.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: AKS 클러스터 노드에서 데이터를 수집 하기 위해 Log Analytics 에이전트의 자동 설치를 사용 하도록 설정 합니다. 또한 기본적으로 자동 프로 비전이 해제 되어 Azure Security Center에서 Azure Log Analytics 모니터링 에이전트의 자동 프로 비전을 설정 합니다. 에이전트를 수동으로 설치할 수도 있습니다. 에서 자동 프로비저닝을 사용 하면 지원 되는 모든 Azure Vm 및 새로 만든 Vm에 Log Analytics 에이전트를 배포 Security Center. Security center는 Azure Virtual Machines (VM), 가상 머신 확장 집합 및 IaaS 컨테이너 (예: Kubernetes 클러스터 노드)에서 보안 취약성 및 위협에 대 한 모니터링 데이터를 수집 합니다. 데이터는 컴퓨터에서 다양 한 보안 관련 구성 및 이벤트 로그를 읽고 분석을 위해 작업 영역에 데이터를 복사 하는 Azure Log Analytics 에이전트를 사용 하 여 수집 됩니다. 

누락 된 업데이트, 잘못 구성 된 OS 보안 설정, endpoint protection 상태, 상태 및 위협 검색에 대 한 가시성을 제공 하려면 데이터 컬렉션이 필요 합니다.

- [Log Analytics 에이전트의 자동 프로 비전을 사용 하도록 설정 하는 방법](../security-center/security-center-enable-data-collection.md)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 기간 구성

**지침**: Azure Kubernetes 서비스 (AKS) 인스턴스 Azure Monitor를 등록 하 고 조직의 규정 준수 요구 사항에 따라 해당 azure Log Analytics 작업 영역 보존 기간을 설정 합니다. 

- [Log Analytics 작업 영역에 대한 로그 보존 매개 변수를 설정하는 방법](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: 클러스터에 대 한 진단 설정을 Azure Monitor 하 고 구성 하기 위해 AKS (Azure Kubernetes Service) 인스턴스를 등록 합니다. 

Azure Monitor의 Log Analytics 작업 영역을 사용 하 여 로그를 검토 하 고 로그 데이터에 대 한 쿼리를 수행 합니다. Azure Monitor 로그는 Azure Portal 또는 CLI를 통해 사용 하도록 설정 되 고 관리 되며 Azure 역할 기반 access control (Azure RBAC) 및 RBAC를 사용 하지 않는 AKS 클러스터 모두에서 작동 합니다.

응용 프로그램 및 서비스 문제를 해결 하기 위해 AKS 마스터 구성 요소 (kube-apiserver 및 kube)에서 생성 된 로그를 봅니다. 중앙 집중식 로그 관리 및 모니터링을 위해 Azure 센티널 또는 타사 SIEM에 대 한 온-보드 데이터를 사용 하도록 설정 합니다.

- [AKS에서 Kubernetes 마스터 노드 로그를 사용 하도록 설정 하 고 검토 하는 방법](view-master-logs.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

- [Azure Monitor에서 사용자 지정 쿼리를 수행하는 방법](../azure-monitor/log-query/get-started-queries.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: Azure Kubernetes 서비스 (AKS)를 Security Center와 함께 사용 하 여 AKS 노드를 보다 자세히 파악할 수 있습니다. 호스트와 클러스터 수준에서 검색 된 위협 및 악의적인 활동에 대 한 Security Center 경고를 검토 합니다. Security Center는 네트워크 데이터, 프로세스 생성 및 Kubernetes 감사 로그와 같은 AKS 클러스터에서 발생 하는 원시 보안 이벤트의 연속 분석을 구현 합니다. 이 활동이 예상되는 동작인지, 아니면 애플리케이션이 잘못 작동하는지를 확인합니다. Azure Monitor에서 메트릭과 로그를 사용 하 여 결과를 substantiate. 

- [Security Center와 Azure Kubernetes Services 통합 이해](../security-center/defender-for-kubernetes-introduction.md)

- [Azure Security Center 표준 계층을 사용 하도록 설정 하는 방법](../security-center/security-center-get-started.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: Azure Kubernetes SERVICE (AKS) 가상 머신 및 가상 머신 확장 집합 노드에 Microsoft 맬웨어 방지 프로그램을 설치 하 고 사용 하도록 설정 합니다. Security Center에서 수정 하기 위한 경고를 검토 합니다.

- [Azure Cloud Services 및 Virtual Machines용 Microsoft 맬웨어 방지 프로그램](../security/fundamentals/antimalware.md)

- [보안 경고 참조 가이드](../security-center/alerts-reference.md)

- [컨테이너에 대 한 경고-Azure Kubernetes Service 클러스터](../security-center/alerts-reference.md#alerts-akscluster)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: Azure Kubernetes 서비스 (AKS)는 클러스터 DNS 관리 및 확인을 위해 coredns 프로젝트를 사용 합니다.

Coredns-custom ConfigMap에 문서화 된 구성을 적용 하 여 DNS 쿼리 로깅을 사용 하도록 설정 합니다. 

- [Azure Kubernetes Service를 사용하여 CoreDNS 사용자 지정](coredns-custom.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

**지침**: AKS (Azure Kubernetes Service)에서 kubectl 명령줄 클라이언트를 사용 하 여 문제 해결을 위해 Kubernetes 클러스터를 관리 하 고 AKS 노드에서 해당 로그를 가져옵니다. Azure Cloud Shell를 사용 하는 경우 Kubectl가 이미 설치 되어 있습니다. Kubectl를 로컬로 설치 하려면 ' AzAksKubectl ' cmdlet을 사용 합니다.

- [빠른 시작-PowerShell을 사용 하 여 Azure Kubernetes Service 클러스터 배포](kubernetes-walkthrough-powershell.md)

- [AKS(Azure Kubernetes Service) 클러스터 노드에서 kubelet 로그 가져오기](kubelet-logs.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [Azure 보안 벤치 마크: id 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조 하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**지침**: Azure Kubernetes SERVICE (AKS) 자체는 일반 사용자 계정 및 암호를 저장 하는 id 관리 솔루션을 제공 하지 않습니다. Azure Active Directory (Azure AD) 통합을 사용 하면 네임 스페이스 내에서 또는 클러스터 전체에서 Kubernetes 리소스에 대 한 액세스 권한을 사용자 또는 그룹에 부여할 수 있습니다. 

임시 쿼리를 수행 하 여 Azure AD PowerShell 모듈을 사용 하 여 AKS 관리 그룹의 멤버인 계정을 검색 합니다.

' 관리 되는 Kubernetes 클러스터에 대 한 액세스 자격 증명 가져오기 ' 같은 작업에 대 한 Azure CLI를 사용 하 여 정기적으로 액세스를 조정 하는 데 도움을 줍니다. AKS의 다른 기본 사용자 유형인 서비스 계정의 업데이트 된 인벤토리를 유지 하려면이 프로세스를 구현 합니다. Security Center의 Id 및 액세스 관리 권장 사항을 적용 합니다.

- [Azure AD와 AKS를 통합 하는 방법](./azure-ad-integration-cli.md)

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [Azure Security Center를 사용 하 여 id 및 액세스를 모니터링 하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

**참고**: Azure Kubernetes SERVICE (AKS)는 일반적인 기본 암호의 개념을 포함 하지 않으며, 일반 사용자 계정 및 암호를 저장할 수 있는 id 관리 솔루션을 제공 하지 않습니다. Azure Active Directory (Azure AD) 통합을 사용 하면 네임 스페이스 내에서 또는 클러스터 전체에서 AKS 리소스에 대 한 역할 기반 액세스 권한을 부여할 수 있습니다. 

임시 쿼리를 수행 하 여 Azure AD PowerShell 모듈을 사용 하 여 AKS 관리 그룹의 멤버인 계정을 검색 합니다.

- [AKS에 대 한 액세스 및 id 옵션 이해](concepts-identity.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: Azure Active Directory (azure AD)를 사용 하 여 AKS (Azure Kubernetes Service) 클러스터에 대 한 사용자 인증을 통합 합니다. Azure AD 인증 토큰을 사용 하 여 AKS 클러스터에 로그인 합니다. Kubeconfig (Kubernetes)에 대 한 관리 액세스에 대 한 RBAC (역할 기반 액세스 제어) 정보 및 사용 권한, 네임 스페이스 및 클러스터 리소스를 구성 합니다. 

전용 관리 계정의 사용에 대 한 정책과 절차를 만듭니다. Security Center Id 및 액세스 관리 권장 사항을 구현 합니다.

- [Azure Security Center를 사용 하 여 id 및 액세스를 모니터링 하는 방법](../security-center/security-center-identity-access.md)

- [클러스터 리소스에 대 한 액세스 제어](azure-ad-rbac.md)

- [Azure 역할 기반 액세스 제어 사용](control-kubeconfig-access.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 SSO(Single Sign-On) 사용

**지침**: AKS 클러스터에 대 한 Azure Active Directory (azure AD) 통합 인증을 사용 하 여 AKS (Azure Kubernetes Service)에 대 한 Single Sign-On를 사용 합니다.

- [Kubernetes 로그, 이벤트 및 pod 메트릭을 실시간으로 보는 방법](../azure-monitor/insights/container-insights-livedata-overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure Kubernetes 서비스 (AKS)에 대 한 인증을 Azure Active Directory (azure AD)와 통합 합니다. 

MFA (Azure AD Multi-Factor Authentication)를 사용 하도록 설정 하 고 Security Center의 Id 및 액세스 관리 권장 사항을 따릅니다.

- [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md) 

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

**지침**: PAW (권한 있는 액세스 워크스테이션) (MFA (Multi-Factor Authentication))를 사용 하 여 지정 된 AKS (Azure Kubernetes Service) 클러스터 및 관련 리소스에 로그인 하도록 구성 합니다.
- [Privileged Access Workstation에 대한 자세한 정보](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정에서 의심 스러운 활동에 대 한 로그 및 경고

**지침**: Azure Kubernetes 서비스 (AKS)에 대 한 azure ad 통합 인증을 사용 하 여 azure ad (azure ad) 보안 보고서를 Azure Active Directory 사용 합니다. 환경에서 의심 스러운 활동이 나 안전 하지 않은 활동이 발생 하는 경우 경고를 생성할 수 있습니다. Security Center를 사용 하 여 id 및 액세스 작업을 모니터링할 수 있습니다.

- [위험한 활동에 대해 플래그가 지정된 Azure AD 사용자를 식별하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center에서 사용자 id 및 액세스 활동을 모니터링 하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: 승인 된 위치 에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명 된 위치를 사용 하 여 IP 주소 범위 또는 국가/지역의 특정 논리적 그룹 에서만 AKS (Azure Kubernetes Service) 클러스터에 대 한 액세스를 허용 합니다. 이렇게 하려면 AKS with Azure Active Directory (Azure AD)에 대 한 통합 인증이 필요 합니다.

제한 된 IP 주소 범위 집합에서 AKS API 서버에 대 한 액세스를 제한 합니다 .이는 클러스터에서 작업을 수행 하 여 리소스를 만들거나 노드 수를 확장 하는 요청을 수신 합니다. 

- [AKS (Azure Kubernetes Service)에서 권한이 부여 된 IP 주소 범위를 사용 하 여 API 서버에 대 한 액세스 보호](api-server-authorized-ip-ranges.md)

- [Azure에서 명명된 위치를 구성하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure Kubernetes 서비스 (AKS)에 대 한 중앙 인증 및 권한 부여 시스템으로 Azure Active Directory (azure AD)를 사용 합니다. Azure AD는 미사용 데이터에 대해 강력한 암호화를 사용 하 여 데이터를 보호 하 고 전송 및 salts, 해시 및 안전 하 게 사용자 자격 증명을 저장 합니다.

Azure RBAC (역할 기반 액세스 제어)를 사용 하 여 AKS 기본 제공 역할을 사용 합니다. 리소스 정책 참가자 및 소유자는 Kubernetes 클러스터에 대 한 정책 할당 작업을 수행 합니다.

- [Azure Policy 개요](../governance/policy/overview.md)

- [AKS와 Azure AD를 통합 하는 방법](./azure-ad-integration-cli.md) 

- [AKS로 관리 되는 Azure AD 통합](managed-aad.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure Kubernetes 서비스 (AKS)에 대 한 azure ad 통합 인증을 사용 하 여 azure ad (azure ad) 보안 보고서를 Azure Active Directory 사용 합니다. Azure AD 로그를 검색 하 여 부실 계정을 검색 합니다. 

Azure Id 액세스 검토를 수행 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리 합니다. Security Center에서 Id 및 액세스 권장 사항을 재구성 합니다.

지원 또는 문제 해결을 위해 사용 되는 역할에 유의 하세요. 예를 들어 Microsoft 지원에서 수행 하는 모든 클러스터 작업 (사용자 동의 포함)은 aks-Kubernetes binding 이름으로 기본 제공 되는 "edit" 역할을 수행 합니다. AKS 지원은 클러스터 구성 및 리소스를 편집 하 여 클러스터 문제를 해결 하 고 진단 하는 데 사용할 수 있습니다. 그러나이 역할은 사용 권한을 수정 하거나 역할 또는 역할 바인딩을 만들 수 없습니다. 이 역할 액세스는 JIT (just-in-time) 액세스를 사용 하는 활성 지원 티켓 에서만 사용 하도록 설정 됩니다.
 
- [AKS(Azure Kubernetes Service)의 액세스 및 ID 옵션](concepts-identity.md)

- [Azure ID 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

- [Azure Security Center에서 사용자의 id 및 액세스 활동을 모니터링 하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화 되는 자격 증명에 대 한 액세스 시도를 모니터링 합니다.

**지침**: Azure Kubernetes 서비스 (AKS)에 대 한 사용자 인증을 azure AD (Azure Active Directory)와 통합 합니다. Azure AD에 대 한 진단 설정을 만들어 Azure Log Analytics 작업 영역에 감사 및 로그인 로그를 보냅니다. Azure Log Analytics 작업 영역 내에서 원하는 경고 (예: 비활성화 된 계정이 로그인을 시도 하는 경우)를 구성 합니다.
- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Azure Monitor를 사용 하 여 로그 경고를 만들고, 보고, 관리 하는 방법](../azure-monitor/platform/alerts-log.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure Kubernetes 서비스 (AKS)에 대 한 사용자 인증을 azure AD (Azure Active Directory)와 통합 합니다. Azure AD의 위험 검색 및 Id 보호 기능을 사용 하 여 사용자 id와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성할 수 있습니다. 비즈니스 요구에 따라 추가 조사를 위해 데이터를 Azure 센티널로 수집 합니다.

- [Azure AD 위험한 로그인을 확인하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오에서 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**지침**: 고객 Lockbox에서 지원 되지 않으므로 AKS (Azure Kubernetes Service)에는 적용 되지 않습니다.
- [지원 되 고객 Lockbox 서비스 목록](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [Azure 보안 벤치 마크: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조 하세요.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: Azure Kubernetes SERVICE (AKS) 배포와 관련 된 리소스에 태그를 사용 하 여 중요 한 정보를 저장 하거나 처리 하는 azure 리소스를 추적 하는 데 도움을 줍니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

- [관리 되는 클러스터에 대 한 태그 업데이트](/rest/api/aks/managedclusters/updatetags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: Azure Kubernetes 서비스 (AKS)를 사용 하 여 동일한 클러스터의 팀과 작업을 논리적으로 격리 하 여 각 팀에 필요한 리소스로 범위가 지정 된 최소 권한 수를 제공 합니다. 

Kubernetes의 네임 스페이스를 사용 하 여 논리적 격리 경계를 만듭니다. 예약, 네트워킹, 인증/권한 부여 및 컨테이너와 같은 격리 및 다중 테 넌 트에 추가 Kubernetes 기능을 구현 하는 것이 좋습니다.

개발, 테스트 및 프로덕션 환경에 대 한 별도의 구독 및/또는 관리 그룹을 구현 합니다. 적절 한 태그가 지정 된 별도의 가상 네트워크에 배포 하 여 AKS 클러스터를 네트워킹으로 분리 합니다.

- [AKS에서의 클러스터 격리 모범 사례에 대해 알아봅니다.](operator-best-practices-cluster-isolation.md)

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [AKS의 네트워크 연결 및 보안에 대 한 모범 사례 이해](operator-best-practices-network.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: 네트워크 경계에서 Azure Marketplace의 타사 솔루션을 사용 하 여 중요 한 정보에 대 한 무단 전송을 모니터링 하 고 정보 보안 전문가에 게 경고 하는 동안 이러한 전송을 차단 합니다.

Microsoft는 기본 플랫폼을 관리 하 고 모든 고객 콘텐츠를 중요 한 것으로 간주 하 고 고객 데이터 손실 및 노출을 방지 하기 위해 좋은 길이로 이동 합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [AKS 기능에 필요한 포트, 주소 및 도메인 이름 목록](limit-egress-traffic.md)

- [Azure 방화벽에 대 한 진단 설정을 구성 하는 방법](../firewall/firewall-diagnostics.md)

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: HTTPS 수신 컨트롤러를 만들고 AKS (Azure Kubernetes Service) 배포에 고유한 TLS 인증서 (또는 필요에 따라 암호화)를 사용 합니다. 

Kubernetes 송신 트래픽은 기본적으로 HTTPS/TLS를 통해 암호화 됩니다. 추가 모니터링을 위해 AKS 인스턴스에서 암호화 되지 않은 송신 트래픽을 검토할 수 있습니다. 여기에는 경우에 따라 업데이트를 검색 하기 위한 NTP 트래픽, DNS 트래픽, HTTP 트래픽이 포함 될 수 있습니다. 

- [AKS에서 HTTPS 수신 컨트롤러를 만들고 고유한 TLS 인증서를 사용 하는 방법](ingress-own-tls.md)

- [암호화를 사용 하 여 AKS에서 HTTPS 수신 컨트롤러를 만드는 방법](ingress-tls.md)

- [AKS에서 사용 하는 잠재적인 외부 포트 및 프로토콜 목록](limit-egress-traffic.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: 데이터 식별, 분류 및 손실 방지 기능은 아직 Azure Storage 또는 계산 리소스에 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현합니다.
Microsoft는 기본 플랫폼을 관리 하 고 모든 고객 콘텐츠를 중요 한 것으로 간주 하 고 고객 데이터 손실 및 노출을 방지 하기 위해 좋은 길이로 이동 합니다. 

Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Azure RBAC를 사용 하 여 리소스에 대 한 액세스 관리

**지침**: azure 리소스에 대 한 세분화 된 액세스 관리를 제공 하는 Azure Resource Manager 기반 azure RBAC (역할 기반 access control) 권한 부여 시스템을 사용 합니다.

사용자 인증을 위해 Azure AD (Azure Active Directory)를 사용 하도록 AKS (Azure Kubernetes Service)를 구성 합니다. 이 구성을 사용 하 여 Azure AD 인증 토큰을 사용 하 여 AKS 클러스터에 로그인 합니다. 

AKS 클러스터에 대 한 정책 할당 작업을 위해 Azure RBAC-리소스 정책 참가자 및 소유자와 AKS 기본 제공 역할을 사용 합니다.

- [AKS에서 Azure RBAC 및 Azure AD Id를 사용 하 여 클러스터 리소스에 대 한 액세스를 제어 하는 방법](azure-ad-rbac.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: 데이터 식별, 분류 및 손실 방지 기능은 아직 Azure Storage 또는 계산 리소스에 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현합니다.
Microsoft는 기본 플랫폼을 관리 하 고 모든 고객 콘텐츠를 중요 한 것으로 간주 하 고 고객 데이터 손실 및 노출을 방지 하기 위해 좋은 길이로 이동 합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: Azure Kubernetes 서비스 (AKS)의 볼륨에 제공 되는 두 가지 기본 저장소 유형은 azure 디스크 또는 Azure Files에서 지원 됩니다. 두 저장소 유형 모두 SSE (Azure Storage 서비스 암호화)를 사용 하 여 보안을 개선 하기 위해 미사용 데이터를 암호화 합니다. 기본적으로 데이터는 Microsoft 관리형 키로 암호화됩니다.

고객 관리 키를 사용 하 여 미사용 암호화는 암호화 키에 대 한 추가 제어를 위해 AKS 클러스터의 OS 및 데이터 디스크를 모두 암호화 하는 데 사용할 수 있습니다. 고객은 키 백업 및 회전과 같은 주요 관리 작업을 담당 합니다. 현재 AKS 노드 수준에서 Azure Disk Encryption을 사용하여 디스크를 암호화할 수 없습니다.

- [AKS(Azure Kubernetes Services)의 스토리지 및 백업 모범 사례](operator-best-practices-storage.md)

- [Azure Kubernetes 서비스 (AKS)에서 Azure 디스크를 사용 하 여 사용자 고유의 키 (BYOK) 가져오기](azure-disk-customer-managed-keys.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 기록 및 경고

**지침**: 컨테이너에 대해 Azure Monitor를 사용 하 여 AKS (Azure Kubernetes Service)에서 호스트 되는 관리 되는 Kubernetes 클러스터에 배포 된 컨테이너 워크 로드의 성능을 모니터링 합니다. 

노드 또는 컨테이너의 CPU 및 메모리 사용률이 정의 된 임계값을 초과 하거나 인프라 또는 노드 상태 롤업에서 클러스터의 상태 변경이 발생 하는 경우 자동 관리 알림 또는 로그 생성에 대 한 경고를 구성 합니다. 

Azure 활동 로그를 사용 하 여 AKS 클러스터 및 관련 리소스를 높은 수준으로 모니터링 합니다. 프로메테우스와 통합 하 여 사용자 지정 경고, 대시보드 및 자세한 분석을 만드는 쿼리를 사용 하 여 노드 및 Kubernetes에서 수집한 응용 프로그램 및 워크 로드 메트릭을 볼 수 있습니다.

- [컨테이너에 대 한 Azure Monitor 이해](../azure-monitor/insights/container-insights-overview.md)

- [컨테이너에 대해 Azure Monitor를 사용 하도록 설정 하는 방법](../azure-monitor/insights/container-insights-onboard.md)

- [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](../azure-monitor/platform/activity-log.md#view-the-activity-log)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="vulnerability-management"></a>취약점 관리

*자세한 내용은 [Azure 보안 벤치 마크: 취약성 관리](../security/benchmarks/security-control-vulnerability-management.md)를 참조 하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행

**지침**: Security Center을 사용 하 여 취약성에 대 한 AKS (Azure Kubernetes Service) 인스턴스를 비롯 한 Azure Container Registry을 모니터링 합니다. Security Center에서 컨테이너 레지스트리 번들을 사용 하도록 설정 하 여 Security Center 레지스트리에 푸시되는 이미지를 스캔할 준비가 되었는지 확인 합니다.

Qualys를 사용 하 여 Security Center 이미지를 검색 한 후에 문제가 발견 되 면 Security Center 대시보드에서이에 대 한 알림이 표시 됩니다. 컨테이너 레지스트리 번들 기능은 Azure Resource Manager 기반 레지스트리에서 사용 되는 이미지의 취약성에 대 한 자세한 표시를 제공 합니다. 

모든 취약성에 대해 조치 가능한 권장 사항에 Security Center를 사용 합니다. 이러한 권장 사항에는 심각도 분류 및 재구성에 대 한 지침이 포함 됩니다. 

- [AKS(Azure Kubernetes Services)의 컨테이너 이미지 관리 및 보안에 대한 모범 사례](../security-center/defender-for-container-registries-introduction.md)

- [AKS의 컨테이너 이미지 관리 및 보안에 대 한 모범 사례 이해](operator-best-practices-container-image-management.md)

- [Azure Security Center와 컨테이너 레지스트리 통합 이해](../security-center/defender-for-container-registries-introduction.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화된 운영 체제 패치 관리 솔루션 배포

**지침**: 고객의 AKS (Azure Kubernetes Service) 클러스터를 보호 하기 위해 보안 업데이트가 Linux 노드에 자동으로 적용 됩니다. 이러한 업데이트는 OS 보안 수정 사항 또는 커널 업데이트를 포함합니다. 

Windows server 노드를 최신 상태로 유지 하는 프로세스는 windows server 노드가 매일 업데이트를 수신 하지 않으므로 Linux를 실행 하는 노드와 다릅니다. 대신, 고객은 AKS 클러스터의 Windows Server 노드 풀에서 업그레이드를 수행 해야 합니다 .이는 Azure 제어판 또는 Azure CLI를 사용 하 여 최신 기본 창 서버 이미지 및 패치를 사용 하 여 새 노드를 배포 합니다. 이러한 업데이트에는 AKS에 대 한 보안 또는 기능 향상이 포함 되어 있습니다.

- [Linux를 실행 하는 AKS 클러스터 노드에 업데이트를 적용 하는 방법 이해](node-updates-kured.md)

- [Windows Server 노드를 사용 하는 AKS 클러스터에 대 한 AKS node 풀을 업그레이드 하는 방법](use-multiple-node-pools.md#upgrade-a-node-pool)

- [AKS (Azure Kubernetes Service) 노드 이미지 업그레이드](node-image-upgrade.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: 타사 소프트웨어 타이틀에 대 한 자동화 된 패치 관리 솔루션 배포

**지침**: 클러스터 수명 기간 동안 AKS (Azure Kubernetes Service) 클러스터 노드의 타사 응용 프로그램이 패치 된 상태로 유지 되도록 수동 프로세스를 구현 합니다. 자동 업데이트를 사용 하도록 설정 하거나, 노드를 모니터링 하거나, 정기적으로 재부팅을 수행 해야 할 수 있습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 연속 취약성 검사 비교

**지침**: 일관 된 간격으로 결과를 Security Center 검색 하 고 결과를 비교 하 여 취약성을 재구성 했는지 확인 합니다. 

PowerShell cmdlet "AzSecurityTask"를 사용 하 여 보안 상태 및 수정 취약점 검색 결과를 강화 하기 위해 수행할 것 Security Center 권장 하는 보안 작업의 검색을 자동화 합니다.

- [PowerShell을 사용 하 여 Azure Security Center에서 검색 된 취약성을 확인 하는 방법](/powershell/module/az.security/get-azsecuritytask?view=azps-3.3.0)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**지침**: Security Center에서 제공 하는 심각도 등급을 사용 하 여 취약성 해결의 우선 순위를 지정 합니다. 

Azure에서 제공 하는 기본 제공 취약점 평가 도구 (예: Qualys 또는 Rapid7)를 사용 하는 경우 일반적인 CVSS (취약성 점수 매기기 시스템) 또는 검색 도구에서 제공 하는 다른 점수 매기기 시스템을 사용 합니다.

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure 보안 벤치 마크: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조 하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화 된 asset discovery 솔루션 사용

**지침**: Azure 리소스 그래프를 사용 하 여 구독 내의 모든 리소스 (예: 계산, 저장소, 네트워크 등)를 쿼리하고 검색 합니다. 테 넌 트에서 적절 한 (읽기) 권한이 있는지 확인 하 고 구독 내의 리소스 뿐만 아니라 모든 Azure 구독을 열거할 수 있습니다.

클래식 Azure 리소스는 리소스 그래프를 통해 검색 될 수 있지만 앞으로 Azure Resource Manager 기반 리소스를 만들고 사용 하는 것이 좋습니다.

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

- [Azure 구독을 확인하는 방법](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Azure RBAC 이해](../role-based-access-control/overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타 데이터를 사용 하 여 Azure 리소스에 태그를 적용 하 여 논리적으로 분류로 구성 합니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 태그 지정, 관리 그룹 및 별도의 구독 (해당 하는 경우)을 사용 하 여 자산을 구성 하 고 추적 합니다. 

AKS (Azure Kubernetes Service) 노드 풀을 만들 때 taints, 레이블 또는 태그를 적용 합니다. 해당 노드 풀 내의 모든 노드는 해당 taint, label 또는 tag도 상속 합니다.

Taints, 레이블 또는 태그를 사용 하 여 정기적으로 인벤토리를 조정 하 고 권한 없는 리소스가 구독에서 적시에 삭제 되도록 할 수 있습니다.

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [및 사용자 태그를 만드는 방법](../azure-resource-manager/management/tag-resources.md)

- [관리 되는 클러스터-태그 업데이트](/rest/api/aks/managedclusters/updatetags)

- [노드 풀의 taint, 레이블 또는 태그 지정](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: 승인 된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 조직 비즈니스 요구에 따라 계산 리소스에 대해 승인 된 Azure 리소스 및 승인 된 소프트웨어 목록을 정의 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy을 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.
-   허용되지 않는 리소스 종류 

-   허용되는 리소스 유형

Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리/검색 합니다. 환경에 있는 모든 Azure 리소스가 조직의 비즈니스 요구 사항에 따라 승인 되었는지 확인 합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: Azure Automation 변경 내용 추적 및 인벤토리 기능을 사용 하 여 사용자 환경에 설치 된 소프트웨어를 확인 합니다. 

컴퓨터에서 소프트웨어, 파일, Linux 디먼, Windows 서비스 및 Windows 레지스트리 키에 대 한 인벤토리를 수집 하 고 보고 승인 되지 않은 소프트웨어 응용 프로그램을 모니터링 합니다. 

사용자 환경 전체에서 작동 문제를 어설션 매크로나 하 고 컴퓨터의 상태를 더 잘 이해 하기 위해 컴퓨터의 구성을 추적 합니다.

- [Azure 가상 컴퓨터 인벤토리를 사용 하도록 설정 하는 방법](../automation/automation-tutorial-installed-software.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: Azure Automation 변경 내용 추적 및 인벤토리 기능을 사용 하 여 사용자 환경에 설치 된 소프트웨어를 확인 합니다. 

컴퓨터에서 소프트웨어, 파일, Linux 디먼, Windows 서비스 및 Windows 레지스트리 키에 대 한 인벤토리를 수집 하 고 보고 승인 되지 않은 소프트웨어 응용 프로그램을 모니터링 합니다. 

사용자 환경 전체에서 작동 문제를 어설션 매크로나 하 고 컴퓨터의 상태를 더 잘 이해 하기 위해 컴퓨터의 구성을 추적 합니다.

- [Azure 가상 컴퓨터 인벤토리를 사용 하도록 설정 하는 방법](../automation/automation-tutorial-installed-software.md)

- [파일 무결성 모니터링을 사용 하는 방법](../security-center/security-center-file-integrity-monitoring.md)

- [Azure 변경 내용 추적 이해](../automation/change-tracking/overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 애플리케이션만 사용

**지침**: Azure Automation 변경 내용 추적 및 인벤토리 기능을 사용 하 여 사용자 환경에 설치 된 소프트웨어를 확인 합니다. 

컴퓨터에서 소프트웨어, 파일, Linux 디먼, Windows 서비스 및 Windows 레지스트리 키에 대 한 인벤토리를 수집 하 고 보고 승인 되지 않은 소프트웨어 응용 프로그램을 모니터링 합니다. 

사용자 환경 전체에서 작동 문제를 어설션 매크로나 하 고 컴퓨터의 상태를 더 잘 이해 하기 위해 컴퓨터의 구성을 추적 합니다.

사용자 환경에 있는 응용 프로그램에 대해 Security Center에서 적응 응용 프로그램 분석을 사용 하도록 설정 합니다.

- [Azure 가상 컴퓨터 인벤토리를 사용 하도록 설정 하는 방법](../automation/automation-tutorial-installed-software.md)

 
적응 응용 프로그램 Azure Security Center 사용 방법
- [컨트롤](../security-center/security-center-adaptive-application.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy을 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리/검색 합니다. 환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](../governance/policy/samples/index.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: 승인 된 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: Azure Policy을 사용 하 여 기본 제공 정책 정의를 사용 하 여 구독에 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성함으로써 Azure 조건부 액세스를 사용하여 Azure Resource Manager와 상호 작용하는 사용자 기능을 제한합니다.
- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: 사용자가 계산 리소스에서 스크립트를 실행 하는 기능을 제한 합니다.

**지침**: Azure Kubernetes SERVICE (AKS) 자체는 일반 사용자 계정 및 암호를 저장 하는 id 관리 솔루션을 제공 하지 않습니다. 대신 AKS 클러스터에 대 한 통합 id 솔루션으로 Azure Active Directory (Azure AD)를 사용 합니다. 

Azure AD 통합을 사용 하 여 네임 스페이스 내에서 또는 클러스터 전체에서 Kubernetes 리소스에 대 한 액세스 권한을 사용자 또는 그룹에 부여 합니다. 

Azure AD PowerShell 모듈을 사용 하 여 AKS 관리 그룹의 구성원 인 계정을 검색 하는 임시 쿼리를 수행 합니다. 정기적으로 액세스를 조정 합니다. ' 관리 되는 Kubernetes 클러스터에 대 한 액세스 자격 증명 가져오기 '와 같은 작업에 Azure CLI를 사용 합니다. Security Center Id 및 액세스 관리 권장 사항을 구현 합니다.

- [Azure CLI를 사용 하 여 AKS 관리](/cli/azure/aks?view=azure-cli-latest)

- [AKS 및 Azure AD 통합 이해](concepts-identity.md)

- [Azure AD와 AKS를 통합 하는 방법](./azure-ad-integration-cli.md)

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [Azure Security Center를 사용 하 여 id 및 액세스를 모니터링 하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**지침**: AKS (Azure Kubernetes Service) 기능을 사용 하 여 각 팀에서 요구 하는 리소스로 범위가 지정 된 최소 권한 수에 대해 동일한 클러스터에서 팀과 작업을 논리적으로 격리 합니다. 

Kubernetes에서 네임 스페이스를 구현 하 여 논리적 격리 경계를 만듭니다. "ContainerService" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 AKS (Azure Kubernetes Service) 인스턴스의 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 

추가 Kubernetes 기능을 검토 하 고 구현 하 고 격리 및 다중 테 넌 트에 대 한 고려 사항은 예약, 네트워킹, 인증/권한 부여 및 컨테이너 영역을 포함 합니다. 또한 개발, 테스트 및 프로덕션을 위해 별도의 구독 및/또는 관리 그룹을 사용 합니다. 가상 네트워크, 적절 하 게 태그가 지정 된 서브넷 및 WAF (웹 응용 프로그램 방화벽)로 보호 되는 AKS 클러스터를 분리 합니다.

- [AKS에서의 클러스터 격리 모범 사례에 대해 알아봅니다.](operator-best-practices-cluster-isolation.md)

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [AKS의 네트워크 연결 및 보안에 대 한 모범 사례 이해](operator-best-practices-network.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure 보안 벤치 마크: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조 하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: "ContainerService" 네임 스페이스에 Azure Policy 별칭을 사용 하 여 AKS (Azure Kubernetes Service) 인스턴스의 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 기본 제공 Azure Policy 정의를 사용 합니다.

AKS에 대 한 기본 제공 정책 정의의 예는 다음과 같습니다.

• Kubernetes 클러스터에서 HTTPS 수신 적용

• 권한 있는 IP 범위는 Kubernetes Services에서 정의 해야 합니다.

• Kubernetes 서비스에는 RBAC (기반 Access Control)를 사용 해야 합니다.

• Kubernetes 클러스터에서 허용 된 컨테이너 이미지만 확인

Azure Resource Manager를 사용 하 여 JavaScript Object Notation (JSON)에서 AKS 구성의 템플릿을 내보냅니다. 정기적으로 검토 하 여 이러한 구성이 조직의 보안 요구 사항을 충족 하는지 확인 합니다. Azure Security Center의 권장 사항을 Azure 리소스에 대 한 보안 구성 기준으로 사용 합니다. 

- [AKS pod 보안 정책을 구성 하 고 관리 하는 방법](use-pod-security-policies.md)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

**지침**: Azure Kubernetes 클러스터 (AKS) 클러스터는 보안에 최적화 된 OS를 사용 하는 호스트 가상 컴퓨터에 배포 됩니다. 호스트 OS에는 공격에 대 한 노출 영역을 줄이고 컨테이너를 안전 하 게 배포할 수 있도록 하는 추가 보안 강화 단계가 포함 되어 있습니다. 

Azure는 다시 부팅 해야 하는 일부 패치를 사용 하 여 AKS 가상 머신 호스트에 일일 패치 (보안 패치 포함)를 적용 합니다. 고객은 필요에 따라 가상 머신 호스트 재부팅을 AKS 예약할 책임이 있습니다. 

- [AKS 에이전트 노드 호스트 OS에 대 한 보안 강화](security-hardened-vm-host-image.md)

- [AKS 가상 컴퓨터 호스트의 보안 강화 이해](security-hardened-vm-host-image.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: pod 보안 정책을 사용 하 여 AKS (Azure Kubernetes Service) 클러스터를 보호 합니다.  클러스터의 보안을 향상 시키기 위해 예약할 수 있는 pod를 제한 합니다. 

Pod는 허용 되지 않는 요청 리소스를 AKS 클러스터에서 실행할 수 없습니다. 

또한 AKS 배포와 관련 된 Azure 리소스 (예: 가상 네트워크, 서브넷, Azure 방화벽, 저장소 계정 등)에 대 한 보안 설정을 적용 하려면 [거부] 및 [없는 경우 배포] 효과를 Azure Policy 사용 합니다. 

다음 네임 스페이스의 별칭을 사용 하 여 사용자 지정 Azure Policy 정의를 만듭니다. 

• ContainerService

• Microsoft 네트워크

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지 관리

**지침**: AKS (Azure Kubernetes Service) 클러스터는 보안에 최적화 된 OS를 사용 하는 호스트 가상 컴퓨터에 배포 됩니다. 호스트 OS에는 공격에 대 한 노출 영역을 줄이고 컨테이너를 안전 하 게 배포할 수 있도록 하는 추가 보안 강화 단계가 포함 되어 있습니다. 

호스트 OS에 기본 제공 되는 CI (Internet Security) 컨트롤의 중앙 목록을 참조 하십시오.  

- [AKS 에이전트 노드 호스트 OS에 대 한 보안 강화](security-hardened-vm-host-image.md)

- [AKS 클러스터의 상태 구성 이해](concepts-clusters-workloads.md#control-plane)

- [AKS 가상 컴퓨터 호스트의 보안 강화 이해](security-hardened-vm-host-image.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: 사용자 지정 Azure Policy 정의를 사용 하는 경우 Azure Repos를 사용 하 여 구성을 안전 하 게 저장 하 고 관리 합니다. Azure Resource Manager를 사용 하 여 JavaScript Object Notation (JSON)에서 Azure Kubernetes 서비스 (AKS) 구성의 템플릿을 내보냅니다. 정기적으로 검토 하 여 구성이 조직의 보안 요구 사항을 충족 하는지 확인 합니다. 

Terraform과 같은 타사 솔루션을 구현 하 여 Kubernetes 클러스터에 대 한 리소스를 선언 하는 구성 파일을 만듭니다. 보안 모범 사례를 구현 하 여 AKS 배포를 강화 하 고 구성을 안전한 위치에 코드로 저장할 수 있습니다.

- [Kubernetes 클러스터 정의](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks#define-a-kubernetes-cluster)

AKS 에이전트 노드 호스트 OS에 대 한 보안 강화

security-hardened-vm-host-image.md

- [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow?view=azure-devops)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

**지침**: Azure Kubernetes 서비스 (AKS)에 적용할 수 없습니다. AKS은 기본적으로 보안에 최적화 된 호스트 운영 체제 (OS)를 제공 합니다. 다른 운영 체제 또는 사용자 지정 운영 체제를 선택할 수 있는 현재 옵션은 없습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스에 대 한 구성 관리 도구 배포

**지침**: Azure Policy을 사용 하 여 기본 제공 정책 정의 및 "ContainerService" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다. 

감사 하 고 시스템 구성을 적용 하는 사용자 지정 정책을 만듭니다. 정책 예외를 관리 하기 위한 프로세스 및 파이프라인을 개발 합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [별칭을 사용 하는 방법](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 구성 관리 도구를 배포 합니다.

**지침**: AKS (Azure Kubernetes Service) 클러스터는 보안에 최적화 된 OS를 사용 하는 호스트 가상 컴퓨터에 배포 됩니다. 호스트 OS에는 공격에 대 한 노출 영역을 줄이고 컨테이너를 안전 하 게 배포할 수 있도록 하는 추가 보안 강화 단계가 포함 되어 있습니다. 

AKS 호스트에 기본 제공 되는 CI (Internet Security) 컨트롤의 센터 목록을 참조 하십시오.  

- [AKS 에이전트 노드 호스트 OS에 대 한 보안 강화](security-hardened-vm-host-image.md)

- [AKS 가상 컴퓨터 호스트의 보안 강화 이해](security-hardened-vm-host-image.md)

- [AKS 클러스터의 상태 구성 이해](concepts-clusters-workloads.md#control-plane)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대 한 자동화 된 구성 모니터링 구현

**지침**: Security Center을 사용 하 여 AKS (Azure Kubernetes Service) 배포와 관련 된 리소스에 대 한 기준 검색을 수행할 수 있습니다. 예제 리소스에는 AKS 클러스터 자체, AKS 클러스터가 배포 된 가상 네트워크, Terraform 상태를 추적 하는 데 사용 되는 Azure Storage 계정 또는 AKS 클러스터의 OS 및 데이터 디스크에 대 한 암호화 키에 사용 되는 Azure Key Vault 인스턴스가 포함 되지만이에 국한 되지 않습니다.

- [Azure Security Center에서 권장 사항을 수정 하는 방법](../security-center/security-center-remediate-recommendations.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대한 자동화된 구성 모니터링 구현

**지침**: "계산 앱" 섹션에서 Security Center 컨테이너 권장 사항을 사용 &amp; 하 여 AKS (Azure Kubernetes Service) 클러스터에 대 한 기준 검색을 수행 합니다. 구성 문제나 취약점이 발견 될 때 Security Center 대시보드에서 알림 받기 이렇게 하려면 Security Center에서 이미지를 검색할 수 있도록 하는 선택적 컨테이너 레지스트리 번들을 사용 하도록 설정 해야 합니다.  

- [Azure Security Center 컨테이너 권장 사항 이해](../security-center/container-security.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: Kubernetes 볼륨 드라이브를 사용 하 여 AKS (Azure Service) 클러스터와 Azure Key Vault를 통합 합니다. Azure Key Vault를 사용 하 여 자격 증명, 저장소 계정 키 또는 인증서와 같은 암호를 저장 하 고 정기적으로 회전 합니다. FlexVolume 드라이버를 사용하면 AKS 클러스터가 기본적으로 Key Vault에서 자격 증명을 검색한 후 요청 pod에만 안전하게 제공할 수 있습니다. Pod 관리 id를 사용 하 여 Key Vault에 대 한 액세스를 요청 하 고,이 볼륨 드라이버를 통해 필요한 자격 증명을 검색 합니다. Key Vault 일시 삭제를 사용하도록 설정되어 있는지 확인합니다. 

응용 프로그램 코드에서 자격 증명을 정의 하지 않아 자격 증명 노출을 제한 합니다. 

고정 또는 공유 자격 증명을 사용 하지 마십시오. 

- [AKS(Azure Kubernetes Service)의 애플리케이션 및 클러스터에 대한 보안 개념](concepts-security.md)

- [AKS 클러스터와 함께 Key Vault를 사용 하는 방법](developer-best-practices-pod-security.md#limit-credential-exposure)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: 보안 모범 사례로 응용 프로그램 코드에서 자격 증명을 정의 하지 마십시오. Azure 리소스에 대 한 관리 되는 id를 사용 하 여 pod가 Azure Key Vault를 포함 하 여 지원 되는 Azure 서비스에 대해 자신을 인증할 수 있도록 합니다. Pod에는 azure AD (Azure Active Directory)를 인증 하 고, 서비스에 액세스 하 고 필요한 작업을 수행할 수 있는 pod를 확인 하는 다른 Azure 서비스에 제공할 수 있는 디지털 토큰을 수신 하는 Azure Id가 할당 됩니다. 

Pod 관리 id는 Linux pod 및 컨테이너 이미지만 사용 하기 위한 것입니다. 디지털 키 및 자격 증명을 저장 하 고 검색 하 Azure Key Vault를 프로 비전 합니다. OS 디스크를 암호화 하는 데 사용 되는 것과 같은 키, AKS 클러스터 데이터를 Azure Key Vault에 저장할 수 있습니다.

AKS 클러스터에서 서비스 주체를 사용할 수도 있습니다. 그러나 결국 서비스 사용자를 사용 하는 클러스터는 클러스터 작동을 유지 하기 위해 서비스 주체를 갱신 해야 하는 상태에 도달할 수 있습니다. 서비스 주체를 관리 하면 복잡성이 증가 하므로 관리 되는 id를 대신 사용 하는 것이 더 쉽습니다. 서비스 사용자와 관리 되는 id 모두에 대해 동일한 권한 요구 사항이 적용 됩니다.

- [Azure Kubernetes 서비스를 사용 하 여 관리 되는 Id 및 Key Vault 이해 (AKS)](developer-best-practices-pod-security.md#limit-credential-exposure)

- [Azure Active Directory Pod ID](https://github.com/Azure/aad-pod-identity)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색 된 자격 증명을 권장 사항과 Azure Key Vault 같은 보다 안전한 위치로 이동 하는 것을 권장 합니다.

응용 프로그램 코드에서 자격 증명을 정의 하지 않아 자격 증명 노출을 제한 합니다. 공유 자격 증명을 사용 하지 않습니다. Azure Key Vault은 디지털 키와 자격 증명을 저장 하 고 검색 하는 데 사용 해야 합니다. Azure 리소스에 대해 관리 ID를 사용하여 pod가 다른 리소스에 대한 액세스를 요청하도록 합니다. 

- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Pod 보안을 위한 개발자 모범 사례](developer-best-practices-pod-security.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [Azure 보안 벤치 마크: 맬웨어 방어](../security/benchmarks/security-control-malware-defense.md)를 참조 하세요.*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: 중앙에서 관리 되는 맬웨어 방지 소프트웨어 사용

**지침**: AKS는 사용자를 대신해 에이전트 노드의 수명 주기 및 작업을 관리 합니다. 에이전트 노드와 연결 된 IaaS 리소스 수정은 지원 되지 않습니다. 그러나 Linux 노드의 경우 디먼 집합을 사용 하 여 맬웨어 방지 솔루션과 같은 사용자 지정 소프트웨어를 설치할 수 있습니다.

- [보안 경고 참조 가이드](../security-center/alerts-reference.md)

- [컨테이너에 대 한 경고-Azure Kubernetes Service 클러스터](../security-center/alerts-reference.md#alerts-akscluster)

- [AKS 공유 책임 및 데몬 집합](support-policies.md#shared-responsibility)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: AKS 리소스에 업로드 되는 파일을 미리 검색 합니다. 데이터 서비스에 대 한 Security Center의 위협 검색을 사용 하 여 Azure Storage 계정을 데이터 저장소로 사용 하거나 AKS 클러스터에 대 한 Terraform 상태를 추적 하는 경우 저장소 계정에 업로드 된 맬웨어를 검색할 수 있습니다. 

- [데이터 서비스에 대 한 Azure Security Center의 위협 검색 이해](../security-center/azure-defender.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명을 업데이트 해야 합니다.

**지침**: AKS는 사용자를 대신해 에이전트 노드의 수명 주기 및 작업을 관리 합니다. 에이전트 노드와 연결 된 IaaS 리소스 수정은 지원 되지 않습니다. 그러나 Linux 노드의 경우 디먼 집합을 사용 하 여 맬웨어 방지 솔루션과 같은 사용자 지정 소프트웨어를 설치할 수 있습니다.

- [보안 경고 참조 가이드](../security-center/alerts-reference.md)

- [컨테이너에 대 한 경고-Azure Kubernetes Service 클러스터](../security-center/alerts-reference.md#alerts-akscluster)

- [AKS 공유 책임 및 데몬 집합](support-policies.md#shared-responsibility)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure 보안 벤치 마크: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조 하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**지침**: Velero와 같은 저장소 유형에 적절 한 도구를 사용 하 여 데이터를 백업 합니다 .이 도구는 추가 클러스터 리소스 및 구성과 함께 영구 볼륨을 백업할 수 있습니다. 정기적으로 이러한 백업에 대 한 무결성 및 보안을 확인 합니다. 

백업 하기 전에 응용 프로그램에서 상태를 제거 합니다. 이 작업을 수행할 수 없는 경우 영구적 볼륨에서 데이터를 백업 하 고 복원 작업을 정기적으로 테스트 하 여 데이터 무결성 및 필요한 프로세스를 확인 합니다.

- [AKS의 저장소 및 백업에 대 한 모범 사례](operator-best-practices-storage.md)

- [AKS의 비즈니스 연속성 및 재해 복구에 대 한 모범 사례](operator-best-practices-multi-region.md)

- [Azure Site Recovery 이해](../site-recovery/site-recovery-overview.md)

- [Azure에서 Velero를 설정 하는 방법](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업을 수행 하 고 고객 관리 키를 백업 합니다.

**지침**: Velero와 같은 저장소 유형에 적절 한 도구를 사용 하 여 데이터를 백업 합니다 .이 도구는 추가 클러스터 리소스 및 구성과 함께 영구 볼륨을 백업할 수 있습니다. 

PowerShell 명령을 사용 하 여 Key Vault 인증서, 키, 관리 되는 저장소 계정 및 암호에 대 한 정기적으로 자동화 된 백업을 수행 합니다. 

예를 들면 다음과 같습니다.

Backup-AzKeyVaultCertificate Backup-AzKeyVaultKey Backup-AzKeyVaultManagedStorageAccount Backup-AzKeyVaultSecret

- [Key Vault 인증서를 백업 하는 방법](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

- [Key Vault 키를 백업 하는 방법](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [관리 저장소 계정을 Key Vault 백업 하는 방법](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Key Vault 비밀을 백업 하는 방법](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

- [Azure Backup를 사용 하도록 설정 하는 방법](../backup/index.yml)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 비롯 한 모든 백업 유효성 검사

**지침**: Velero 백업 내에서 콘텐츠의 데이터 복원을 정기적으로 수행 합니다. 필요한 경우 격리 된 가상 네트워크에 대 한 복원을 테스트 합니다.

PowerShell 명령을 사용 하 여 Key Vault 인증서, 키, 관리 되는 저장소 계정 및 암호의 데이터 복원을 정기적으로 수행 합니다. 

예를 들면 다음과 같습니다.

Restore-AzKeyVaultCertificate Restore-AzKeyVaultKey Restore-AzKeyVaultManagedStorageAccount Restore-AzKeyVaultSecret

- [Key Vault 인증서를 복원 하는 방법](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0)

- [Key Vault 키를 복원 하는 방법](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

- [관리 되는 저장소 계정을 Key Vault 복원 하는 방법](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Key Vault 비밀을 복원 하는 방법](/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0)

- [Azure Virtual Machine 백업에서 파일을 복구 하는 방법](../backup/backup-azure-restore-files-from-vm.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객이 관리 하는 키를 보호 해야 합니다.

**지침**: Velero와 같은 저장소 유형에 적절 한 도구를 사용 하 여 데이터를 백업 합니다 .이 도구는 추가 클러스터 리소스 및 구성과 함께 영구 볼륨을 백업할 수 있습니다. 

AKS (Azure Kubernetes Service) 배포에 대해 Azure Key Vault를 사용 하는 경우 Key Vault에서 Soft-Delete를 사용 하 여 실수로 인 한 삭제 또는 악의적인 삭제 로부터 키를 보호 합니다.

- [Azure Storage 서비스 암호화 이해](../storage/common/storage-service-encryption.md)

- [Key Vault에서 일시 삭제를 사용하도록 설정하는 방법](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [Azure 보안 벤치 마크: 인시던트 응답](../security/benchmarks/security-control-incident-response.md)을 참조 하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 검색에서 사후 검토에 이르는 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할을 정의하는 인시던트 대응 계획이 있는지 확인합니다.

- [Azure Security Center 내에서 워크플로 자동화를 구성하는 방법](../security-center/security-center-planning-and-operations-guide.md)

- [자체 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [또한 고객은 NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용 하 여 고유한 인시던트 대응 계획을 만드는 데 도움이 될 수 있습니다.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: 경고에 Security Center 할당 된 심각도를 사용 하 여 먼저 조사 해야 하는 경고의 우선 순위를 지정 합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용된 결과 또는 분석의 신뢰도 및 경고가 발생된 활동의 배후에 악의적인 의도가 있었음에 대한 신뢰 수준을 기준으로 합니다.
구독 (예: 프로덕션, 비프로덕션)을 명확 하 게 표시 하 고 Azure 리소스를 명확 하 게 식별 하 고 범주화 하기 위한 이름 지정 시스템을 만듭니다.

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 결함을 식별하고 필요에 따라 계획을 수정합니다.

이에 대 한 NIST의 게시: 테스트, 학습 및 연습 프로그램 가이드를 참조 하세요. 
- [계획 및 기능](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 고객 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: 연속 내보내기 기능을 사용 하 여 Security Center 경고 및 권장 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. 또한 Security Center 데이터 커넥터를 선택 하 여 조직의 비즈니스 요구 사항에 따라 Azure 센티널로 경고를 스트리밍할 수 있습니다.

- [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 대한 "Logic Apps"를 통해 응답을 자동으로 트리거합니다.

- [워크플로 자동화와 Logic Apps를 구성하는 방법](../security-center/workflow-automation.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure 보안 벤치 마크: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조 하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대 한 정기적인 침투 테스트를 수행 하 고 모든 중요 한 보안 결과를 수정 하세요.

**지침**: 다음 Microsoft 시행 규칙에 따라 침투 테스트에서 Microsoft 정책을 위반하지 않는지 확인합니다. https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

- [Microsoft에서 관리하는 클라우드 인프라, 서비스 및 애플리케이션에 대한 레드 팀 및 라이브 사이트 침투 테스트 실행에 대한 자세한 내용은 ](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)를 참조하세요.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](../security/benchmarks/overview.md)를 참조하세요.
- [Azure 보안 기준](../security/benchmarks/security-baselines-overview.md)에 대해 자세히 알아보세요.