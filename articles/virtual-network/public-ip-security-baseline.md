---
title: Azure 공용 IP에 대한 Azure 보안 기준
description: Azure 공용 IP 보안 기준은 Azure Security Benchmark에 지정된 보안 추천 사항을 구현하기 위한 절차적 참고 자료와 리소스를 제공합니다.
author: msmbaldwin
ms.service: virtual-network
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 26a92586d0f87f79436f17914455a82b50a17eab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606109"
---
# <a name="azure-security-baseline-for-azure-public-ip"></a>Azure 공용 IP에 대한 Azure 보안 기준

해당 보안 기준은 [Azure Security Benchmark 버전 1.0](../security/benchmarks/overview-v1.md)의 참고 자료를 Azure 공용 IP에 적용합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 Azure Security Benchmark 및 Azure 공용 IP에 적용되는 관련 참고 자료에서 정의된 **보안 컨트롤** 에 따라 그룹화됩니다. Azure 공용 IP에 해당 사항이 없는 **컨트롤** 은 제외되었습니다.

Azure 공용 IP가 Azure Security Benchmark에 완전히 매핑되는 방법을 확인하려면 [전체 Azure 공용 IP 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조하세요.

## <a name="network-security"></a>네트워크 보안

자세한 내용은 [Azure Security Benchmark: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조하세요.

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

**참고 자료**: Azure 공용 IP에는 태그를 할당할 수 있습니다. 네트워크 보안 그룹 및 네트워크 보안과 관련된 기타 리소스에 리소스 태그를 사용합니다.  태그 지정과 관련된 기본 제공 Azure Policy 정의를 사용합니다. 예를 들어 “태그 및 해당 값 필요”를 사용하면 모든 리소스가 태그를 포함하여 생성되도록 하고 태그가 없는 기존 리소스에 대해 알려줍니다.  

Azure PowerShell 또는 Azure CLI를 사용하여 태그를 기준으로 리소스에 대한 작업을 조회하거나 수행할 수 있습니다. 

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

- [Azure Virtual Network를 만드는 방법](quick-create-portal.md) 

- [네트워크 보안 그룹 규칙을 사용하여 네트워크 트래픽을 필터링하는 방법](tutorial-filter-network-traffic.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure Security Benchmark: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조하세요.*

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**참고 자료**: Azure 활동 로그를 사용하여 구성을 모니터링하고 Azure 공용 IP 인스턴스의 변경 내용을 검색합니다. 컨트롤 플레인(예: Azure Portal)을 제외하면 공용 IP 자체는 네트워크 트래픽과 관련된 로그를 생성하지 않습니다.

공용 IP는 Azure 가상 네트워크의 리소스를 모니터링 및 진단하고, 메트릭을 조회하고, 리소스에 대한 로그를 활성화 또는 비활성화하는 도구를 제공합니다.

대신 Azure Sentinel 또는 타사 SIEM에 데이터를 사용하도록 설정하고 온보딩할 수 있습니다.

- [Azure Monitor를 사용하여 플랫폼 로그 및 메트릭을 수집하는 방법](../azure-monitor/essentials/diagnostic-settings.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**참고 자료**: Azure 활동 로그를 사용하여 구성을 모니터링하고 Azure 공용 IP 인스턴스에 대한 변경 내용을 검색합니다. 컨트롤 플레인(예: Azure Portal)을 제외하면 공용 IP 자체는 감사 로그를 생성하지 않습니다. 공용 IP는 Azure 가상 네트워크의 리소스를 모니터링 및 진단하고, 메트릭을 조회하고, 리소스에 대한 로그를 활성화 또는 비활성화하는 도구를 제공합니다.

- [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**참고 자료**: Azure Monitor를 사용하여 조직의 규정 준수 의무에 따라 공용 IP 인스턴스와 연결된 Log Analytics 작업 영역에 대한 로그 보존 기간을 설정합니다.

- [로그 보존 매개 변수를 설정하는 방법](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**참고 자료**: 공용 IP는 Azure 가상 네트워크의 리소스를 모니터링 및 진단하고, 메트릭을 조회하고, 리소스에 대한 로그를 활성화 또는 비활성화하는 도구를 제공합니다. 

Azure 활동 로그를 사용하여 구성을 모니터링하고 Azure 공용 IP 인스턴스에 대한 변경 내용을 검색합니다. 

컨트롤 플레인(예: Azure Portal)을 제외하면 공용 IP 자체는 네트워크 트래픽과 관련된 로그를 생성하지 않습니다.

- [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상 활동에 대한 경고 사용

**참고 자료**: 공용 IP와 관련된 활동 로그를 기반으로 경고를 구성합니다. Azure Monitor를 사용하여 메일로 알림을 보내거나, 웹후크를 호출하거나, Azure 논리 앱을 호출하도록 경고를 구성합니다.

- [Azure Security Center에서 경고를 관리하는 방법](../security-center/security-center-managing-and-responding-alerts.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [Azure Security Benchmark: ID 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정 인벤토리 유지 관리

**참고 자료**: Azure RBAC(역할 기반 액세스 제어)를 사용하여 역할 할당을 사용하는 공용 IP 인스턴스와 같은 Azure 리소스에 대한 액세스를 관리합니다. 관련 역할을 사용자, 그룹, 서비스 사용자, 관리 ID에 할당합니다.

인벤토리에 포함되었거나 쿼리가 미리 정의된 Azure 기본 제공 역할은 Azure CLI, Azure PowerShell 또는 Azure Portal과 같은 도구를 통해 특정 리소스를 위해 존재합니다.

- [PowerShell을 사용하여 Azure AD(Azure Active Directory)에서 디렉터리 역할을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrole)

- [PowerShell을 사용하여 Azure AD에서 디렉터리 역할의 구성원을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrolemember)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다. 

Azure AD(Azure Active Directory) PIM(Privileged Identity Management) 및 Azure Resource Manager를 사용하여 Just-in-Time 액세스를 사용하도록 설정합니다. 

- [Privileged Identity Management에 대한 자세한 정보](/azure/active-directory/privileged-identity-management)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**참고 자료**: Azure Active Directory(Azure AD) 다단계 인증을 사용하도록 설정하고, Security Center ID 및 액세스 관리 추천 사항을 따릅니다.

- [Azure에서 다단계 인증을 사용하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

**참고 자료**: Azure AD Multi-Factor Authentication을 사용하도록 설정한 PAW(Privileged Access Workstation)를 사용하여 Azure Sentinel 관련 리소스에 로그인하고 구성합니다.

- [권한 있는 액세스 워크스테이션](/security/compass/privileged-access-devices)

- [클라우드 기반 Azure AD Multi-Factor Authentication 배포 계획](../active-directory/authentication/howto-mfa-getstarted.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

**참고 자료**: 환경에서 의심스럽거나 안전하지 않은 활동이 발생하는 경우 로그와 경고를 생성하기 위해 Azure AD(Azure Active Directory) PIM(Privileged Identity Management)을 사용합니다.

위험한 사용자 동작에 대한 경고 및 보고서를 위해 Azure AD 위험 검색을 검토하고 작업합니다.

- [PIM(Privileged Identity Management)을 배포하는 방법](/azure/active-directory/privileged-identity-management/pim-deployment-plan)

- [Azure AD 위험 탐지 이해](../active-directory/identity-protection/overview-identity-protection.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**참고 자료**: 조건부 액세스 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리적 그룹화에서만 Azure Portal에 액세스할 수 있도록 허용합니다.

- [Azure에서 명명된 위치를 구성하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**참고 자료**: Azure AD(Azure Active Directory)를 중앙 인증 및 권한 부여 시스템으로 사용합니다. Azure AD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다.

- [Azure AD 인스턴스를 만들고 구성하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**참고 자료**: Azure AD(Azure Active Directory)에서 로그를 사용하여 부실 계정을 검색합니다. 

Azure ID 액세스 검토를 사용하여 그룹 멤버 자격, 엔터프라이즈 애플리케이션에 대한 액세스, 역할 할당을 효율적으로 관리합니다. 사용자의 액세스를 정기적으로 검토하여 사용자가 계속해서 승인된 액세스를 유지하도록 합니다.

- [Azure AD 보고 이해](/azure/active-directory/reports-monitoring/)

- [Azure ID 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화된 자격 증명에 대한 액세스 시도 모니터링

**참고 자료**: Azure AD(Azure Active Directory) 로그인 활동, 감사, 위험 이벤트 로그 원본에 대한 액세스를 기반으로 하는 모든 SIEM 및 모니터링 도구와의 통합을 구현합니다.
Azure AD 사용자 계정에 대한 진단 설정을 만들고 감사 로그와 로그인 로그를 Log Analytics 작업 영역으로 보내서 해당 프로세스를 간소화합니다. Log Analytics 작업 영역 내에서 원하는 경고를 구성합니다. 

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**참고 자료**: Azure AD(Azure Active Directory) ID 보호 기능을 사용하여 사용자 ID와 관련해 감지된 의심스러운 동작에 대한 자동 응답을 구성합니다. 비즈니스 요구 사항과 필요에 따라 추가 조사를 위해 데이터를 Azure Sentinel로 수집합니다.
- [Azure AD 위험한 로그인을 확인하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure Security Benchmark: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화된 자산 검색 솔루션 사용

**참고 자료**: Azure Resource Graph를 사용하여 구독 내의 모든 리소스(예: 컴퓨팅, 스토리지, 네트워크, 포트, 프로토콜 등)를 쿼리 및 검색합니다. 테넌트에서 적절한 권한(읽기)을 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거합니다.

클래식 Azure 리소스는 Resource Graph를 통해 검색할 수 있지만, 추후에는 Azure Resource Manager 리소스를 만들어 사용하는 것이 좋습니다.

- [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

- [Azure 구독을 확인하는 방법](/powershell/module/az.accounts/get-azsubscription)

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

또한 Azure Policy에서 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.

- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**참고 자료**: Azure Policy를 사용하여 구독에 만들 수 있는 리소스 형식에 대한 제한을 설정합니다.

Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리하고 검색합니다. 환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**참고 자료**: Azure Policy에서 다음과 같은 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 유형에 제한을 둡니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

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

**참고 자료**: Azure Policy를 사용하여 Azure 공용 IP에 대한 표준 보안 구성을 정의 및 구현합니다. “Microsoft.Network” 네임스페이스에서 Azure Policy 별칭을 사용하여 Azure 공용 IP 인스턴스의 네트워크 구성을 감사 및 시행하는 사용자 지정 정책을 만듭니다. 기본 제공 정책 정의를 사용할 수도 있습니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [정책 별칭을 사용하여 사용자 지정 정책을 만드는 방법](../governance/policy/tutorials/create-custom-policy-definition.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: [거부] 및 [존재하지 않으면 배포] Azure 정책을 사용하여 보안 설정을 Azure 리소스 전체에 적용합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**참고 자료**: 사용자 지정 Azure Policy 정의를 사용하는 경우 Azure DevOps 또는 Azure Repos를 사용하여 코드를 안전하게 저장하고 관리할 수 있습니다.

- [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow)

- [Azure Repos 설명서](/azure/devops/repos/)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스를 위한 구성 관리 도구 배포

**참고 자료**: Azure Policy를 사용하여 Azure 공용 IP에 대한 표준 보안 구성을 정의 및 구현합니다. “Microsoft.Network” 네임스페이스에서 Azure Policy 별칭을 사용하여 Azure 공용 IP 인스턴스의 네트워크 구성을 감사 및 시행하는 사용자 지정 정책을 만듭니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [정책 별칭을 사용하여 사용자 지정 정책을 만드는 방법](../governance/policy/tutorials/create-custom-policy-definition.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대한 자동화된 구성 모니터링 구현

**참고 자료**: “Microsoft.Network” 네임스페이스에서 기본 제공 Azure Policy 정의 및 Azure Policy 별칭을 사용하여 시스템 구성을 경고, 감사, 적용하는 사용자 지정 Azure Policy 정의를 만듭니다. Azure Policy의 [감사], [거부], [존재하지 않으면 배포]를 사용하여 Azure 리소스에 대한 구성을 자동으로 적용합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../security/benchmarks/security-control-incident-response.md)을 참조하세요.

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 검색에서 사후 검토에 이르는 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할을 정의하는 인시던트 대응 계획이 있는지 확인합니다.

- [Azure Security Center 내에서 워크플로 자동화를 구성하는 방법](../security-center/security-center-planning-and-operations-guide.md)

- [자체 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [고객은 NIST의 컴퓨터 보안 인시던트 처리 가이드를 사용하여 자체 인시던트 응답 계획을 만들 수도 있습니다.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center에서 심각도를 각 경고에 할당하여 먼저 조사해야 하는 경고에 대한 우선 순위를 지정합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용되는 결과 또는 분석의 신뢰도 및 경고가 발생한 활동에 악의적인 의도가 있었다는 신뢰 수준을 기준으로 합니다.

또한 구독(예: 프로덕션, 비 프로덕션)을 명확하게 표시하고 Azure 리소스를 명확하게 식별하고 분류하는 명명 시스템을 만듭니다.

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md) 

- [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md).

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 격차를 식별하고 필요에 따라 계획을 수정합니다.

- [NIST의 게시물을 참조하세요. IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 안내](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 고객 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**참고 자료**: 연속 내보내기 기능을 사용하여 Security Center 경고 및 추천 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 Azure Sentinel로 경고를 스트림할 수 있습니다.

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

**참고 자료**: Microsoft 클라우드 침투 테스트 참여 규칙을 따라서 침투 테스트가 Microsoft 정책을 위반하지 않도록 하세요. Microsoft의 전략과 Microsoft에서 관리하는 클라우드 인프라, 서비스, 애플리케이션에 대한 레드 팀 실행 및 실시간 사이트 침투 테스트를 사용합니다. 

- [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud 레드 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
