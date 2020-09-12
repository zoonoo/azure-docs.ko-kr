---
title: Azure 개인 링크에 대 한 azure 보안 기준
description: Azure 개인 링크 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: private-link
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 654fc453f0b4167ae91afcab811de321925c6bf5
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89614677"
---
# <a name="azure-security-baseline-for-azure-private-link"></a>Azure 개인 링크에 대 한 azure 보안 기준

이 보안 기준은 azure [보안 벤치 마크](../security/benchmarks/overview.md) 에서 Azure 개인 링크에 대 한 지침을 적용 합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 azure 보안 벤치 마크에 정의 된 **보안 컨트롤과** Azure 개인 링크에 적용 되는 관련 지침에 따라 그룹화 됩니다. Azure 개인 링크에 적용할 수 없는 **컨트롤이** 제외 되었습니다. Azure 개인 링크가 Azure 보안 벤치 마크에 완전히 매핑되는 방식을 보려면 [전체 azure Virtual Network 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조 하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [Azure 보안 벤치 마크: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조 하세요.*

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용 하 여 리소스 구성을 모니터링 하 고 개인 링크와 관련 된 네트워크 리소스에 대 한 변경 내용을 검색 합니다. 

중요 한 리소스의 변경 내용이 발생 하는 경우 트리거할 Azure Monitor 내에서 경고를 만듭니다.

- [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](/azure/azure-monitor/platform/activity-log-view)

- [Azure Monitor에서 경고를 만드는 방법](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure 보안 벤치 마크: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조 하세요.*

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: 개인 링크 끝점, 가상 네트워크 및 네트워크 보안 그룹과 같은 네트워크 리소스에 의해 생성 된 보안 데이터를 집계 하기 위해 Azure Monitor를 통해 로그를 수집 합니다. 

Azure Monitor 내에서 Log Analytics 작업 영역을 사용 하 여 분석을 쿼리하고 수행 하며 장기/보관 저장소에 Azure Storage 계정을 사용할 수 있습니다.

또한 조직의 비즈니스 요구 사항에 따라 Azure 센티널 또는 타사 SIEM에 대 한 온-보드 데이터를 사용 하도록 설정 합니다.

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

- [Azure Monitor를 사용 하 여 플랫폼 로그 및 메트릭을 수집 하는 방법](../azure-monitor/platform/diagnostic-settings.md)

- [개인 링크에 대 한 로깅 및 모니터링](private-link-overview.md#logging-and-monitoring)

- [네트워크 보안 그룹에 대한 진단 로깅](../virtual-network/virtual-network-nsg-manage-log.md)

- [Azure Monitor 및 타사 SIEM 통합을 시작하는 방법](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: 작업을 시작한 사람, 작업 상태, 작업 상태 및 기타 유용한 감사 정보 등 개인 링크 리소스에 대해 수행 되는 로그 작업을 Azure Monitor 활동 로그를 사용 하도록 설정 합니다. 

- [Azure Monitor를 사용 하 여 플랫폼 로그 및 메트릭을 수집 하는 방법](../azure-monitor/platform/diagnostic-settings.md)

- [Azure 활동 로그 이벤트를 보고 검색하기](/azure/azure-monitor/platform/activity-log-view)

- [개인 링크에 대 한 로깅 및 모니터링](private-link-overview.md#logging-and-monitoring)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침**: 개인 링크와 관련 된 로그의 경우 Azure Monitor 내의 조직의 규정 준수 규정에 따라 Log Analytics 작업 영역 보존 기간을 설정 합니다. 장기/보관 로그 저장소에 Azure Storage 계정을 사용 합니다.

- [Log Analytics에서 데이터 보존 기간 변경](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Azure Storage 계정 로그에 대 한 보존 정책을 구성 하는 방법](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그를 모니터링 하 고 검토 합니다.

**지침**: 비정상적인 동작에 대 한 로그를 분석 및 모니터링 하 고 정기적으로 결과를 검토 합니다. Azure Monitor의 Log Analytics 작업 영역을 사용 하 여 로그를 검토 하 고 로그 데이터에 대 한 쿼리를 수행 합니다.

또 다른 옵션은 Azure 센티널 또는 타사 SIEM에 대 한 온보드 데이터를 사용 하도록 설정 하는 것입니다.

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

- [Log Analytics 작업 영역 이해](../azure-monitor/log-query/get-started-portal.md)

- [Azure Monitor에서 사용자 지정 쿼리를 수행하는 방법](../azure-monitor/log-query/get-started-queries.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: 보안 로그 및 이벤트에 있는 비정상적인 활동을 모니터링 하 고 경고 하기 위해 Log Analytics 작업 영역으로 구성 된 Security Center를 사용 합니다.

조직의 비즈니스 요구 사항에 따라 Azure 센티널 또는 타사 SIEM에 대 한 온-보드 데이터를 사용 하도록 설정 합니다.

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

- [Azure Security Center에서 경고를 관리 하는 방법](../security-center/security-center-managing-and-responding-alerts.md)

- [Log analytics 로그 데이터를 경고 하는 방법](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [Azure 보안 벤치 마크: id 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조 하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**지침**: 명시적으로 할당 하 고 쿼리할 수 있는 Azure Active Directory (Azure AD) 기본 제공 관리자 역할을 사용 합니다. Azure AD PowerShell 모듈을 실행 하 여 임시 쿼리를 수행 하 여 관리 그룹의 구성원 인 계정을 검색 합니다.

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrole)

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다. Security Center의 Id 및 액세스 관리 기능을 사용 하 여 관리 계정 수를 모니터링 합니다.

또한 Microsoft 서비스에 대 한 권한 있는 역할 Privileged Identity Management Azure Active Directory (Azure AD) 및 Azure Resource Manager를 사용 하 여 Just-in-time/액세스에 충분 한 액세스를 사용 하도록 설정 합니다.

- [Privileged Identity Management에 대 한 자세한 정보](/azure/active-directory/privileged-identity-management/)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: SSO (Azure Active Directory Single Sign-On)를 사용 하십시오.

**지침**: 가능 하면 서비스 별로 개별 독립 실행형 자격 증명을 구성 하는 대신 Azure Active Directory에서 Single Sign-On를 사용 합니다.

- [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 대해 multi-factor authentication을 사용 하십시오.

**지침**: Azure Active Directory (Azure AD) multi-factor AUTHENTICATION (MFA)을 사용 하도록 설정 하 고 Security Center Id 및 액세스 관리 권장 사항을 따릅니다.

- [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: 관리 작업에는 안전한 Azure 관리 워크스테이션 사용

**지침**: PAW (권한 있는 액세스 워크스테이션)를 사용 하 여 Azure 네트워크 리소스에 로그인 하 고 구성 하도록 Multi-Factor Authentication 구성 합니다.

- [Privileged Access Workstation에 대한 자세한 정보](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정에서 의심 스러운 활동에 대 한 로그 및 경고

**지침**: Azure Active Directory (Azure AD) 위험 검색 기능을 활용 하 여 위험한 사용자 동작에 대 한 경고 및 보고서를 볼 수 있습니다. Security Center 위험 검색 경고를 Azure Monitor에 수집 하 고 작업 그룹을 사용 하 여 사용자 지정 경고/알림을 구성 합니다.

- [Azure Security Center 위험 탐지 이해 (의심 스러운 활동)](/azure/active-directory/reports-monitoring/concept-risk-events)

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [사용자 지정 경고 및 알림에 대 한 작업 그룹을 구성 하는 방법](../azure-monitor/platform/action-groups.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명 된 위치를 사용 하 여 IP 주소 범위 또는 국가/지역의 특정 논리적 그룹 에서만 액세스할 수 있도록 합니다.

- [Azure에서 명명 된 위치를 구성 하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure Active Directory (Azure AD)를 중앙 인증 및 권한 부여 시스템으로 사용 합니다. Azure AD는 미사용 데이터 및 전송 중인 데이터에 대해 강력한 암호화를 사용 하 여 데이터를 보호 하 고 사용자 자격 증명을 salts, 해시 및 안전 하 게 저장 합니다.  

- [Azure AD 인스턴스를 만들고 구성 하는 방법](../active-directory-domain-services/tutorial-create-instance.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure Active Directory는 부실 계정을 검색하는 데 도움이 되는 로그를 제공합니다. 또한 Azure Id 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리 합니다. 사용자 액세스를 정기적으로 검토 하 여 적절 한 사용자만 계속 액세스할 수 있도록 할 수 있습니다.

- [Azure AD 보고 이해](/azure/active-directory/reports-monitoring/)

- [Azure ID 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화 되는 자격 증명에 대 한 액세스 시도를 모니터링 합니다.

**지침**: siem/모니터링 도구와 통합 하려면 Azure Active Directory (Azure AD) 로그인 활동, 감사 및 위험 이벤트 로그 원본을 사용 하세요.

Azure AD 사용자 계정에 대 한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 전송 하 여이 프로세스를 간소화 합니다. Log Analytics 작업 영역 내에서 원하는 경고를 구성 합니다.

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대 한 경고

**지침**: Azure AD (Azure Active Directory) 위험 및 id 보호 기능을 사용 하 여 네트워크 리소스에 대 한 사용자 id와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성 합니다. 

추가 조사를 위해 데이터를 Azure 센티널로 수집 합니다.

- [Azure AD 위험한 로그인을 확인하는 방법](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [Azure 보안 벤치 마크: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조 하세요.*

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 환경 유형 및 데이터 민감도 수준과 같은 개별 보안 도메인에 대해 별도의 구독 및 관리 그룹을 사용 하 여 격리를 구현 합니다. 

비즈니스 요구 사항에 따라 응용 프로그램 및 엔터프라이즈 환경으로 Azure 리소스에 대 한 액세스 수준을 제한 합니다. 

Azure Active Directory 역할 기반 액세스 제어를 통해 Azure 리소스에 대 한 액세스를 제어 합니다.

- [추가 Azure 구독을 만드는 방법](/azure/billing/billing-create-subscription)

- [관리 그룹을 만드는 방법](/azure/governance/management-groups/create)

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: 전송 중인 모든 중요 한 정보를 암호화 합니다. 가상 네트워크의 Azure 리소스에 연결 하는 모든 클라이언트가 TLS 1.2 이상을 협상할 수 있는지 확인 합니다. 개인 링크는 기본 프로토콜을 방해 하지 않습니다. 고객이 사용 하는 다른 제품에 대 한 모범 사례를 사용 합니다.

해당 하는 경우 미사용 암호화 및 전송 중인 암호화에 대 한 Security Center 권장 사항을 따릅니다.

- [Azure를 사용 하 여 전송 중인 암호화 이해](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: 역할 기반 액세스 제어를 사용 하 여 리소스에 대 한 액세스를 제어 합니다.

**지침**: azure RBAC (역할 기반 액세스 제어)를 사용 하 여 데이터 및 리소스에 대 한 액세스를 제어 하 고, 그렇지 않으면 서비스별 액세스 제어 방법을 사용 합니다.

- [여기서 간단한 설명 및 Azure 기본 제공 역할의 고유 ID를 숙지 합니다.](../role-based-access-control/built-in-roles.md)

PowerShell cmd "AzRoleDefinition" 또는 "az role definition list"를 호출 하 여 사용자 환경에서 역할 목록을 검색 합니다.

"가시성" 설정을 통해 서비스의 노출을 제어 하는 옵션을 검토 합니다. 개인 링크에 있습니다. 이러한 표시 유형 설정은 소비자가 서비스에 연결할 수 있는지 여부를 결정 합니다. 

다른 가상 네트워크에서 사용 하도록 서비스를 개인으로 설정 합니다 (Azure RBAC 권한만 해당). 제한 된 신뢰할 수 있는 구독 집합에 대 한 노출을 제한 하거나, 모든 Azure 구독이 개인 링크 서비스에 대 한 연결을 요청할 수 있도록 공개를 설정 합니다.

- [Azure RBAC를 구성 하는 방법](../role-based-access-control/role-assignments-portal.md)

- [개인 링크 서비스에 대 한 속성](private-link-service-overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고

**지침**: Azure Monitor 활동 로그 경고를 사용 하 여 개인 링크 서비스 및 끝점과 같은 중요 한 Azure 리소스에 변경 내용이 발생 하는 경우에 대 한 경고를 만듭니다. 

- [네트워크 보안 그룹에 대한 진단 로깅](private-link-overview.md#logging-and-monitoring)

- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure 보안 벤치 마크: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조 하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화 된 asset discovery 솔루션 사용

**지침**: Azure 리소스 그래프를 사용 하 여 개인 링크 서비스 및 구독 내 끝점과 같은 모든 네트워킹 리소스를 쿼리하고 검색 합니다. 

테넌트에서 적절한 권한(읽기)이 있는지 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거할 수 있습니다.

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

- [Azure 구독을 확인하는 방법](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC 이해](../role-based-access-control/overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타 데이터를 사용 하 여 Azure 리소스에 태그를 적용 하 여 논리적으로 분류로 구성 합니다.

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 태그 지정, 관리 그룹 및 별도의 구독 (해당 하는 경우)을 사용 하 여 개인 링크 및 관련 리소스를 구성 하 고 추적 합니다. 

정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

- [추가 Azure 구독을 만드는 방법](/azure/billing/billing-create-subscription)

- [관리 그룹을 만드는 방법](/azure/governance/management-groups/create)

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: 승인 된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 조직 요구 사항에 따라 계산 리소스에 대해 승인 된 Azure 리소스 및 소프트웨어의 인벤토리를 만듭니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy을 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

또한 Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리/검색 합니다. 이는 저장소 계정을 사용 하는 환경 등의 높은 보안 기반 환경에서 유용할 수 있습니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

- [개인 링크에 대 한 Azure policy 샘플 기본 제공](../governance/policy/samples/built-in-policies.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="67-remove-unapproved-azure-resources"></a>6.7: 승인 되지 않은 Azure 리소스 제거

**지침**: 고객은 고객의 회사 정책에 따라 리소스를 만들거나 사용 하는 데 필요한 Azure Policy를 방지할 수 있습니다. 권한 없는 리소스를 제거 하는 고유한 프로세스를 구현할 수 있습니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy을 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](/azure/governance/policy/samples/not-allowed-resource-types)

- [개인 링크에 대 한 Azure policy 샘플 기본 제공](../governance/policy/samples/built-in-policies.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성함으로써 Azure 조건부 액세스를 사용하여 Azure Resource Manager와 상호 작용하는 사용자 기능을 제한합니다.

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure 보안 벤치 마크: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조 하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Policy 별칭을 사용 하 여 기본 제공 Azure Policy 정의와 함께 Azure 네트워크 리소스의 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다.

Azure Resource Manager에는 JavaScript Object Notation (JSON)에서 템플릿을 내보낼 수 있는 기능이 있습니다. 이 아티팩트를 검토 하 여 구성이 조직의 보안 요구 사항을 충족 하거나 초과 하는지 확인 해야 합니다.

Security Center에서 Azure 리소스에 대 한 보안 구성 기준으로 권장 사항을 구현 합니다.

- [사용 가능한 Azure 정책 별칭을 확인하는 방법](/powershell/module/az.resources/get-azpolicyalias)

- [자습서: 규정 준수를 적용하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)

- [개인 링크에 대 한 Azure policy 샘플 기본 제공](../governance/policy/samples/built-in-policies.md)

- [Azure Portal에서 템플릿에 대 한 단일 및 다중 리소스 내보내기](../azure-resource-manager/templates/export-template-portal.md)

- [보안 권장 사항 - 참조 가이드](../security-center/recommendations-reference.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: Azure Resource Manager 템플릿 및 Azure Policy를 사용 하 여 개인 링크 및 관련 된 리소스와 관련 된 Azure 리소스를 안전 하 게 구성 합니다.  

Azure Resource Manager 템플릿은 Azure 리소스를 배포 하는 데 사용 되는 JavaScript Object Notation (JSON) 기반 파일입니다. 사용자 지정 템플릿은 코드 리포지토리에서 안전 하 게 저장 하 고 유지 관리 해야 합니다. 

[거부] 및 [존재하지 않으면 배포] Azure 정책을 사용하여 보안 설정을 Azure 리소스 전체에 적용합니다.

- [Azure Resource Manager 템플릿 만들기에 대 한 정보](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

- [전용 끝점에 대 한 Azure Resource Manager 템플릿 샘플](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [개인 링크에 대 한 Azure policy 샘플 기본 제공](../governance/policy/samples/built-in-policies.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: Azure devops를 사용 하 여 사용자 지정 azure 정책, Azure Resource Manager 템플릿 및 필요한 상태 구성 스크립트와 같은 코드를 안전 하 게 저장 하 고 관리 합니다. 

액세스를 위해 Azure DevOps와 통합 된 경우 Azure Active Directory (Azure AD)에 정의 된 특정 사용자, 기본 제공 보안 그룹 또는 그룹에 대 한 권한을 부여 하거나 거부 하거나, Team Foundation Server와 통합 된 경우 Active Directory 합니다.

- [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow)

- [Azure DevOps의 사용 권한 및 그룹 정보](/azure/devops/organizations/security/about-permissions)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스에 대 한 구성 관리 도구 배포

**지침**: Azure Policy을 사용 하 여 Azure 리소스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. 

Azure Policy 별칭을 사용 하 여 Azure 리소스의 네트워크 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 

또한 구독에서 관리 되는 특정 리소스와 관련 된 기본 제공 정책 정의를 활용 합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [별칭을 사용 하는 방법](../governance/policy/concepts/definition-structure.md#aliases)

- [개인 링크에 대 한 Azure policy 샘플 기본 제공](../governance/policy/samples/built-in-policies.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대 한 자동화 된 구성 모니터링 구현

**지침**: Azure Policy을 사용 하 여 Azure 리소스 구성을 감사 합니다. 예를 들어 Azure Policy는 전용 끝점으로 구성 되지 않은 리소스를 검색 하는 데 사용할 수 있습니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [개인 링크에 대 한 Azure policy 샘플 기본 제공](../governance/policy/samples/built-in-policies.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure 보안 벤치 마크: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조 하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 정기 자동 백업 확인

**지침**: Azure Resource Manager을 사용 하 여 개인 링크 서비스, 끝점 및 관련 리소스를 배포할 수 있습니다. Azure Resource Manager은 개인 링크 끝점 및 관련 리소스를 복원 하기 위한 백업으로 사용할 수 있는 템플릿을 내보내는 기능을 제공 합니다. 

Azure Automation를 사용 하 여 정기적으로 Azure Resource Manager 템플릿 내보내기 API를 호출 합니다.

- [Azure Resource Manager 개요](../azure-resource-manager/management/overview.md)

- [전용 끝점에 대 한 Azure Resource Manager 템플릿 샘플](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [Azure Portal에서 템플릿에 대 한 단일 및 다중 리소스 내보내기](../azure-resource-manager/templates/export-template-portal.md)

- [리소스 그룹-템플릿 내보내기](/rest/api/resources/resourcegroups/exporttemplate)

- [Azure Automation 소개](../automation/automation-intro.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업을 수행 하 고 고객 관리 키를 백업 합니다.

**지침**: Azure Resource Manager을 사용 하 여 개인 링크 서비스, 끝점 및 관련 리소스를 배포할 수 있습니다. Azure Resource Manager은 개인 링크 끝점 및 관련 리소스를 복원 하기 위한 백업으로 사용할 수 있는 템플릿을 내보내는 기능을 제공 합니다.  

Azure Automation를 사용 하 여 정기적으로 Azure Resource Manager 템플릿 내보내기 API를 호출 합니다.  

Azure Key Vault 내에서 고객이 관리 하는 키를 백업 합니다.

- [Azure Resource Manager 개요](../azure-resource-manager/management/overview.md)

- [전용 끝점에 대 한 Azure Resource Manager 템플릿 샘플](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [Azure Portal에서 템플릿에 대 한 단일 및 다중 리소스 내보내기](../azure-resource-manager/templates/export-template-portal.md)

- [리소스 그룹-템플릿 내보내기](/rest/api/resources/resourcegroups/exporttemplate)

- [Azure Automation 소개](../automation/automation-intro.md)

- [Azure에서 키 자격 증명 모음 키를 백업하는 방법](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 비롯 한 모든 백업 유효성 검사

**지침**: 비즈니스 요구 사항에 따라 정기적으로 Azure Resource Manager 템플릿 배포를 정기적으로 수행할 수 있는 기능을 확인 합니다. 

또한 백업 된 고객 관리 키의 복원에 대 한 유효성을 검사 합니다.

- [ARM 템플릿을 사용 하 여 리소스 배포 및 Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

- [Azure에서 키 자격 증명 모음 키를 복원하는 방법](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객이 관리 하는 키를 보호 해야 합니다.

**지침**: Azure devops를 사용 하 여 Azure Resource Manager 템플릿과 같은 코드를 안전 하 게 저장 하 고 관리 합니다. 

이러한 리소스에 액세스 하기 위해 Azure DevOps와 통합 된 경우에는 Azure Active Directory (Azure AD)에 정의 된 특정 사용자, 기본 제공 보안 그룹 또는 그룹에 대 한 권한을 부여 하거나 거부 하 고, Team Foundation Server와 통합 된 경우에는 Active Directory 합니다.

- [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow)

- [Azure DevOps의 사용 권한 및 그룹 정보](/azure/devops/organizations/security/about-permissions)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [Azure 보안 벤치 마크: 인시던트 응답](../security/benchmarks/security-control-incident-response.md)을 참조 하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 

담당자의 모든 역할 및 인시던트 처리 또는 관리의 단계를 정의 하는 사고 대응 계획을 검색 하 여 인시던트 사후 검토에서 확인 합니다.

- [사용자 고유의 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [또한 고객은 NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용 하 여 고유한 인시던트 대응 계획을 만드는 데 도움이 될 수 있습니다.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center 각 경고에 심각도를 할당 하 여 먼저 조사 해야 하는 경고의 우선 순위를 지정 합니다. 심각도는 경고를 실행 하는 데 사용 되는 찾기 또는 분석에 사용 되는 것과 경고를 발생 시킨 활동의 악의적인 의도를 받은 신뢰 수준에 Security Center 따라 달라 집니다.

태그를 사용 하 여 구독 (예: 프로덕션, 비프로덕션)을 명확 하 게 표시 하 고, 특히 중요 한 데이터를 처리 하는 Azure 리소스를 명확 하 게 식별 하 고 범주화 하는 명명 시스템을 만듭니다.  

고객은 인시던트가 발생 한 Azure 리소스 및 환경의 중요도에 따라 경고 수정의 우선 순위를 지정 해야 합니다.

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md)

- [태그를 사용하여 Azure 리소스 구성](/azure/azure-resource-manager/resource-group-using-tags).

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: Azure 리소스를 보호하는 데 도움이 되도록 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 결함을 식별하고 필요에 따라 계획을 수정합니다.

- [NIST 게시물 - IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 가이드](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 

문제가 해결 되었는지 확인 하려면 발생 후 인시던트를 검토 합니다.

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: Azure 리소스에 대 한 위험을 식별 하는 데 도움이 되도록 연속 내보내기 기능을 사용 하 여 Security Center 경고 및 권장 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. 

또한 비즈니스 운영에 필요한 대로 Security Center 데이터 커넥터를 사용 하 여 경고를 Azure 센티널로 스트리밍합니다.

- [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure 리소스를 보호 하기 위해 Security Center의 Workflow Automation 기능을 사용 하 여 보안 경고 및 권장 사항에 대 한 Logic Apps 응답을 자동으로 트리거합니다.

- [워크플로 자동화 및 Logic Apps를 구성하는 방법](../security-center/workflow-automation.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure 보안 벤치 마크: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조 하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대 한 정기적인 침투 테스트를 수행 하 고 모든 중요 한 보안 결과를 수정 하세요.

**지침**: Microsoft Engagement 규칙에 따라 침투 테스트가 microsoft 정책을 위반 하지 않는지 확인 합니다. 

Microsoft에서 관리 하는 클라우드 인프라, 서비스 및 응용 프로그램에 대 한 레드 팀 및 라이브 사이트 침투 테스트의 전략과 실행을 사용 합니다.

- [Engagement의 침투 테스트 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft 클라우드 Red 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
