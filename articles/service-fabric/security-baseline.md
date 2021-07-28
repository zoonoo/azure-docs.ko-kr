---
title: Service Fabric에 대한 Azure 보안 기준
description: Service Fabric 보안 기준은 Azure Security Benchmark에 지정된 보안 권장 사항을 구현하기 위한 절차 지침과 리소스를 제공합니다.
author: msmbaldwin
ms.service: service-fabric
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ee294cff85bb71b5c2a238fcf985fc870ed32618
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285465"
---
# <a name="azure-security-baseline-for-service-fabric"></a>Service Fabric에 대한 Azure 보안 기준

이 보안 기준은 [Azure Security Benchmark 버전 1.0](../security/benchmarks/overview-v1.md)에서 Service Fabric에 대한 지침을 적용합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 Azure Security Benchmark에서 정의된 **보안 제어** 및 Service Fabric에 적용되는 관련 지침에 따라 그룹화됩니다.

> [!NOTE]
> Service Fabric에 적용되지 않거나 Microsoft에 책임이 있는 **제어** 는 제외되었습니다. Service Fabric이 Azure Security Benchmark에 완전히 매핑되는 방법을 확인하려면 **[전체 Azure Active Directory 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/service-fabric-security-baseline-v1.1.xlsx)** 을 참조하세요.

## <a name="network-security"></a>네트워크 보안

