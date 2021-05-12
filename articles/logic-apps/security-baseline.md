---
title: Logic Apps에 대한 Azure 보안 기준
description: Logic Apps 보안 기준은 Azure Security Benchmark에 지정된 보안 권장 사항을 구현하기 위한 절차 지침과 리소스를 제공합니다.
author: msmbaldwin
ms.service: logic-apps
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 0b74962a192de6a10c09b9855780ed0cd6244515
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967299"
---
# <a name="azure-security-baseline-for-logic-apps"></a>Logic Apps에 대한 Azure 보안 기준

이 보안 기준은 [Azure Security Benchmark 버전 1.0](../security/benchmarks/overview-v1.md)에서 Logic Apps에 대한 지침을 적용합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 Azure Security Benchmark 및 Logic Apps에 적용되는 관련 지침으로 정의된 **보안 컨트롤** 에 따라 그룹화됩니다. Logic Apps에 적용되지 않거나 Microsoft의 책임인 **컨트롤** 은 제외되었습니다.

Logic Apps가 완전히 Azure Security Benchmark에 매핑되는 방법을 보려면 [전체 Logic Apps 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조하세요.

## <a name="network-security"></a>네트워크 보안

자세한 내용은 [Azure Security Benchmark: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조하세요.

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: "전역" 다중 테넌트 Logic Apps 서비스에서 실행되는 커넥터는 Microsoft에서 배포하고 관리합니다. 이러한 커넥터는 Office 365, Azure Blob Storage, SQL Server, Dynamics, Salesforce, SharePoint 등을 포함하여 클라우드 서비스, 온-프레미스 시스템 또는 둘 다에 액세스하기 위한 트리거와 작업을 제공합니다. 네트워크 보안 그룹 또는 Azure 방화벽에 대한 규칙을 지정하는 경우 AzureConnectors 서비스 태그를 사용하여 관련 리소스에 대한 액세스를 허용할 수 있습니다.

Azure 가상 네트워크의 리소스에 직접 액세스해야 하는 논리 앱의 경우 전용 리소스에서 논리 앱을 빌드, 배포 및 실행할 수 있는 ISE(통합 서비스 환경)를 만들 수 있습니다. 일부 Azure 가상 네트워크는 프라이빗 엔드포인트(Azure Private Link)를 사용하여 Azure Storage, Azure Cosmos DB 또는 Azure SQL Database, Azure에서 호스트되는 파트너 서비스 또는 고객 서비스와 같은 Azure PaaS 서비스에 대한 액세스를 제공합니다. 논리 앱이 프라이빗 엔드포인트를 사용하는 가상 네트워크에 액세스해야 하는 경우 ISE 내에서 해당 논리 앱을 만들고, 배포하고, 실행해야 합니다.

ISE를 만들 때 내부 또는 외부 액세스 엔드포인트 중 하나를 사용하도록 선택할 수 있습니다. 이 선택에 따라 ISE의 논리 앱에서 요청 또는 웹후크 트리거가 가상 네트워크 외부의 호출을 받을 수 있을지 여부가 결정됩니다. 내부 및 외부 액세스 엔드포인트는 가상 네트워크 내부 또는 외부에서 실행에 대한 입력 및 출력을 포함하여 논리 앱의 실행 기록을 볼 수 있는지 여부에도 영향을 줍니다.

ISE와 관련된 모든 가상 네트워크 서브넷 배포에 애플리케이션의 신뢰할 수 있는 포트 및 원본에 특정한 네트워크 액세스 제어와 함께 네트워크 보안 그룹이 적용되었는지 확인합니다. ISE에서 논리 앱을 배포하는 경우 Private Link를 사용합니다. Azure Private Link를 사용하면 가상 네트워크의 프라이빗 엔드포인트를 통해 Azure PaaS Services 및 Azure 호스트 고객 소유/파트너 서비스에 액세스할 수 있습니다. 또는 특정 사용 사례가 있는 경우 Azure Firewall을 구현하여 이 요구 사항을 충족할 수 있습니다. 보안 규칙을 설정할 때 복잡성을 줄이기 위해 특정 Azure 서비스에 대한 IP 주소 접두사 그룹을 나타내는 서비스 태그를 사용합니다.

- [Logic Apps용 커넥터 이해](../connectors/apis-list.md)

- [Azure의 서비스 태그 이해](../virtual-network/service-tags-overview.md)

- [ISE(통합 서비스 환경)를 사용하여 Azure Logic Apps에서 Azure Virtual Network 리소스에 액세스 이해](connect-virtual-network-vnet-isolated-environment-overview.md)

- [가상 네트워크 서비스 엔드포인트 이해](../virtual-network/virtual-network-service-endpoints-overview.md)

- [Azure Private Link 이해](../private-link/private-link-overview.md)

- [ISE 엔드포인트 액세스 이해](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview#ise-endpoint-access)

- [Virtual Network를 만드는 방법](../virtual-network/quick-create-portal.md)

- [보안 구성을 사용하여 NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Firewall 배포 및 구성 방법](../firewall/tutorial-firewall-deploy-portal.md)

- [ISE에 대한 액세스가 가능하도록 설정하는 방법](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment#enable-access-for-ise)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: 가상 네트워크, 서브넷 및 네트워크 인터페이스의 구성 및 트래픽을 모니터링 및 기록

**지침**: 외부 액세스 지점을 사용하는 ISE(통합 서비스 환경)에서 논리 앱을 실행하는 경우 NSG(네트워크 보안 그룹)를 사용하여 데이터 반출의 위험을 줄일 수 있습니다. NSG 흐름 로그를 사용하도록 설정하고, 트래픽 감사를 위해 로그를 Azure Storage 계정에 보냅니다. 또한 NSG 흐름 로그를 Log Analytics 작업 영역에 보내고, 트래픽 분석을 사용하여 Azure 클라우드의 트래픽 흐름에 대한 인사이트를 제공할 수 있습니다. 트래픽 분석의 장점으로 네트워크 활동의 시각화 핫 스폿 식별, 보안 위협 식별, 트래픽 흐름 패턴 이해, 네트워크 구성 오류 파악 등의 기능이 있습니다.

- [ISE 엔드포인트 액세스 이해](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview#ise-endpoint-access)

- [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [트래픽 분석을 사용하도록 설정하고 사용하는 방법](../network-watcher/traffic-analytics.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**:논리 앱이 들어오는 호출 또는 요청을 수신하는 요청 기반 트리거(예: 요청 또는 Webhook 트리거)를 사용하는 경우에는 권한 있는 클라이언트만 논리 앱을 호출할 수 있도록 액세스를 제한할 수 있습니다.

ISE(통합 서비스 환경)에서 논리 앱을 실행하는 경우 DDoS 공격으로부터 보호하기 위해 ISE와 연결된 가상 네트워크에서 DDoS Protection Standard를 사용하도록 설정합니다. Azure Security Center 통합 위협 인텔리전스를 사용하여 알려진 악성 인터넷 IP 주소 또는 사용하지 않는 인터넷 IP 주소와의 통신을 거부합니다.

위협 인텔리전스를 사용하도록 설정하고 악의적인 네트워크 트래픽에 대해 "경고 및 거부"로 구성된 각 조직의 네트워크 경계에서 Azure Firewall을 배포합니다.

Azure Security Center Just-in-Time 네트워크 액세스를 사용하여 제한된 기간 동안 엔드포인트의 노출을 승인된 IP 주소로 제한하는 NSG를 구성합니다.

또한, Azure Security Center 적응형 네트워크 강화를 사용하여 실제 트래픽 및 위협 인텔리전스를 기반으로 포트와 원본 IP를 제한하는 NSG 구성을 권장합니다.

- [Logic Apps에 대한 인바운드 호출을 보호하는 방법](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#secure-inbound-requests)

- [인바운드 IP 주소 제한 방법](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#restrict-inbound-ip-addresses)

- [DDoS 보호를 구성하는 방법](../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall 배포 방법](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Security Center 통합 위협 인텔리전스 이해](../security-center/azure-defender.md)

- [Azure Security Center 적응형 네트워크 강화 이해](../security-center/security-center-adaptive-network-hardening.md)

- [Azure Security Center Just-in-Time 네트워크 액세스 제어 이해](../security-center/security-center-just-in-time.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷 기록

**지침**: 외부 액세스 지점을 사용하는 ISE(통합 서비스 환경)에서 논리 앱을 실행하는 경우 NSG(네트워크 보안 그룹)를 사용하여 데이터 반출 위험을 줄일 수 있습니다. NSG 흐름 로그를 사용하도록 설정하고, 트래픽 감사를 위해 로그를 Azure Storage 계정에 보냅니다. 또한 NSG 흐름 로그를 Log Analytics 작업 영역에 보내고, 트래픽 분석을 사용하여 Azure 클라우드의 트래픽 흐름에 대한 인사이트를 제공할 수 있습니다. 트래픽 분석의 장점으로 네트워크 활동의 시각화 핫 스폿 식별, 보안 위협 식별, 트래픽 흐름 패턴 이해, 네트워크 구성 오류 파악 등의 기능이 있습니다.

네트워크 트래픽에 대한 추가 보호 및 정보를 제공하기 위해 각 Application Gateway 인스턴스에서 사용하도록 설정한 경우에만 생성되는 액세스 로그를 참조할 수 있습니다. 이 로그를 사용하여 Application Gateway 액세스 패턴을 확인하고 중요한 정보를 분석할 수 있습니다. 여기에는 호출자의 IP, 요청된 URL, 응답 대기 시간, 반환 코드, 바이트 입출력이 포함됩니다.

그렇지 않으면 마켓플레이스의 타사 솔루션을 활용하여 이 요구 사항을 충족할 수 있습니다.

- [ISE 엔드포인트 액세스 이해](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview#ise-endpoint-access)

- [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [트래픽 분석을 사용하도록 설정하고 사용하는 방법](../network-watcher/traffic-analytics.md)

- [내부 VNET의 API Management를 Application Gateway와 통합하는 방법](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [WAF 액세스 로그를 이해하는 방법](https://docs.microsoft.com/azure/web-application-firewall/ag/web-application-firewall-logs#access-log)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 IDS/IPS(침입 탐지/침입 방지 시스템) 배포

**지침**: 페이로드 검사 기능을 사용하여 IDS/IPS 기능을 지원하는 Azure Marketplace에서 제공하는 제안을 선택합니다.  페이로드 검사에 기반한 침입 탐지 및/또는 방지 기능이 요구 사항이 아니면, 위협 인텔리전스가 포함된 Azure Firewall을 사용할 수 있습니다. Azure Firewall 위협 인텔리전스 기반 필터링은 알려진 악성 IP 주소 및 도메인과 주고받는 트래픽을 경고하고 거부할 수 있습니다. IP 주소 및 도메인은 Microsoft 위협 인텔리전스 피드에서 제공됩니다.

조직의 각 네트워크 경계에 원하는 방화벽 솔루션을 배포하여 악성 트래픽을 탐지 및/또는 거부할 수 있습니다.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Azure Firewall 배포 방법](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Firewall을 사용하여 경고를 구성하는 방법](../firewall/threat-intel.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침**: ISE(통합 서비스 환경)에서 논리 앱을 실행하는 경우 Azure Application Gateway를 배포합니다.

- [내부 VNET의 API Management를 Application Gateway와 통합하는 방법](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [HTTPS를 사용하도록 Application Gateway를 구성하는 방법](../application-gateway/create-ssl-portal.md) 

- [Azure 애플리케이션 게이트웨이를 사용하는 계층 7 부하 분산 이해](../application-gateway/overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: Azure Logic Apps 인스턴스에 액세스해야 하는 리소스의 경우 가상 네트워크 서비스 태그를 사용하여 네트워크 보안 그룹 또는 Azure Firewall에서 네트워크 액세스 제어를 정의합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 서비스 태그 이름(예: LogicApps, LogicAppsManagement)을 규칙의 적절한 원본 또는 대상 필드에 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. Microsoft에서는 서비스 태그에서 압축한 주소 접두사를 관리하고 주소를 변경하는 대로 서비스 태그를 자동으로 업데이트합니다.

- [서비스 태그 사용에 대한 자세한 정보](../virtual-network/service-tags-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지

**지침**: Azure Policy를 사용하여 Azure Logic Apps 인스턴스와 관련된 네트워크 리소스에 대한 표준 보안 구성을 정의하고 구현합니다. "Microsoft.Logic" 및 "Microsoft.Network" 네임스페이스에서 Azure Policy 별칭을 사용하여 Azure Logic Apps 인스턴스의 네트워크 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. 다음과 같은 기본 제공 정책 정의를 사용할 수도 있습니다.

- Logic Apps의 진단 로그를 사용하도록 설정해야 함

- DDoS Protection 표준을 사용하도록 설정해야 합니다.

또한 Azure Blueprints를 사용하여 주요 환경 아티팩트(예: Azure Resource Manager 템플릿, Azure RBAC(Azure 역할 기반 액세스 제어) 및 정책)를 단일 청사진 정의로 패키지화하여 대규모 Azure 배포를 간소화할 수 있습니다. Blueprint를 새로운 구독 및 환경에 쉽게 적용하고 버전 관리를 통해 제어 및 관리를 세부적으로 조정합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint를 만드는 방법](../governance/blueprints/create-blueprint-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

**지침**: 개별 NSG 규칙의 경우 "설명" 필드를 사용하여 네트워크에서 주고 받는 트래픽을 허용하는 모든 규칙에 대한 비즈니스 요구 및/또는 기간(등)을 지정합니다.

태그 지정과 관련된 기본 제공 Azure Policy 정의(예: "태그 및 해당 값 필요")를 사용하여 모든 리소스에서 태그를 포함하도록 만들고 태그가 없는 기존 리소스를 알립니다.

Azure PowerShell 또는 Azure CLI를 사용하여 태그를 기준으로 리소스에 대한 작업을 조회하거나 수행할 수 있습니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

- [Virtual Network를 만드는 방법](../virtual-network/quick-create-portal.md)

- [보안 구성을 사용하여 NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

- [Logic Apps에 대한 Azure Policy 정의 목록](policy-reference.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용하여 네트워크 리소스 구성을 모니터링하고 Azure Logic Apps 인스턴스 관련 네트워크 리소스에 대한 변경 내용을 검색합니다. 중요한 네트워크 리소스에 변경 내용이 발생하는 경우 트리거하는 Azure Monitor 내에서 경고를 만듭니다.

- [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Azure Monitor에서 경고를 만드는 방법](/azure/azure-monitor/platform/alerts-activity-log)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure Security Benchmark: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조하세요.*

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: 런타임 중에 논리 앱에 대한 보다 풍부한 디버깅 정보를 얻기 위해 Azure Monitor 로그를 설정 및 사용하여 트리거 이벤트, 실행 이벤트 및 작업 이벤트와 같은 런타임 데이터 및 이벤트에 대한 정보를 기록하고 Log Analytics 작업 영역에 저장할 수 있습니다. Azure Monitor를 사용하면 클라우드 및 온-프레미스 환경을 모니터링하여 가용성과 성능을 더 쉽게 유지할 수 있습니다. Azure Monitor 로그를 사용하여 이 정보를 수집하고 검토하는 데 도움이 되는 로그 쿼리를 만들 수 있습니다. 또한 Azure Storage 및 Azure Event Hubs와 같은 다른 Azure 서비스에서 이 진단 데이터를 사용할 수도 있습니다.

또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다. 

- [Azure 활동 로그에 대한 진단 설정을 사용하도록 설정하는 방법](/azure/azure-monitor/platform/activity-log)

- [Azure Monitor 로그를 설정하고 Azure Logic Apps에 대한 진단 데이터를 수집하는 방법](monitor-logic-apps-log-analytics.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: 런타임 중에 논리 앱에 대한 보다 풍부한 디버깅 정보를 얻기 위해 Azure Monitor 로그를 설정 및 사용하여 트리거 이벤트, 실행 이벤트 및 작업 이벤트와 같은 런타임 데이터 및 이벤트에 대한 정보를 기록하고 Log Analytics 작업 영역에 저장할 수 있습니다. Azure Monitor를 사용하면 클라우드 및 온-프레미스 환경을 모니터링하여 가용성과 성능을 더 쉽게 유지할 수 있습니다. Azure Monitor 로그를 사용하여 이 정보를 수집하고 검토하는 데 도움이 되는 로그 쿼리를 만들 수 있습니다. 또한 Azure Storage 및 Azure Event Hubs와 같은 다른 Azure 서비스에서 이 진단 데이터를 사용할 수도 있습니다.

또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다. 

- [Azure 활동 로그에 대한 진단 설정을 사용하도록 설정하는 방법](/azure/azure-monitor/platform/activity-log)

- [Azure Monitor 로그를 설정하고 Azure Logic Apps에 대한 진단 데이터를 수집하는 방법](monitor-logic-apps-log-analytics.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 관한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초가 됩니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 제어와 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 플랜이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Logic**:

[!INCLUDE [Resource Policy for Microsoft.Logic 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.logic-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침**: 논리 앱을 만들고 실행한 후에는 해당 논리 앱의 실행 상태, 실행 기록, 트리거 기록 및 성능을 확인할 수 있습니다. 실시간 이벤트 모니터링 및 보다 풍부한 디버깅은 Azure Monitor 로그를 사용하여 논리 앱에 대한 진단 로깅을 설정합니다. 이 Azure 서비스를 사용하면 클라우드 및 온-프레미스 환경을 모니터링하여 가용성과 성능을 더 쉽게 유지할 수 있습니다. 그러면 트리거 이벤트, 실행 이벤트 및 작업 이벤트와 같은 이벤트를 찾고 볼 수 있습니다. Azure Monitor 로그에 이 정보를 저장하여 이 정보를 찾고 분석하는 데 도움이 되는 로그 쿼리를 만들 수 있습니다. 또한 Azure Storage 및 Azure Event Hubs와 같은 다른 Azure 서비스에서 이 진단 데이터를 사용할 수도 있습니다.

Azure Monitor에서 조직의 규정 준수 규정에 따라 Azure Logic Apps 인스턴스와 연결된 로그의 로그 보존 기간을 설정합니다.

- [실행 상태를 모니터링하고, 트리거 기록을 검토하고, Azure Logic Apps에 대한 경고를 설정하는 방법](monitor-logic-apps.md)

- [로그 보존 매개 변수를 설정하는 방법](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: 논리 앱에 대한 로깅을 설정하려면 논리 앱을 만들 때 Log Analytics를 사용하도록 설정하거나 기존 논리 앱에 대한 Log Analytics 작업 영역에 Logic Apps 관리 솔루션을 설치할 수 있습니다. 이 솔루션은 논리 앱 실행에 대한 집계 정보를 제공하며, 상태, 실행 시간, 다시 제출 상태 및 상관 관계 ID와 같은 특정 세부 정보를 포함합니다. 다음으로 이 정보에 대한 로깅을 사용하도록 설정하고 쿼리를 만들려면 Azure Monitor 로그를 설정합니다.

Azure 활동 로그 진단 설정을 사용하도록 설정하고, 로그를 Log Analytics 작업 영역에 보낼 수도 있습니다. Log Analytics에서 쿼리를 수행하여 용어를 검색하고, 추세를 식별하고, 패턴을 분석하고, Azure Logic Apps에 대해 수집되었을 수 있는 Activity Log 데이터를 기반으로 하여 다양한 인사이트를 제공합니다.

또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다. 

- [Azure Monitor 로그를 설정하고 Azure Logic Apps에 대한 진단 데이터를 수집하는 방법](monitor-logic-apps-log-analytics.md)

- [Azure 활동 로그에 대한 진단 설정을 사용하도록 설정하는 방법](/azure/azure-monitor/platform/activity-log)

- [Azure Monitor의 Log Analytics에서 Azure 활동 로그를 수집하고 분석하는 방법](/azure/azure-monitor/platform/activity-log-collect)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상 활동에 대한 경고 사용

**지침**: 보안 로그 및 이벤트에서 발견된 비정상적인 활동을 모니터링하고 경고하기 위해 Log Analytics에서 Azure Security Center를 사용합니다.

또는, 데이터를 사용하고 Azure Sentinel로 온보딩할 수 있습니다.

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

- [Azure Security Center에서 경고를 관리하는 방법](../security-center/security-center-managing-and-responding-alerts.md)

- [Log Analytics 로그 데이터에 대해 경고하는 방법](/azure/azure-monitor/learn/tutorial-response)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [Azure Security Benchmark: ID 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정 인벤토리 유지 관리

**지침**: Azure AD(Azure Active Directory)에는 명시적으로 할당되고 쿼리할 수 있어야 하는 기본 제공 역할이 있습니다. Azure AD PowerShell 모듈을 사용하여 임시 쿼리를 수행해서 관리 그룹의 멤버인 계정을 검색합니다.

로그인하지 않고 Azure AD로 보호되는 다른 리소스에 쉽게 액세스하여 ID를 인증하기 위해 논리 앱에서 자격 증명 또는 비밀 대신 관리 ID(이전의 MSI(관리 서비스 ID))를 사용할 수 있습니다. 이 ID는 Azure에서 관리되며, 비밀을 제공하거나 순환할 필요가 없기 때문에 자격 증명을 보호하는 데 도움이 됩니다.

논리 앱에 대한 모든 요청 엔드포인트는 엔드포인트 URL에 SAS(공유 액세스 서명)를 포함합니다. 요청 기반 트리거에 대한 엔드포인트 URL을 다른 상대방과 공유하는 경우 특정 키를 사용하고 만료 날짜가 있는 콜백 URL을 생성할 수 있습니다. 이렇게 하면 특정 시간 범위에 따라 키를 원활하게 롤링하거나 논리 앱을 트리거하는 액세스를 제한할 수 있습니다.

- [PowerShell을 사용하여 Azure AD에서 디렉터리 역할을 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Azure Logic Apps에서 관리 ID를 사용하여 Azure 리소스에 대한 액세스 인증](create-managed-service-identity.md)

- [PowerShell을 사용하여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

- [SAS를 사용하여 Azure Logic Apps에서 액세스 및 데이터를 보호하는 방법](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#sas)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

**지침**: Azure AD(Azure Active Directory) 및 Azure Logic Apps에는 기본 암호 개념이 없습니다.

기본 인증을 사용하는 경우 사용자 이름 및 암호를 지정해야 합니다. 이러한 자격 증명을 만들 때 강력한 암호 또는 인증을 구성해야 합니다.

코드 제공 인프라(Infrastructure as Code)를 사용하는 경우에는 코드에서 강력한 암호는 피하고 대신 Azure Key Vault를 사용하여 자격 증명을 저장 및 검색합니다.

- [Logic Apps에서 데이터를 보호하고 액세스하는 방법](logic-apps-securing-a-logic-app.md)

- [Azure Key Vault에서 비밀을 설정 및 검색하는 방법](../key-vault/general/quick-create-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다. Azure Security Center ID와 액세스 관리를 사용하여 관리 계정 수를 모니터링합니다.

또한 전용 관리 계정을 추적하는 데 도움이 되도록 Azure 다음과 같은 Azure Security Center 또는 기본 제공 Azure 정책의 추천 사항을 사용할 수 있습니다.

- 구독에 둘 이상의 소유자를 할당해야 합니다.
- 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.
- 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.

자세한 내용은 다음 참조 문서를 참조하세요.

- [Azure Security Center를 사용하여 ID 및 액세스를 모니터링하는 방법(미리 보기)](../security-center/security-center-identity-access.md)

- [Azure Policy를 사용하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory SSO(Single Sign-On) 사용

**지침**: Azure 앱 등록(서비스 주체)을 사용하여 API 호출을 통해 Recovery Services 자격 증명 모음과 상호 작용하는 데 사용할 수 있는 토큰을 검색합니다.

또한 대부분의 커넥터에서는 먼저 대상 서비스 또는 시스템에 대한 연결을 만들고 인증 자격 증명 또는 기타 구성 정보를 제공해야 논리 앱에서 트리거 또는 작업을 사용할 수 있습니다. 예를 들어 데이터에 액세스하거나 사용자 대신 게시하기 위해 Twitter 계정에 대한 연결에 권한을 부여해야 합니다.

Azure AD(Azure Active Directory)를 사용하는 커넥터의 경우 연결을 만드는 것은 액세스 토큰을 암호화한 후 Azure 암호 저장소에 안전하게 저장하는 서비스(예: Office 365, Salesforce 또는 GitHub)에 로그인하는 것을 의미합니다. FTP 및 SQL과 같은 다른 커넥터에는 서버 주소, 사용자 이름 및 암호와 같은 구성 세부 정보를 포함하는 연결이 필요합니다. 이러한 연결 구성 세부 정보 또한 암호화된 후 안전하게 저장됩니다.

- [Azure REST API를 호출하는 방법](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Azure AD에 클라이언트 애플리케이션을 등록하는 방법](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [워크플로 트리거 API 정보](/rest/api/logic/workflowtriggers)

- [커넥터 구성 이해](../connectors/apis-list.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure AD(Azure Active Directory) 다단계 인증을 사용하도록 설정하고, Azure Security Center ID 및 액세스 관리 추천 사항을 따릅니다.

- [Azure에서 다단계 인증을 사용하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: 관리 작업에 안전한 Azure 관리 워크스테이션 사용

**지침**: Azure 리소스에 로그인하고 구성하도록 구성된 다단계 인증을 사용하여 PAW(Privileged Access Workstation)를 사용합니다.

- [Privileged Access Workstation에 대한 자세한 정보](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure에서 다단계 인증을 사용하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

**지침**: 환경에서 의심스럽거나 안전하지 않은 활동이 발생하는 경우 로그와 경고를 생성하기 위해 Azure AD(Azure Active Directory) PIM(Privileged Identity Management)을 사용합니다.

또한 Azure AD 위험 탐지를 사용하여 위험한 사용자 동작에 대한 경고 및 보고서를 봅니다.

- [PIM(Privileged Identity Management)을 배포하는 방법](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure AD 위험 탐지 이해](../active-directory/identity-protection/overview-identity-protection.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리적 그룹화에서만 Azure Portal에 액세스할 수 있도록 허용합니다.

또한 논리 앱에 대한 모든 요청 엔드포인트는 엔드포인트 URL에 SAS(공유 액세스 서명)를 포함합니다. 특정 IP 주소의 요청만 수락하도록 논리 앱을 제한할 수 있습니다.

- [Azure에서 명명된 위치를 구성하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

- [Logic Apps에서 인바운드 IP 주소를 제한하는 방법 이해](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#restrict-inbound-ip-addresses)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure AD(Azure Active Directory)를 Azure Logic Apps 인스턴스의 중앙 인증 및 권한 부여 시스템으로 사용합니다. Azure AD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다.

Logic Apps에서 지원하는 경우 로그인하지 않고 Azure AD로 보호되는 다른 리소스에 쉽게 액세스하고 ID를 인증하기 위해 자격 증명 또는 비밀 대신 관리 ID를 사용합니다. 이 ID는 Azure에서 관리되며, 비밀을 제공하거나 순환할 필요가 없기 때문에 자격 증명을 보호하는 데 도움이 됩니다.

Azure Logic Apps는 시스템이 할당한 관리 ID 및 사용자가 할당한 관리 ID를 모두 지원합니다. 논리 앱은 시스템이 할당한 ID 또는 단일 사용자가 할당한 ID를 사용할 수 있습니다. 이러한 ID는 논리 앱 그룹 간에 공유할 수 있지만 둘 다 공유할 수는 없습니다. 현재 특정 기본 제공 트리거 및 작업만 관리 ID를 지원하며, 다음과 같은 관리 커넥터 또는 연결은 지원하지 않습니다.

- HTTP
- Azure 기능
- Azure API Management
- Azure App Services

자세한 내용은 다음 참조 문서를 참조하세요.

- [Azure AD 인스턴스를 만들고 구성하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Azure Logic Apps에서 관리 ID를 사용하여 Azure 리소스에 대한 액세스 인증](create-managed-service-identity.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure AD(Azure Active Directory)는 부실 계정을 발견하는 데 도움이 되는 로그를 제공합니다. 또한 Azure Identity Access Reviews를 사용하여 그룹 멤버 자격, 엔터프라이즈 애플리케이션에 대한 액세스, 역할 할당을 효율적으로 관리합니다. 사용자의 액세스를 정기적으로 검토하여 적합한 사용자만 계속 액세스할 수 있도록 합니다.

- [Azure AD 보고 이해](/azure/active-directory/reports-monitoring/)

- [Azure ID 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화된 자격 증명에 대한 액세스 시도 모니터링

**지침**: Azure AD(Azure Active Directory)를 Azure Logic Apps 인스턴스의 중앙 인증 및 권한 부여 시스템으로 사용합니다. Azure AD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다.

Azure AD 로그인 활동, 감사 및 위험 이벤트 로그 원본에 액세스할 수 있으며, 이를 통해 Azure Sentinel 또는 타사 SIEM과 통합할 수 있습니다.

Azure AD 사용자 계정에 대한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 보내서 이 프로세스를 간소화할 수 있습니다. Log Analytics 내에서 원하는 로그 경고를 구성할 수 있습니다.

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

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

**지침**: 태그를 사용하여 중요한 정보를 저장하거나 처리하는 Azure 리소스를 추적할 수 있도록 지원합니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: "전역" 다중 테넌트 Logic Apps 서비스에서 실행되는 커넥터는 Microsoft에서 배포하고 관리합니다. 이러한 커넥터는 Office 365, Azure Blob Storage, SQL Server, Dynamics, Salesforce, SharePoint 등을 포함하여 클라우드 서비스, 온-프레미스 시스템 또는 둘 다에 액세스하기 위한 트리거와 작업을 제공합니다.

Azure 가상 네트워크의 리소스에 직접 액세스해야 하는 논리 앱의 경우 전용 리소스에서 논리 앱을 빌드, 배포 및 실행할 수 있는 ISE(통합 서비스 환경)를 만들 수 있습니다. 일부 Azure 가상 네트워크는 프라이빗 엔드포인트(Azure Private Link)를 사용하여 Azure Storage, Azure Cosmos DB 또는 Azure SQL Database, Azure에서 호스트되는 파트너 서비스 또는 고객 서비스와 같은 Azure PaaS 서비스에 대한 액세스를 제공합니다. 논리 앱이 프라이빗 엔드포인트를 사용하는 가상 네트워크에 액세스해야 하는 경우 ISE 내에서 해당 논리 앱을 만들고, 배포하고, 실행해야 합니다.

ISE를 만들 때 내부 또는 외부 액세스 엔드포인트 중 하나를 사용하도록 선택할 수 있습니다. 이 선택에 따라 ISE의 논리 앱에서 요청 또는 웹후크 트리거가 가상 네트워크 외부의 호출을 받을 수 있는지 여부가 결정됩니다.

환경 형식 및 데이터 민감도 수준과 같은 개별 보안 도메인에 별도의 구독 및 관리 그룹을 사용하여 격리를 구현합니다. 애플리케이션 및 엔터프라이즈 환경에서 요구하는 Azure 리소스에 대한 액세스 수준을 제한할 수 있습니다. Azure RBAC(역할 기반 액세스 제어)를 통해 Azure 리소스에 대한 액세스를 제어할 수 있습니다.

- [Logic Apps용 커넥터 이해](../connectors/apis-list.md)

- [ISE(통합 서비스 환경)를 사용하여 Azure Logic Apps에서 Azure Virtual Network 리소스에 액세스](connect-virtual-network-vnet-isolated-environment-overview.md)

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](/azure/governance/management-groups/create)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: 현재 사용할 수 없음: 현재 Azure Logic Apps에서는 데이터 식별, 분류 및 손실 방지 기능을 사용할 수 없습니다.

중요한 정보를 무단 전송하는지 모니터링하는 네트워크 경계에서 Azure Marketplace의 타사 솔루션을 활용하고, 정보 보안 전문가에게 경고를 보내는 동안 해당 전송을 차단합니다. 

Microsoft는 Azure Logic Apps의 기본 인프라를 관리하고, 고객 데이터의 손실 또는 노출을 방지하기 위해 엄격한 제어를 구현했습니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: 전송 중인 모든 중요한 정보를 암호화합니다. Azure Logic Apps에서 논리 앱을 실행하는 동안 모든 데이터는 TLS(전송 계층 보안)를 사용하여 미사용 상태에서 전송 중에 암호화됩니다. 논리 앱의 실행 기록을 볼 때는 Logic Apps가 액세스를 인증한 다음, 각 실행의 요청과 응답에 대한 입력 및 출력 링크를 제공합니다. 단, 암호, 비밀, 키 또는 기타 중요한 정보를 처리하는 작업의 경우 다른 사용자가 해당 데이터를 보거나 액세스하지 못하도록 하는 것이 좋습니다. 예를 들어, Azure Key Vault에서 논리 앱이 HTTP 작업을 인증할 때 사용할 비밀을 가져오는 경우, 이 비밀을 시야에서 숨기는 것이 좋습니다.

요청 트리거는 오직 인바운드 요청에 대해서만 TLS(전송 계층 보안) 1.2를 지원합니다. Azure 리소스에 연결하는 모든 클라이언트가 TLS 1.2 이상과 협상할 수 있는지 확인합니다. HTTP 커넥터를 사용하는 아웃바운드 호출은 TLS(전송 계층 보안) 1.0, 1.1 및 1.2를 지원합니다. 

적용 가능한 경우 미사용 암호화 및 전송 중인 암호화에 대한 Azure Security Center의 권장 사항을 따릅니다.

- [Azure Logic Apps의 액세스 및 데이터 보호 - 요청 기반 트리거에 대한 인바운드 호출](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#secure-inbound-requests)

- [Azure Logic Apps의 액세스 및 데이터 보호 - 다른 서비스 및 시스템에 대한 아웃바운드 호출](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#secure-outbound-requests)

- [Azure를 통한 전송 중 데이터 암호화 이해](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

- [Azure를 통한 미사용 데이터 암호화 이해](../security/fundamentals/encryption-atrest.md)

- [Azure Logic Apps에서 고객 관리 키를 설정하여 ISE(통합 서비스 환경)에 대한 미사용 데이터 암호화](customer-managed-keys-integration-service-environment.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: Azure Logic Apps에서 많은 트리거와 작업에는 논리 앱의 실행 기록에서 해당 데이터를 가려서 입력, 출력 또는 둘 다를 보호하는 데 사용할 수 있는 설정이 있습니다.

Microsoft는 Azure Logic Apps의 기본 인프라를 관리하고, 고객 데이터의 손실 또는 노출을 방지하기 위해 엄격한 제어를 구현했습니다.

- [실행 기록 데이터에 대한 액세스 보호](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#access-to-run-history-data)

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 제어 

**지침**: 특정 사용자나 그룹만 논리 앱 관리, 편집 및 보기와 같은 특정 작업을 실행하도록 허용할 수 있습니다. 이들의 권한을 제어하려면 Azure RBAC(역할 기반 액세스 제어)를 사용합니다. 그러면 Azure 구독의 멤버에게 사용자 지정 또는 기본 제공 역할을 할당할 수 있습니다.

- 논리 앱 참가자: 논리 앱을 관리할 수 있지만 앱에 대한 액세스는 변경할 수 없습니다.
- 논리 앱 운영자: 논리 앱을 읽고 사용하거나 사용하지 않도록 설정할 수는 있지만 편집하거나 업데이트할 수 없습니다.

다른 사람이 논리 앱을 변경하거나 삭제하지 못하게 하려면 Azure 리소스 잠금을 사용할 수 있습니다. 이 기능은 다른 사람이 프로덕션 리소스를 변경하거나 삭제하지 못하도록 합니다.

- [Azure Logic Apps에 대한 액세스 보호](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#access-to-logic-app-operations)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: Azure Logic Apps는 Azure Storage를 사용하여 미사용 데이터를 저장하고 자동으로 암호화합니다. 이 암호화는 데이터를 보호하고 조직의 보안 및 규정 준수 노력에 부합하는 데 도움이 됩니다. 기본적으로 Azure Storage는 Microsoft 관리 키를 사용하여 데이터를 암호화합니다.

논리 앱을 호스트하기 위한 ISE(통합 서비스 환경)를 만들 때 Azure Storage에 사용되는 암호화 키를 더 많이 제어하려는 경우 Azure Key Vault를 사용하여 사용자 자신의 키를 설정, 사용 및 관리할 수 있습니다. 이 기능은 BYOK("Bring Your Own Key")라고도 하며, 키는 "고객 관리 키"라고 합니다.

- [Azure Logic Apps의 통합 서비스 환경에 대한 미사용 데이터 암호화](customer-managed-keys-integration-service-environment.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 기록 및 경고

**지침**: Azure Monitor에서 Azure 활동 로그를 사용하여 Azure Logic Apps 및 기타 중요하거나 관련된 리소스가 변경되는 경우에 대한 경고를 만듭니다.

- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](/azure/azure-monitor/platform/alerts-activity-log)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure Security Benchmark: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화된 자산 검색 솔루션 사용

**지침**: Azure Resource Graph를 사용하여 구독 내 모든 리소스(예: 컴퓨팅, 스토리지, 네트워크, 포트, 프로토콜 등)를 쿼리/검색합니다. 테넌트에서 적절한 권한(읽기)을 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거합니다.

클래식 Azure 리소스는 Resource Graph를 통해 검색할 수 있지만, 추후에는 Azure Resource Manager 리소스를 만들어 사용하는 것이 좋습니다.

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

**지침**: 해당하는 경우 태그 지정, 관리 그룹 및 별도의 구독을 사용하여 Azure 리소스를 구성하고 추적합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

또한 Azure Policy에서 다음과 같은 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 유형을 제한합니다.

- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

자세한 내용은 다음 참조 문서를 참조하세요.

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](/azure/governance/management-groups/create)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: 승인된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 조직 요구 사항에 따라 승인된 Azure 리소스(예를 들어, 커넥터) 및 컴퓨팅 리소스에 승인된 소프트웨어의 인벤토리를 만듭니다.

참고: Google의 데이터 및 개인정보처리방침으로 인해 Google 승인 서비스에만 Gmail 커넥터를 사용할 수 있습니다. 이러한 상황은 진행 중에 있으며 나중에 다른 Google 커넥터에 영향을 줄 수 있습니다.

- [모든 Logic Apps 커넥터 목록](/connectors/connector-reference/connector-reference-logicapps-connectors)

- [Gmail 커넥터의 문제 및 제한 사항 이해](/connectors/gmail/#known-issues-and-limitations)

- [Google의 개인정보처리방침에 대한 자세한 정보](../connectors/connectors-google-data-security-privacy-policy.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy를 사용하여 구독에 만들 수 있는 리소스 유형에 대한 제한을 설정합니다. 

Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리/검색합니다.  환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

자세한 내용은 다음 참조 문서를 참조하세요.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: 승인된 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Azure Resource Manager와 상호 작용하는 사용자 기능 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성하여 사용자가 Azure Resource Manager와 상호 작용하는 기능을 제한하도록 Azure 조건부 액세스를 구성합니다.

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 사용자가 컴퓨팅 리소스 내에서 스크립트를 실행하는 기능 제한

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**지침**: 비즈니스 운영에 필요하지만 조직에 높은 위험을 초래할 수 있는 Logic Apps 관련 리소스는 자체 가상 머신 및/또는 가상 네트워크 내에 격리하고 Azure Firewall 또는 네트워크 보안 그룹으로 충분히 보호해야 합니다.

비즈니스 작업을 수행하는 데 필요하지만 조직에서 높은 위험을 초래할 수 있는 Logic Apps는 특정 권한 및 Azure RBAC 경계를 포함하는 별도의 리소스 그룹을 통해 가능한 모든 위치에서 격리해야 합니다.

- [가상 네트워크를 만드는 방법](../virtual-network/quick-create-portal.md) 

- [보안 구성을 사용하여 NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

- [관리 그룹을 만드는 방법](/azure/governance/management-groups/create) 

- [Azure RBAC를 통해 Logic Apps에 대한 액세스를 보호하는 방법](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#access-to-logic-app-operations)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure Security Benchmark: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Policy를 사용하여 Azure Logic Apps 인스턴스에 대한 표준 보안 구성을 정의하고 구현합니다. "Microsoft.Logic" 네임스페이스에 Azure Policy 별칭을 사용하여 Logic Apps 인스턴스의 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. 예를 들어 액세스를 제한하려는 리소스에 대한 연결을 다른 사용자가 만들거나 사용하지 못하도록 차단할 수 있습니다.

또한 Azure Resource Manager에는 템플릿을 JSON(JavaScript Object Notation) 형식으로 내보낼 수 있는 기능이 있으며, 내보낸 템플릿은 검토하여 배포 전에 조직의 보안 요구 사항을 충족하는지 확인해야 합니다.

또한 보안 매개 변수를 사용하여 중요한 데이터 및 비밀을 보호합니다.

- [사용 가능한 Azure Policy 별칭을 보는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Logic Apps에서 커넥터로 만든 연결 차단](block-connections-connectors.md)

- [Azure Portal에서 템플릿으로 단일 및 다중 리소스 내보내기](../azure-resource-manager/templates/export-template-portal.md)

- [Azure Logic Apps에 대한 Azure Resource Manager 템플릿을 배포하는 방법](logic-apps-deploy-azure-resource-manager-templates.md)

- [보안 작업 매개 변수 이해](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#secure-action-parameters)

- [매개 변수에 대한 보안 권장 사항](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-best-practices#security-recommendations-for-parameters)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: [거부] 및 [존재하지 않으면 배포] Azure 정책을 사용하여 보안 설정을 Azure 리소스 전체에 적용합니다.

Azure Policy를 사용하여 Azure Logic Apps 인스턴스에 대한 표준 보안 구성을 정의하고 구현합니다. "Microsoft.Logic" 네임스페이스에 Azure Policy 별칭을 사용하여 Logic Apps 인스턴스의 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. 예를 들어 액세스를 제한하려는 리소스에 대한 연결을 다른 사용자가 만들거나 사용하지 못하도록 차단할 수 있습니다.

또한 Azure Resource Manager에는 템플릿을 JSON(JavaScript Object Notation) 형식으로 내보낼 수 있는 기능이 있으며, 내보낸 템플릿은 검토하여 배포 전에 조직의 보안 요구 사항을 충족하는지 확인해야 합니다.

또한 난독 처리를 사용하여 실행 기록의 데이터를 보호해야 합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

- [Azure Logic Apps에서 커넥터로 만든 연결 차단](block-connections-connectors.md)

- [Azure Portal에서 템플릿으로 단일 및 다중 리소스 내보내기](../azure-resource-manager/templates/export-template-portal.md)

- [Azure Logic Apps에 대한 Azure Resource Manager 템플릿을 배포하는 방법](logic-apps-deploy-azure-resource-manager-templates.md)

- [실행 기록 입력 및 출력에 대한 액세스 보호](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#obfuscate)

- [매개 변수 입력에 대한 액세스 보호](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#secure-action-parameters)

- [매개 변수에 대한 보안 권장 사항](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-best-practices#security-recommendations-for-parameters)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지 관리

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: 사용자 지정 Azure 정책 정의를 사용하는 경우 Azure DevOps 또는 Azure Repos를 사용하여 코드를 안전하게 저장하고 관리합니다.

또한 Azure Resource Manager에는 템플릿을 JSON(JavaScript Object Notation) 형식으로 내보낼 수 있는 기능이 있으며, 내보낸 템플릿은 검토하여 배포 전에 조직의 보안 요구 사항을 충족하는지 확인해야 합니다.

- [Azure DevOps에 코드를 저장하는 방법](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Azure Repos 설명서](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

- [Azure Portal에서 템플릿으로 단일 및 다중 리소스 내보내기](../azure-resource-manager/templates/export-template-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스를 위한 구성 관리 도구 배포

**지침**: "Microsoft.Logic" 네임스페이스에서 기본 제공 Azure Policy 정의 및 Azure Policy 별칭을 사용하여 시스템 구성을 경고, 감사 및 적용하는 사용자 지정 정책을 만듭니다. Azure Policy 별칭을 사용하여 Azure 리소스의 네트워크 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. 또한 정책 예외를 관리하는 프로세스와 파이프라인을 개발합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 구성 관리 도구 배포

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대한 자동화된 구성 모니터링 구현

**지침**: "Microsoft.Logic" 네임스페이스에서 기본 제공 Azure Policy 정의 및 Azure Policy 별칭을 사용하여 시스템 구성을 경고, 감사 및 적용하는 사용자 지정 정책을 만듭니다. Azure Policy의 [감사], [거부], [존재하지 않으면 배포]를 사용하여 Azure 리소스에 대한 구성을 자동으로 적용합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대한 자동화된 구성 모니터링 구현

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: 난독 처리를 사용하여 논리 앱 실행 기록의 입력 및 출력을 보호합니다. 여러 환경에 배포하는 경우에는 논리 앱의 워크플로 정의에서 환경에 따라 달라지는 값을 매개 변수화하는 것이 좋습니다. 이렇게 하면 Azure Resource Manager 템플릿을 사용하여 논리 앱을 배포하기 때문에 하드 코딩된 데이터를 피할 수 있고, 보안 매개 변수를 정의하여 이 데이터를 별도의 입력으로 전달하기 때문에(매개 변수 파일을 사용하여 템플릿의 매개 변수를 통해) 중요한 데이터를 보호할 수 있습니다. Key Vault를 사용하여 중요한 데이터를 저장하고 배포 시 Key Vault에서 해당 값을 검색하는 보안 템플릿 매개 변수를 사용할 수 있습니다. 그런 다음, 매개 변수 파일에서 키 자격 증명 모음 및 비밀을 참조하면 됩니다. 

논리 앱을 호스트하기 위한 ISE(통합 서비스 환경)를 만들 때 Azure Storage에 사용되는 암호화 키를 더 많이 제어하려는 경우 Azure Key Vault를 사용하여 사용자 자신의 키를 설정, 사용 및 관리할 수 있습니다. 이 기능은 BYOK("Bring Your Own Key")라고도 하며, 키는 "고객 관리 키"라고 합니다.

- [Azure Logic Apps에서 실행 기록의 입력 및 출력 보호](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#obfuscate)

- [매개 변수에 대한 보안 권장 사항](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-best-practices#security-recommendations-for-parameters)

- [Azure Logic Apps에서 매개 변수 입력에 대한 액세스 보호](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#access-to-parameter-inputs)

- [Azure Key Vault를 사용하여 배포 중에 보안 매개 변수 값 전달](../azure-resource-manager/templates/key-vault-parameter.md)

- [Azure Logic Apps에서 고객 관리 키를 설정하여 ISE(통합 서비스 환경)에 대한 미사용 데이터 암호화](customer-managed-keys-integration-service-environment.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: 로그인하지 않고 Azure Active Directory(Azure AD)로 보호되는 다른 리소스에 쉽게 액세스하여 ID를 인증하기 위해 논리 앱에서 자격 증명 또는 비밀 대신 관리 ID(이전의 MSI(관리 서비스 ID))를 사용할 수 있습니다. 이 ID는 Azure에서 관리되며, 비밀을 제공하거나 순환할 필요가 없기 때문에 자격 증명을 보호하는 데 도움이 됩니다.

현재 특정 기본 제공 트리거 및 작업만 관리 ID를 지원하며, 다음과 같은 관리 커넥터 또는 연결은 지원하지 않습니다.

- HTTP
- Azure 기능
- Azure API Management
- Azure App Services

자세한 내용은 다음 참조 문서를 참조하세요.

- [Azure Logic Apps에서 관리 ID를 사용하여 Azure 리소스에 대한 액세스를 인증하는 방법](create-managed-service-identity.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 난독 처리를 사용하여 논리 앱 실행 기록의 입력 및 출력을 보호합니다. 여러 환경에 배포하는 경우에는 논리 앱의 워크플로 정의에서 환경에 따라 달라지는 값을 매개 변수화하는 것이 좋습니다. 이렇게 하면 Azure Resource Manager 템플릿을 사용하여 논리 앱을 배포하기 때문에 하드 코딩된 데이터를 피할 수 있고, 보안 매개 변수를 정의하여 이 데이터를 별도의 입력으로 전달하기 때문에(매개 변수 파일을 사용하여 템플릿의 매개 변수를 통해) 중요한 데이터를 보호할 수 있습니다. Key Vault를 사용하여 중요한 데이터를 저장하고 배포 시 Key Vault에서 해당 값을 검색하는 보안 템플릿 매개 변수를 사용할 수 있습니다. 그런 다음, 매개 변수 파일에서 키 자격 증명 모음 및 비밀을 참조하면 됩니다. 

자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별할 수도 있습니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다. 

- [Azure Logic Apps에서 실행 기록의 입력 및 출력 보호](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#obfuscate)

- [매개 변수에 대한 보안 권장 사항](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-best-practices#security-recommendations-for-parameters)

- [Azure Logic Apps에서 매개 변수 입력에 대한 액세스 보호](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#access-to-parameter-inputs)

- [Azure Key Vault를 사용하여 배포 중에 보안 매개 변수 값 전달](../azure-resource-manager/templates/key-vault-parameter.md)

- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [Azure Security Benchmark: 맬웨어 방어](../security/benchmarks/security-control-malware-defense.md)를 참조하세요.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: 중앙 관리형 맬웨어 방지 소프트웨어 사용

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다. Microsoft 맬웨어 방지는 Azure 서비스(예: Azure Logic Apps)를 지원하는 기본 호스트에서 사용하도록 설정되어 있지만 고객 콘텐츠에서 실행되지 않습니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: Microsoft 맬웨어 방지는 Azure 서비스(예: Azure Backup)를 지원하는 기본 호스트에서 사용하도록 설정되어 있지만 콘텐츠에서 실행되지 않습니다. 

비 컴퓨팅 Azure 리소스(예: App Service, Data Lake Storage, Blob Storage 등)로 업로드되는 모든 파일을 미리 검사합니다. 

데이터 서비스를 위한 Azure Security Center의 위협 탐지를 사용하여 스토리지 계정에 업로드된 맬웨어를 검색합니다. 

- [Azure Cloud Services 및 Virtual Machines용 Microsoft 맬웨어 방지 이해](../security/fundamentals/antimalware.md)

- [데이터 서비스를 위한 Azure Security Center의 위협 탐지 이해](/azure/security-center/threat-protection)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명이 업데이트되었는지 확인

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure Security Benchmark: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**지침**: 데이터를 보호하고, 중요한 비즈니스 기능을 지원하는 리소스를 신속하게 복원하고, BC(비즈니스 연속성)를 유지하기 위해 운영을 계속할 수 있도록 DR(재해 복구) 솔루션을 구현합니다.

이 재해 복구 전략은 Azure Logic Apps도 사용할 수 있는 대체 위치에서 대기 또는 백업 논리 앱으로 장애 조치(failover)하도록 주 논리 앱을 설정하는 데 중점을 둔 것입니다. 이렇게 하면 주 논리 앱이 손실, 중단 또는 실패하는 경우 보조 논리 앱에서 작업을 수행할 수 있습니다. 이 전략을 사용하려면 보조 논리 앱 및 종속 리소스가 대체 위치에 이미 배포되고 준비되어 있어야 합니다.

또한 논리 앱의 기본 워크플로 정의를 Azure Resource Manager 템플릿으로 확장해야 합니다. 이 템플릿은 논리 앱을 프로비전하고 배포하기 위한 인프라, 리소스, 매개 변수 및 기타 정보를 정의합니다.

- [Azure Logic Apps에 대한 비즈니스 연속성 및 재해 복구에 대한 자세한 정보](business-continuity-disaster-recovery-guidance.md)

- [Azure Resource Manager 템플릿을 사용하여 Azure Logic Apps에 대한 배포를 자동화하는 방법](logic-apps-azure-resource-manager-templates-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업 수행 및 고객 관리형 키 백업

**지침**: 데이터를 보호하고, 중요한 비즈니스 기능을 지원하는 리소스를 신속하게 복원하고, BC(비즈니스 연속성)를 유지하기 위해 운영을 계속할 수 있도록 DR(재해 복구) 솔루션을 구현합니다.

이 재해 복구 전략은 Azure Logic Apps도 사용할 수 있는 대체 위치에서 대기 또는 백업 논리 앱으로 장애 조치(failover)하도록 주 논리 앱을 설정하는 데 중점을 둔 것입니다. 이렇게 하면 주 논리 앱이 손실, 중단 또는 실패하는 경우 보조 논리 앱에서 작업을 수행할 수 있습니다. 이 전략을 사용하려면 보조 논리 앱 및 종속 리소스가 대체 위치에 이미 배포되고 준비되어 있어야 합니다.

또한 논리 앱의 기본 워크플로 정의를 Azure Resource Manager 템플릿으로 확장해야 합니다. 이 템플릿은 논리 앱을 프로비전하고 배포하기 위한 인프라, 리소스, 매개 변수 및 기타 정보를 정의합니다.

논리 앱에 대한 모든 요청 엔드포인트는 엔드포인트 URL에 SAS(공유 액세스 서명)를 포함합니다. Azure Key Vault를 사용하여 암호를 저장하는 경우 키와 URL의 자동화된 정기 백업을 수행해야 합니다.

- [Azure Logic Apps에 대한 비즈니스 연속성 및 재해 복구에 대한 자세한 정보](business-continuity-disaster-recovery-guidance.md)

- [Azure Resource Manager 템플릿을 사용하여 Azure Logic Apps에 대한 배포 자동화](logic-apps-azure-resource-manager-templates-overview.md)

- [SAS를 사용하여 Azure Logic Apps에서 액세스 및 데이터 보호](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#sas)

- [Key Vault 키를 백업하는 방법](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리형 키를 포함한 모든 백업의 유효성 검사

**지침**: 재해 복구 전략은 Azure Logic Apps도 사용할 수 있는 대체 위치에서 대기 또는 백업 논리 앱으로 장애 조치(failover)하도록 주 논리 앱을 설정하는 데 중점을 둔 것입니다. 이렇게 하면 주 논리 앱이 손실, 중단 또는 실패하는 경우 보조 논리 앱에서 작업을 수행할 수 있습니다. 이 전략을 사용하려면 보조 논리 앱 및 종속 리소스가 대체 위치에 이미 배포되고 준비되어 있어야 합니다.

백업된 고객 관리형 키의 복원을 테스트합니다. 이는 ISE(통합 서비스 환경)에서 실행되는 Logic Apps에만 적용됩니다.

- [Azure Logic Apps에 대한 비즈니스 연속성 및 재해 복구에 대한 자세한 정보](business-continuity-disaster-recovery-guidance.md)

- [Azure Logic Apps에서 고객 관리 키를 설정하여 ISE(통합 서비스 환경)에 대한 미사용 데이터 암호화](customer-managed-keys-integration-service-environment.md)

- [Azure에서 키 자격 증명 모음 키를 복원하는 방법](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리형 키 보호 보장

**지침**: 재해 복구 전략은 Azure Logic Apps도 사용할 수 있는 대체 위치에서 대기 또는 백업 논리 앱으로 장애 조치(failover)하도록 주 논리 앱을 설정하는 데 중점을 둔 것입니다. 이렇게 하면 주 논리 앱이 손실, 중단 또는 실패하는 경우 보조 논리 앱에서 작업을 수행할 수 있습니다. 이 전략을 사용하려면 보조 논리 앱 및 종속 리소스가 대체 위치에 이미 배포되고 준비되어 있어야 합니다. 

백업된 고객 관리 키를 보호합니다. 이는 ISE(통합 서비스 환경)에서 실행되는 Logic Apps에만 적용됩니다.

Key Vault에서 일시 삭제 및 제거 방지를 사용하도록 설정하여 실수로 또는 악의적으로 삭제되지 않도록 키를 보호합니다.

- [Azure Logic Apps에 대한 비즈니스 연속성 및 재해 복구에 대한 자세한 정보](business-continuity-disaster-recovery-guidance.md)

- [Azure Logic Apps에서 고객 관리 키를 설정하여 ISE(통합 서비스 환경)에 대한 미사용 데이터 암호화](customer-managed-keys-integration-service-environment.md)

- [Key Vault에서 일시 삭제 및 제거 보호를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/storage/blobs/soft-delete-overview?tabs=azure-portal)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../security/benchmarks/security-control-incident-response.md)을 참조하세요.

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 탐지에서 인시던트 사후 검토까지의 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할도 정의하는 인시던트 대응 계획이 작성되어 있는지 확인합니다. 

- [사용자 고유의 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용하여 고유한 인시던트 대응 계획 수립 지원](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center에서 심각도를 각 경고에 할당하여 먼저 조사해야 하는 경고에 대한 우선 순위를 지정합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용되는 결과 또는 메트릭의 신뢰도 및 경고가 발생한 활동에 악의적인 의도가 있었다는 신뢰 수준을 기준으로 합니다. 

또한 태그를 사용하여 구독(예: 프로덕션, 비 프로덕션)을 명확하게 표시하고 Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 명확하게 식별하고 분류하는 명명 시스템을 만듭니다.  인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다.

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md)

- [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md).

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: Azure 리소스를 보호하는 데 도움이 되도록 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 결함을 식별하고 필요에 따라 계획을 수정합니다.

- [NIST 게시물 - IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 가이드](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: Azure 리소스에 대한 위험을 식별하는 데 도움이 되도록 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다.

- [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 관한 "Logic Apps"를 통해 응답을 자동으로 생성함으로써 Azure 리소스를 보호합니다.

- [워크플로 자동화와 Logic Apps를 구성하는 방법](../security-center/workflow-automation.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure Security Benchmark: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대한 침투 테스트를 정기적으로 수행 및 모든 중요한 보안 결과를 수정

**지침**: Microsoft Engagement 규칙에 따라 침투 테스트가 Microsoft 정책을 위반하지 않는지 확인합니다. Microsoft 관리형 클라우드 인프라, 서비스, 애플리케이션에 대한 Microsoft의 전략과 Red Teaming 및 라이브 사이트 침투 테스트의 실행을 사용합니다.

- [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud 레드 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
