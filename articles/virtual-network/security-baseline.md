---
title: Virtual Network에 대한 Azure 보안 기준
description: Virtual Network 보안 기준은 Azure Security Benchmark에 지정된 보안 권장 사항을 구현하기 위한 절차 지침과 리소스를 제공합니다.
author: msmbaldwin
ms.service: virtual-network
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 433f20de4d150aadb48ead685e981c60579f2730
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285533"
---
# <a name="azure-security-baseline-for-virtual-network"></a>Virtual Network에 대한 Azure 보안 기준

이 보안 기준은 [Azure Security Benchmark 버전 1.0](../security/benchmarks/overview-v1.md)의 지침을 Azure Virtual Network에 적용합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 Azure Security Benchmark에서 정의한 **보안 컨트롤** 및 Azure Virtual Network에 적용되는 관련 지침에 따라 그룹화됩니다. 

> [!NOTE]
> Virtual Network에 적용되지 않거나 Microsoft의 책임인 **컨트롤** 은 제외되었습니다. Virtual Network가 Azure Security Benchmark에 완전히 매핑되는 방법을 확인하려면 **[전체 Virtual Network 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/virtual-network-security-baseline-v1.1.xlsx)** 을 참조하세요.

## <a name="network-security"></a>네트워크 보안

자세한 내용은 [Azure Security Benchmark: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조하세요.

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: 가상 네트워크, 서브넷 및 네트워크 인터페이스의 구성 및 트래픽을 모니터링 및 기록

**지침**: Security Center를 사용하고 네트워크 보호 권장 사항에 따라 Azure에서 네트워크 리소스를 보호합니다. 

네트워크 보안 그룹 흐름 로그를 Log Analytics 작업 영역에 보내고, 트래픽 분석을 사용하여 Azure 클라우드의 트래픽 흐름에 대한 인사이트를 제공할 수 있습니다. 트래픽 분석은 네트워크 활동을 시각화하고, 핫 스폿을 식별하며, 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하며, 잘못된 네트워크 구성을 파악할 수 있는 기능을 제공합니다. 

Azure Monitor 로그를 사용하여 사용자 환경에 대한 인사이트를 제공합니다. 작업 영역은 데이터를 정렬하고 분석하는 데 사용해야 하며, Application Insights 및 Security Center와 같은 다른 Azure 서비스와 통합할 수 있습니다. 

Azure Storage 계정 또는 이벤트 허브로 보낼 리소스 로그를 선택합니다. 다른 플랫폼을 사용하여 로그를 분석할 수도 있습니다. 

- [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [트래픽 분석을 사용하도록 설정하고 사용하는 방법](../network-watcher/traffic-analytics.md)

- [Security Center에서 제공하는 네트워크 보안 이해](../security-center/security-center-network-recommendations.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 관한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 제어와 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: Azure Virtual Network에서 DDoS(분산 서비스 거부) 표준 보호를 사용하도록 설정하여 DDoS 공격으로부터 보호합니다.

위협 인텔리전스 기반 필터링을 사용하도록 설정하고 악성 네트워크 트래픽에 대해 "경고 및 거부"하도록 구성된 조직의 각 네트워크 경계에 Azure Firewall을 배포합니다.

Security Center의 위협 방지 기능을 사용하여 알려진 악성 IP 주소와의 통신을 검색합니다.

Security Center의 적응형 네트워크 강화를 적용하여 실제 트래픽 및 위협 인텔리전스를 기반으로 포트 및 원본 IP를 제한하는 네트워크 보안 그룹 구성을 권장할 수 있습니다. 

- [Azure Portal을 사용하여 Azure DDoS Protection 표준 관리](../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall 위협 인텔리전스 기반 필터링](../firewall/threat-intel.md)

- [Security Center의 위협 방지](../security-center/azure-defender.md)

- [Azure Security Center의 적응형 네트워크 강화](../security-center/security-center-adaptive-network-hardening.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 관한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 제어와 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷 기록

**지침**: 네트워크 패킷을 기록하는 데 일반적으로 사용 가능한 패킷 캡처 도구 외에도 VPN Gateway의 패킷 캡처를 사용합니다. 

Azure Marketplace 제품에서 제공하는 패킷 브로커 파트너 솔루션을 통해 TAP(터미널 액세스 지점) 또는 네트워크 표시 기능을 제공하는 에이전트 기반 또는 NVA 솔루션을 검토할 수도 있습니다.

- [VPN 게이트웨이에 대한 패킷 캡처 구성](../vpn-gateway/packet-capture.md) 

- [네트워크 가상 어플라이언스 파트너](https://azure.microsoft.com/solutions/network-appliances)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 관한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 제어와 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 IDS/IPS(침입 탐지/침입 방지 시스템) 배포

**지침**: 위협 인텔리전스를 사용하여 가상 네트워크에 배포된 Azure Firewall을 사용합니다. Azure Firewall 위협 인텔리전스 기반 필터링을 사용하면 알려진 악성 IP 주소 및 도메인과 주고받는 트래픽을 경고하거나 거부할 수 있습니다. IP 주소 및 도메인은 Microsoft 위협 인텔리전스 피드에서 제공됩니다. 

페이로드 검사 기능을 사용하여 IDS/IPS 기능을 지원하는 Azure Marketplace에서 제공하는 적절한 제품을 선택할 수도 있습니다.

악의적인 트래픽을 탐지 및/또는 거부하기 위해 각 조직의 네트워크 경계에서 원하는 방화벽 솔루션을 배포합니다.

- [Azure Firewall 배포 방법](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Firewall을 사용하여 경고를 구성하는 방법](../firewall/threat-intel.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: Virtual Network 서비스 태그를 사용하여 네트워크 보안 그룹 또는 Azure Firewall에서 네트워크 액세스 제어를 정의합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 규칙의 적절한 소스 또는 대상 필드에 서비스 태그 이름(예: ApiManagement)을 지정하여 해당 서비스에 대한 트래픽을 허용하거나 거부합니다. Microsoft에서는 서비스 태그에서 압축한 주소 접두사를 관리하고 주소를 변경하는 대로 서비스 태그를 자동으로 업데이트합니다.

애플리케이션 보안 그룹을 사용하여 복잡한 보안 구성을 간소화할 수 있습니다. 애플리케이션 보안 그룹을 통해 네트워크 보안을 애플리케이션 구조의 자연 확장으로 구성할 수 있습니다. 이렇게 하면 가상 머신을 그룹화하고 해당 그룹에 따라 네트워크 보안 정책을 정의할 수 있습니다.

- [서비스 태그 이해 및 사용](service-tags-overview.md)

- [애플리케이션 보안 그룹 이해 및 사용](https://docs.microsoft.com/azure/virtual-network/network-security-groups-overview#application-security-groups)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지

**지침**: Azure Policy를 사용하여 네트워크 리소스에 대한 표준 보안 구성을 정의 및 구현하고 구현을 위한 기본 제공 네트워크 정책 정의를 검토합니다.

가상 네트워크와 관련된 사용 가능한 보안 권장 사항을 포함하는 Security Center에 대한 기본 정책을 참조하세요.

Azure Blueprints를 사용하여 주요 환경 아티팩트(예: Azure Resource Manager 템플릿, Azure RBAC(역할 기반 액세스 제어) 할당 및 정책)를 단일 청사진 정의로 패키지하여 대규모 Azure 배포를 간소화할 수 있습니다. 버전 관리를 통해 세부적으로 조정된 제어 및 관리를 위해 새 구독에 Azure Blueprint를 적용할 수 있습니다. 

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [네트워킹을 위한 Azure Policy 샘플](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network) 

- [Azure Blueprint를 만드는 방법](../governance/blueprints/create-blueprint-portal.md)

- [Azure Security Center에서 모니터링 사용](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Security%20Center/AzureSecurityCenter.json)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

**지침**: 네트워크 보안 그룹 및 네트워크 보안/트래픽 흐름과 관련된 기타 리소스에 태그를 사용합니다. "설명" 필드를 사용하여 개별 네트워크 보안 그룹 규칙에 대해 네트워크와 주고받는 트래픽을 허용하는 규칙에 대한 비즈니스 요구, 기간 및 기타 정보를 지정합니다.
태그 지정과 관련된 기본 제공 Azure Policy 정의(예: "태그 및 해당 값 필요")를 사용하여 모든 리소스에서 태그를 포함하도록 만들고 태그가 없는 기존 리소스를 알립니다.

Azure PowerShell 또는 Azure CLI를 선택하여 태그를 기반으로 리소스를 조회하거나 작업을 수행합니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

- [Virtual Network를 만드는 방법](quick-create-portal.md)

- [보안 구성을 사용하여 NSG를 만드는 방법](tutorial-filter-network-traffic.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용하여 리소스 구성을 모니터하고 가상 네트워크의 변경 내용을 검색합니다. Azure Monitor 내에서 중요한 리소스가 변경되면 트리거되는 경고를 만듭니다.

- [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Azure Monitor에서 경고를 만드는 방법](../azure-monitor/alerts/alerts-activity-log.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure Security Benchmark: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조하세요.*

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: 이벤트 원본, 날짜, 사용자, 타임스탬프, 원본 주소, 대상 주소 및 기타 유용한 요소를 포함하는 감사 및 활동 로그에 액세스할 수 있도록 Azure Monitor를 사용합니다. 

Azure Monitor 내에서 Log Analytics 작업 영역을 사용하여 분석을 쿼리 및 수행하고, Azure Storage 계정을 장기/보관 스토리지에 사용할 수 있습니다.
또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다. 

- [Azure Monitor를 사용한 플랫폼 로그 및 메트릭 수집 방법](../azure-monitor/essentials/diagnostic-settings.md)

- [Azure 활동 로그 이벤트를 보고 검색하기](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: 이벤트 원본, 날짜, 사용자, 타임스탬프, 원본 주소, 대상 주소 및 기타 유용한 요소를 포함하는 감사 및 활동 로그에 액세스할 수 있도록 Azure Monitor를 사용합니다.

- [Azure Monitor를 사용한 플랫폼 로그 및 메트릭 수집 방법](../azure-monitor/essentials/diagnostic-settings.md)

- [Azure 활동 로그 이벤트를 보고 검색하기](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침**: Azure Monitor 내에서 조직의 규정 준수 규칙에 따라 Log Analytics 작업 영역 보존 기간을 설정합니다. 보안 로그 스토리지 보존의 장기/보관 스토리지에 Azure Storage 계정을 사용합니다.

- [Log Analytics에서 데이터 보존 기간 변경](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Azure Storage 계정 로그에 관한 보존 정책을 구성하는 방법](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: 로그를 분석하고 모니터링하여 비정상 동작이 있는지 확인하고 결과를 정기적으로 검토합니다. Azure Monitor의 Log Analytics 작업 영역을 사용하여 분석을 쿼리 및 수행하고, Azure Storage 계정을 장기/보관 스토리지에 사용할 수 있습니다. 

또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다. 

- [Log Analytics 작업 영역 이해](../azure-monitor/logs/log-analytics-tutorial.md)

- [Azure Monitor에서 사용자 지정 쿼리를 수행하는 방법](../azure-monitor/logs/get-started-queries.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

- [Azure Monitor 및 타사 SIEM 통합을 시작하는 방법](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상 활동에 대한 경고 사용

**지침**: 보안 로그 및 이벤트에서 발견된 비정상적인 활동을 모니터링하고 경고하기 위해 Log Analytics 작업 영역에서 Security Center를 사용합니다.

또는 경고를 위해 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다.

- [Security Center에서 경고를 관리하는 방법](../security-center/security-center-managing-and-responding-alerts.md)

- [Log Analytics 로그 데이터에 대한 경고 방법](../azure-monitor/alerts/tutorial-response.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: 조직의 요구에 따라 DNS 로깅 솔루션을 위한 Azure Marketplace에서 타사 솔루션을 구현합니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [Azure Security Benchmark: ID 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정 인벤토리 유지 관리

**지침**: 명시적으로 할당하여 쿼리할 수 있는 Azure AD(Azure Active Directory) 기본 제공 관리자 역할을 사용하세요.

Azure AD PowerShell 모듈을 사용하여 임시 쿼리를 수행해서 관리 그룹의 구성원인 계정을 검색합니다.

- [PowerShell을 사용하여 Azure AD에서 디렉터리 역할을 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [PowerShell을 사용하여 Azure AD에서 디렉터리 역할의 구성원을 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다. Security Center의 ID와 액세스 관리를 사용하여 관리 계정 수를 모니터링합니다.

Azure Active Directory(Azure AD) Privileged Identity Management Privileged Roles for Microsoft Services와 Azure Resource Manager를 사용하여 Just-In-Time/Just-Enough-Access를 사용하도록 설정합니다.

- [Privileged Identity Management에 대한 자세한 정보](/azure/active-directory/privileged-identity-management/)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory SSO(Single Sign-On) 사용

**지침**: 서비스별로 개별 독립 실행형 자격 증명을 구성하는 대신 Azure AD(Azure Active Directory)와 함께 SSO를 사용합니다. Security Center의 ID 및 액세스 관리 권장 사항을 사용합니다.

- [Azure AD의 애플리케이션에 대한 Single Sign-On](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure AD(Azure Active Directory) 다단계 인증을 사용하도록 설정하고, Security Center의 ID 및 액세스 관리 권장 사항을 따릅니다.

- [Azure에서 다단계 인증을 사용하는 방법](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: 관리 작업에 안전한 Azure 관리 워크스테이션 사용

**지침**: Azure 네트워크 리소스를 로그인하고 액세스하도록 구성된 다단계 인증과 함께 PAW(Privileged Access Workstation)를 사용합니다. 

- [Privileged Access Workstation에 대한 자세한 정보](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure에서 다단계 인증을 사용하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

**지침**: Azure AD(Azure Active Directory) 위험 탐지 기능을 활용하여 위험한 사용자 동작에 대한 경고와 보고서를 볼 수 있습니다. 

Security Center 위험 검색 경고를 Azure Monitor로 수집하고, 작업 그룹을 사용하여 사용자 지정 경고/알림을 구성합니다.

- [Security Center 위험 탐지 이해(의심스러운 활동)](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 

- [사용자 지정 경고 및 알림에 대한 작업 그룹 구성 방법](../azure-monitor/alerts/action-groups.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리 그룹에서만 액세스하도록 허용합니다.

- [Azure에서 명명된 위치를 구성하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure AD(Azure Active Directory)를 서비스에 대한 중앙 인증 및 권한 부여 시스템으로 사용합니다. Azure AD는 미사용 데이터와 전송 중인 데이터에 강력한 암호화를 사용하여 데이터를 보호하며 사용자 자격 증명을 솔트 및 해시하고 안전하게 저장합니다.  

- [Azure AD 인스턴스를 만들고 구성하는 방법](../active-directory-domain-services/tutorial-create-instance.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure AD(Azure Active Directory)를 사용하여 부실 계정을 검색하는 데 도움이 되는 로그를 제공합니다. 

Azure ID 액세스 검토를 수행하여 그룹 멤버 자격, 엔터프라이즈 애플리케이션에 대한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자 액세스를 정기적으로 검토하여 활성 사용자만 계속 액세스할 수 있도록 합니다.

- [Azure AD 보고 이해](/azure/active-directory/reports-monitoring/)

- [Azure ID 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화된 자격 증명에 대한 액세스 시도 모니터링

**지침**: Azure AD(Azure Active Directory) 로그인 활동, 감사 및 위험 이벤트 로그 원본을 액세스를 기반으로 하는 모든 SIEM 또는 모니터링 도구와 통합합니다.

Azure AD 사용자 계정에 대한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 보내서 이 프로세스를 간소화합니다. Log Analytics 작업 영역 내에서 원하는 경고를 구성할 수 있습니다.

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure AD(Azure Active Directory) 위험 및 ID 보호 기능을 사용해 가상 네트워크에 대한 사용자 ID와 관련하여 탐지된 의심스러운 동작에 대한 자동 응답을 구성합니다. 추가 조사를 위해 Azure Sentinel에 데이터를 수집합니다.

- [Azure AD 위험한 로그인을 확인하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-protection"></a>데이터 보호

자세한 내용은 [Azure Security Benchmark: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조하세요.

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: 전송 중인 모든 중요한 정보를 암호화합니다. 가상 네트워크의 Azure 리소스에 연결하는 클라이언트가 TLS 1.2 이상을 협상할 수 있는지 확인합니다. 미사용 암호화 및 전송 중 암호화에 대한 Security Center 권장 사항을 따릅니다. 

Microsoft에서는 내부적으로는 Azure 네트워크 내에서 전송 중인 데이터를, 외부적으로는 인터넷을 통해 최종 사용자에게 전송 중인 데이터를 보호하기 위해 고객이 사용할 수 있는 여러 옵션을 제공합니다. 해당 옵션에는 가상 사설망(IPsec/IKE 암호화 활용), TLS(전송 계층 보안) 1.2 이상(Application Gateway, Azure Front Door 등 Azure 구성 요소 사용), Azure Virtual Machines의 직접 프로토콜(예: Windows IPsec 또는 SMB) 등을 통한 통신이 포함됩니다.

또한 Azure 데이터 센터 간에 이동하는 모든 Azure 트래픽에 대해 MACsec(데이터 링크 계층의 IEEE 표준)를 사용하는 “기본적으로 암호화”가 지원되도록 설정되어 있어 고객 데이터의 기밀성과 무결성이 보장됩니다.

- [Azure를 통한 전송 중 데이터 암호화 이해](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 관리 

**지침**: Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 데이터 및 리소스에 대한 액세스를 관리합니다. 그렇지 않으면 서비스별 액세스 제어 메서드를 사용합니다. 

소유자, 기여자 또는 네트워크 기여자와 같은 기본 제공 역할을 선택하고 역할을 적절한 범위에 할당합니다. 예를 들어 가상 네트워크에 필요한 특정 권한이 있는 가상 네트워크 기능의 하위 집합을 이러한 역할 중 하나에 할당할 수 있습니다. 

- [Azure RBAC를 구성하는 방법](../role-based-access-control/role-assignments-portal.md)

- [가상 네트워크 계획](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm#permissions)

- [Azure 기본 제공 역할 검토](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#networking)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 기록 및 경고

**지침**: Azure 활동 로그와 함께 Azure Monitor를 사용하여 Virtual Networks 및 네트워크 보안 그룹과 같은 중요한 Azure 리소스에 변경 내용이 발생하는 경우에 대한 경고를 만듭니다.

- [네트워크 보안 그룹에 대한 진단 로깅](virtual-network-nsg-manage-log.md)

- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](../azure-monitor/alerts/alerts-activity-log.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure Security Benchmark: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화된 자산 검색 솔루션 사용

**지침**: Azure Resource Graph를 사용하여 Virtual Networks, 구독 내 하위 집합 같은 모든 네트워킹 리소스를 쿼리하고 발견합니다. 테넌트에서 적절한 권한(읽기)이 있는지 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거할 수 있습니다.

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

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

**지침**: 적합할 경우 태그 지정, 관리 그룹, 별도의 구독을 사용하여 Virtual Network 및 관련 리소스를 구성하고 추적합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: 승인된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 조직 요구 사항에 따라 승인된 Azure 리소스 및 계산 리소스에 승인된 소프트웨어의 인벤토리를 만듭니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy에서 다음의 기본 제공 정책 정의를 사용해 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.

- 허용되지 않는 리소스 종류 

- 허용되는 리소스 유형 

Azure Storage 계정을 사용하는 것과 같은 높은 보안 기반 환경에서 Azure 리소스 그래프를 사용하여 구독 내의 리소스를 쿼리하거나 검색합니다. 

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

- [가상 네트워크용 Azure Policy 샘플 기본 제공](policy-reference.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: 조직의 정책에서 요구하는 대로 Azure Policy에서 리소스 생성 또는 사용을 차단합니다. 권한 없는 리소스를 제거하는 프로세스를 구현합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy에서 다음의 기본 제공 정책 정의를 사용해 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

- [가상 네트워크용 Azure Policy 샘플 기본 제공](policy-reference.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Azure Resource Manager와 상호 작용하는 사용자 기능 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 액세스 차단을 구성함으로써 Azure 조건부 액세스를 사용하여 Azure Resource Manager와 상호 작용하는 사용자 기능을 제한합니다.

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure Security Benchmark: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Policy 별칭을 사용하여 Azure 네트워크 리소스의 구성을 감사하거나 적용하고 기본 제공 Azure Policy 정의를 사용하는 사용자 지정 정책도 만듭니다.

JSON(JavaScript Object Notation) 형식의 Azure Resource Manager를 사용하여 빌드 템플릿을 내보내고 이를 검토하여 구성이 조직의 보안 요구 사항을 충족하거나 초과하는지 확인합니다.

Security Center에서 Azure 리소스에 대한 보안 구성 기준으로 권장 사항을 구현합니다.

- [사용 가능한 Azure Policy 별칭을 보는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [자습서: 규정 준수를 적용하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)

- [가상 네트워크용 Azure Policy 샘플 기본 제공](policy-reference.md)

- [Azure Portal에서 템플릿에 대한 단일 및 다중 리소스 내보내기](../azure-resource-manager/templates/export-template-portal.md)

- [보안 권장 사항 - 참조 가이드](../security-center/recommendations-reference.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: Azure Resource Manager 템플릿 및 Azure Policy를 사용하여 가상 네트워크 및 관련 리소스에 연결된 Azure 리소스를 안전하게 구성합니다.  Azure Resource Manager 템플릿은 Azure 리소스와 함께 가상 머신을 배포하는 데 사용되는 JSON(JavaScript Object Notation) 기반 파일입니다. Microsoft는 기본 템플릿에 대한 유지 관리를 수행합니다.  

[거부] 및 [존재하지 않으면 배포] Azure Policy 효과를 사용하여 보안 설정을 Azure 리소스 전체에 적용합니다.

- [Azure Resource Manager 템플릿 만들기에 대한 정보](../virtual-machines/windows/ps-template.md) 

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

- [가상 네트워크용 Azure Resource Manager 템플릿 샘플](template-samples.md)

- [가상 네트워크용 Azure Policy 샘플 기본 제공](policy-reference.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: Azure DevOps를 사용하여 사용자 지정 Azure Policy, Azure Resource Manager 템플릿 및 원하는 상태 구성 스크립트와 같은 코드를 안전하게 저장하고 관리합니다. 등

Azure DevOps에서 관리할 리소스(예: 코드, 빌드 및 작업 추적)에 액세스할 수 있는 권한이 있어야 합니다. 대부분의 권한은 기본 제공 보안 그룹을 통해 부여됩니다. Azure AD(Azure Active Directory)(Azure DevOps와 통합된 경우)나 Active Directory(Team Foundation Server와 통합된 경우)에 정의된 특정 사용자, 기본 제공 보안 그룹 또는 그룹에 대한 권한을 부여하거나 거부할 수 있습니다.

- [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Azure DevOps의 사용 권한 및 그룹 정보](/azure/devops/organizations/security/about-permissions)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스를 위한 구성 관리 도구 배포

**지침**: Azure Policy를 사용하여 Azure 리소스에 대한 표준 보안 구성을 정의하고 구현합니다. Azure Policy 별칭을 사용하여 Azure 리소스의 네트워크 구성과 특정 리소스와 관련된 기본 제공 정책 정의를 감사하거나 적용하는 사용자 지정 정책을 만듭니다. 

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [별칭을 사용하는 방법](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

- [가상 네트워크용 Azure Policy 샘플 기본 제공](policy-reference.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대한 자동화된 구성 모니터링 구현

**지침**: Security Center를 사용하여 Azure Virtual Network 및 관련된 리소스에 대한 기준을 검색합니다. Azure Policy를 사용하여 Azure 리소스 구성을 경고하고 감사합니다.

- [Security Center에서 권장 사항을 수정하는 방법](../security-center/security-center-remediate-recommendations.md)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [가상 네트워크용 Azure Policy 샘플 기본 제공](policy-reference.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: Azure Key Vault와 함께 관리되는 서비스 ID를 사용하여 Azure Virtual Network에서 호스트되는 Azure 리소스를 위한 비밀 관리를 간소화하고 보호합니다.

- [Azure 관리 ID와 통합하는 방법](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [주요 자격 증명 모음을 만드는 방법](../key-vault/secrets/quick-create-portal.md) 

- [관리 ID를 사용하여 주요 자격 증명 모음 인증을 제공하는 방법](../key-vault/general/assign-access-policy-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure Security Benchmark: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**지침**: Azure Resource Manager를 사용하여 가상 네트워크 및 관련 리소스를 배포합니다. Azure Resource Manager는 가상 네트워크 및 관련 리소스를 복원하기 위한 백업으로 사용할 수 있는 템플릿을 내보내는 기능을 제공합니다.  Azure Automation을 사용하여 정기적으로 Azure Resource Manager 템플릿 내보내기 API를 호출합니다.

- [Azure Resource Manager 개요](../azure-resource-manager/management/overview.md)

- [가상 네트워크용 Azure Resource Manager 템플릿 샘플](template-samples.md)

- [Azure Portal에서 템플릿에 대한 단일 및 다중 리소스 내보내기](../azure-resource-manager/templates/export-template-portal.md)

- [리소스 그룹 - 템플릿 내보내기](/rest/api/resources/resourcegroups/exporttemplate)

- [Azure Automation 소개](../automation/automation-intro.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업 수행 및 고객 관리형 키 백업

**지침**: Azure Resource Manager를 사용하여 가상 네트워크 및 관련 리소스를 배포합니다. Azure Resource Manager는 가상 네트워크 및 관련 리소스를 복원하기 위한 백업으로 사용할 수 있는 템플릿을 내보내는 기능을 제공합니다. Azure Automation을 사용하여 정기적으로 Azure Resource Manager 템플릿 내보내기 API를 호출합니다. Azure Key Vault 내에서 고객 관리형 키를 백업합니다.

- [Azure Resource Manager 개요](../azure-resource-manager/management/overview.md)

- [가상 네트워크용 Azure Resource Manager 템플릿 샘플](template-samples.md)

- [Azure Portal에서 템플릿에 대한 단일 및 다중 리소스 내보내기](../azure-resource-manager/templates/export-template-portal.md)

- [리소스 그룹 - 템플릿 내보내기](/rest/api/resources/resourcegroups/exporttemplate)

- [Azure Automation 소개](../automation/automation-intro.md)

- [Azure에서 키 자격 증명 모음 키를 백업하는 방법](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리형 키를 포함한 모든 백업의 유효성 검사

**지침**: 정기적으로 Azure Resource Manager 템플릿을 격리된 구독에 배포하고 백업된 고객 관리 키의 복원을 테스트합니다.

- [ARM 템플릿 및 Azure Portal을 사용하여 리소스 배포](../azure-resource-manager/templates/deploy-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리형 키 보호 보장

**지침**: Azure DevOps를 사용하여 사용자 지정 Azure Policy 정의 및 Azure Resource Manager 템플릿과 같은 코드를 안전하게 저장하고 관리합니다.

Azure AD(Azure Active Directory)(Azure DevOps와 통합된 경우)나 Active Directory(Team Foundation Server와 통합된 경우)에 정의된 특정 사용자, 기본 제공 보안 그룹 또는 그룹에 대한 권한을 부여하거나 거부합니다.

Azure RBAC(역할 기반 액세스 제어)를 사용하여 고객 관리형 키를 보호합니다. 

주요 자격 증명 모음에서 일시 삭제 및 제거 방지를 사용하도록 설정하여 실수로 또는 악의적으로 삭제되지 않도록 키를 보호합니다.

- [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow)

- [Azure DevOps의 사용 권한 및 그룹 정보](/azure/devops/organizations/security/about-permissions)

- [Key Vault에서 일시 삭제 및 제거 방지를 사용하도록 설정하는 방법](../storage/blobs/soft-delete-blob-overview.md)

- [Azure Storage Blob에 대한 일시 삭제](../storage/blobs/soft-delete-blob-overview.md)

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

**지침**: Security Center에서 심각도를 각 경고에 할당하여 먼저 조사해야 하는 경고에 대한 우선 순위를 지정합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용되는 결과 또는 분석의 신뢰도 및 경고가 발생한 활동에 악의적인 의도가 있었다는 신뢰 수준을 기준으로 합니다.

태그를 사용하여 구독(예: 프로덕션 또는 비프로덕션)을 명확하게 표시하고 Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 명확하게 식별하고 분류하는 명명 시스템을 만듭니다.  인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다.

- [Security Center의 보안 경고](../security-center/security-center-alerts-overview.md)

- [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: Azure 리소스를 보호하는 데 도움이 되도록 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 결함을 식별하고 필요에 따라 계획을 수정합니다.

- [NIST 게시물 - IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 가이드](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.

- [Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: Azure 리소스에 대한 위험을 식별하는 데 도움이 되도록 연속 내보내기 기능을 사용하여 Security Center 경고 및 권장 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. 

또한 Security Center 데이터 커넥터를 사용하여 Azure Sentinel에 경고를 스트리밍할 수 있습니다.

- [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Security Center의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 관한 'Logic Apps'를 통해 응답을 자동으로 트리거함으로써 Azure 리소스를 보호합니다.

- [워크플로 자동화와 Logic Apps를 구성하는 방법](../security-center/workflow-automation.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure Security Benchmark: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대한 침투 테스트를 정기적으로 수행 및 모든 중요한 보안 결과를 수정

**지침**: Microsoft Engagement 규칙에 따라 침투 테스트가 Microsoft 정책을 위반하지 않는지 확인합니다. Microsoft 관리형 클라우드 인프라, 서비스, 애플리케이션에 대한 Microsoft의 Red Teaming 및 라이브 사이트 침투 테스트 전략과 실행을 사용합니다.

- [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud 레드 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
