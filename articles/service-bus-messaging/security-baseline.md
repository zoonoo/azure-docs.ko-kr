---
title: Service Bus에 대한 Azure 보안 기준
description: Service Bus 보안 기준은 Azure Security Benchmark에 지정된 보안 권장 사항을 구현하기 위한 절차 지침과 리소스를 제공합니다.
author: msmbaldwin
ms.service: service-bus-messaging
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 9f437c01b4c8ec1df618ac3f4be9ecd55a930fb1
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967815"
---
# <a name="azure-security-baseline-for-service-bus"></a>Service Bus에 대한 Azure 보안 기준

이 보안 기준은 [Azure Security Benchmark 버전 1.0](../security/benchmarks/overview-v1.md)에서 Service Bus에 대한 지침을 적용합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 Azure Security Benchmark 및 Service Bus에 적용되는 관련 지침에서 정의하는 **보안 컨트롤** 에 따라 그룹화됩니다. Service Bus에 적용되지 않거나 Microsoft의 책임인 **컨트롤** 은 제외되었습니다.

Service Bus가 어떻게 Azure Security Benchmark에 완전히 매핑되는지 알아보려면 [전체 Service Bus 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조하세요.

## <a name="network-security"></a>네트워크 보안

자세한 내용은 [Azure Security Benchmark: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조하세요.

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: Service Bus와 Azure Private Link 서비스를 통합하면 가상 네트워크에 바인딩된 가상 머신과 같은 워크로드에서 메시지 기능에 안전하게 액세스할 수 있습니다. Service Bus 네임스페이스에 대한 프라이빗 엔드포인트 연결을 만듭니다. 프라이빗 엔드포인트는 가상 네트워크의 개인 IP 주소를 사용하여 서비스를 가상 네트워크에 효과적으로 제공합니다. 서비스에 대한 모든 트래픽은 프라이빗 엔드포인트를 통해 라우팅할 수 있기 때문에 게이트웨이, NAT 디바이스, ExpressRoute 연결, VPN 연결 또는 공용 IP 주소가 필요하지 않습니다.

방화벽을 사용하여 Azure Service Bus 네임스페이스를 보호할 수도 있습니다. Azure Service Bus는 인바운드 방화벽 지원을 위해 IP 기반 액세스 제어를 지원합니다. Azure Portal, Azure Resource Manager 템플릿을 사용하거나 Azure CLI 또는 Azure PowerShell을 통해 방화벽 규칙을 설정할 수 있습니다.

- [프라이빗 엔드포인트를 통한 Azure Service Bus 네임스페이스 액세스 허용](private-link-service.md)

- [특정 IP 주소 또는 범위에서 Azure Service Bus 네임스페이스에 대한 액세스 허용](service-bus-ip-filtering.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 관한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초가 됩니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 제어와 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**기본 제공 Azure Policy 정의 - Microsoft.ServiceBus**:

[!INCLUDE [Resource Policy for Microsoft.ServiceBus 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.servicebus-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: 가상 네트워크, 서브넷 및 네트워크 인터페이스의 구성 및 트래픽을 모니터링 및 기록

**지침**: Azure 가상 머신을 사용하여 Service Bus 엔터티에 액세스하는 경우 NSG(네트워크 보안 그룹) 흐름 로그를 사용하도록 설정하고, 트래픽 감사를 위해 로그를 스토리지 계정에 보냅니다. 또한 NSG 흐름 로그를 Log Analytics 작업 영역에 보내고, 트래픽 분석을 사용하여 Azure 클라우드의 트래픽 흐름에 대한 인사이트를 제공할 수 있습니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화하고, 핫 스폿을 식별하며, 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하며, 잘못된 네트워크 구성을 파악할 수 있다는 것입니다. 

Azure Security Center를 사용하고 네트워크 보호 권장 사항에 따라 Azure에서 Service Bus 리소스를 안전하게 보호합니다.

- [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [트래픽 분석을 사용하도록 설정하고 사용하는 방법](../network-watcher/traffic-analytics.md)

- [Network Watcher를 사용하도록 설정하는 방법](../network-watcher/network-watcher-create.md)

- [Azure Security Center에서 제공하는 네트워크 보안 이해](../security-center/security-center-network-recommendations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: Service Bus 네임스페이스와 연결된 가상 네트워크에서 DDoS Protection 표준을 사용하도록 설정하여 DDoS(분산 서비스 거부) 공격으로부터 보호합니다. Azure Security Center 통합 위협 인텔리전스를 사용하여 알려진 악성 인터넷 IP 주소 또는 사용하지 않는 인터넷 IP 주소와의 통신을 거부합니다.

- [DDoS 보호 구성 방법](../ddos-protection/manage-ddos-protection.md)

- [Azure Security Center 통합 위협 인텔리전스](../security-center/azure-defender.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷 기록

**지침**: Azure 가상 머신을 사용하여 Service Bus 엔터티에 액세스하는 경우 Network Watcher 패킷 캡처를 사용하여 비정상적인 활동을 조사할 수 있습니다.

- [Network Watcher를 사용하도록 설정하는 방법](../network-watcher/network-watcher-create.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 IDS/IPS(침입 탐지/침입 방지 시스템) 배포

**지침**: Azure 가상 머신을 사용하여 Service Bus 엔터티에 액세스하는 경우 Azure Marketplace에서 페이로드 검사 기능을 사용하여 IDS/IPS 기능을 지원하는 제안을 선택합니다. 페이로드 검사에 기반한 침입 탐지 및/또는 방지가 조직에 필요하지 않은 경우 Azure Service Bus의 기본 제공 방화벽 기능을 사용할 수 있습니다. 방화벽 규칙을 사용하여 제한된 범위의 IP 주소 또는 특정 IP 주소의 Service Bus 네임스페이스에 대한 액세스를 제한할 수 있습니다.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [지정된 IP 주소에 대한 Service Bus 네임스페이스에 방화벽 규칙을 추가하는 방법](service-bus-ip-filtering.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: 가상 네트워크 서비스 태그를 사용하여 Service Bus 리소스에 대한 트래픽을 필터링하는 네트워크 보안 그룹 또는 Azure Firewall에서 네트워크 액세스 제어를 정의합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 서비스 태그 이름(예: ServiceBus)을 규칙의 적절한 원본 또는 대상 필드에 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. Microsoft에서는 서비스 태그에서 압축한 주소 접두사를 관리하고 주소를 변경하는 대로 서비스 태그를 자동으로 업데이트합니다. 

- [서비스 태그 이해 및 사용](../virtual-network/service-tags-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지

**지침**: Azure Policy를 사용하여 Azure Service Bus 네임스페이스와 연결된 네트워크 리소스에 대한 표준 보안 구성을 정의하고 구현합니다. "Microsoft.ServiceBus" 및 "Microsoft.Network" 네임스페이스에서 Azure Policy 별칭을 사용하여 Service Bus 네임스페이스의 네트워크 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. Azure Service Bus와 관련된 기본 제공 정책 정의도 다음과 같이 사용할 수 있습니다.

- Service Bus는 가상 네트워크 서비스 엔드포인트를 사용해야 함
- Service Bus의 진단 로그를 사용하도록 설정해야 합니다.

기본 제공 정의가 조직의 요구 사항에 맞지 않아도 사용자 지정 정책 정의를 생성할 수 있습니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Service Bus 네임스페이스에 대한 기본 제공 Azure Policy](https://docs.microsoft.com/azure/service-bus-messaging/policy-reference#azure-service-bus-messaging)

- [네트워킹을 위한 Azure Policy 샘플](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Azure Blueprint를 만드는 방법](../governance/blueprints/create-blueprint-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

**지침**: Service Bus와 연결된 네트워크 보안 및 트래픽 흐름과 관련된 가상 네트워크 및 기타 리소스에 대한 태그를 사용합니다. 개별 네트워크 보안 그룹 규칙의 경우 “설명” 필드를 사용하여 Service Bus 네임스페이스와 연결된 네트워크에 대한 트래픽을 허용하는 모든 규칙의 비즈니스 요구, 기간 및 기타 설명 정보를 지정합니다. 

태그 지정과 관련된 기본 제공 Azure Policy 정의(예: “태그 및 해당 값 필요”)를 사용하여 모든 리소스에서 태그를 포함하도록 만들고 태그가 없는 기존 리소스를 알립니다. 

Azure PowerShell 또는 Azure CLI를 사용하여 태그를 기반으로 리소스에 대한 작업을 조회하거나 수행할 수 있습니다. 

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

- [Virtual Network를 만드는 방법](../virtual-network/quick-create-portal.md) 

- [보안 구성을 사용하여 NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용하여 네트워크 리소스 구성을 모니터링하고, Azure Service Bus 관련 네트워크 리소스에 대한 변경 내용을 검색합니다. 중요한 네트워크 리소스에 변경 내용이 발생하는 경우 트리거하는 Azure Monitor 내에서 경고를 만듭니다.

- [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Azure Monitor에서 경고를 만드는 방법](../azure-monitor/alerts/alerts-activity-log.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure Security Benchmark: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조하세요.*

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure Monitor를 통해 로그를 수집하여 Service Bus 리소스에서 생성된 보안 데이터를 집계합니다. Azure Monitor에서 Log Analytics 작업 영역을 사용하여 분석을 쿼리 및 수행하고, 장기 또는 보관 스토리지에 Azure Storage 계정을 구성합니다. Service Bus와 관련된 로그를 Azure Sentinel 또는 타사 SIEM으로 보낼 수 있도록 구성할 수도 있습니다.

- [Azure Service Bus에 대한 진단 설정을 구성하는 방법](service-bus-diagnostic-logs.md)

- [Azure 활동 로그 이해](../azure-monitor/essentials/platform-logs-overview.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md) 

- [Azure Monitor 및 타사 SIEM 통합을 시작하는 방법](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: Azure Service Bus 네임스페이스에 대한 진단 설정을 사용하도록 설정합니다. Azure Service Bus는 현재 활동 및 작업 또는 진단 로그를 지원합니다. 활동 로그에는 작업에 대해 수행된 작업 관련 정보가 포함됩니다. 진단 로그는 API를 사용하거나 언어 SDK를 사용하는 관리 클라이언트를 통해 네임스페이스에 대해 수행된 작업 및 동작에 대한 자세한 정보를 제공합니다. 특히 이러한 로그는 큐 생성, 사용된 리소스 및 작업 상태를 비롯한 작업 형식을 캡처합니다.

- [Azure Service Bus에 진단 설정을 사용하도록 설정하는 방법](service-bus-diagnostic-logs.md)

- [Azure 활동 로그에 대한 진단 설정을 사용하도록 설정하는 방법](../azure-monitor/essentials/activity-log.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 관한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초가 됩니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 제어와 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**기본 제공 Azure Policy 정의 - Microsoft.ServiceBus**:

[!INCLUDE [Resource Policy for Microsoft.ServiceBus 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.servicebus-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침**: Azure Monitor 내에서 조직의 규정 준수 규정에 따라 Log Analytics 작업 영역 보존 기간을 설정하여 Service Bus와 관련된 인시던트를 캡처하고 검토합니다.

- [Log Analytics 작업 영역에 대한 로그 보존 기간 매개 변수를 설정하는 방법](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: 비정상 동작에 대한 로그를 분석 및 모니터링하고, Service Bus 엔터티와 관련된 결과를 정기적으로 검토합니다. Azure Monitor를 사용하여 Service Bus와 관련된 로그를 검토하고 로그 데이터에 대한 쿼리를 수행합니다.

- [Log Analytics 작업 영역에 대한 자세한 정보](../azure-monitor/logs/log-analytics-tutorial.md)

- [Azure Monitor에서 사용자 지정 쿼리를 수행하는 방법](../azure-monitor/logs/get-started-queries.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상 활동에 대한 경고 사용

**지침**: 보안 로그 및 이벤트에서 발견된 비정상적인 활동을 모니터링하고 경고하기 위해 Log Analytics 작업 영역에서 Azure Security Center를 사용합니다. 또는 데이터를 사용하도록 설정하고 Azure Sentinel에 온보딩할 수도 있습니다.

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

- [Azure Security Center에서 경고를 관리하는 방법](../security-center/security-center-managing-and-responding-alerts.md)

- [Log Analytics 로그 데이터에 대한 경고 방법](../azure-monitor/alerts/tutorial-response.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [Azure Security Benchmark: ID 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정 인벤토리 유지 관리

**지침**: Azure RBAC(역할 기반 액세스 제어)를 사용하면 역할 할당을 통해 Azure 리소스에 대한 액세스를 관리할 수 있습니다. 역할은 사용자, 그룹 서비스 주체, 관리 ID에 할당할 수 있습니다. Service Bus에 대해 미리 정의된 기본 제공 역할이 있으며 이러한 역할은 Azure CLI, Azure PowerShell, Azure Portal과 같은 도구를 통해 쿼리하거나 인벤토리에 포함할 수 있습니다.

- [Azure Service Bus에 대한 Azure 기본 제공 역할](https://docs.microsoft.com/azure/service-bus-messaging/authenticate-application#azure-built-in-roles-for-azure-service-bus)

- [PowerShell을 사용하여 Azure AD(Azure Active Directory)에서 디렉터리 역할을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrole)

- [PowerShell을 사용하여 Azure AD에서 디렉터리 역할의 구성원을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrolemember)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

**지침**: Service Bus에 대한 컨트롤 플레인 액세스는 Azure AD(Azure Active Directory)를 통해 제어됩니다. Azure AD에는 기본 암호 개념이 없습니다.

Service Bus에 대한 데이터 평면 액세스는 관리 ID, 앱 등록, 공유 액세스 서명을 사용하여 Azure AD를 통해 제어됩니다. 공유 액세스 서명은 Service Bus 네임스페이스에 연결되는 클라이언트에서 사용하며 언제든지 다시 생성할 수 있습니다.

- [Service Bus에 대한 공유 액세스 서명 이해](service-bus-sas.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다. Azure Security Center ID와 액세스 관리를 사용하여 관리 계정 수를 모니터링합니다.

또한 전용 관리 계정을 추적하는 데 도움이 되도록 Azure 다음과 같은 Azure Security Center 또는 기본 제공 Azure 정책의 추천 사항을 사용할 수 있습니다.

- 구독에 둘 이상의 소유자를 할당해야 합니다.
- 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.
- 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.

기본 제공 정의가 조직의 요구 사항에 맞지 않아도 사용자 지정 정책 정의를 생성할 수 있습니다.

- [Azure Security Center를 사용하여 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

- [Azure Policy를 사용하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory SSO(Single Sign-On) 사용

**지침**: Microsoft Azure는 Azure AD(Azure Active Directory)를 기반으로 하는 리소스 및 애플리케이션에 대한 통합 액세스 제어 관리를 제공합니다. Azure Service Bus를 통해 Azure AD를 사용할 때의 주요 이점은 더 이상 코드에 자격 증명을 저장할 필요가 없다는 점입니다. 대신 Microsoft ID 플랫폼에서 OAuth 2.0 액세스 토큰을 요청할 수 있습니다. 토큰을 요청하는 리소스 이름은 https://azure.microsoft.com/services/service-bus/ 입니다. Azure AD는 애플리케이션을 실행하는 보안 주체(사용자, 그룹, 서비스 주체)를 인증합니다. 인증이 성공하면 Azure AD는 애플리케이션에 액세스 토큰을 반환하고, 애플리케이션은 액세스 토큰을 사용하여 Azure Service Bus 리소스에 대한 요청 권한을 부여할 수 있습니다.

- [Service Bus 리소스에 액세스하기 위해 Azure AD를 사용하여 애플리케이션을 인증하는 방법](authenticate-application.md)

- [Azure AD를 사용하는 SSO 이해](../active-directory/manage-apps/what-is-single-sign-on.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure AD(Azure Active Directory) 다단계 인증을 사용하도록 설정하고, Azure Security Center ID 및 액세스 관리 권장 사항에 따라 Service Bus 사용 리소스를 보호합니다.

- [Azure에서 다단계 인증을 사용하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: 관리 작업에 안전한 Azure 관리 워크스테이션 사용

**지침**: Service Bus 사용 리소스에 로그인하고 구성하도록 구성된 다단계 인증을 사용하여 PAW(Privileged Access Workstation)를 사용합니다.

- [Privileged Access Workstation에 대한 자세한 정보](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure에서 다단계 인증을 사용하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

**지침**: Azure AD(Azure Active Directory) 보안 보고서를 사용하고 모니터링하여 사용 환경에서 의심스럽거나 안전하지 않은 활동 발생을 탐지합니다. Azure Security Center를 사용하여 ID 및 액세스 활동을 모니터링합니다.

- [위험한 활동에 대해 플래그가 지정된 Azure AD 사용자를 식별하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center에서 사용자의 ID 및 액세스 활동을 모니터링하는 방법](../security-center/security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: Azure AD(Azure Active Directory) 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리 그룹에서만 액세스하도록 허용합니다.

- [Azure AD 명명된 위치를 구성하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure AD(Azure Active Directory)를 Service Bus와 같은 Azure 리소스에 대한 중앙 인증 및 권한 부여 시스템으로 사용합니다. 이를 통해 관리가 중요한 리소스에 대한 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용할 수 있습니다.

- [Azure AD 인스턴스를 만들고 구성하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Azure AD를 사용하여 Service Bus 리소스에 대한 액세스 권한 부여](authenticate-application.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure AD(Azure Active Directory)는 부실 계정을 발견하는 데 도움이 되는 로그를 제공합니다. 또한 Azure ID 액세스 검토를 사용하여 그룹 구성원, 엔터프라이즈 애플리케이션에 대한 액세스, 역할 할당을 효율적으로 관리합니다. 사용자의 액세스를 정기적으로 검토하여 적합한 사용자만 계속 액세스할 수 있도록 합니다.

추가로 Service Bus 네임스페이스의 공유 액세스 서명을 정기적으로 순환합니다.

- [Azure AD 보고 이해](/azure/active-directory/reports-monitoring/)

- [Azure ID 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

- [Service Bus 네임스페이스에 대한 공유 액세스 서명 이해](service-bus-sas.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화된 자격 증명에 대한 액세스 시도 모니터링

**지침**: Azure AD(Azure Active Directory) 로그인 활동, 감사, 위험 이벤트 로그 원본에 액세스할 수 있으며, 이를 통해 Azure Sentinel 또는 타사 SIEM 도구와 통합할 수 있습니다.

Azure AD 사용자 계정에 대한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 보내면 이 프로세스를 간소화할 수 있습니다. 그런 다음 Azure Monitor의 로그에서 발생하는 특정 작업에 대해 원하는 로그 경고를 구성할 수 있습니다.

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Azure AD를 사용하여 Service Bus 리소스에 대한 액세스 권한 부여](authenticate-application.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure AD(Azure Active Directory)의 ID 보호 및 위험 탐지 기능을 사용하여 Service Bus 사용 리소스와 관련하여 탐지된 의심스러운 작업에 대한 자동화된 응답을 구성합니다. 조직의 보안 대응을 구현하기 위해 Azure Sentinel을 통해 자동화된 대응을 사용하도록 설정할 수 있습니다.

- [Azure AD 위험한 로그인을 확인하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오 중 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**지침**: 현재 사용할 수 없습니다. Customer Lockbox는 아직 Service Bus에서 지원되지 않습니다.

- [Customer Lockbox 지원 서비스 목록](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-protection"></a>데이터 보호

자세한 내용은 [Azure Security Benchmark: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조하세요.

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: Service Bus와 관련된 리소스에 태그를 사용하여 중요한 정보를 저장하거나 처리하는 Azure 리소스를 추적할 수 있습니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 개발, 테스트, 프로덕션을 위한 별도의 구독 및 관리 그룹을 구현합니다. Service Bus 네임스페이스는 프라이빗 엔드포인트가 구성되어 있고 태그가 적절하게 지정된 가상 네트워크로 구분되어야 합니다.

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [태그를 만들고 활용하는 방법](../azure-resource-manager/management/tag-resources.md)

- [Virtual Network를 만드는 방법](../virtual-network/quick-create-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: 가상 머신을 사용하여 Service Bus 엔터티에 액세스하는 경우 가상 네트워크, 프라이빗 엔드포인트, Service Bus 방화벽, 네트워크 보안 그룹, 서비스 태그를 사용하여 데이터 반출 가능성을 완화합니다.

Microsoft는 Azure Service Bus의 기본 인프라를 관리하고, 고객 데이터의 손실 또는 노출을 방지하기 위해 엄격한 제어를 구현했습니다.

- [Azure Service Bus에 대한 IP 방화벽 규칙 구성](service-bus-ip-filtering.md)

- [특정 가상 네트워크에서 Azure Service Bus 네임스페이스에 대한 액세스 허용](private-link-service.md)

- [프라이빗 엔드포인트를 통한 Azure Service Bus 네임스페이스 액세스 허용](private-link-service.md)

- [네트워크 보안 그룹 및 서비스 태그 이해](../virtual-network/network-security-groups-overview.md)

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: Azure Service Bus는 기본적으로 TLS 암호화 통신을 적용합니다. 현재 TLS 버전 1.0, 1.1, 1.2가 지원됩니다. 그러나 TLS 1.0 및 TLS 1.1은 업계 전반에서 사용이 중단되고 있으니 가능하면 TLS 1.2를 사용합니다.

- [Service Bus의 보안 기능을 이해하려면 네트워크 보안을 참조하세요.](network-security.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: 데이터 식별, 분류, 손실 방지 기능은 Azure Service Bus에서 아직 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고 고객 데이터 손실 및 노출을 방지하기 위해 많은 시간을 투자합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: 역할 기반 액세스 제어를 사용하여 리소스에 대한 액세스 제어

**지침**: Azure Service Bus에서는 Azure AD(Azure Active Directory)를 사용하여 Service Bus 엔터티에 대한 요청에 권한을 부여할 수 있습니다. Azure AD를 사용하면 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 사용자 또는 애플리케이션 서비스 주체일 수 있는 보안 주체에 권한을 부여할 수 있습니다.

- [Azure RBAC 및 Azure Service Bus에서 사용할 수 있는 역할 이해](authenticate-application.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: Azure Service Bus는 Microsoft 관리형 키 또는 고객 관리형 키를 사용하여 미사용 데이터를 암호화하는 옵션을 지원합니다. 이 기능을 사용하면 Azure Service Bus 미사용 데이터를 암호화하는 데 사용되는 고객 관리형 키에 대한 액세스를 만들고, 순환하고, 사용하지 않도록 설정하고, 취소할 수 있습니다.

- [Azure Service Bus를 암호화하기 위해 고객 관리형 키를 구성하는 방법](configure-customer-managed-key.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 기록 및 경고

**지침**: Azure Monitor에서 Azure 활동 로그를 사용하여 Azure Service Bus 및 기타 중요하거나 관련된 리소스의 프로덕션 인스턴스가 변경되는 경우에 대한 경고를 만듭니다.

- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](../azure-monitor/alerts/alerts-activity-log.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure Security Benchmark: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화된 자산 검색 솔루션 사용

**지침**: Azure Resource Graph를 사용하여 구독 내에서 모든 리소스(Azure Service Bus 네임스페이스 포함)를 쿼리하고 검색합니다. 테넌트에서 적절한 권한(읽기)이 있는지 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거할 수 있습니다.

- [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

- [Azure 구독을 확인하는 방법](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC 이해](../role-based-access-control/overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타데이터를 제공하는 Azure 리소스에 태그를 적용하여 논리적으로 분류 체계로 구성합니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 해당하는 경우 태그 지정, 관리 그룹, 별도의 구독을 사용하여 Azure Service Bus 네임스페이스 및 관련 리소스를 구성하고 추적합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: 승인된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 조직 요구 사항에 따라 승인된 Azure 리소스 및 컴퓨팅 리소스에 승인된 소프트웨어의 인벤토리를 만듭니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy에서 다음의 기본 제공 정책 정의를 사용해 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

또한 Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리하거나 검색합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy에서 다음의 기본 제공 정책 정의를 사용해 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.

- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

기본 제공 정의가 조직의 요구 사항에 맞지 않아도 사용자 지정 정책 정의를 생성할 수 있습니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](../governance/policy/samples/index.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Azure Resource Manager와 상호 작용하는 사용자 기능 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성하여 사용자가 Azure Resource Manager와 상호 작용하는 기능을 제한하도록 Azure 조건부 액세스를 구성합니다.

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure Security Benchmark: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Service Bus 배포에 대한 표준 보안 구성을 정의하고 구현합니다. Azure Service Bus에 대해 다음과 같은 기본 제공 정책 정의를 사용할 수도 있습니다.
- Service Bus의 진단 로그를 사용하도록 설정해야 합니다.
- Service Bus 가상 네트워크 서비스 엔드포인트를 사용하여 네트워크 트래픽을 개인 네트워크로 제한해야 합니다.

“Microsoft.ServiceBus” 네임스페이스에서 Azure Policy 별칭을 사용하여 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다.

- [Service Bus에 대한 기본 제공 Azure 정책](policy-reference.md)

- [사용 가능한 Azure Policy 별칭 확인 방법](/powershell/module/az.resources/get-azpolicyalias)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: Azure Policy [거부] 및 [존재하지 않으면 배포]를 사용하여 Service Bus 사용 리소스 또는 애플리케이션 전체에 보안 설정을 적용합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy 효과에 대한 자세한 정보](../governance/policy/concepts/effects.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스를 위한 구성 관리 도구 배포

**지침**: “Microsoft.ServiceBus” 네임스페이스에 Azure Policy 별칭을 사용하여 시스템 구성을 경고, 감사, 적용하는 사용자 지정 정책을 만듭니다. 또한 정책 예외를 관리하는 프로세스와 파이프라인을 개발합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대한 자동화된 구성 모니터링 구현

**지침**: “Microsoft.ServiceBus” 네임스페이스에 Azure Policy 별칭을 사용하여 시스템 구성을 경고, 감사, 적용하는 사용자 지정 정책을 만듭니다. Azure Policy [감사], [거부], [존재하지 않으면 배포]를 사용하여 Azure Service Bus 배포 및 관련 리소스에 대한 구성을 자동으로 적용합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: Service Bus 엔터티에 액세스하는 데 사용되는 Azure App Service에서 실행되는 Azure 가상 머신 또는 웹 애플리케이션의 경우 Azure Key Vault와 함께 관리 서비스 ID를 사용하여 Azure Service Bus 배포에 대한 공유 액세스 서명 관리를 간소화하고 보호합니다. Key Vault 일시 삭제를 사용하도록 설정되어 있는지 확인합니다.

- [Azure AD(Azure Active Directory)로 관리 ID를 인증하여 Azure Service Bus 리소스에 액세스](service-bus-managed-service-identity.md)

- [Service Bus에 대한 고객 관리형 키 구성](configure-customer-managed-key.md)

- [Key Vault를 만드는 방법](../key-vault/general/quick-create-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: Service Bus 엔터티에 액세스하는 데 사용되는 Azure App Service에서 실행되는 Azure 가상 머신 또는 웹 애플리케이션의 경우 Azure Key Vault와 함께 관리 서비스 ID를 사용하여 Azure Service Bus를 간소화하고 보호합니다. Key Vault 일시 삭제를 사용하도록 설정되어 있는지 확인합니다.

관리 ID를 사용하여 Azure AD(Azure Active Directory)에서 자동으로 관리 ID를 Azure 서비스에 제공합니다. 관리 ID를 사용하면 코드의 자격 증명 없이 Azure Key Vault를 포함하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있습니다.

- [Azure AD에서 관리 ID를 인증하여 Service Bus 리소스에 액세스](service-bus-managed-service-identity.md)

- [Service Bus에 대한 고객 관리형 키 구성](configure-customer-managed-key.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [Azure Security Benchmark: 맬웨어 방어](../security/benchmarks/security-control-malware-defense.md)를 참조하세요.*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: 비 컴퓨팅 Azure 리소스(예: Azure Service Bus, App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL 등)에 업로드되는 모든 콘텐츠를 미리 검사합니다. Microsoft는 이러한 인스턴스의 데이터에 액세스할 수 없습니다.

Microsoft 맬웨어 방지는 Azure 서비스를 지원하는 기본 호스트에서 사용하도록 설정되어 있지만 고객 콘텐츠에서 실행되지 않습니다.

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure Security Benchmark: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**지침**: Azure Service Bus에 대한 지리적 재해 복구를 구성합니다. (가용성 영역을 사용하지 않는 경우)전체 Azure 지역 또는 데이터 센터에서 가동 중지 시간이 발생하면 데이터 처리가 다른 지역 또는 데이터 센터에서 계속 작동되는 것이 중요합니다. 따라서 지리적 재해 복구 및 지리적 복제는 기업에 중요한 기능입니다. Azure Service Bus는 네임스페이스 수준에서 지역 재해 복구 및 지역 복제를 둘 다 지원합니다.

- [Azure Service Bus에 대한 지리적 재해 복구 이해](service-bus-geo-dr.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업 수행 및 고객 관리형 키 백업

**지침**: Azure Service Bus는 Azure SSE(Azure 스토리지 서비스 암호화)를 사용하여 미사용 데이터의 암호화를 제공합니다. Service Bus는 Azure Storage를 사용하여 데이터를 저장하며, 기본적으로 Azure Storage에 저장된 모든 데이터는 Microsoft 관리형 키를 사용하여 암호화됩니다. Azure Key Vault를 사용하여 고객 관리형 키를 저장하는 경우 키를 정기적으로 자동 백업해야 합니다.

Backup-AzKeyVaultSecret PowerShell 명령을 사용하여 Key Vault 비밀의 자동 백업을 정기적으로 수행합니다.

- [Azure Service Bus 미사용 데이터를 암호화하기 위해 고객 관리형 키를 구성하는 방법](configure-customer-managed-key.md)

- [Key Vault 비밀을 백업하는 방법](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리형 키를 포함한 모든 백업의 유효성 검사

**지침**: Service Bus 데이터를 암호화하는 데 사용되는 백업된 고객 관리형 키의 테스트 복원

- [Azure Service Bus 미사용 데이터를 암호화하기 위해 고객 관리형 키를 구성하는 방법](configure-customer-managed-key.md)

- [Azure에서 키 자격 증명 모음 키를 복원하는 방법](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리형 키 보호 보장

**지침**: Key Vault의 일시 삭제를 사용하도록 설정하여 실수로 또는 악의적으로 삭제되지 않도록 키를 보호합니다. Azure Service Bus에는 일시 삭제 및 제거 안 함을 구성하기 위해 고객 관리형 키가 필요합니다.

- [Key Vault에서 일시 삭제를 사용하도록 설정하는 방법](../storage/blobs/soft-delete-blob-overview.md)

- [키를 사용하여 Key Vault 설정](../event-hubs/configure-customer-managed-key.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../security/benchmarks/security-control-incident-response.md)을 참조하세요.

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 탐지에서 인시던트 사후 검토까지의 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할도 정의하는 서면 인시던트 대응 계획이 있는지 확인합니다. 

- [자체 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [NIST의 컴퓨터 보안 인시던트 처리 가이드를 사용하여 고유한 인시던트 대응 계획 수립 지원](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Azure Security Center는 먼저 조사해야 하는 경고의 우선 순위를 지정하는 데 도움이 되도록 심각도를 각 경고에 할당합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용되는 결과 또는 분석의 신뢰도 및 경고가 발생한 활동에 악의적인 의도가 있었다는 신뢰 수준을 기준으로 합니다.

또한 태그를 사용하여 구독을 표시하고 Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 식별하고 분류할 수 있는 명명 시스템을 만듭니다. 인시던트가 발생한 Azure 리소스 및 환경의 중요도를 기준으로 경고 수정에 대한 우선 순위를 지정해야 합니다. 

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md) 

- [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md).

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 격차를 식별하고 필요에 따라 계획을 수정합니다.

- [NIST 게시물 - IT 플랜 및 기능에 대한 테스트, 학습, 연습 프로그램에 대한 가이드](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다. 

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: Azure 리소스에 대한 위험을 식별하는 데 도움이 되도록 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 권장 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 권장 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다. 

- [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md) 

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 대한 응답을 자동으로 트리거하여 Azure 리소스를 보호합니다. 

- [Security Center에서 워크플로 자동화를 구성하는 방법](../security-center/workflow-automation.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure Security Benchmark: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대한 침투 테스트를 정기적으로 수행 및 모든 중요한 보안 결과를 수정

**지침**: 침투 테스트가 Microsoft 정책을 위반하지 않도록 Microsoft 클라우드 침투 테스트 시행 규칙을 따릅니다. Microsoft의 전략과 Microsoft에서 관리하는 클라우드 인프라, 서비스, 애플리케이션에 대한 레드 팀 실행 및 실시간 사이트 침투 테스트를 사용합니다. 

- [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud 레드 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