자세한 내용은 [Azure Security Benchmark: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조하세요.

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: 애플리케이션의 신뢰할 수 있는 포트 및 원본과 관련된 네트워크 액세스 제어가 적용된 네트워크 보안 그룹이 모든 Virtual Network 서브넷 배포에 있는지 확인합니다. 

- [템플릿을 사용하여 Azure Firewall 배포](../firewall/deploy-template.md)

- [Azure NSG(네트워크 보안 그룹)를 사용하여 경계 네트워크 만들기](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices#use-network-isolation-and-security-with-azure-service-fabric)

- [기존 가상 네트워크와 Azure Service Fabric 클러스터를 통합하는 방법](service-fabric-patterns-networking.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: 가상 네트워크, 서브넷, NIC의 구성과 트래픽 모니터링 및 기록

**지침**: Security Center를 사용하고 Service Fabric 클러스터를 보호하는 데 사용되는 가상 네트워크, 서브넷 및 네트워크 보안 그룹에 대한 네트워크 보호 권장 사항을 수정합니다. 네트워크 보안 그룹 흐름 로그를 사용하도록 설정하고, 트래픽 감사를 위해 로그를 Azure Storage 계정에 보냅니다. 또한 네트워크 보안 그룹 흐름 로그를 Azure Log Analytics 작업 영역에 보내고, Azure 트래픽 분석을 사용하여 Azure 클라우드의 트래픽 흐름에 대한 인사이트를 제공할 수 있습니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화하고, 핫 스폿을 식별하며, 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하며, 잘못된 네트워크 구성을 정확히 파악할 수 있다는 것입니다.

- [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure 트래픽 분석을 사용하도록 설정하고 사용하는 방법](../network-watcher/traffic-analytics.md)

- [Azure Security Center에서 제공하는 네트워크 보안 이해](../security-center/security-center-network-recommendations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: 사용자, 디바이스 또는 기타 애플리케이션에 대한 단일 수신 지점을 제공할 수 있는 프런트 엔드 게이트웨이를 제공합니다. Azure API Management는 Service Fabric과 직접 통합되어 백 엔드 서비스에 대한 액세스를 보호하고, 제한을 사용하여 DOS 공격을 방지하며, API 키, JWT 토큰, 인증서 및 기타 자격 증명을 확인할 수 있습니다.

들어오는 트래픽을 추가로 검사하기 위해 중요한 웹 애플리케이션 외부에 Azure WAF(Web Application Firewall)를 배포하는 것이 좋습니다. WAF에 대한 진단 설정을 사용하도록 설정하고, 로그를 스토리지 계정, Event Hub 또는 Log Analytics 작업 영역에 수집합니다.

- [Service Fabric 및 API Management 개요](service-fabric-api-management-overview.md)

- [내부 VNET의 API Management를 Application Gateway와 통합](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [Azure WAF를 배포하는 방법](../web-application-firewall/ag/create-waf-policy-ag.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: DDoS 공격으로부터 보호하려면 Azure Service Fabric가 배포된 가상 네트워크에서 Azure DDoS 표준 보호를 사용하도록 설정합니다. Azure Security Center 통합 위협 인텔리전스를 사용하여 알려진 악의적이거나 사용하지 않는 인터넷 IP 주소와의 통신을 거부합니다.

- [DDoS 보호를 구성하는 방법](../ddos-protection/manage-ddos-protection.md)

- [Azure Security Center 통합 위협 인텔리전스 이해](../security-center/azure-defender.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷 기록

**지침**: Service Fabric 클러스터를 보호하는 데 사용되는 서브넷에 연결된 네트워크 보안 그룹에 네트워크 보안 그룹 흐름 로그를 사용하도록 설정합니다. NSG 흐름 로그를 Azure Storage 계정에 기록하여 흐름 레코드를 생성합니다. 비정상 활동을 조사하는 데 필요한 경우 Azure Network Watcher 패킷 캡처를 사용하도록 설정합니다.

- [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Network Watcher를 사용하도록 설정하는 방법](../network-watcher/network-watcher-create.md)

- [트래픽 분석을 사용하여 NSG 흐름 로그 시각화](../network-watcher/traffic-analytics.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 IDS/IPS(침입 탐지/침입 방지 시스템) 배포

**지침**: 페이로드 검사 기능을 사용하여 IDS/IPS 기능을 지원하는 Azure Marketplace에서 제공하는 제안을 선택합니다.  페이로드 검사에 기반한 침입 탐지 및/또는 방지 기능이 요구 사항이 아니면, 위협 인텔리전스가 포함된 Azure Firewall을 사용할 수 있습니다. Azure Firewall 위협 인텔리전스 기반 필터링은 알려진 악성 IP 주소 및 도메인과 주고받는 트래픽을 경고하고 거부할 수 있습니다. IP 주소 및 도메인은 Microsoft 위협 인텔리전스 피드에서 제공됩니다. 

원하는 방화벽 솔루션을 각 조직의 네트워크 경계에 배포하여 악성 트래픽을 탐지 및/또는 거부합니다. 

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall) 

- [Azure Firewall을 배포하는 방법](../firewall/tutorial-firewall-deploy-portal.md) 

- [Azure Firewall을 사용하여 경고를 구성하는 방법](../firewall/threat-intel.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침**: HTTPS/SSL이 신뢰할 수 있는 인증서에 사용하도록 설정된 웹 애플리케이션용 Azure Application Gateway를 배포합니다. 

- [Application Gateway를 배포하는 방법](../application-gateway/quick-create-portal.md)

- [HTTPS를 사용하도록 Application Gateway를 구성하는 방법](../application-gateway/create-ssl-portal.md)

- [Azure 웹 애플리케이션 게이트웨이를 사용한 계층 7 부하 분산 이해](../application-gateway/overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: 가상 네트워크 서비스 태그를 사용하여 Azure Service Fabric 클러스터가 배포된 서브넷에 연결된 NSG(네트워크 보안 그룹)에 대한 네트워크 액세스 제어를 정의합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 서비스 태그 이름(예: ApiManagement)을 규칙의 적절한 원본 또는 대상 필드에 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. Microsoft에서는 서비스 태그에서 압축한 주소 접두사를 관리하고 주소를 변경하는 대로 서비스 태그를 자동으로 업데이트합니다.

- [가상 네트워크 서비스 태그](../virtual-network/service-tags-overview.md)

- [Service Fabric 네트워킹 모범 사례](service-fabric-best-practices-networking.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지

**지침**: Azure Service Fabric 클러스터와 관련된 네트워크 리소스에 대한 표준 보안 구성을 정의하고 구현합니다. "Microsoft.ServiceFabric" 및 "Microsoft.Network" 네임스페이스에서 Azure Policy 별칭을 사용하여 Azure Service Fabric 클러스터의 네트워크 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다.

또한 Azure Blueprints에서 주요 환경 아티팩트(예: Azure Resource Manager 템플릿, Azure RBAC 제어 및 정책)를 단일 청사진 정의로 패키지하여 대규모 Azure 배포를 간소화할 수 있습니다. Blueprint를 새로운 구독 및 환경에 쉽게 적용하고 버전 관리를 통해 제어 및 관리를 세부적으로 조정합니다.

- [사용 가능한 Azure Policy 별칭을 확인하는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint를 만드는 방법](../governance/blueprints/create-blueprint-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

**지침**: Service Fabric 클러스터와 연결된 네트워크 보안 및 트래픽 흐름과 관련된 네트워크 서비스 그룹 및 기타 리소스에 태그를 사용합니다. 개별 네트워크 보안 그룹 규칙의 경우 "설명" 필드를 사용하여 네트워크에서 들어오고 나가는 트래픽을 허용하는 모든 규칙에 대한 비즈니스 요구 사항, 기간 등을 지정합니다.

태그 지정과 관련된 기본 제공 Azure Policy 정의(예: "태그 및 해당 값 필요")를 사용하여 모든 리소스에서 태그를 포함하도록 만들고 태그가 없는 기존 리소스를 알립니다.

Azure PowerShell 또는 Azure CLI(명령줄 인터페이스)를 사용하여 태그를 기준으로 리소스에 대한 작업을 조회하거나 수행할 수 있습니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

- [가상 네트워크를 만드는 방법](../virtual-network/quick-create-portal.md)

- [보안 구성을 사용하여 NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용하여 네트워크 리소스 구성을 모니터링하고, Azure Service Fabric 배포와 관련된 네트워크 리소스에 대한 변경 내용을 검색합니다. 중요한 네트워크 리소스에 변경 내용이 발생하는 경우 트리거하는 Azure Monitor 내에서 경고를 만듭니다.

- [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Azure Monitor에서 경고를 만드는 방법](/azure/azure-monitor/platform/alerts-activity-log)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure Security Benchmark: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조하세요.*

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure Service Fabric 클러스터를 Azure Monitor에 온보딩하여 클러스터에서 생성한 보안 데이터를 집계할 수 있습니다. Service Fabric과 관련된 진단 문제 및 해결 방법 예제를 참조하세요.

- [Service Fabric와 Azure Monitor 로그 통합 구성](service-fabric-diagnostics-oms-setup.md)

- [Azure Service Fabric에서 컨테이너 모니터링에 대한 Azure Monitor 로그 설정](service-fabric-tutorial-monitoring-wincontainers.md)

- [일반적인 Service Fabric 시나리오 진단](service-fabric-diagnostics-common-scenarios.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: Azure Monitor를 Service Fabric 클러스터에 사용하도록 설정하고, 해당 클러스터를 Log Analytics 작업 영역으로 이동합니다. 그러면 모든 Azure Service Fabric 클러스터 노드에 대한 관련 클러스터 정보 및 OS 메트릭이 기록됩니다.

- [Service Fabric와 Azure Monitor 로그 통합 구성](service-fabric-diagnostics-oms-setup.md)

- [Azure Service Fabric에서 컨테이너 모니터링에 대한 Azure Monitor 로그 설정](service-fabric-tutorial-monitoring-wincontainers.md)

- [Log Analytics 에이전트를 노드에 배포하는 방법](service-fabric-diagnostics-oms-agent.md)

- [Log Analytics 로그 검색](/azure/azure-monitor/log-query/log-query-overview)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: Azure Service Fabric 클러스터를 Azure Monitor에 온보딩합니다. 조직의 준수 규정에 따라 설정된 로그 보존 기간이 사용된 Log Analytics 작업 영역에 있는지 확인합니다.

- [Service Fabric와 Azure Monitor 로그 통합 구성](service-fabric-diagnostics-oms-setup.md)

- [Azure Service Fabric에서 컨테이너 모니터링에 대한 Azure Monitor 로그 설정](service-fabric-tutorial-monitoring-wincontainers.md)

- [Log Analytics 에이전트를 노드에 배포하는 방법](service-fabric-diagnostics-oms-agent.md) 

- [Log Analytics 작업 영역 보존 기간을 구성하는 방법](/azure/azure-monitor/platform/manage-cost-storage)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침**: Service Fabric 클러스터를 Azure Monitor에 온보딩합니다. 사용된 Log Analytics 작업 영역에 조직의 규정 준수에 따라 설정된 로그 보존 기간이 있는지 확인합니다.

- [Service Fabric와 Azure Monitor 로그 통합 구성](service-fabric-diagnostics-oms-setup.md)

- [Azure Service Fabric에서 컨테이너 모니터링에 대한 Azure Monitor 로그 설정](service-fabric-tutorial-monitoring-wincontainers.md)

- [Log Analytics 에이전트를 노드에 배포하는 방법](service-fabric-diagnostics-oms-agent.md)

- [Log Analytics 작업 영역 보존 기간을 구성하는 방법](/azure/azure-monitor/platform/manage-cost-storage)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: Azure Log Analytics 작업 영역 쿼리를 사용하여 Azure Service Fabric 로그를 쿼리합니다.

- [Log Analytics 로그 검색](/azure/azure-monitor/log-query/log-query-overview)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상 활동에 대한 경고 사용

**지침**: Azure Log Analytics 작업 영역을 사용하여 Azure Service Fabric 클러스터와 관련된 보안 로그 및 이벤트의 비정상 활동을 모니터링하고 경고합니다.

- [Azure Security Center에서 경고를 관리하는 방법](../security-center/security-center-managing-and-responding-alerts.md)

- [Log Analytics 로그 데이터에 관해 경고하는 방법](/azure/azure-monitor/learn/tutorial-response)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: 기본적으로 Windows Defender는 Windows Server 2016에 설치됩니다. Windows Defender를 사용하지 않는 경우 구성 규칙에 대한 맬웨어 방지 설명서를 참조하세요. Windows Defender는 Linux에서 지원되지 않습니다.

- [자세한 내용은 Windows Server 2016의 Windows Defender 바이러스 백신을 참조하세요.](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: DNS 로깅에 대한 타사 솔루션을 구현합니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

**지침**: 노드 단위로 콘솔 로깅을 수동으로 구성합니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [Azure Security Benchmark: ID 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정 인벤토리 유지 관리

**지침**: Azure Service Fabric 클러스터를 프로비전하는 중에 생성되는 로컬 관리 계정 및 사용자가 만드는 다른 계정에 대한 기록을 유지 관리합니다. 또한 Azure AD(Azure Active Directory) 통합을 사용하는 경우 Azure AD에는 명시적으로 할당해야 하고 따라서 쿼리할 수 있는 기본 제공 역할이 있습니다. Azure AD PowerShell 모듈을 사용하여 임시 쿼리를 수행하여 관리 그룹의 멤버인 계정을 검색합니다.

또한 Azure Security Center ID 및 액세스 관리 권장 사항을 사용할 수 있습니다.

- [PowerShell을 사용하여 Azure AD에서 디렉터리 역할을 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [PowerShell을 사용하여 Azure AD에서 디렉터리 역할 멤버를 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

- [Azure Security Center에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

**지침**: 클러스터를 프로비전할 때 Azure에서 웹 포털에 대한 새 암호를 만들어야 합니다. 변경할 기본 암호는 없지만, 웹 포털 액세스에 대해 다른 암호를 지정할 수 있습니다.

- [Azure Portal에서 만들기](service-fabric-cluster-creation-via-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: Service Fabric에 대한 인증을 Azure AD(Azure Active Directory)와 통합합니다. 전용 관리 계정을 사용하는 방법에 대한 정책과 절차를 만듭니다.

또한 Security Center ID 및 액세스 관리 권장 사항을 사용할 수 있습니다.

- [Azure AD 클라이언트 인증 설정](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-create-vnet-and-windows-cluster#set-up-azure-active-directory-client-authentication)

- [Azure Security Center에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 SSO(Single Sign-On) 사용

**지침**: 가능하면 서비스별로 개별 독립 실행형 자격 증명을 구성하는 대신 Azure AD(Azure Active Directory) SSO를 사용합니다. Security Center ID 및 액세스 관리 권장 사항을 사용합니다. 

- [Azure AD를 사용하는 SSO 이해](../active-directory/manage-apps/what-is-single-sign-on.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**참고 자료**: Azure Active Directory(Azure AD) 다단계 인증을 사용하도록 설정하고, Security Center ID 및 액세스 관리 추천 사항을 따릅니다.

- [Azure에서 다단계 인증을 사용하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

**지침**: 다단계 인증이 구성된 PAW(Privileged Access Workstation)를 사용하여 Service Fabric 클러스터 및 관련 리소스에 로그인하고 구성합니다.

- [Privileged Access Workstation에 대한 자세한 정보](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure에서 다단계 인증을 사용하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

**지침**: 환경에서 의심스럽거나 안전하지 않은 활동이 발생하는 경우 로그와 경고를 생성하기 위해 Azure AD(Azure Active Directory) PIM(Privileged Identity Management)을 사용합니다. 또한 Azure AD 위험 탐지를 사용하여 위험한 사용자 동작에 대한 경고 및 보고서를 봅니다.

- [PIM(Privileged Identity Management)을 배포하는 방법](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure AD 위험 탐지 이해](../active-directory/identity-protection/overview-identity-protection.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리 그룹에서만 액세스하도록 허용합니다. 

- [Azure에서 명명된 위치를 구성하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure AD(Azure Active Directory)를 중앙 인증 및 권한 부여 시스템으로 사용하여 Service Fabric 클러스터의 관리 엔드포인트에 대한 액세스를 보호합니다. Azure AD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다.

- [Azure AD 인스턴스를 만들고 구성하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Service Fabric 클라이언트 인증에 대한 Azure AD 설정](service-fabric-cluster-creation-setup-aad.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.ServiceFabric**:

[!INCLUDE [Resource Policy for Microsoft.ServiceFabric 3.9](../../includes/policy/standards/asb/rp-controls/microsoft.servicefabric-3-9.md)]

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Service Fabric 클러스터에서 Azure AD(Azure Active Directory) 인증을 사용합니다. Azure AD는 부실 계정을 검색하는 데 유용한 로그를 제공합니다. 또한 Azure ID 액세스 검토를 사용하여 그룹 멤버 자격, 엔터프라이즈 애플리케이션에 대한 액세스, 역할 할당을 효율적으로 관리합니다. 사용자의 액세스를 정기적으로 검토하여 적합한 사용자만 계속 액세스할 수 있는지 확인합니다.

- [Azure ID 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="311-alert-on-account-login-behavior-deviation"></a>3.11: 계정 로그인 동작 편차에 대한 경고

**지침**: 비활성화된 계정에 액세스하려는 시도를 모니터링하려면 Azure AD(Azure Active Directory) 로그인 및 감사 로그를 사용합니다. 로그는 타사 SIEM/모니터링 도구에 통합할 수 있습니다.

Azure AD 사용자 계정에 대한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Azure Log Analytics 작업 영역으로 보내면 이 프로세스를 간소화할 수 있습니다. Azure Log Analytics 작업 영역 내에서 원하는 경고를 구성합니다.

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure AD(Azure Active Directory) 위험 및 ID 보호 기능을 사용하여 사용자 ID와 관련하여 감지된 의심스러운 동작에 대한 자동 응답을 구성합니다. 추가 조사를 위해 Azure Sentinel에 데이터를 수집할 수도 있습니다.

- [Azure AD 위험한 로그인을 확인하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-protection"></a>데이터 보호

자세한 내용은 [Azure Security Benchmark: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조하세요.

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 Service Fabric 배포와 관련된 리소스에 사용하여 중요한 정보를 저장하거나 처리하는 Azure 리소스를 추적할 수 있도록 지원합니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 개발, 테스트 및 프로덕션을 위한 별도의 구독 및/또는 관리 그룹을 구현합니다. 리소스는 Virtual Network 또는 서브넷으로 구분하고, 태그를 적절하게 지정하고, 네트워크 보안 그룹 또는 Azure Firewall을 통해 보호해야 합니다. 중요한 데이터를 저장하거나 처리하는 리소스는 충분히 격리해야 합니다. 중요한 데이터를 저장하거나 처리하는 Virtual Machines의 경우 사용하지 않을 때 이를 해제하는 정책 및 절차를 구현합니다. 

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

- [Virtual Network를 만드는 방법](../virtual-network/quick-create-portal.md)

- [보안 구성을 사용하여 NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Firewall을 배포하는 방법](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Firewall을 사용하여 경고 또는 거부를 구성하는 방법](../firewall/threat-intel.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: 중요한 정보의 무단 전송을 모니터링하고 정보 보안 전문가에게 경고하는 동시에 이러한 전송을 차단하는 자동화된 도구를 네트워크 경계에 배포합니다. 

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고 고객 데이터 손실 및 노출을 방지하기 위해 많은 시간을 투자합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다. 

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: 전송 중인 모든 중요한 정보를 암호화합니다. Azure 리소스에 연결하는 모든 클라이언트에서 TLS 1.2 이상을 협상할 수 있는지 확인합니다. 

Service Fabric 클라이언트-노드 상호 인증의 경우 X.509 인증서를 http 통신의 서버 ID 및 TLS 암호화에 사용합니다. 복제하는 동안 노드 간의 애플리케이션 구성 값과 데이터의 암호화 및 암호 해독을 포함하여 애플리케이션 보안을 위해 원하는 수의 추가 인증서를 클러스터에 설치할 수 있습니다.
해당하는 경우 미사용 암호화 및 전송 중 암호화에 대한 Security Center 권장 사항을 따릅니다. 

- [Azure를 사용한 전송 중 암호화 이해](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

- [서비스 패브릭 클러스터 보안 시나리오](service-fabric-cluster-security.md)

- [TLS 구성에 대한 Service Fabric 문제 해결 가이드](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: 데이터 식별, 분류 및 손실 방지 기능은 아직 Azure Storage 또는 컴퓨팅 리소스에 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고 고객 데이터 손실 및 노출을 방지하기 위해 많은 시간을 투자합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: 중요한 정보를 저장하거나 처리하는 Service Fabric 클러스터의 경우 태그를 사용하여 클러스터 및 관련 리소스를 중요한 것으로 표시합니다. 데이터 식별, 분류, 손실 방지 기능은 아직 Azure Storage 또는 컴퓨팅 리소스에 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고 고객 데이터 손실 및 노출을 방지하기 위해 많은 시간을 투자합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: 모든 Azure 리소스에서 미사용 암호화를 사용합니다. Microsoft는 Azure에서 암호화 키를 관리할 수 있도록 허용하는 것을 권장하지만, 일부 인스턴스에서 사용자 고유의 키를 관리할 수 있는 옵션이 있습니다. 

- [Azure의 저장 데이터 암호화 이해](../security/fundamentals/encryption-atrest.md)  

- [고객 관리형 암호화 키를 구성하는 방법](../storage/common/customer-managed-keys-configure-key-vault.md)

- [Windows에서 Azure Service Fabric 클러스터 노드에 디스크 암호화 사용](service-fabric-enable-azure-disk-encryption-windows.md)

- [Linux에서 Azure Service Fabric 클러스터 노드에 디스크 암호화 사용](service-fabric-enable-azure-disk-encryption-linux.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.ServiceFabric**:

[!INCLUDE [Resource Policy for Microsoft.ServiceFabric 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.servicefabric-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 기록 및 경고

**지침**: Azure Monitor를 Azure 활동 로그에 사용하여 중요한 Azure 리소스가 변경되는 경우에 대한 경고를 만듭니다. 

- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](/azure/azure-monitor/platform/alerts-activity-log)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="vulnerability-management"></a>취약성 관리

*자세한 내용은 [Azure Security Benchmark: 취약성 관리](../security/benchmarks/security-control-vulnerability-management.md)를 참조하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행

**지침**: 서비스의 견고성과 안정성을 평가하기 위해 Service Fabric 오류 분석 서비스 및 비정상 상황 서비스를 정기적으로 실행하여 클러스터 전체에서 오류를 시뮬레이션합니다.

Security Center의 권장 사항에 따라 Azure 가상 머신 및 컨테이너 이미지에 대한 취약성 평가를 수행합니다. 

타사 솔루션을 사용하여 네트워크 디바이스 및 웹 애플리케이션에 대한 취약성 평가를 수행합니다. 원격 검사를 수행하는 경우 단일 영구 관리 계정을 사용하지 않습니다. 검사 계정에 대한 JIT 프로비전 방법을 구현하는 것이 좋습니다. 검사 계정에 대한 자격 증명은 보호하고, 모니터링하고, 취약성 검사에만 사용해야 합니다. 

- [Service Fabric 오류 분석 서비스 소개](service-fabric-testability-overview.md)

- [Service Fabric 클러스터에서 제어되는 비정상 상황 유도](service-fabric-controlled-chaos.md)

- [Azure Security Center 취약성 평가 권장 사항을 구현하는 방법](../security-center/deploy-vulnerability-assessment-vm.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화된 운영 체제 패치 관리 솔루션 배포

**지침**: Service Fabric 클러스터의 가상 머신 확장 집합에서 자동 OS 이미지 업그레이드를 사용하도록 설정합니다. 

또는 프로덕션 환경으로 이동하기 전에 OS 패치를 먼저 테스트하려면 수동 트리거를 확장 집합의 OS 이미지 업그레이드에 사용합니다. 수동 트리거 옵션은 기본 제공 롤백을 제공하지 않습니다. Azure Automation의 업데이트 관리를 사용하여 OS 패치를 모니터링합니다. 

- [Service Fabric 클러스터 노드에 대한 패치 관리](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code#azure-virtual-machine-operating-system-automatic-upgrade-configuration)

- [Azure 가상 머신 확장 집합을 사용하여 자동 OS 업그레이드](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)

- [최신 확장 집합 모델로 VM을 최신 상태로 유지하는 방법](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

- [Azure Automation - 업데이트 관리 개요](/azure/automation/update-management/update-mgmt-overview)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: 타사 소프트웨어 타이틀을 위한 자동화된 패치 관리 솔루션 배포

**지침**: Azure Service Fabric 클러스터의 가상 머신 확장 집합에서 자동 OS 이미지 업그레이드를 사용하도록 설정합니다. POA(패치 오케스트레이션 애플리케이션)는 Azure 외부에서 호스팅되는 Service Fabric 클러스터를 위한 대체 솔루션입니다. POA는 일부 추가 호스팅 오버헤드를 포함하여 Azure 클러스터에서 사용할 수 있습니다.

- [Service Fabric 클러스터 노드에 대한 패치 관리](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code#azure-virtual-machine-operating-system-automatic-upgrade-configuration)

- [Azure 가상 머신 확장 집합을 사용하여 자동 OS 업그레이드](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)

- [Service Fabric 클러스터에 대한 OS 패치 일정을 구성하는 방법](service-fabric-patch-orchestration-application.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 연속 취약성 검색 비교

**참고 자료**: 검사 결과를 일관된 간격으로 내보내고 결과를 비교하여 취약성이 수정되었는지 확인합니다. Security Center에서 제안하는 취약성 관리 권장 사항을 사용하는 경우 선택한 솔루션의 포털로 회전하여 기록 검사 데이터를 확인할 수 있습니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**지침**: 일반적인 위험 채점 프로그램(예: Common Vulnerability Scoring System) 또는 타사 검사 도구에 제공된 기본 위험 등급을 사용합니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure Security Benchmark: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화된 자산 검색 솔루션 사용

**지침**: Azure Resource Graph를 사용하여 구독 내 모든 리소스(예: 컴퓨팅, 스토리지, 네트워크, 포트, 프로토콜 등)를 쿼리/검색합니다. 테넌트에서 적절한 권한(읽기)을 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거합니다.

클래식 Azure 리소스는 Resource Graph를 통해 검색할 수 있지만 앞으로 Azure Resource Manager 리소스를 만들어 사용하는 것이 좋습니다.

- [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

- [Azure 구독을 확인하는 방법](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Azure RBAC 이해](../role-based-access-control/overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타데이터를 제공하는 Azure 리소스에 태그를 적용하여 논리적으로 분류 체계로 구성합니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 해당하는 경우 태그 지정, 관리 그룹 및 별도의 구독을 사용하여 자산을 구성하고 추적합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: 승인된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 컴퓨팅 리소스에 대해 승인된 Azure 리소스 및 승인된 소프트웨어를 정의합니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.

허용되지 않는 리소스 종류

허용되는 리소스 유형

Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리/검색합니다. 환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: 타사 솔루션을 구현하여 클러스터 노드에 승인되지 않은 소프트웨어 애플리케이션이 있는지 모니터링합니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: Azure Resource Graph를 사용하여 구독 내에서 Service Fabric 클러스터를 포함한 모든 리소스(예: 컴퓨팅, 스토리지, 네트워크, 포트 및 프로토콜 등)를 쿼리하고 검색합니다. 승인되지 않은 Azure 리소스가 검색되면 제거합니다. Service Fabric 클러스터 노드의 경우 승인되지 않은 소프트웨어를 제거하거나 경고하는 타사 솔루션을 구현합니다.

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 애플리케이션만 사용

**지침**: Service Fabric 클러스터 노드의 경우 권한 없는 소프트웨어가 실행되지 않도록 방지하는 타사 솔루션을 구현합니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy를 사용하여 환경에서 프로비전할 수 있는 서비스를 제한합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: 승인된 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: Azure Service Fabric 클러스터 노드의 경우 권한 없는 소프트웨어가 실행되지 않도록 방지하는 타사 솔루션을 구현합니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Azure Resource Manager와 상호 작용하는 사용자 기능 제한

**지침**: Azure 조건부 액세스를 사용하여 "Microsoft Azure Management" 앱에 대한 "액세스 차단"을 구성함으로써 Azure Resource Manager와 상호 작용하는 사용자의 기능을 제한합니다. 

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 사용자가 컴퓨팅 리소스 내에서 스크립트를 실행하는 기능 제한

**지침**: 운영 체제별 구성 또는 타사 리소스를 사용하여 Azure Compute 리소스 내에서 스크립트를 실행하는 사용자의 기능을 제한할 수 있습니다.

- [예: Windows 환경에서 PowerShell 스크립트 실행을 제어하는 방법](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7&amp;preserve-view=true)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**지침**: 비즈니스 운영에 필요하지만 조직에 더 높은 위험이 발생할 수 있는 소프트웨어는 자체 가상 머신 및/또는 가상 네트워크 내에서 격리하고, Azure Firewall 또는 네트워크 보안 그룹을 사용하여 충분히 보호해야 합니다. 

- [가상 네트워크를 만드는 방법](../virtual-network/quick-create-portal.md) 

- [보안 구성을 사용하여 NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure Security Benchmark: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: "Microsoft.ServiceFabric" 네임스페이스에서 Azure Policy 별칭을 사용하여 Service Fabric 클러스터의 네트워크 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다.

- [사용 가능한 Azure Policy 별칭을 확인하는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

**지침**: Service Fabric 운영 체제 이미지는 Microsoft에서 관리하고 유지 관리합니다. 고객은 클러스터 노드의 운영 체제에 대한 보안 구성을 구현해야 합니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: [거부] 및 [존재하지 않으면 배포] Azure Policy 효과를 사용하여 Azure Service Fabric 클러스터 및 관련 리소스에 대한 보안 설정을 적용합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지 관리

**지침**: Service Fabric 클러스터 운영 체제 이미지는 Microsoft에서 관리하고 유지 관리합니다. 고객은 OS 수준 상태 구성을 구현해야 합니다.

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: 사용자 지정 Azure 정책 정의를 사용하는 경우 Azure DevOps 또는 Azure Repos를 사용하여 코드를 안전하게 저장하고 관리합니다.

- [Azure DevOps에 코드를 저장하는 방법](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Azure Repos 설명서](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

**지침**: 사용자 지정 이미지를 사용하는 경우 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 권한 있는 사용자만 이미지에 액세스할 수 있도록 합니다. 컨테이너 이미지의 경우 Azure Container Registry에 저장하고 Azure RBAC를 활용하여 권한 있는 사용자만 이미지에 액세스할 수 있도록 합니다. 

- [Azure RBAC 이해](../role-based-access-control/rbac-and-directory-admin-roles.md) 

- [Container Registry용 Azure RBAC 이해](../container-registry/container-registry-roles.md) 

- [Azure RBAC를 구성하는 방법](../role-based-access-control/quickstart-assign-role-user-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스를 위한 구성 관리 도구 배포

**지침**: "Microsoft.ServiceFabric" 네임스페이스에서 Azure Policy 별칭을 사용하여 시스템 구성을 경고, 감사 및 적용하는 사용자 지정 정책을 만듭니다. 또한 정책 예외를 관리하는 프로세스와 파이프라인을 개발합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대한 자동화된 구성 모니터링 구현

**지침**: "Microsoft.ServiceFabric" 네임스페이스에서 Azure Policy 별칭을 사용하여 Service Fabric 클러스터의 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다.

- [사용 가능한 Azure Policy 별칭을 확인하는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대한 자동화된 구성 모니터링 구현

**지침**: Security Center를 사용하여 OS에 대한 기준 검사 및 컨테이너에 대한 Docker 설정을 수행합니다. 

- [Azure Security Center 컨테이너 권장 사항 이해](../security-center/container-security.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: Azure Key Vault와 함께 관리되는 서비스 ID를 사용하여 클라우드 애플리케이션에 대한 비밀 관리를 간소화하고 보호합니다. 

- [Service Fabric에서 Azure에 관리 ID 사용](concepts-managed-identity.md)

- [새 Service Fabric 클러스터에 대한 관리 ID 지원 구성](configure-new-azure-service-fabric-enable-managed-identity.md)

- [Service Fabric 애플리케이션에서 관리 ID 사용](how-to-managed-identity-service-fabric-app-code.md)

- [Service Fabric 애플리케이션에 대한 KeyVaultReference 지원](service-fabric-keyvault-references.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: 관리 ID는 Azure에 배포된 Service Fabric 클러스터 및 Azure 리소스로 배포된 애플리케이션에 사용할 수 있습니다. 관리 ID를 사용하면 코드의 자격 증명 없이 Key Vault를 포함하여 Azure AD(Azure Active Directory) 인증을 지원하는 모든 서비스에 인증할 수 있습니다.

- [Service Fabric에서 Azure에 관리 ID 사용](concepts-managed-identity.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: Azure Service Fabric 배포와 관련된 코드를 사용하는 경우 코드 내에서 자격 증명을 식별하는 자격 증명 스캐너를 구현할 수 있습니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

Azure Key Vault를 사용하여 Service Fabric 클러스터 인증서를 자동으로 회전시킵니다.

- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Service Fabric 클러스터에서 인증서 관리](https://docs.microsoft.com/azure/service-fabric/cluster-security-certificate-management#certificate-rotation)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [Azure Security Benchmark: 맬웨어 방어](../security/benchmarks/security-control-malware-defense.md)를 참조하세요.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: 중앙 관리형 맬웨어 방지 소프트웨어 사용

**지침**: 기본적으로 Windows Defender 바이러스 백신은 Windows Server 2016에 설치됩니다. 사용자 인터페이스가 기본적으로 일부 SKU에 설치되지만 필요하지는 않습니다.

Windows Defender를 사용하지 않는 경우 구성 규칙에 대한 맬웨어 방지 설명서를 참조하세요. Linux에서는 Windows Defender가 지원되지 않습니다.

- [Windows Server 2016의 Windows Defender 바이러스 백신 이해](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure Security Benchmark: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**지침**: Service Fabric의 백업 및 복원 서비스를 사용하면 상태 저장 서비스에 저장된 정보를 자동으로 쉽게 자동으로 백업할 수 있습니다. 애플리케이션 데이터를 정기적으로 백업하는 것은 데이터 손실 및 서비스 비가용성을 방지하는 데 필수적입니다. Service Fabric은 백업 및 복원 서비스(선택 사항)를 제공하므로 추가 코드를 작성할 필요 없이 상태 저장 Reliable Services(Actor Services 포함)의 정기적인 백업을 구성할 수 있습니다. 또한 이전에 수행한 백업 복원을 수월하게 수행할 수 있습니다.

- [Azure Service Fabric 클러스터에서 정기적인 백업 및 복원](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업 수행 및 고객 관리형 키 백업

**지침**: Service Fabric 클러스터에서 백업 복원 서비스를 사용하도록 설정하고, 상태 저장 서비스를 정기적으로 요청 시 백업하는 백업 정책을 만듭니다. Azure Key Vault 내에서 고객 관리형 키를 백업합니다.

- [Azure Service Fabric 클러스터에서 정기적인 백업 및 복원](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

- [Azure Service Fabric의 정기 백업 구성 이해](service-fabric-backuprestoreservice-configure-periodic-backup.md)

- [Azure에서 키 자격 증명 모음 키를 백업하는 방법](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리형 키를 포함한 모든 백업의 유효성 검사

**지침**: 백업 구성 정보 및 사용 가능한 백업을 정기적으로 검토하여 백업 복원 서비스에서 복원을 수행할 수 있는지 확인합니다. 백업된 고객 관리형 키의 복원을 테스트합니다.

- [Azure Service Fabric의 정기 백업 구성 이해](service-fabric-backuprestoreservice-configure-periodic-backup.md)

- [Azure Service Fabric에서 백업 복원](service-fabric-backup-restore-service-trigger-restore.md)

- [Azure에서 키 자격 증명 모음 키를 복원하는 방법](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리형 키 보호 보장

**지침**: Service Fabric 백업 복원 서비스의 백업은 구독의 Azure Storage 계정을 사용합니다. Azure Storage는 미사용 스토리지 계정의 모든 데이터를 암호화합니다. 기본적으로 데이터는 Microsoft 관리형 키로 암호화됩니다. 암호화 키에 대한 추가 제어를 위해 스토리지 데이터의 암호화에 사용할 고객 관리형 키를 제공할 수 있습니다.

고객 관리형 키를 사용하는 경우 Key Vault의 일시 삭제를 사용하도록 설정하여 실수로 인한 삭제 또는 악의적인 삭제로부터 키를 보호해야 합니다.

- [Azure Storage 미사용 데이터 암호화](../storage/common/storage-service-encryption.md)

- [Key Vault에서 일시 삭제를 사용하도록 설정하는 방법](../storage/blobs/soft-delete-blob-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../security/benchmarks/security-control-incident-response.md)을 참조하세요.

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 개발합니다. 탐지에서 인시던트 사후 검토까지의 인시던트 처리 및 관리 단계뿐만 아니라 담당자의 모든 역할도 정의하는 서면 인시던트 대응 계획이 있는지 확인합니다. 

- [자체 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [NIST의 컴퓨터 보안 인시던트 처리 가이드를 사용하여 고유한 인시던트 대응 계획 수립 지원](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center에서 심각도를 각 경고에 할당하여 먼저 조사해야 하는 경고에 대한 우선 순위를 지정합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용되는 결과 또는 메트릭의 신뢰도 및 경고가 발생한 활동 배후에 악의적인 의도가 있었다는 신뢰 수준을 기준으로 합니다.

또한 태그를 사용하여 구독을 표시하고, Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 식별하고 분류할 수 있는 명명 시스템을 만듭니다.  인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다. 

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md) 

- [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md).

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 격차를 식별하고 필요에 따라 계획을 수정합니다. 

- [NIST 게시물 - IT 계획 및 기능 테스트, 학습 및 연습 프로그램에 대한 가이드](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다. 

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: 연속 내보내기 기능을 사용하여 Security Center 경고 및 추천 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Security Center 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다.

- [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 대한 "Logic Apps"를 통해 응답을 자동으로 트리거합니다.

- [워크플로 자동화와 Logic Apps를 구성하는 방법](../security-center/workflow-automation.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure Security Benchmark: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대한 침투 테스트를 정기적으로 수행 및 모든 중요한 보안 결과를 수정

**지침**: 침투 테스트가 Microsoft 정책을 위반하지 않도록 Microsoft Cloud 침투 테스트 참여 규칙을 따릅니다. Microsoft의 전략과 Microsoft에서 관리하는 클라우드 인프라, 서비스, 애플리케이션에 대한 레드 팀 실행 및 실시간 사이트 침투 테스트를 사용합니다. 

- [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud 레드 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
