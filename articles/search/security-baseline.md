---
title: Azure Cognitive Search에 대한 Azure 보안 기준
description: Azure Cognitive Search 보안 기준은 Azure Security Benchmark에 지정된 보안 권장 사항을 구현하기 위한 절차 지침과 리소스를 제공합니다.
author: msmbaldwin
ms.service: search
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 313ac05d8e4fdc19736d0c35285c2b854ec26968
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589196"
---
# <a name="azure-security-baseline-for-azure-cognitive-search"></a>Azure Cognitive Search에 대한 Azure 보안 기준

이 보안 기준은 [Azure Security Benchmark 버전 1.0](../security/benchmarks/overview-v1.md)의 지침을 Azure Cognitive Search에 적용합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 Azure Security Benchmark에서 정의된 **보안 컨트롤** 및 Azure Cognitive Search에 적용되는 관련 지침에 따라 그룹화됩니다. Azure Cognitive Search에 적용할 수 없는 **컨트롤** 또는 고객은 제외되었습니다.

Azure Cognitive Search가 Azure Security Benchmark에 완전히 매핑되는 방식을 확인하려면 [전체 Azure Cognitive Search 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조하세요.

## <a name="network-security"></a>네트워크 보안

자세한 내용은 [Azure Security Benchmark: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조하세요.

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: 모든 Microsoft Azure Virtual Network 서브넷 배포에 "최소 권한" 액세스 체계를 구현하는 규칙이 적용된 네트워크 보안 그룹이 있는지 확인합니다. 애플리케이션의 신뢰할 수 있는 포트 및 IP 주소 범위에 대해서만 액세스를 허용합니다. Azure 프라이빗 엔드포인트를 사용하여 Azure Cognitive Search를 배포하여(가능한 경우) 가상 네트워크에서 서비스에 대한 프라이빗 액세스를 가능하게 합니다.

Cognitive Search는 네트워크 액세스 제어 목록을 관리하는 추가 네트워크 보안 기능도 지원합니다. 방화벽 기능을 사용하여 특정 공용 IP 주소 범위에서 액세스를 제한하여 신뢰할 수 있는 출처와의 통신만 허용하도록 검색 서비스를 구성합니다.

- [Azure Cognitive Search용 프라이빗 엔드포인트를 구성하는 방법](service-create-private-endpoint.md)

- [Azure Cognitive Search 방화벽을 구성하는 방법](service-configure-firewall.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: 가상 네트워크, 서브넷 및 NIC의 트래픽 및 구성을 모니터링 및 기록

**지침**: Cognitive Search는 가상 네트워크에 직접 배포할 수 없습니다. 하지만 클라이언트 애플리케이션 또는 데이터 원본이 가상 네트워크에 있는 경우 네트워크 내 구성 요소(클라우드의 검색 서비스로 전송된 요청 포함)에 대한 트래픽을 모니터링하고 기록할 수 있습니다. 표준 권장 사항에는 네트워크 보안 그룹 흐름 로그를 사용하도록 설정하기 및 Azure Storage 또는 Log Analytics 작업 영역에 로그 보내기가 포함됩니다. 필요에 따라 트래픽 패턴에 대한 인사이트를 얻기 위해 트래픽 분석을 사용할 수 있습니다.

- [네트워크 보안 그룹 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [트래픽 분석을 사용하도록 설정하고 사용하는 방법](../network-watcher/traffic-analytics.md)

- [Azure Security Center에서 제공하는 네트워크 보안 이해](../security-center/security-center-network-recommendations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: Cognitive Search는 분산 서비스 거부 공격에 대처하기 위한 특정 기능을 제공하지 않지만 일반적인 보호를 위해 Cognitive Search 서비스와 연결된 가상 네트워크에서 DDoS Protection 표준을 사용하도록 설정할 수 있습니다.

- [DDoS Protection을 구성하는 방법](../ddos-protection/manage-ddos-protection.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷 기록

**지침**: Cognitive Search 서비스에 연결할 Azure VM(가상 머신)을 보호하는 네트워크 보안 그룹에 대해 네트워크 보안 그룹 흐름 로그를 사용하도록 설정합니다. 트래픽 감사를 위해 Azure Storage 계정에 로그를 보냅니다. 

비정상적인 활동을 조사하는 데 필요한 경우 Network Watcher 패킷 캡처를 사용하도록 설정합니다.

- [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Network Watcher를 사용하도록 설정하는 방법](../network-watcher/network-watcher-create.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 IDS/IPS(침입 탐지/침입 방지 시스템) 배포

**지침**: Cognitive Search는 네트워크 침입 탐지를 지원하지 않지만 침입 완화를 위해 Cognitive Search 서비스에서 허용하는 IP 주소를 지정하도록 방화벽 규칙을 구성할 수 있습니다. 공용 인터넷에서 검색 트래픽을 차단하는 프라이빗 엔드포인트를 구성합니다.

- [데이터 암호화를 위해 고객 관리형 키를 구성하는 방법](search-security-manage-encryption-keys.md)

- [인덱스 및 동의어 맵에서 고객 관리형 키 정보를 가져오는 방법](search-security-get-encryption-keys.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: Cognitive Search에서 인덱서 및 기술 세트를 활용하는 경우 서비스 태그를 사용하여 외부 리소스에 연결할 권한이 있는 IP 주소 범위를 나타냅니다. 

규칙의 적절한 원본 또는 대상 필드에 서비스 태그 이름(예: AzureCognitiveSearch)을 지정하여 리소스에 대한 트래픽을 허용하거나 거부합니다. 

- [가상 네트워크 서비스 태그](../virtual-network/service-tags-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

**지침**: Azure 프라이빗 엔드포인트를 사용하여 Cognitive Search를 구성하여 검색 서비스를 가상 네트워크와 통합할 수 있습니다.  네트워크 보안 그룹 및 네트워크 보안 및 트래픽 흐름과 관련된 기타 리소스에 리소스 태그를 사용합니다. 개별 네트워크 보안 그룹 규칙의 경우 "설명" 필드를 사용하여 네트워크와 주고받는 트래픽을 허용하는 규칙을 문서화합니다. 
 

태그 지정과 관련된 기본 제공 Azure Policy 정의(예: "태그 및 해당 값 필요" 효과)를 사용하여 모든 리소스가 태그를 사용하여 만들어졌는지 확인하고 태그가 지정되지 않은 기존 리소스를 알려줍니다. 

Azure PowerShell 또는 Azure CLI를 사용하여 태그를 기반으로 리소스를 조회하거나 작업을 수행할 수 있습니다.  

 
- [Cognitive Search용 프라이빗 엔드포인트를 만드는 방법](service-create-private-endpoint.md) 

 
 
- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

- [Azure Virtual Network를 만드는 방법](../virtual-network/quick-create-portal.md) 

 
- [네트워크 보안 그룹 규칙을 사용하여 네트워크 트래픽을 필터링하는 방법](../virtual-network/tutorial-filter-network-traffic.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure Security Benchmark: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조하세요.*

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure Monitor를 통해 Cognitive Search와 관련된 로그를 수집하여 엔드포인트 디바이스, 네트워크 리소스 및 기타 보안 시스템에 의해 생성된 보안 데이터를 집계합니다. Azure Monitor에서 Log Analytics 작업 영역을 사용하여 분석을 쿼리 및 수행하고 장기 보관 스토리지에 Azure Storage 계정을 사용합니다. 또는 이 데이터를 사용하도록 설정하고 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다.
 

 
- [Azure Monitor 및 타사 SIEM 통합을 시작하는 방법](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)
 

 
- [Azure Monitor를 사용하여 플랫폼 로그 및 메트릭을 수집하는 방법](../azure-monitor/essentials/diagnostic-settings.md) 
 

 
- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: 진단 및 운영 로그는 Cognitive Search의 세부 운영에 대한 인사이트를 제공하며 서비스 및 서비스에 액세스하는 워크로드를 모니터링하는 데 유용합니다.  진단 데이터를 캡처하려면 로깅 정보가 저장되는 위치를 지정하여 로깅을 사용하도록 설정합니다.
 

 
- [Azure Cognitive Search에 대한 로그 데이터를 수집하고 분석하는 방법](search-monitor-logs.md) 

 
- [Azure Monitor를 사용하여 플랫폼 로그 및 메트릭을 수집하는 방법](../azure-monitor/essentials/diagnostic-settings.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure 보안 벤치마크](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초가 됩니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Search**:

[!INCLUDE [Resource Policy for Microsoft.Search 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.search-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침**: 진단 메트릭에 제공되는 기록 데이터는 Cognitive Search를 통해 기본적으로 30일 동안 보존됩니다. 더 오래 보존하려면 기록된 이벤트 및 메트릭을 유지하기 위한 스토리지 옵션을 지정하는 설정을 사용하도록 설정해야 합니다.
 

 
Azure Monitor에서 조직의 준수 규정에 따라 Log Analytics 작업 영역 보존 기간을 설정합니다. Azure Storage 계정을 장기 보관 스토리지에 사용합니다. 
 

 
- [Log Analytics에서 데이터 보존 기간 변경](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period) 

 
- [Azure Storage 계정 로그에 관한 보존 정책을 구성하는 방법](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#enable-logs)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: Cognitive Search 서비스의 로그를 분석하고 비정상적인 동작을 모니터링합니다. Azure Monitor의 Log Analytics를 사용하여 로그를 검토하고 로그 데이터에 대한 쿼리를 수행합니다. 또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다. 

 
 
- [Cognitive Search에 대한 로그 데이터를 수집하고 분석하는 방법](search-monitor-logs.md)
 
- [Power BI에서 검색 로그 데이터를 시각화하는 방법](search-monitor-logs-powerbi.md)
 

 
- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)
 

 
- [Log Analytics에 대해 알아보기](../azure-monitor/logs/log-analytics-tutorial.md)
 

 
- [Azure Monitor에서 사용자 지정 쿼리를 수행하는 방법](../azure-monitor/logs/get-started-queries.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상 활동에 대한 경고 사용

**지침**: 보안 로그 및 이벤트에서 발견된 비정상적인 활동을 모니터링하고 경고하기 위해 Log Analytics 작업 영역에서 Security Center를 사용합니다. 또는 데이터를 사용하도록 설정하고 Azure Sentinel에 온보딩할 수 있습니다.
 

 
- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)
 

 
- [Azure Security Center에서 경고를 관리하는 방법](../security-center/security-center-managing-and-responding-alerts.md)
 

 
- [Log Analytics 로그 데이터에 관해 경고하는 방법](../azure-monitor/alerts/tutorial-response.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [Azure Security Benchmark: ID 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정 인벤토리 유지 관리

**지침**: Azure RBAC(역할 기반 액세스 제어)를 사용하면 역할 할당을 통해 Azure 리소스에 대한 액세스를 관리할 수 있습니다. 이러한 역할은 사용자, 그룹 서비스 주체, 관리 ID에 할당할 수 있습니다. 특정 리소스에 대해 미리 정의된 기본 제공 역할이 있으며 이러한 역할은 Azure CLI, Azure PowerShell 또는 Azure Portal과 같은 도구를 통해 쿼리하거나 인벤토리에 포함할 수 있습니다.

Cognitive Search 역할은 서비스 수준 관리 작업을 지원하는 권한과 연결됩니다. 이러한 역할은 서비스 엔드포인트에 대한 액세스 권한을 부여하지 않습니다. 엔드포인트용 작업(예: 인덱스 관리, 인덱스 채우기 및 검색 데이터 쿼리)에 대한 액세스는 API 키를 사용하여 요청을 인증합니다.

- [Azure Cognitive Search에 대한 관리 액세스를 위한 역할 설정](search-security-rbac.md)

- [Azure Cognitive Search 서비스에 대한 api-key 만들기 및 관리](search-security-api-keys.md)

- [PowerShell을 사용하여 Azure AD(Azure Active Directory)에서 디렉터리 역할을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [PowerShell을 사용하여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: Cognitive Search에는 인덱스 및 작업을 관리하는 데 사용할 수 있는 로컬 수준 또는 Azure AD(Azure Active Directory) 관리자 계정 개념이 없습니다. 

관리 작업에 대해 명시적으로 할당해야 하는 Azure AD 기본 제공 역할을 사용합니다. Azure AD PowerShell 모듈을 호출하여 임시 쿼리를 수행해서 관리 그룹의 멤버인 계정을 검색합니다.

- [Cognitive Search에서 관리 액세스용 역할을 사용하는 방법 ](search-security-rbac.md)

- [PowerShell을 사용하여 Azure AD에서 디렉터리 역할을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrole)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 SSO(Single Sign-On) 사용

**지침**: Azure AD(Azure Active Directory)에서 SSO 인증을 사용하여 Azure Resource Manager를 통해 지원되는 관리 작업에 대한 검색 서비스 정보에 액세스합니다. 

조직의 기존 ID로 서비스에 SSO를 사용하도록 설정하여 ID 및 자격 증명 수를 줄이는 프로세스를 설정합니다.

- [Azure AD를 사용한 SSO 이해](../active-directory/manage-apps/what-is-single-sign-on.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure AD(Azure Active Directory)의 다단계 인증 기능을 사용하도록 설정하고 Security Center의 ID 및 액세스 권장 사항을 따릅니다.

- [Azure에서 다단계 인증을 사용하는 방법](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

**지침**: Azure 리소스에 로그인하고 액세스하도록 구성된 다단계 인증을 사용하여 PAW(Privileged Access Workstation)를 사용합니다.
 

 
- [안전한 Azure 관리형 워크스테이션 이해](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
 

 
- [Azure AD(Azure Active Directory) 다단계 인증을 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

**지침**: Azure AD(Azure Active Directory) 보안 보고서 및 모니터링을 사용하여 환경에서 의심스럽거나 안전하지 않은 활동이 발생하면 감지합니다. Security Center를 사용하여 ID 및 액세스 활동을 모니터링합니다.

- [위험한 활동에 대해 플래그가 지정된 Azure AD 사용자를 식별하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center에서 사용자의 ID 및 액세스 활동을 모니터링하는 방법](../security-center/security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure AD(Azure Active Directory)를 Azure Cognitive Search의 서비스 수준 관리 작업을 위한 중앙 인증 및 권한 부여 시스템으로 사용합니다. Azure AD ID는 검색 서비스 엔드포인트에 대한 액세스 권한을 부여하지 않습니다.  인덱스 관리, 인덱스 채우기 및 검색 데이터에 대한 쿼리와 같은 작업에 대한 액세스는 API 키를 통해 가능합니다.

- [Azure AD 인스턴스를 만들고 구성하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Azure Cognitive Search 서비스에 대한 api-key 만들기 및 관리](search-security-api-keys.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure AD(Azure Active Directory)는 부실 계정을 검색하는 데 도움이 되는 로그를 제공합니다. Azure AD의 ID 및 액세스 검토를 사용하여 그룹 멤버 자격, 엔터프라이즈 애플리케이션에 대한 액세스 및 역할 할당을 효율적으로 관리합니다. 사용자의 액세스를 정기적으로 검토하여 적합한 사용자만 계속 액세스할 수 있도록 합니다. 

인덱스 관리, 인덱스 채우기 및 쿼리와 같은 검색 서비스 엔드포인트의 활동에 대한 Cognitive Search의 진단 로그를 검토합니다.

- [Azure AD 보고 이해](/azure/active-directory/reports-monitoring/)

- [Azure AD ID 및 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

- [Azure Cognitive Search의 작업 및 활동 모니터링](search-monitor-usage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화된 자격 증명에 대한 액세스 시도 모니터링

**지침**: Azure AD(Azure Active Directory) 로그인 활동, 감사 및 위험 이벤트 로그 원본에 대한 액세스를 통해 SIEM 또는 모니터링 도구와 통합할 수 있습니다.

Azure AD 사용자 계정에 대한 진단 설정을 만들고 감사 로그와 로그인 로그를 Log Analytics 작업 영역으로 보내서 이 프로세스를 간소화합니다. Log Analytics 작업 영역 내에서 원하는 경고를 구성합니다.

- [Azure 활동 로그를 Azure Monitor와 통합하는 방법](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure AD(Azure Active Directory) ID 보호 기능을 사용하여 사용자 ID와 관련해 감지된 의심스러운 동작에 대한 자동 응답을 구성합니다. 필요에 따라 추가 조사를 위해 데이터를 Azure Sentinel에 수집할 수도 있습니다.

- [Azure AD 위험한 로그인을 확인하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-protection"></a>데이터 보호

자세한 내용은 [Azure Security Benchmark: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조하세요.

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용하여 중요한 정보를 저장하거나 처리하는 Azure 리소스를 추적할 수 있도록 지원합니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 개발, 테스트 및 프로덕션을 위한 별도의 구독 및/또는 관리 그룹을 구현합니다. 리소스는 가상 네트워크/서브넷으로 구분되고, 적절하게 태그가 지정되고, 네트워크 보안 그룹 또는 Azure Firewall 내에서 보호되어야 합니다. 중요한 데이터를 저장하거나 처리하는 리소스는 격리되어야 합니다. Private Link를 사용하여 Cognitive Search에 대한 프라이빗 엔드포인트를 구성합니다.

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

- [Cognitive Search용 프라이빗 엔드포인트를 만드는 방법](service-create-private-endpoint.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: 네트워크 경계에서 Azure Marketplace의 타사 솔루션을 사용하여 중요한 정보의 무단 전송 여부를 모니터링하고 그러한 전송을 차단하며 정보 보안 전문가에게 경고합니다.

Microsoft는 기본 플랫폼을 관리하고 모든 고객 콘텐츠를 중요하게 취급하며 고객 데이터 손실 및 노출을 방지합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: 데이터 식별, 분류 및 손실 방지 기능은 Cognitive Search에서 아직 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현합니다. 

Microsoft는 기본 플랫폼을 관리하고 모든 고객 콘텐츠를 중요하게 취급하며 고객 데이터 손실 및 노출을 방지합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 관리

**지침**: 서비스 관리의 경우 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 키 및 구성에 대한 액세스를 관리합니다. 인덱싱 및 쿼리와 같은 콘텐츠 작업의 경우 Cognitive Search는 ID 기반 액세스 제어 모델 대신 키를 사용합니다. Azure RBAC를 사용하여 키에 대한 액세스를 제어합니다. 

 
- [Azure RBAC를 구성하는 방법](../role-based-access-control/role-assignments-portal.md)  
 
 
- [Cognitive Search에 대한 관리 액세스용 역할을 사용하는 방법 ](search-security-rbac.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: Cognitive Search는 Microsoft 관리형 키를 사용하여 미사용 상태의 인덱싱된 콘텐츠를 자동으로 암호화합니다. 보호가 더 필요하면 Azure Key Vault에서 만들고 관리하는 키를 사용하여 두 번째 암호화 계층으로 기본 암호화를 보완할 수 있습니다.

- [Azure Cognitive Search에서 데이터 암호화에 사용할 고객 관리형 키 구성](search-security-manage-encryption-keys.md)

- [Azure의 저장 데이터 암호화 이해](../security/fundamentals/encryption-atrest.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 기록 및 경고

**지침**: Azure Monitor에서 Azure 활동 로그를 사용하여 Cognitive Search 및 기타 중요하거나 관련된 리소스의 프로덕션 인스턴스가 변경되는 경우에 대한 경고를 만듭니다. 

 
- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](../azure-monitor/alerts/alerts-activity-log.md) 

 
- [Cognitive Search 활동에 대한 경고를 만드는 방법](search-monitor-logs.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure Security Benchmark: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화된 자산 검색 솔루션 사용

**지침**: Azure Resource Graph를 사용하여 구독 내의 모든 리소스(예: 컴퓨팅, 스토리지, 네트워크, 포트, 프로토콜 등)를 쿼리하고 검색합니다.

테넌트에서 적절한 권한(읽기)을 확인하고, 모든 Azure 구독은 물론 구독 내의 리소스를 열거합니다.

- [Azure Resource Graph Explorer를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

- [Azure 구독을 확인하는 방법](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Azure RBAC 이해](../role-based-access-control/overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타데이터를 사용하여 Azure 리소스에 태그를 적용하여 논리적으로 분류 체계를 구성합니다.

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

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: 승인된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: Cognitive Search의 인덱싱 및 기술 세트 처리와 관련이 있는 승인된 Azure 리소스 목록을 정의합니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: 이전에 조직 정책 및 표준에 따라 사용이 승인된 Azure 리소스의 인벤토리를 정의한 다음, Azure Policy 또는 Azure Resource Graph를 사용하여 승인되지 않은 Azure 리소스를 모니터링하는 것이 좋습니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy에서 다음과 같은 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리하거나 검색합니다. 환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](/azure/governance/policy/samples/)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Azure Resource Manager와 상호 작용하는 사용자 기능 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성함으로써 Azure 조건부 액세스를 사용하여 Azure Resource Manager와 상호 작용하는 사용자 기능을 제한합니다.  

 
다른 모든 작업, 특히 Cognitive Search 콘텐츠와 관련된 요청을 인증하는 데 사용되는 키에 대한 액세스를 제어합니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure Security Benchmark: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: "Microsoft.Search" 네임스페이스에서 Azure Policy 별칭을 사용하여 Azure Cognitive Search 리소스에 대한 구성을 감사하거나 적용하기 위한 사용자 지정 정책을 만듭니다. 다음과 같은 Cognitive Search 서비스에 대해 기본 제공 Azure Policy 정의를 사용할 수도 있습니다.

Azure 리소스에 대한 감사 로깅 사용

Azure Resource Manager는 템플릿을 JSON(JavaScript Object Notation) 형식으로 내보낼 수 있는 기능이 있으며, 내보낸 템플릿은 검토하여 구성이 조직의 보안 요구 사항을 충족하는지 확인해야 합니다.

Azure Security Center의 권장 사항을 Azure 리소스에 대한 보안 구성 기준으로 사용할 수도 있습니다.

- [Azure Cognitive Search에 대한 Azure Policy 규정 준수 컨트롤](security-controls-policy.md)

- [사용 가능한 Azure Policy 별칭을 보는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: Azure Policy [거부] 및 [존재하지 않으면 배포] 효과를 사용하여 Cognitive Search 서비스 리소스 전체에 보안 설정을 적용합니다. 

Azure Resource Manager 템플릿은 조직에 필요한 Azure 리소스의 보안 구성을 유지 관리하는 데 사용할 수 있습니다. 

- [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

- [Azure Cognitive Search에 대한 Azure Policy 규정 준수 컨트롤](security-controls-policy.md)

- [규정 준수를 적용하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager 템플릿 개요](../azure-resource-manager/templates/overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: 사용자 지정 Azure 정책 정의를 사용하는 경우 Azure DevOps 또는 Azure Repos를 사용하여 코드를 안전하게 저장하고 관리합니다.

- [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow)

- [Azure Repos 설명서](/azure/devops/repos/index)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스를 위한 구성 관리 도구 배포

**지침**: Azure Policy를 사용하여 Cognitive Search 서비스 리소스에 대한 표준 보안 구성을 정의하고 구현합니다. 

별칭을 사용하여 네트워크 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. 특정 리소스와 관련된 기본 제공 정책 정의도 사용할 수 있습니다. 

또한 Azure Automation을 사용하여 구성 변경 내용을 배포하고 정책 예외를 관리할 수 있습니다. 

- [Azure Cognitive Search에 대한 Azure Policy 규정 준수 컨트롤](security-controls-policy.md)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대한 자동화된 구성 모니터링 구현

**지침**: Security Center를 사용하여 Cognitive Search 서비스 리소스의 기준 검사를 수행합니다.  또한 Azure Policy를 사용하여 리소스 구성을 경고하고 감사합니다. 

- [Azure Security Center에서 권장 사항을 수정하는 방법](../security-center/security-center-remediate-recommendations.md)

- [Azure Cognitive Search에 대한 Azure Policy 규정 준수 컨트롤](security-controls-policy.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: Azure Key Vault와 함께 Azure 관리 ID를 사용하여 클라우드 애플리케이션에 대한 비밀 관리를 간소화합니다.

- [Azure 리소스에 대한 관리 ID를 사용하는 방법](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Key Vault를 만드는 방법](../key-vault/general/quick-create-portal.md)

- [관리 ID를 사용하여 Key Vault 인증을 제공하는 방법](../key-vault/general/assign-access-policy-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: Azure 관리 ID를 사용하여 Azure AD(Azure Active Directory)에서 자동으로 관리되는 ID를 사용하는 Key Vault 및 인덱서 데이터 원본과 같은 다른 Azure 서비스에 대한 액세스 권한을 Cognitive Search에 부여합니다. 관리 ID를 사용하면 코드의 자격 증명 없이 Azure Key Vault를 포함하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있습니다. 

- [관리 ID를 사용하여 데이터 원본에 대한 인덱서 연결 설정](search-howto-managed-identities-data-sources.md)

- [관리 ID를 사용하여 데이터 암호화를 위한 고객 관리형 키 구성](search-security-manage-encryption-keys.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [Azure Security Benchmark: 맬웨어 방어](../security/benchmarks/security-control-malware-defense.md)를 참조하세요.*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: Cognitive Search, Blob Storage, Azure SQL Database 등의 비 컴퓨팅 Azure 리소스에 업로드되는 콘텐츠를 미리 검사합니다. 

비 컴퓨팅 Azure 리소스에 업로드되는 콘텐츠를 미리 검사하는 것은 사용자의 책임입니다. Microsoft는 고객 데이터에 액세스할 수 없으므로 사용자를 대신해서 고객 콘텐츠의 맬웨어 방지 검사를 수행할 수 없습니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명이 업데이트되었는지 확인

**지침**: Cognitive Search에는 적용되지 않습니다. 해당 리소스에 맬웨어 방지 솔루션을 설치할 수 없습니다. 기본 플랫폼의 경우 Microsoft가 맬웨어 방지 소프트웨어 및 서명 업데이트를 처리합니다.  

 
조직에서 소유하고 검색 솔루션에 사용되는 컴퓨팅 리소스는 Security Center, Compute &amp; Apps의 권장 사항에 따라 모든 엔드포인트가 최신 서명을 사용하는 최신 상태인지 확인합니다. Linux의 경우 타사 맬웨어 방지 솔루션을 사용합니다.

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure Security Benchmark: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**지침**: 검색 서비스에 저장된 콘텐츠는 Azure Backup 또는 기타 기본 제공 메커니즘을 통해 백업할 수 없지만 애플리케이션 소스 코드 및 기본 데이터 원본에서 인덱스를 다시 작성하거나, 사용자 지정 도구를 빌드하여 인덱싱된 콘텐츠를 검색하고 저장할 수 있습니다. 

 
- [GitHub Index-backup-restore 샘플](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/index-backup-restore)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업 수행 및 고객 관리형 키 백업

**지침**: Cognitive Search는 현재 검색 서비스의 데이터에 대한 자동 백업을 지원하지 않으며 수동 프로세스를 통해 백업해야 합니다. Azure Key Vault에서 고객 관리형 키를 백업할 수도 있습니다.
 

- [Azure Cognitive Search 인덱스 백업 및 복원](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Azure에서 Key Vault 키를 백업하는 방법](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리형 키를 포함한 모든 백업의 유효성 검사

**지침**: Cognitive Search는 현재 검색 서비스의 데이터에 대한 자동 백업을 지원하지 않으며 수동 프로세스를 통해 백업및 복원해야 합니다. 백업 프로세스의 엔드투엔드 무결성을 보장하기 위해 수동으로 백업한 콘텐츠의 데이터 복원을 정기적으로 수행합니다.

- [Azure Cognitive Search 인덱스 백업 및 복원](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Azure에서 Key Vault 키를 복원하는 방법](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리형 키 보호 보장

**지침**: Cognitive Search는 현재 검색 서비스의 데이터에 대한 자동 백업을 지원하지 않으며 수동 프로세스를 통해 백업해야 합니다.  Azure Key Vault에서 고객 관리형 키를 백업할 수도 있습니다.  

 
Key Vault에서 일시 삭제 및 제거 보호를 사용하도록 설정하여 실수로 또는 악의적으로 삭제되지 않도록 키를 보호합니다. Azure Storage를 사용하여 수동 백업을 저장하는 경우 BLOB 또는 BLOB 스냅샷이 삭제될 때 일시 삭제를 사용하여 데이터를 저장하고 복구할 수 있습니다. 
 

 
- [Azure Cognitive Search 인덱스 백업 및 복원](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)
 

 
- [Key Vault에서 일시 삭제 및 제거 보호를 사용하도록 설정하는 방법](../storage/blobs/soft-delete-blob-overview.md) 

- [Azure Blob 스토리지에 대한 일시 삭제](../storage/blobs/soft-delete-blob-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../security/benchmarks/security-control-incident-response.md)을 참조하세요.

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 개발합니다. 탐지에서 인시던트 사후 검토까지의 인시던트 처리 및 관리 단계뿐만 아니라 담당자의 모든 역할도 정의하는 서면 인시던트 대응 계획이 있는지 확인합니다.

- [자체 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [고객은 NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용하여 사용자 고유의 인시던트 대응 계획을 만들 수도 있음](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center에서 심각도를 각 경고에 할당하여 먼저 조사해야 하는 경고에 대한 우선 순위를 지정합니다. 심각도는 Security Center가 결과에 얼마나 확신하는지 또는 경고를 발행하는 데 분석적으로 사용되었는지, 그리고 경고를 유발한 활동 배경에 악의적인 의도가 있었다는 신뢰 수준을 기반으로 합니다.

또한 태그를 사용하여 구독을 표시하고, Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 식별하고 분류할 수 있는 명명 시스템을 만듭니다. 인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고 해결의 우선 순위를 지정하는 것은 사용자의 책임입니다.

- [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md)

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 격차를 식별하고 필요에 따라 계획을 수정합니다.

- [NIST의 게시물인 "IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 안내"를 참조하세요.](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: 연속 내보내기 기능을 사용하여 Security Center 경고 및 권장 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 권장 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Security Center 데이터 커넥터를 사용하여 Azure Sentinel에 경고를 스트리밍할 수 있습니다.

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
