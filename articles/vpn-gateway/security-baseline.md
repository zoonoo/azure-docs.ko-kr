---
title: VPN Gateway에 대 한 Azure 보안 기준
description: VPN Gateway 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7604e8519e7ae8db255a0e033ca3df0bb941a845
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91626261"
---
# <a name="azure-security-baseline-for-vpn-gateway"></a>VPN Gateway에 대 한 Azure 보안 기준

이 보안 기준은 [Azure Security 벤치 마크 버전 1.0](../security/benchmarks/overview-v1.md) 의 지침을 VPN Gateway 적용 합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 Azure 보안 벤치 마크에 정의 된 **보안 컨트롤** 및 VPN Gateway에 적용 되는 관련 지침에 따라 그룹화 됩니다. VPN Gateway에 적용할 수 없는 **컨트롤** 은 제외 되었습니다.

VPN Gateway 완전히 Azure 보안 벤치 마크에 매핑되는 방법을 보려면 [전체 VPN Gateway 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조 하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [Azure 보안 벤치 마크: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조 하세요.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: VPN gateway 서브넷을 사용 하는 경우 게이트웨이 서브넷에 nsg (네트워크 보안 그룹)를 연결 하지 마세요. 이 서브넷에 네트워크 보안 그룹을 연결하면 VPN Gateway가 정상적으로 작동하지 않을 수 있습니다.  그러나 Virtual Network에서 VPN이 아닌 다른 게이트웨이 서브넷에 대 한 네트워크 보안 그룹을 사용 하도록 설정 합니다.

- [Virtual Network를 만드는 방법](../virtual-network/quick-create-portal.md) 

- [보안 구성을 사용 하 여 NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md) 

- [Azure Portal을 사용하여 경로 기반 VPN 게이트웨이 만들기](create-routebased-vpn-gateway-portal.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: 가상 네트워크, 서브넷 및 네트워크 인터페이스의 구성 및 트래픽을 모니터링 하 고 기록 합니다.

**지침**: Azure Security Center을 사용 하 고 네트워크 보호 권장 사항을 따라 Azure에서 네트워크 리소스를 보호 합니다. 

- [Azure Security Center에서 제공 하는 네트워크 보안 이해](../security-center/security-center-network-recommendations.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷을 기록 합니다.

**지침**: 요구 사항에 따라 게이트웨이에서 또는 특정 연결에서 VPN gateway 패킷 캡처를 사용 하도록 설정 합니다.

- [VPN gateway에 대 한 패킷 캡처 구성](packet-capture.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지 관리

**지침**: Azure Policy을 사용 하 여 네트워크 리소스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다.

또한 Azure 청사진을 사용 하 여 단일 청사진 정의에서 Azure Resource Manager 템플릿, Azure RBAC 할당 및 Azure Policy 할당과 같은 주요 환경 아티팩트를 패키지화 하 여 대규모 Azure 배포를 간소화할 수 있습니다. 새 구독 또는 기존 구독에 청사진을 적용 하 고 버전 관리를 통해 제어 및 관리를 세부적으로 조정할 수 있습니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md) 

- [네트워킹에 대 한 Azure Policy 샘플](/azure/governance/policy/samples/#network) 

- [Azure Blueprint를 만드는 방법](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용 하 여 리소스 구성을 모니터링 하 고 가상 네트워크 리소스에 대 한 변경 내용을 검색 합니다. VPN 게이트웨이와 관련 된 중요 한 리소스의 변경 내용이 발생 하는 경우 트리거할 Azure Monitor 내에서 경고를 만듭니다.

- [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](/azure/azure-monitor/platform/activity-log-view)

- [Azure Monitor에서 경고를 만드는 방법](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure 보안 벤치 마크: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조 하세요.*

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: VPN gateway 리소스와 같은 네트워크 리소스에 의해 생성 된 보안 데이터를 집계 하기 위해 Azure Monitor를 통해 작업 및 진단 로그를 수집 합니다. Azure Monitor를 사용 하 여 로그 데이터에 대 한 분석을 쿼리하고 수행할 수 있으며, 이러한 로그의 장기/보관 저장소에 Azure Storage 계정을 사용할 수 있습니다. 

또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다.

- [VPN Gateway에서 진단 로그 이벤트에 대 한 경고 설정](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

- [Azure Monitor를 사용 하 여 플랫폼 로그 및 메트릭을 수집 하는 방법](../azure-monitor/platform/diagnostic-settings.md)

- [Azure Monitor 및 타사 SIEM 통합을 시작하는 방법](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: 감사, 보안 및 진단 로그에 대 한 액세스를 위해 VPN Gateway 리소스에 대 한 진단 설정을 사용 하도록 설정 합니다. 자동으로 사용할 수 있는 활동 로그에는 이벤트 원본, 날짜, 사용자, 타임스탬프, 원본 주소, 대상 주소 및 기타 유용한 요소가 포함됩니다. 

- [Azure Monitor를 사용 하 여 플랫폼 로그 및 메트릭을 수집 하는 방법](../azure-monitor/platform/diagnostic-settings.md) 

- [Azure의 로깅 및 다른 로그 유형 이해](../azure-monitor/platform/platform-logs-overview.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침**: Azure Monitor에서 조직의 규정 준수 규정에 따라 Log Analytics 작업 영역 보존 기간을 설정 합니다. 장기 및 보관 저장소에 Azure Storage 계정을 사용 합니다. 

- [Log Analytics에서 데이터 보존 기간 변경](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period) 

- [Azure Storage 계정 로그에 대 한 보존 정책을 구성 하는 방법](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그를 모니터링 하 고 검토 합니다.

**지침**: 비정상적인 동작에 대 한 로그를 분석 및 모니터링 하 고 결과를 정기적으로 검토 합니다. Azure Monitor 및 Log Analytics 작업 영역을 사용 하 여 로그를 검토 하 고 로그 데이터에 대 한 쿼리를 수행할 수 있습니다. 

또는 Azure 센티널 또는 타사 SIEM에 대 한 온보드 데이터를 사용 하도록 설정할 수 있습니다. 

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md) 

- [Log Analytics 쿼리 시작](../azure-monitor/log-query/get-started-portal.md) 

- [Azure Monitor에서 사용자 지정 쿼리를 수행하는 방법](../azure-monitor/log-query/get-started-queries.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: 보안 로그 및 이벤트에서 발견 된 비정상적인 활동을 모니터링 하 고 경고 하기 위해 Log Analytics 작업 영역에서 Azure Security Center를 사용 합니다.

또는 온보드 데이터를 Azure 센티널로 설정할 수 있습니다.

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

- [Azure Security Center에서 경고를 관리 하는 방법](../security-center/security-center-managing-and-responding-alerts.md)

- [Log analytics 로그 데이터를 경고 하는 방법](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: 조직의 요구 사항에 따라 DNS 로깅 솔루션에 대 한 Azure Marketplace에서 타사 솔루션을 구현 합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [Azure 보안 벤치 마크: id 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조 하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**지침**: azure 역할 기반 액세스 제어 (RBAC)를 사용 하 여 역할 할당을 통해 azure 리소스에 대 한 액세스를 관리할 수 있습니다. 이러한 역할은 사용자, 그룹 서비스 사용자 및 관리 되는 id에 할당할 수 있습니다. 특정 리소스에 대 한 미리 정의 된 기본 제공 역할이 있으며 이러한 역할은 Azure CLI, Azure PowerShell 또는 Azure Portal와 같은 도구를 통해 인벤토리 또는 쿼리할 수 있습니다.

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할을 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0) 

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다. 

Azure AD Privileged Identity Management 및 Azure Resource Manager를 사용 하 여 Just-in-time 액세스를 사용 하도록 설정할 수도 있습니다. 

- [Privileged Identity Management에 대 한 자세한 정보](/azure/active-directory/privileged-identity-management/)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: SSO (Azure Active Directory Single Sign-On)를 사용 하십시오.

**지침**: 가능 하면 서비스 별로 개별 독립 실행형 자격 증명을 구성 하는 대신 Azure Active Directory SSO를 사용 합니다. Azure Security Center id 및 액세스 권장 사항을 사용 합니다. 

- [Azure AD를 사용 하 여 SSO 이해](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 대해 multi-factor authentication을 사용 하십시오.

**지침**: AZURE AD MFA를 사용 하도록 설정 하 고 Azure Security Center id 및 액세스 권장 사항에 따릅니다. 

- [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: 관리 작업에는 안전한 Azure 관리 워크스테이션 사용

**지침**: 높은 권한이 필요한 관리 작업에 대해 Azure에서 관리 하는 안전한 워크스테이션 (권한 있는 액세스 워크스테이션 또는 PAW 라고도 함)을 사용 합니다.

- [안전 하 고 Azure로 관리 되는 워크스테이션 이해](../active-directory/devices/concept-azure-managed-workstation.md)

- [Azure AD MFA를 사용 하도록 설정 하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정에서 의심 스러운 활동에 대 한 로그 및 경고

**지침**: Azure Active Directory 보안 보고서 및 모니터링을 사용 하 여 환경에서 의심 스러운 활동이 나 안전 하지 않은 활동이 발생 하는 시기를 검색 합니다. Azure Security Center를 사용하여 ID 및 액세스 활동을 모니터링합니다.

- [위험한 활동에 대해 플래그가 지정된 Azure AD 사용자를 식별하는 방법](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Azure Security Center에서 사용자의 ID 및 액세스 활동을 모니터링하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: Azure AD 명명 된 위치를 사용 하 여 IP 주소 범위 또는 국가/지역의 특정 논리적 그룹 에서만 액세스할 수 있도록 합니다. 

- [Azure AD 명명 된 위치를 구성 하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure Active Directory (Azure AD)를 중앙 인증 및 권한 부여 시스템으로 사용 합니다. Azure AD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다. 

- [Azure AD 인스턴스를 만들고 구성 하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure Active Directory (Azure AD)는 오래 된 계정을 검색 하는 데 도움이 되는 로그를 제공 합니다. 또한 Azure AD id 및 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리 합니다. 사용자 액세스를 정기적으로 검토 하 여 적절 한 사용자만 계속 액세스할 수 있도록 할 수 있습니다. 

- [Azure AD 보고 이해](/azure/active-directory/reports-monitoring/) 

- [Azure AD id 및 액세스 검토를 사용 하는 방법](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화 되는 자격 증명에 대 한 액세스 시도를 모니터링 합니다.

**지침**: siem/모니터링 도구와 통합할 수 있도록 하는 Azure AD 로그인 활동, 감사 및 위험 이벤트 로그 원본에 액세스할 수 있습니다. 

Azure AD 사용자 계정에 대 한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 전송 하 여이 프로세스를 간소화할 수 있습니다. Log Analytics 작업 영역 내에서 원하는 경고를 구성할 수 있습니다. 

- [Azure Monitor와 Azure 활동 로그를 통합 하는 방법](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure AD ID 보호 기능을 사용 하 여 사용자 id와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성 합니다. 추가 조사를 위해 데이터를 Azure Sentinel로 수집할 수도 있습니다. 

- [Azure AD 위험한 로그인을 확인하는 방법](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [Azure 보안 벤치 마크: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조 하세요.*

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: VPN gateway에는 각 고객 가상 네트워크에 대 한 전용 VM 인스턴스가 있습니다. 환경 유형 및 데이터 민감도 수준과 같은 개별 보안 도메인에 대해 별도의 가상 네트워크, 구독 및 관리 그룹을 사용 하 여 격리를 구현 합니다. 응용 프로그램 및 엔터프라이즈 환경에서 요구 하는 Azure 리소스에 대 한 액세스 수준을 제한할 수 있습니다. Azure Active Directory 역할 기반 액세스 제어를 통해 Azure 리소스에 대 한 액세스를 제어할 수 있습니다.

- [추가 Azure 구독을 만드는 방법](/azure/billing/billing-create-subscription)

- [관리 그룹을 만드는 방법](/azure/governance/management-groups/create)

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: network 경계에서 Azure Marketplace의 타사 솔루션을 사용 하 여 중요 한 정보를 무단으로 전송 하는 행위를 모니터링 하 고 정보 보안 전문가에 게 경고 하는 동안 이러한 전송을 차단 합니다. 

Microsoft에서 관리 하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요 한 것으로 간주 하 고 고객 데이터 손실 및 노출을 방지 합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다. 

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: vpn 게이트웨이는 Azure vpn gateway와 고객 온-프레미스 vpn 장치 (S2S) 또는 VPN 클라이언트 (P2S) 간에 고객 패킷을 암호화 합니다. VPN 게이트웨이는 VNet 간 암호화도 지원 합니다.

가상 네트워크의 해당 리소스에 대해 미사용 암호화 및 전송 중인 암호화에 대 한 Azure Security Center 권장 사항을 따릅니다.

- [VPN 유형 정보](vpn-gateway-about-vpn-gateway-settings.md#vpntype)

- [사이트 간 VPN Gateway 연결에 대한 VPN 디바이스 및 IPsec/IKE 매개 변수 정보](vpn-gateway-about-vpn-devices.md#ipsec)

- [암호화 요구 사항 및 Azure VPN Gateway 정보](vpn-gateway-about-compliance-crypto.md)

- [S2S VPN 또는 VNet 간 연결에 대한 IPsec/IKE 정책 구성](vpn-gateway-ipsecikepolicy-rm-powershell.md)

- [Azure를 사용 하 여 전송 중인 암호화 이해](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: 타사 활성 검색 도구를 사용 하 여 기술 시스템에서 저장, 처리 또는 전송 하는 모든 중요 한 정보 (온사이트 또는 원격 서비스 공급자 포함)를 식별 하 고 조직의 중요 한 정보 인벤토리를 업데이트 합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: 역할 기반 액세스 제어를 사용 하 여 리소스에 대 한 액세스를 제어 합니다.

**지침**: AZURE AD RBAC를 사용 하 여 데이터 및 리소스에 대 한 액세스를 제어 하 고, 그렇지 않으면 서비스별 액세스 제어 방법을 사용 합니다. 소유자, 참가자 또는 네트워크 참가자와 같은 기본 제공 역할 기반 액세스 제어 역할을 사용한 다음 적절 한 범위에 역할을 할당 합니다. 사용자 지정 역할을 만들고 가상 네트워크, 서브넷, VPN 게이트웨이, 네트워크 인터페이스, 네트워크 보안 그룹 및 경로 테이블에 필요한 특정 사용 권한을 역할에 할당 하 여 가상 네트워크 기능 하위 집합에 대 한 특정 권한을 할당 합니다.

- [Azure에서 RBAC를 구성 하는 방법](../role-based-access-control/role-assignments-portal.md)

- [가상 네트워크 계획](../virtual-network/virtual-network-vnet-plan-design-arm.md#permissions)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고

**지침**: VPN 게이트웨이와 같은 중요 한 azure 리소스에 변경 내용이 발생 하는 경우 Azure 활동 로그를 트리거하기 위해 Azure Monitor 경고를 구성 합니다. 

- [VPN Gateway 메트릭에 대 한 경고 설정](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)

- [VPN Gateway에서 진단 로그 이벤트에 대 한 경고 설정](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)

- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="vulnerability-management"></a>취약점 관리

*자세한 내용은 [Azure 보안 벤치 마크: 취약성 관리](../security/benchmarks/security-control-vulnerability-management.md)를 참조 하세요.*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**지침**: 일반적인 위험 점수 매기기 프로그램 (예: 일반적인 취약성 점수 매기기 시스템) 또는 타사 검사 도구에서 제공 하는 기본 위험 등급을 사용 합니다.

- [NIST 게시--일반적인 취약성 점수 매기기 시스템](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure 보안 벤치 마크: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조 하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화 된 asset discovery 솔루션 사용

**지침**: Azure 리소스 그래프를 사용 하 여 구독 내에서 VPN 게이트웨이와 관련 된 모든 리소스를 쿼리하고 검색 합니다. 테 넌 트에서 적절 한 (읽기) 권한이 있는지 확인 하 고 구독의 모든 리소스를 열거할 수 있습니다. 또한 Azure CLI를 사용 하 여 VPN Gateway 리소스를 열거할 수도 있습니다.

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

- [VPN gateway에 대 한 Azure CLI](https://docs.microsoft.com/cli/azure/network/vnet-gateway?view=azure-cli-latest)

- [Azure 구독을 확인하는 방법](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Azure RBAC 이해](../role-based-access-control/overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: VPN Gateway 리소스에 태그를 적용 하 여 정의 된 분류에 따라 논리적으로 구성 합니다.

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 해당 하는 경우 태그 지정, 관리 그룹 및 별도의 구독을 사용 하 여 VPN Gateway 리소스를 구성 하 고 추적 합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다. 

- [추가 Azure 구독을 만드는 방법](/azure/billing/billing-create-subscription) 

- [관리 그룹을 만드는 방법](/azure/governance/management-groups/create) 

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: 승인 된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 조직 요구 사항에 따라 승인 된 Azure 리소스 및 계산 리소스에 대 한 승인 된 소프트웨어의 인벤토리를 만듭니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure policy를 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정 합니다.

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

또한 Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리하고 검색 합니다. 

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

- [Virtual network에 대 한 Azure policy 샘플 기본 제공](/azure/virtual-network/policy-samples)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: 고객은 조직의 보안 요구 사항에 따라 필요에 따라 Azure Policy 정의를 할당 하 여 리소스를 만들거나 사용 하지 못하게 할 수 있습니다. 그러나 승인 되지 않았거나 권한이 없는 리소스를 제거 하는 고유한 프로세스를 구현 해야 합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy을 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 구독에 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](/azure/governance/policy/samples/not-allowed-resource-types)

- [Virtual network에 대 한 Azure policy 샘플 기본 제공](/azure/virtual-network/policy-samples)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침**: Azure AD 조건부 액세스를 사용 하 여 "Microsoft Azure 관리" 앱에 대 한 "액세스 차단"을 구성 함으로써 사용자의 Azure 리소스 관리자와 상호 작용 하는 기능을 제한 합니다. 

- [Azure 리소스 관리자에 대 한 액세스를 차단 하도록 조건부 액세스를 구성 하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure 보안 벤치 마크: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조 하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Policy 별칭을 사용 하 여 사용자 지정 정책을 만들어 VPN Gateway를 비롯 한 Azure 네트워크 리소스의 구성을 감사 하거나 적용 합니다. 기본 제공 Azure Policy 정의를 사용할 수도 있습니다.

Azure Resource Manager은 구성이 조직의 보안 요구 사항을 충족 하거나 초과 하는지 확인 하기 위해 검토 해야 하는 Java JSON (Script Object Notation)에서 템플릿을 내보낼 수 있습니다.

Azure Security Center의 권장 사항을 Azure 리소스에 대 한 보안 구성 기준으로 사용할 수도 있습니다.

- [사용 가능한 Azure 정책 별칭을 확인하는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [자습서: 규정 준수를 적용하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)

- [Virtual network에 대 한 Azure policy 샘플 기본 제공](/azure/virtual-network/policy-samples)

- [Azure Portal에서 템플릿에 대 한 단일 및 다중 리소스 내보내기](../azure-resource-manager/templates/export-template-portal.md)

- [보안 권장 사항 - 참조 가이드](../security-center/recommendations-reference.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: Azure Resource Manager 템플릿 및 Azure Policy 할당을 사용 하 여 VPN Gateway 및 관련 리소스와 연결 된 Azure 리소스를 안전 하 게 구성 합니다. Azure Resource Manager 템플릿은 Azure 리소스와 함께 가상 머신을 배포 하는 데 사용 되는 JSON 기반 파일 이며 사용자 지정 템플릿은 유지 관리 해야 합니다. Microsoft는 기본 템플릿에 대 한 유지 관리를 수행 합니다.  [거부] 및 [없는 경우 배포] 모드의 Azure Policy를 사용 하 여 Azure 리소스에서 보안 설정을 적용 합니다.

- [Azure Resource Manager 템플릿 만들기에 대 한 정보](../virtual-machines/windows/ps-template.md)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

- [가상 네트워크용 Azure Resource Manager 템플릿 샘플](../virtual-network/template-samples.md)

- [Virtual network에 대 한 Azure policy 샘플 기본 제공](/azure/virtual-network/policy-samples)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: Azure devops를 사용 하 여 사용자 지정 Azure Policy 정의, Azure Resource Manager 템플릿 및 필요한 상태 구성 스크립트와 같은 코드를 안전 하 게 저장 하 고 관리할 수 있습니다. Azure DevOps에서 관리 하는 리소스에 액세스 하려면 Azure DevOps와 통합 된 경우 Azure Active Directory (Azure AD)에 정의 된 특정 사용자, 기본 제공 보안 그룹 또는 그룹에 대 한 권한을 부여 하거나 거부할 수 있습니다. 또는 TFS와 통합 된 경우 Active Directory 합니다. 

- [Azure DevOps에 코드를 저장하는 방법](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [Azure DevOps의 사용 권한 및 그룹 정보](/azure/devops/organizations/security/about-permissions)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스에 대 한 구성 관리 도구 배포

**지침**: Azure Policy을 사용 하 여 Azure 리소스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. Azure Policy 별칭을 사용 하 여 Azure 리소스의 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 특정 리소스와 관련 된 기본 제공 정책 정의를 사용할 수도 있습니다. 또한 Azure Automation를 사용 하 여 구성 변경 내용을 배포할 수 있습니다. 

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md) 

- [별칭을 사용 하는 방법](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대 한 자동화 된 구성 모니터링 구현

**지침**: Azure Policy 정의를 할당 하 여 리소스 VPN Gateway와 관련 된 리소스 구성을 측정 합니다. Azure Policy insights를 사용 하 여 리소스 구성을 감사 하 고 중요 한 구성 변경에 대 한 경고를 생성 합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Virtual network에 대 한 Azure policy 샘플 기본 제공](/azure/virtual-network/policy-samples)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: VPN Gateway 서비스는 내부적으로 고객 사전 공유 키와 인증서를 암호화 된 형식으로 저장 하 고 전송 합니다. 고객은 자신의 시스템에서 미리 공유한 키 또는 인증서를 보호 해야 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: AZURE P2S VPN은 다음과 같은 세 가지 인증 방법을 지원 합니다.

- 인증서 기반
- RADIUS
- Azure AD(Azure Active Directory)

Azure AD를 사용 하면 관리 되는 id를 활용할 수 있으므로 권장 됩니다.

- [테넌트 구성](openvpn-azure-ad-tenant.md)

- [여러 클라이언트 앱을 사용하여 테넌트 구성](openvpn-azure-ad-tenant-multi-app.md)

- [Multi-Factor Authentication 구성](openvpn-azure-ad-mfa.md)

- [VPN 클라이언트 구성](openvpn-azure-ad-client.md)

- [관리 Id를 구성 하는 방법](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다. 

- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure 보안 벤치 마크: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조 하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 정기 자동 백업 확인

**지침**: Azure Resource Manager을 사용 하 여 VPN Gateway 리소스를 배포할 수 있습니다. Azure Resource Manager는 VPN Gateway 리소스를 복원 하기 위해 백업으로 사용할 수 있는 템플릿을 내보내는 기능을 제공 합니다. Azure Automation를 사용 하 여 정기적으로 Azure Resource Manager 템플릿 내보내기 API를 호출 합니다.

- [Azure Resource Manager 개요](../azure-resource-manager/management/overview.md)

- [가상 네트워크용 Azure Resource Manager 템플릿 샘플](../virtual-network/template-samples.md)

- [리소스 그룹-템플릿 내보내기](/rest/api/resources/resourcegroups/exporttemplate)

- [Azure Automation 소개](../automation/automation-intro.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업을 수행 하 고 고객 관리 키를 백업 합니다.

**지침**: Azure Resource Manager을 사용 하 여 VPN Gateway 리소스를 배포할 수 있습니다. Azure Resource Manager은 VPN gateway 및 관련 리소스를 복원 하기 위해 백업으로 사용할 수 있는 템플릿을 내보내는 기능을 제공 합니다. Azure Automation를 사용 하 여 정기적으로 Azure Resource Manager 템플릿 내보내기 API를 호출 합니다.

- [Azure Portal에서 템플릿에 대 한 단일 및 다중 리소스 내보내기](../azure-resource-manager/templates/export-template-portal.md)

- [리소스 그룹-템플릿 내보내기](/rest/api/resources/resourcegroups/exporttemplate)

- [Azure Automation 소개](../automation/automation-intro.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 비롯 한 모든 백업 유효성 검사

**지침**: 필요한 경우 격리 된 구독에 정기적으로 Azure Resource Manager 템플릿 배포를 정기적으로 수행할 수 있는지 확인 합니다.

- [ARM 템플릿을 사용 하 여 리소스 배포 및 Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

- [Azure에서 키 자격 증명 모음 키를 복원하는 방법](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객이 관리 하는 키를 보호 해야 합니다.

**지침**: Azure devops를 사용 하 여 사용자 지정 Azure Policy 정의 및 Azure Resource Manager 템플릿과 같은 코드를 안전 하 게 저장 하 고 관리 합니다. Azure DevOps에서 관리 하는 리소스를 보호 하기 위해 Azure DevOps와 통합 된 경우 특정 사용자, 기본 제공 보안 그룹 또는 Azure Active Directory (Azure AD)에 정의 된 그룹에 대 한 권한을 부여 하거나 거부할 수 있습니다.

- [Azure DevOps에 코드를 저장하는 방법](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure DevOps의 사용 권한 및 그룹 정보](/azure/devops/organizations/security/about-permissions)

- [Azure Storage Blob에 대한 일시 삭제](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [Azure 보안 벤치 마크: 인시던트 응답](../security/benchmarks/security-control-incident-response.md)을 참조 하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대 한 인시던트 대응 가이드를 개발 합니다. 담당자의 모든 역할을 정의 하는 사고 대응 계획 및 인시던트를 검색 하 고 인시던트를 해결 하는 방법에 대 한 해결 방법을 확인 합니다. 

- [사용자 고유의 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [NIST의 컴퓨터 보안 인시던트 처리 가이드를 사용 하 여 고유한 인시던트 대응 계획을 만드는 데 도움이 됩니다.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Azure Security Center는 각 경고에 심각도를 할당 하 여 먼저 조사 해야 하는 경고의 우선 순위를 지정할 수 있도록 합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용된 결과 또는 분석의 신뢰도 및 경고가 발생된 활동의 배후에 악의적인 의도가 있었음에 대한 신뢰 수준을 기준으로 합니다.

또한 태그를 사용 하 여 구독을 표시 하 고, 특히 중요 한 데이터를 처리 하는 Azure 리소스를 식별 하 고 분류 하는 명명 시스템을 만듭니다.  사고가 발생 한 Azure 리소스 및 환경의 중요도에 따라 경고 수정의 우선 순위를 지정 하는 것은 사용자의 책임입니다. 

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md) 

- [태그를 사용하여 Azure 리소스 구성](/azure/azure-resource-manager/resource-group-using-tags).

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 정기적으로 시스템의 인시던트 응답 기능을 테스트 하 여 Azure 리소스를 보호 하는 연습을 수행 합니다. 약한 지점과 격차를 확인 한 후 필요에 따라 응답 계획을 수정 합니다. 

- [NIST의 게시-IT 계획 및 기능에 대 한 테스트, 학습 및 연습 프로그램 가이드](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다. 

- [Azure Security Center 보안 연락처를 설정 하는 방법](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: Azure 리소스에 대 한 위험을 식별 하는 데 도움이 되도록 연속 내보내기 기능을 사용 하 여 Azure Security Center 경고 및 권장 사항을 내보냅니다. 연속 내보내기를 사용 하면 경고 및 권장 사항을 수동으로 또는 지속적인 지속적인 방식으로 내보낼 수 있습니다. Azure Security Center data connector를 사용 하 여 경고를 Azure 센티널로 스트리밍할 수 있습니다. 

- [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md) 

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: 워크플로 자동화 기능 Azure Security Center 사용 하 여 Azure 리소스를 보호 하기 위해 보안 경고 및 권장 사항에 대 한 응답을 자동으로 트리거합니다. 

- [Security Center에서 워크플로 자동화를 구성 하는 방법](../security-center/workflow-automation.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure 보안 벤치 마크: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조 하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대 한 정기적인 침투 테스트를 수행 하 고 모든 중요 한 보안 결과를 수정 하세요.

**지침**: Engagement의 Microsoft 클라우드 침투 테스트 규칙에 따라 침투 테스트가 Microsoft 정책을 위반 하지 않는지 확인 합니다. Microsoft에서 관리 하는 클라우드 인프라, 서비스 및 응용 프로그램에 대 한 레드 팀 및 라이브 사이트 침투 테스트의 전략과 실행을 사용 합니다. 

- [Engagement의 침투 테스트 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft 클라우드 Red 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
