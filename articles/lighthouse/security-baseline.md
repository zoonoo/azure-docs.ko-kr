---
title: Azure Lighthouse에 대한 Azure 보안 기준
description: Azure Lighthouse 보안 기준은 Azure Security Benchmark에 지정된 보안 권장 사항을 구현하기 위한 절차 지침과 리소스를 제공합니다.
author: msmbaldwin
ms.service: lighthouse
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 69234d7c22f1725f6f21fe52a455e64d743f052e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709855"
---
# <a name="azure-security-baseline-for-azure-lighthouse"></a>Azure Lighthouse에 대한 Azure 보안 기준

이 보안 기준은 [Azure Security Benchmark 버전 2.0](../security/benchmarks/overview.md)의 지침을 Azure Lighthouse에 적용합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 Azure Security Benchmark 및 Azure Lighthouse에 적용되는 관련 지침에 정의된 **보안 컨트롤** 에 따라 그룹화됩니다. Azure Lighthouse에 적용할 수 없는 **컨트롤** 은 제외되었습니다.

Azure Lighthouse가 Azure Security Benchmark에 완전히 매핑되는 방법을 확인하려면 [전체 Azure Lighthouse 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조하세요.

## <a name="identity-management"></a>ID 관리

자세한 내용은 [Azure Security Benchmark: ID 관리](../security/benchmarks/security-controls-v2-identity-management.md)를 참조하세요.

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Azure Active Directory를 중앙 ID 및 인증 시스템으로 표준화

**지침**: Azure Lighthouse는 기본 ID 및 액세스 관리 서비스로 Azure AD(Azure Active Directory)를 사용합니다. Azure AD를 표준화하여 다음에서 조직의 ID 및 액세스 관리를 관리합니다.
- Microsoft Cloud 리소스(예: Azure Portal, Azure Storage, Azure Virtual Machine(Linux 및 Windows), Azure Key Vault, PaaS 및 SaaS 애플리케이션)
- 조직의 리소스(예: Azure의 애플리케이션 또는 회사 네트워크 리소스)

Azure Lighthouse를 사용하면 관리 테넌트의 지정된 사용자에게는 고객 테넌트의 위임된 구독 및/또는 리소스 그룹에 액세스할 수 있는 Azure 기본 제공 역할이 있습니다. 현재는 소유자 역할 또는 DataActions 권한이 있는 기본 제공 역할을 제외한 모든 기본 제공 역할이 지원됩니다. 사용자 액세스 관리자 역할은 관리 ID에 역할 할당에서 제한된 용도로만 지원됩니다. 사용자 지정 역할 및 클래식 구독 관리자 역할은 지원되지 않습니다.

- [Azure Active Directory의 테넌시](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Azure AD 인스턴스를 만들고 구성하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [애플리케이션에 외부 ID 공급자 사용](../active-directory/external-identities/identity-providers.md) 

- [Azure Active Directory의 ID 보안 점수란?](../active-directory/fundamentals/identity-secure-score.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: 애플리케이션 ID를 안전하게 자동으로 관리

**지침**: Azure 관리 ID는 Azure AD 인증을 지원하는 Azure 서비스 및 리소스를 인증할 수 있습니다. 소스 코드 또는 구성 파일에 자격 증명을 하드 코딩하는 것을 방지하기 위해 미리 정의된 액세스 권한 부여 규칙을 통해 인증을 사용할 수 있습니다. Azure Lighthouse를 사용하면 고객의 구독에 대한 사용자 액세스 관리자 역할을 가진 사용자가 해당 고객의 테넌트에서 관리 ID를 만들 수 있습니다. 이 역할은 일반적으로 Azure Lighthouse에서 지원되지 않지만 이 특정 시나리오에서 사용할 수 있습니다. 이 권한을 가진 사용자는 관리 ID에 특정 기본 제공 역할을 하나 이상 할당할 수 있습니다.

관리 ID를 지원하지 않는 서비스의 경우 Azure AD를 사용하여 리소스 수준에서 제한된 권한을 가진 서비스 주체를 만듭니다. Azure Lighthouse를 사용하면 서비스 주체가 온보딩 프로세스 중에 부여된 역할에 따라 고객 리소스에 액세스할 수 있습니다. 인증서 자격 증명을 사용하여 서비스 사용자를 구성하고 클라이언트 암호를 대체하는 것이 좋습니다. 두 경우 모두 Azure 관리 ID와 함께 Azure Key Vault를 사용하므로 런타임 환경(예: Azure 함수)이 키 자격 증명 모음에서 자격 증명을 검색할 수 있습니다.

- [Azure 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)

- [Azure 서비스 주체](/powershell/azure/create-azure-service-principal-azureps)

- [보안 주체 등록에 Azure Key Vault 사용](../key-vault/general/authentication.md#authorize-a-security-principal-to-access-key-vault)

- [고객 테넌트에서 관리 ID에 역할을 할당할 수 있는 사용자 만들기](how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: 모든 Azure Active Directory 기반 액세스에 강력한 인증 제어 사용

**지침**: 위임된 고객 리소스에 대한 액세스 권한을 가질 사용자를 포함하여 관리 테넌트의 모든 사용자에 대해 MFA(다단계 인증)이 필요합니다. 또한 고객에게 테넌트에서도 MFA를 구현하도록 요청하는 것이 좋습니다.

- [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: 비정상 계정 활동 모니터링 및 경고

**지침**: Azure AD에서 제공하는 데이터 원본은 다음과 같습니다. 

-   로그인 – 로그인 보고서는 관리되는 애플리케이션 및 사용자 로그인 활동의 사용 정보를 제공합니다.

-   감사 로그 - Azure AD의 다양한 기능에 의해 수행된 모든 변경 내용에 대한 로그를 통해 추적 기능을 제공합니다. 로그된 변경 감사 로그의 예제로는 사용자, 앱, 그룹, 역할 및 정책의 추가 또는 제거가 있습니다.

-   위험한 로그인 - 위험한 로그인은 사용자 계정의 정당한 소유자가 아닌 사용자에 의해 수행된 로그인 시도에 대한 지표입니다.

-   위험 플래그가 지정된 사용자 - 위험한 사용자는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다.

이러한 데이터 원본은 Azure Monitor, Azure Sentinel 또는 타사 SIEM 시스템과 통합할 수 있습니다.

Azure Lighthouse를 사용하는 서비스 공급자는 Azure AD 로그를 Azure Sentinel에 전달하고, 테넌트 간 경고를 보고 설정하여 비정상 계정 활동을 모니터링 및 경고합니다

- [Azure AD의 감사 활동 보고서](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure AD 위험한 로그인을 확인하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [대규모 Azure Sentinel 작업 영역 관리](how-to/manage-sentinel-workspaces.md)

- [Azure AD의 데이터를 Azure Sentinel에 연결](../sentinel/connect-azure-active-directory.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: 조건에 따라 Azure 리소스 액세스 제한

**지침**: Azure Lighthouse는 위임된 고객 리소스에 대한 조건부 액세스 기능을 지원하지 않습니다. 관리 테넌트에서 MFA(다단계 인증) 사용에 특정 IP 범위에서의 사용자 로그인을 요구하는 등 보다 세분화된 사용자 정의 조건 기반 액세스 제어를 위해서는 Azure AD 조건부 액세스를 사용합니다. 다양한 사용 사례에 대한 Azure AD 조건부 액세스 정책을 통해 세부적인 인증 세션 관리를 사용할 수도 있습니다. 

위임된 고객 리소스에 대한 액세스 권한이 있는 사용자를 포함하여 관리 테넌트의 모든 사용자에 대해 MFA(다단계 인증)를 요구해야 합니다. 또한 고객에게 테넌트에서도 MFA를 구현하도록 요청하는 것이 좋습니다.

- [Azure 조건부 액세스 개요](../active-directory/conditional-access/overview.md)

- [일반 조건부 액세스 정책](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [조건부 액세스를 사용하여 인증 세션 관리를 구성합니다.](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="privileged-access"></a>권한 있는 액세스

자세한 내용은 [Azure Security Benchmark: 권한 있는 액세스](../security/benchmarks/security-controls-v2-privileged-access.md)를 참조하세요.

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: 높은 권한이 있는 사용자 보호 및 제한

**지침**: 높은 권한의 사용자 계정 수를 제한하고 승격된 수준에서 이러한 계정을 보호합니다. Azure Lighthouse를 사용하도록 설정하고 사용하는 데는 전역 관리자 계정이 필요하지 않습니다.

테넌트 수준 활동 로그 데이터에 액세스하려면 루트 범위(/)의 모니터링 리더 Azure 기본 제공 역할을 계정에 할당해야 합니다. 루트 범위의 Monitoring Reader 역할은 광범위한 액세스이기 때문에 개별 사용자 또는 그룹이 아닌 서비스 주체 계정에 이 역할을 할당하는 것이 좋습니다. 이 할당은 승격된 추가 액세스 권한이 있는 전역 관리자 역할을 가진 사용자가 수행해야 합니다. 이 승격된 액세스는 역할 할당을 수행하기 전에 즉시 추가해야 하며, 할당이 완료되면 제거됩니다.

- [Azure AD의 관리자 역할 권한](../active-directory/roles/permissions-reference.md)

- [테넌트 수준 활동 로그 데이터를 모니터링하기 위한 액세스 권한 할당](how-to/monitor-delegation-changes.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: 중요 비즈니스용 시스템에 대한 관리 액세스 제한

**지침**: Azure Lighthouse는 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 구독 및 관리 그룹에 대한 액세스 권한이 부여되는 계정을 제한함으로써 중요 비즈니스용 시스템에 대한 액세스를 격리합니다.

사용자가 특정 작업을 수행하는 데 필요한 권한만 갖도록 최소 권한 원칙을 따라야 합니다.

또한 중요 비즈니스용 시스템에 설치된 에이전트를 사용하여 Active Directory DC(도메인 컨트롤러), 보안 도구 및 시스템 관리 도구 등의 중요 비즈니스용 액세스에 대한 관리 권한이 있는 관리, ID 및 보안 시스템에 대한 액세스를 제한해야 합니다. 이러한 관리 및 보안 시스템을 손상시키는 공격자는 중요 비즈니스용 자산을 손상시키기 위해 시스템을 즉시 무기화할 수 있습니다.

모든 유형의 액세스 제어는 일관된 액세스 제어를 보장하기 위해 엔터프라이즈 조각화 전략에 맞춰야 합니다.

- [Azure 구성 요소 및 참조 모델](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [관리 그룹 액세스](../governance/management-groups/overview.md#management-group-access)

- [Azure 구독 관리자](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Azure Lighthouse에 대한 사용자 및 역할을 정의하기 위한 모범 사례](concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure Lighthouse는 Azure AD(Azure Active Directory) 계정을 사용하여 리소스를 관리하고, 사용자 계정 및 액세스 할당을 정기적으로 검토하여 계정 및 해당 액세스가 유효한지 확인합니다. Azure AD 액세스 검토를 사용하여 그룹 멤버 자격, 엔터프라이즈 애플리케이션에 대한 액세스 및 역할 할당을 검토할 수 있습니다. Azure AD 보고는 부실 계정을 발견하는 데 유용한 로그를 제공합니다. 또한 Azure AD Privileged Identity Management를 사용하여 검토 프로세스를 지원하는 액세스 검토 보고서 워크플로를 만들 수 있습니다.

고객은 Azure Portal에서 Azure Lighthouse를 통해 관리 테넌트의 사용자에게 부여된 액세스 수준을 검토할 수 있습니다. 언제든지 이 액세스 권한을 제거할 수 있습니다.

또한 Azure Privileged Identity Management는 과도한 수의 관리자 계정이 생성되었을 때 경고하고, 부실하거나 부적절하게 구성된 관리자 계정을 식별하도록 구성할 수 있습니다.

참고: 일부 Azure 서비스는 Azure AD를 통해 관리되지 않는 로컬 사용자 및 역할을 지원합니다. 이러한 사용자는 별도로 관리해야 합니다.

- [PIM(Privileged Identity Management)에서 Azure 리소스 역할에 대한 액세스 검토 만들기](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [위임에 대한 액세스 권한 제거](how-to/remove-delegation.md)

- [Azure AD ID 및 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

- [Azure Portal에서 서비스 공급자 페이지 보기](how-to/view-manage-service-providers.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Azure AD에서 응급 액세스 설정

**지침**: Azure Lighthouse는 Azure Active Directory와 통합되어 해당 리소스를 관리합니다. Azure AD 조직에서 실수로 차단되는 것을 방지하려면 일반 관리 계정을 사용할 수 없는 경우 액세스할 수 있도록 응급 액세스 계정을 설정합니다. 응급 액세스 계정은 일반적으로 권한이 높으며 특정 사용자에게 할당되면 안 됩니다. 응급 액세스 계정은 일반 관리 계정을 사용할 수 없는 '비상' 시나리오의 긴급한 상황으로 제한됩니다.

응급 액세스 계정의 자격 증명(예: 암호, 인증서 또는 스마트 카드)을 안전하게 유지하고 비상시에만 사용할 권한이 있는 사용자에게만 알립니다.

- [Azure AD에서 응급 액세스 계정 관리](../active-directory/roles/security-emergency-access.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="pa-5-automate-entitlement-management"></a>PA-5: 권한 관리 자동화 

**지침**: Azure Lighthouse는 Azure AD(Azure Active Directory)와 통합되어 해당 리소스를 관리합니다. Azure AD 권한 관리 기능을 사용하여 액세스 할당, 검토, 만료 등의 액세스 요청 워크플로를 자동화할 수 있습니다. 이중 또는 다단계 승인도 지원됩니다.

- [Azure AD 액세스 검토란?](../active-directory/governance/access-reviews-overview.md) 

- [Azure AD 권한 관리란?](../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: 권한 있는 액세스 워크스테이션 사용

**지침**: 안전하고 격리된 워크스테이션은 관리자, 개발자 및 중요 서비스 운영자와 같은 중요한 역할의 보안에 매우 중요합니다. 요구 사항에 따라 프로덕션 환경에서 Azure Lighthouse를 사용하여 관리 작업을 수행하는 데 매우 안전한 사용자 워크스테이션 및/또는 Azure Bastion을 사용할 수 있습니다. Azure Active Directory, Microsoft Defender ATP(Advanced Threat Protection) 및/또는 Microsoft Intune을 사용하여 관리 작업을 위한 관리형 보안 사용자 워크스테이션을 배포할 수 있습니다. 보안 워크스테이션을 중앙에서 관리하여 강력한 인증, 소프트웨어 및 하드웨어 기준 설정, 제한된 논리 및 네트워크 액세스를 비롯한 보안 구성을 적용할 수 있습니다. 

- [권한 있는 액세스 워크스테이션 이해](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [권한 있는 액세스 워크스테이션 배포](/security/compass/privileged-access-deployment)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: 충분한 관리 수행(최소 권한 원칙) 

**지침**: Azure Lighthouse는 Azure RBAC(역할 기반 액세스 제어)와 통합되어 해당 리소스를 관리합니다. Azure RBAC를 사용하면 역할 할당을 통해 Azure 리소스 액세스를 관리할 수 있습니다. 이러한 기본 제공 역할을 사용자, 그룹, 서비스 주체 및 관리 ID에 할당할 수 있습니다. 특정 리소스에 대해 미리 정의된 기본 제공 역할이 있으며 이러한 역할은 Azure CLI, Azure PowerShell 또는 Azure Portal과 같은 도구를 통해 쿼리하거나 인벤토리에 포함할 수 있습니다. Azure RBAC를 통해 리소스에 할당하는 권한은 항상 역할에 필요한 권한으로 제한해야 합니다. 이는 Azure AD PIM(Privileged Identity Management)의 JIT(Just-in-Time) 접근 방식을 보완하며 정기적으로 검토해야 합니다. 기본 제공 역할을 사용하여 권한을 할당하고, 필요한 경우에만 사용자 지정 역할을 만듭니다.

Azure Lighthouse를 사용하면 Azure 기본 제공 역할을 사용하여 위임된 고객 리소스에 액세스할 수 있습니다. 대부분의 경우 많은 개별 사용자 계정이 아닌 그룹 또는 서비스 주체에 이러한 역할을 할당하는 것이 좋습니다. 이렇게 하면 액세스 요구 사항이 변경될 때 플랜을 업데이트한 후 다시 게시하지 않고도 개별 사용자에 대한 액세스 권한을 추가하거나 제거할 수 있습니다.

고객 리소스를 관리 테넌트에 위임하려면 온보딩하려는 구독에 대해 소유자 기본 제공 역할이 있는(또는 온보딩하려는 리소스 그룹을 포함하는) 고객 테넌트의 비게스트 계정에서 배포를 수행해야 합니다.

- [Azure Lighthouse의 테넌트, 사용자 및 역할 이해](concepts/tenants-users-roles.md)

- [Azure Lighthouse에 최소 권한 원칙을 적용하는 방법](concepts/recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege)

- [Azure RBAC(Azure 역할 기반 액세스 제어)란?](../role-based-access-control/overview.md) 

- [Azure AD ID 및 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="asset-management"></a>자산 관리

자세한 내용은 [Azure Security Benchmark: 자산 관리](../security/benchmarks/security-controls-v2-asset-management.md)를 참조하세요.

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: 보안 팀에서 자산 위험에 대한 가시성을 확보하도록 보장

**지침**: Azure Security Center를 사용하여 보안 위험을 모니터링할 수 있도록 Azure 테넌트 및 구독에서 보안 읽기 권한자 권한을 보안 팀에 부여합니다. 

보안 팀의 책임이 구성된 방식에 따라 보안 위험 모니터링은 중앙 보안 팀 또는 로컬 팀의 책임이 될 수 있습니다. 즉, 보안 인사이트 및 위험이 항상 조직 내의 중앙에서 집계되어야 합니다. 

보안 읽기 권한자 권한은 전체 테넌트(루트 관리 그룹)에 광범위하게 적용하거나 범위를 관리 그룹 또는 특정 구독으로 지정할 수 있습니다. 

참고: 워크로드 및 서비스에 대한 가시성을 얻으려면 추가 권한이 필요할 수 있습니다. 

- [보안 읽기 권한자 역할 개요](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 관리 그룹 개요](../governance/management-groups/overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: 보안 팀에 자산 인벤토리 및 메타데이터에 대한 액세스 권한이 있는지 확인

**지침**: 고객의 보안 팀은 활동 로그를 검토하여 Azure Lighthouse를 사용하는 서비스 공급자가 수행한 활동을 확인할 수 있습니다. 

서비스 공급자가 보안 팀에서 위임된 고객 리소스를 검토할 수 있게 하려는 경우 보안 팀의 권한 부여에는 Reader 기본 제공 역할이 포함되어야 합니다.

- [고객이 서비스 공급자 작업을 검토하는 방법](how-to/view-service-provider-activity.md)

- [Azure Lighthouse에 고객을 온보딩할 때 역할을 지정하는 방법](how-to/onboard-customer.md#define-roles-and-permissions)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: 승인된 Azure 서비스만 사용

**지침**: Azure Policy를 사용하여 환경에서 사용자가 프로비전할 수 있는 서비스를 감사하고 제한합니다. Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리하고 검색합니다. 또한 Azure Monitor를 사용하여 승인되지 않은 서비스가 검색되면 경고를 트리거하는 규칙을 만들 수 있습니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](../governance/policy/samples/built-in-policies.md#general) 

- [Azure Resource Graph Explorer를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: 자산 수명 주기 관리의 보안 보장

**지침**: Azure Lighthouse를 통해 위임된 리소스가 더 이상 필요 없으면 해당 액세스 권한을 제거하여 서비스 공급자가 더 이상 액세스할 수 없도록 합니다. 고객 또는 서비스 공급자가 액세스 권한을 제거할 수 있습니다. 

- [위임에 대한 액세스 권한 제거](how-to/remove-delegation.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: Azure Resource Manager와 상호 작용하는 사용자 기능 제한

**지침**: Azure Lighthouse는 ID 및 인증을 위해 Azure AD(Azure Active Directory)와 통합됩니다. Azure Conditional Access를 사용하여 "Microsoft Azure Management" 앱에 대한 "액세스 차단"을 구성하면 Azure Resource Manager와 상호 작용하는 사용자 기능을 제한할 수 있습니다.

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="logging-and-threat-detection"></a>로깅 및 위협 탐지

자세한 내용은 [Azure Security Benchmark: 로깅 및 위협 탐지](../security/benchmarks/security-controls-v2-logging-threat-detection.md)를 참조하세요.

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Azure 리소스에 대한 위협 탐지 사용

**지침**: Azure Lighthouse를 통해 고객의 Azure 리소스를 모니터링하면 잠재적인 위협과 변칙을 확인할 수 있습니다. 분석가가 면밀히 살펴볼 수 있도록 가양성을 줄여 고품질 경고를 얻는 데 중점을 두세요. 경고는 로그 데이터, 에이전트 또는 기타 데이터를 원본으로 사용할 수 있습니다.

Azure 서비스 원격 분석 모니터링 및 서비스 로그 분석을 기반으로 하는 Azure Security Center 기본 제공 위협 탐지 기능을 사용합니다. 데이터는 시스템에서 다양한 보안 관련 구성 및 이벤트 로그를 읽고 분석용으로 작업 영역에 데이터를 복사하는 Log Analytics 에이전트를 사용하여 수집됩니다. 

또한 Azure Sentinel을 사용하여 고객 환경에서 특정 조건과 일치하는 위협을 탐지하는 분석 규칙을 작성합니다. 규칙은 조건이 일치할 때 인시던트를 생성하므로 각 인시던트를 조사할 수 있습니다. 또한 Azure Sentinel은 타사 위협 인텔리전스를 가져와서 위협 탐지 기능을 향상시킬 수 있습니다. 

- [위협 방지 및 Azure Security Center](../security-center/azure-defender.md)

- [Azure Security Center 보안 경고 참조 가이드](../security-center/alerts-reference.md)

- [위협 탐지를 위한 사용자 지정 분석 규칙 만들기](../sentinel/tutorial-detect-threats-custom.md)

- [대규모 Azure Sentinel 작업 영역 관리](how-to/manage-sentinel-workspaces.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Azure ID 및 액세스 관리에 위협 탐지 사용

**지침**: Azure Lighthouse를 통해 Azure Security Center를 사용하면 사용자가 관리하는 고객 테넌트의 의심스러운 특정 활동(예: 실패한 인증 시도 횟수 초과, 구독에서 더 이상 사용되지 않는 계정)에 대해 경고할 수 있습니다.

Azure AD(Azure Active Directory)는 Azure AD 보고에서 볼 수 있거나 더 정교한 모니터링 및 분석 사용 사례를 위해 Azure Monitor, Azure Sentinel 또는 기타 SIEM/모니터링 도구와 통합할 수 있는 다음과 같은 사용자 로그를 제공합니다.
- 로그인 – 로그인 보고서는 관리되는 애플리케이션 및 사용자 로그인 활동의 사용에 대한 정보를 제공합니다.
- 감사 로그 - Azure AD 내의 다양한 기능에 의해 수행된 모든 변경 내용에 대한 로그를 통한 추적 기능을 제공합니다. 감사 로그의 예제로는 사용자, 앱, 그룹, 역할 및 정책 추가 또는 제거와 같은 Azure AD 내의 모든 리소스에 대한 변경 내용이 있습니다.
- 위험한 로그인 - 위험한 로그인은 사용자 계정의 정당한 소유자가 아닌 사용자에 의해 수행된 로그인 시도에 대한 지표입니다.
- 위험 플래그가 지정된 사용자 - 위험한 사용자는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다.

또한 Azure Security Center는 과도한 인증 시도 실패 횟수, 구독에서 사용되지 않는 계정과 같은 의심스러운 특정 활동에 대해 경고할 수 있습니다. 기본 보안 예방 조치 모니터링 외에도 Azure Security Center의 위협 방지 모듈은 개별 Azure 컴퓨팅 리소스(가상 머신, 컨테이너, 앱 서비스), 데이터 리소스(SQL DB 및 스토리지) 및 Azure 서비스 계층에서 더 심층적인 보안 경고를 수집할 수 있습니다. 이 기능을 사용하면 개별 리소스 내에서 비정상 계정 활동을 볼 수 있습니다. 

- [Azure Lighthouse를 사용하는 향상된 서비스 및 시나리오](concepts/cross-tenant-management-experience.md#enhanced-services-and-scenarios)

- [Azure Active Directory의 감사 활동 보고서](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure ID 보호 사용](../active-directory/identity-protection/overview-identity-protection.md) 

- [위협 방지 및 Azure Security Center](../security-center/azure-defender.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Azure 리소스에 대한 로깅 사용

**지침**: 자동으로 사용할 수 있는 활동 로그에는 Azure Lighthouse 리소스에 대한 모든 쓰기 작업(PUT, POST, DELETE)이 포함되며 읽기 작업(GET)은 제외됩니다. 활동 로그를 사용하면 문제를 해결할 때 오류를 찾거나 조직의 사용자가 리소스를 수정한 방법을 모니터링할 수 있습니다.

Azure Lighthouse를 사용하면 사용자가 관리하는 고객 테넌트에서 확장 가능한 방식으로 Azure Monitor 로그를 사용할 수 있습니다. 고객 데이터를 사용자의 테넌트로 내보내지 않고 고객의 테넌트에 유지되도록 고객 테넌트에 직접 Log Analytics 작업 영역을 만듭니다. 또한 Log Analytics에서 지원하는 모든 리소스 또는 서비스를 중앙에서 모니터링할 수 있으므로 모니터링하는 데이터 형식에 대한 유연성을 높일 수 있습니다.

Azure Lighthouse에 대한 구독을 위임 받은 고객은 Azure 활동 로그 데이터를 보면 수행된 모든 작업을 확인할 수 있습니다. 이렇게 하면 고객은 고객 소유의 Azure AD(Azure Active Directory) 테넌트 내에서 사용자가 수행하는 작업과 함께, 서비스 공급자가 수행하는 작업을 완벽하게 파악할 수 있습니다.

- [Azure Monitor를 사용하여 플랫폼 로그 및 메트릭을 수집하는 방법](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure의 로깅 및 기타 로그 형식 이해](../azure-monitor/essentials/platform-logs-overview.md)

- [위임된 대규모 리소스 모니터링](how-to/monitor-at-scale.md)

- [서비스 공급자 작업 보기](how-to/view-service-provider-activity.md)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: 보안 로그 관리 및 분석 중앙 집중화

**지침**: 상관 관계를 사용하도록 설정하기 위해 로깅 스토리지 및 분석을 중앙 집중화합니다. 각 로그 원본에 대해 데이터 소유자, 액세스 지침, 스토리지 위치, 데이터를 처리하고 액세스하는 데 사용되는 도구, 데이터 보존 요구 사항을 할당했는지 확인합니다.

Azure 활동 로그를 중앙 로깅에 통합해야 합니다. Azure Monitor를 통해 로그를 수집하여 엔드포인트 디바이스, 네트워크 리소스 및 기타 보안 시스템에 의해 생성된 보안 데이터를 집계합니다. Azure Monitor에서 Log Analytics 작업 영역을 사용하여 분석을 쿼리 및 수행하고 장기 보관 스토리지에 Azure Storage 계정을 사용합니다.

또한 데이터를 Azure Sentinel 또는 타사 SIEM에서 사용하도록 설정하고 온보딩할 수 있습니다.

Azure Lighthouse를 사용하면 사용자가 관리하는 고객 테넌트에서 확장 가능한 방식으로 Azure Monitor 로그를 사용할 수 있습니다. 고객 데이터를 사용자의 테넌트로 내보내지 않고 고객의 테넌트에 유지되도록 고객 테넌트에 직접 Log Analytics 작업 영역을 만듭니다. 또한 Log Analytics에서 지원하는 모든 리소스 또는 서비스를 중앙에서 모니터링할 수 있으므로 모니터링하는 데이터 형식에 대한 유연성을 높일 수 있습니다.

Azure Lighthouse에 대한 구독을 위임 받은 고객은 Azure 활동 로그 데이터를 보면 수행된 모든 작업을 확인할 수 있습니다. 이렇게 하면 고객은 고객 소유의 Azure AD(Azure Active Directory) 테넌트 내에서 사용자가 수행하는 작업과 함께, 서비스 공급자가 수행하는 작업을 완벽하게 파악할 수 있습니다.

많은 조직에서 자주 사용되는 "핫" 데이터에 Azure Sentinel을 사용하고, 낮은 빈도로 사용되는 "콜드" 데이터에는 Azure Storage를 사용합니다.

- [Azure Monitor를 사용하여 플랫폼 로그 및 메트릭을 수집하는 방법](../azure-monitor/essentials/diagnostic-settings.md)

- [위임된 대규모 리소스 모니터링](how-to/monitor-at-scale.md)

- [고객이 서비스 공급자 작업을 보는 방법](how-to/view-service-provider-activity.md)

- [대규모 Azure Sentinel 작업 영역 관리](how-to/manage-sentinel-workspaces.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: 로그 스토리지 보존 구성

**지침**: 현재 Azure Lighthouse는 보안 관련 로그를 생성하지 않습니다. 서비스 공급자 작업을 보려는 고객은 규정 준수, 규제 및 비즈니스 요구 사항에 따라 로그 보존을 구성할 수 있습니다. 

Azure Monitor에서 조직의 준수 규정에 따라 Log Analytics 작업 영역의 보존 기간을 설정할 수 있습니다. 장기 보관 스토리지에는 Azure Storage, Data Lake 또는 Log Analytics 작업 영역 계정을 사용합니다.

- [Log Analytics에서 데이터 보존 기간 변경](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Azure Storage 계정 로그에 대한 보존 정책을 구성하는 방법](../storage/common/manage-storage-analytics-logs.md#configure-logging)

- [Azure Security Center 경고 및 권장 사항 내보내기](../security-center/continuous-export.md) 구성. 고객이 로그 보존을 설정할 수 없습니다.

- [고객이 서비스 공급자의 활동 로그 데이터를 검토하는 방법](how-to/view-service-provider-activity.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../security/benchmarks/security-controls-v2-incident-response.md)을 참조하세요.

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: 준비 - Azure에 대한 인시던트 응답 프로세스 업데이트

**지침**: 조직이 보안 인시던트에 대응하는 프로세스를 보유하고, 해당 프로세스를 Azure에 대해 업데이트했으며, 준비 상태를 확인하기 위해 프로세스를 정기적으로 수행하는지 확인합니다.

- [엔터프라이즈 환경에서 보안 구현](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [인시던트 응답 참조 가이드](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: 준비 - 인시던트 알림 설정

**지침**: Azure Security Center에서 보안 인시던트 연락처 정보를 설정합니다. 이 연락처 정보는 MSRC(Microsoft 보안 대응 센터)가 불법적인 당사자나 권한 없는 당사자가 데이터에 액세스한 것을 발견한 경우 Microsoft가 연락하는 데 사용됩니다. 또한 인시던트 응답 요구 사항에 따라 다양한 Azure 서비스에서 인시던트 경고 및 알림을 사용자 지정하는 옵션도 있습니다. 

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: 탐지 및 분석 – 고품질 경고를 기반으로 인시던트 만들기

**지침**: 고품질 경고를 만들고 경고의 품질을 측정하는 프로세스가 있는지 확인합니다. 이렇게 하면 이전 인시던트에서 상황을 확인하고 분석가를 위한 경고의 우선 순위를 지정할 수 있으므로 가양성으로 인해 시간을 낭비하지 않습니다. 

고품질 경고는 이전 인시던트, 유효성이 검사된 커뮤니티 소스, 다양한 신호 원본을 결합하고 상관 관계를 설정하여 경고를 생성하고 정리하도록 설계된 도구 등에서 얻은 경험을 기반으로 구축할 수 있습니다. 

Azure Security Center는 많은 Azure 자산에 대해 고품질 경고를 제공합니다. ASC 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다. Azure Sentinel을 사용하면 조사를 위해 인시던트를 자동으로 생성하는 고급 경고 규칙을 만들 수 있습니다. 

Azure 리소스에 대한 위험을 식별하는 데 도움이 되도록 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 경고 및 추천 사항을 수동 또는 지속적인 방식으로 내보냅니다.

- [내보내기를 구성하는 방법](../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: 검색 및 분석 – 인시던트 조사

**지침**: 분석가가 잠재적 인시던트를 조사할 때 다양한 데이터 원본을 쿼리하고 사용하여 발생한 상황에 대한 전체 보기를 작성할 수 있는지 확인합니다. 사각지대를 방지하기 위해 다양한 로그를 수집하여 킬 체인 전체에서 잠재적 공격자의 활동을 추적해야 합니다.  또한 다른 분석가 및 향후 기록 참조를 위해 인사이트 및 습득 지식을 캡처해야 합니다.  

조사할 데이터 원본에는 범위 내 서비스 및 실행 중인 시스템에서 이미 수집되고 있는 중앙 집중식 로깅 원본이 포함되지만 다음과 같은 원본도 포함될 수 있습니다.

- 네트워크 데이터 – 네트워크 보안 그룹의 흐름 로그, Azure Network Watcher 및 Azure Monitor를 사용하여 네트워크 흐름 로그 및 기타 분석 정보를 캡처합니다. 

- 실행 중인 시스템의 스냅샷 

    - Azure 가상 머신의 스냅샷 기능을 사용하여 실행 중인 시스템의 디스크에 대한 스냅샷을 만듭니다. 

    - 운영 체제의 기본 메모리 덤프 기능을 사용하여 실행 중인 시스템의 메모리에 대한 스냅샷을 만듭니다.

    - Azure 서비스의 스냅샷 기능 또는 소프트웨어의 자체 기능을 사용하여 실행 중인 시스템의 스냅샷을 만듭니다.

Azure Sentinel은 거의 모든 로그 원본 및 사례 관리 포털에서 광범위한 데이터 분석을 제공하여 인시던트의 전체 수명 주기를 관리합니다. 조사 중에 인텔리전스 정보는 추적 및 보고를 위해 인시던트에 연결할 수 있습니다. 

- [Windows 컴퓨터의 디스크 스냅샷 만들기](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux 컴퓨터의 디스크 스냅샷 만들기](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure 지원 진단 정보 및 메모리 덤프 수집](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Azure Sentinel을 사용하여 인시던트 조사](../sentinel/tutorial-investigate-cases.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: 탐지 및 분석 – 인시던트 우선 순위 지정

**지침**: 경고 심각도 및 자산 민감도에 따라 먼저 집중해야 하는 인시던트에 대한 컨텍스트를 분석가에게 제공합니다. 

Azure Security Center는 먼저 조사해야 하는 경고의 우선 순위를 지정하는 데 도움이 되도록 심각도를 각 경고에 할당합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용되는 결과 또는 분석의 신뢰도 및 경고가 발생한 활동에 악의적인 의도가 있었다는 신뢰 수준을 기준으로 합니다.

또한 태그를 사용하여 리소스를 표시하고, Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 식별하고 분류할 수 있는 명명 시스템을 만듭니다.  인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다.

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md)

- [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md).

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: 차단, 제거, 복구 - 인시던트 처리 자동화

**지침**: 수동 반복 작업을 자동화하여 응답 시간을 단축하고 분석가의 부담을 줄입니다. 수동 작업은 실행하는 데 더 오래 걸려 각 인시던트의 속도를 늦추고 분석가가 처리할 수 있는 인시던트 수를 줄입니다. 또한 수동 작업은 분석가를 지치게 하여 지연을 유발하는 인간 오류의 위험을 높이고, 복잡한 작업에 효과적으로 집중할 수 있는 분석가의 능력을 저하시킵니다. Azure Security Center 및 Azure Sentinel의 워크플로 자동화 기능을 사용하여 작업을 자동으로 트리거하거나 플레이북을 실행하여 들어오는 보안 경고에 대응합니다. 플레이북은 알림 보내기, 계정 사용 안 함 및 문제가 있는 네트워크 격리와 같은 작업을 수행합니다. 

- [Security Center에서 워크플로 자동화 구성](../security-center/workflow-automation.md)

- [Azure Security Center에서 자동화된 위협 대응 설정](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Azure Sentinel에서 자동화된 위협 응답 설정](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="posture-and-vulnerability-management"></a>태세 및 취약성 관리

자세한 내용은 [Azure Security Benchmark: 태세 및 취약성 관리](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)를 참조하세요.

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Azure 서비스에 대한 보안 구성 설정 

**지침**: Azure Lighthouse는 Azure 리소스의 구성을 감사 및 적용하기 위해 Azure Security Center에서 사용할 수 있는 아래의 서비스별 정책을 지원합니다. Azure Security Center 또는 Azure Policy 이니셔티브에서 이를 구성할 수 있습니다.

- 관리 테넌트 ID가 Azure Lighthouse를 통해 온보딩하도록 허용

- 관리 테넌트에 대한 범위 위임 감사

Azure Blueprints를 사용하여 단일 청사진 정의에서 Azure Resources Manager 템플릿, Azure RBAC 컨트롤 및 정책을 포함한 서비스 및 애플리케이션 환경의 배포 및 구성을 자동화할 수 있습니다.

- [Azure Lighthouse 정책](samples/policy-reference.md)

- [자습서 - 규정 준수를 적용하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Blueprints](../governance/blueprints/overview.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Azure 서비스에 대한 보안 구성 유지

**지침**: Azure Lighthouse는 Azure 리소스의 구성을 감사 및 적용하기 위해 Azure Security Center에서 사용할 수 있는 아래의 서비스별 정책을 지원합니다. Azure Security Center 또는 Azure Policy 이니셔티브에서 이를 구성할 수 있습니다.

- [Azure Lighthouse 정책](samples/policy-reference.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: 컴퓨팅 리소스에 대한 보안 구성 설정

**지침**: Azure Security Center 및 Azure Policy를 사용하여 VM, 컨테이너 등을 비롯한 모든 컴퓨팅 리소스에 대한 보안 구성을 설정합니다.

- [Azure Security Center 권장 사항을 모니터링하는 방법](../security-center/security-center-recommendations.md) 

- [보안 권장 사항 - 참조 가이드](../security-center/recommendations-reference.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: 정기적인 공격 시뮬레이션 수행

**지침**: 필요에 따라 Azure 리소스에 대한 침투 테스트 또는 레드 팀 활동을 수행하고 모든 중요한 보안 결과를 수정해야 합니다.
Microsoft Cloud 침투 테스트 시행 규칙에 따라 침투 테스트가 Microsoft 정책을 위반하지 않는지 확인합니다. Microsoft의 전략과 Microsoft에서 관리하는 클라우드 인프라, 서비스, 애플리케이션에 대한 레드 팀 실행 및 실시간 사이트 침투 테스트를 사용합니다.

- [Azure의 침투 테스트](../security/fundamentals/pen-testing.md)

- [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud 레드 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="governance-and-strategy"></a>거버넌스 및 전략

자세한 내용은 [Azure Security Benchmark: 거버넌스 및 전략](../security/benchmarks/security-controls-v2-governance-strategy.md)을 참조하세요.

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: 자산 관리 및 데이터 보호 전략 정의 

**지침**: 시스템 및 데이터를 지속적으로 모니터링하고 보호하기 위한 명확한 전략을 문서화하고 전달해야 합니다. 중요 비즈니스용 데이터 및 시스템의 검색, 평가, 보호, 모니터링에 우선 순위를 지정합니다. 

이 전략에는 다음 요소에 대한 문서화된 지침, 정책 및 표준이 포함되어야 합니다. 

-   비즈니스 위험에 따른 데이터 분류 표준

-   위험 및 자산 인벤토리에 대한 보안 조직의 가시성 

-   사용할 Azure 서비스에 대한 보안 조직의 승인 

-   수명 주기 전체에서 자산 보안

-   조직 데이터 분류에 따라 필요한 액세스 제어 전략

-   Azure 기본 및 타사 데이터 보호 기능 사용

-   전송 중 및 미사용 사용 사례에 대한 데이터 암호화 요구 사항

-   적절한 암호화 표준

자세한 내용은 다음 참조 문서를 참조하세요.
- [Azure 보안 아키텍처 권장 사항 - 스토리지, 데이터, 암호화](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Azure 보안 기본 사항 - Azure 데이터 보안, 암호화, 스토리지](../security/fundamentals/encryption-overview.md)

- [클라우드 채택 프레임워크 - Azure 데이터 보안 및 암호화 모범 사례](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark - 자산 관리](../security/benchmarks/security-controls-v2-incident-response.md)

- [Azure Security Benchmark - 데이터 보호](../security/benchmarks/security-controls-v2-data-protection.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: 엔터프라이즈 구분 전략 정의 

**지침**: ID, 네트워크, 애플리케이션, 구독, 관리 그룹 및 기타 컨트롤의 조합을 사용하여 자산에 대한 액세스를 구분하는 엔터프라이즈 전체 전략을 수립합니다.

서로 통신하고 데이터에 액세스해야 하는 시스템의 일상 작업을 사용하도록 설정해야 할 필요성과 보안 분리의 필요성을 신중하게 조정해야 합니다.

구분 전략이 네트워크 보안, ID 및 액세스 모델, 애플리케이션 권한/액세스 모델 및 사용자 프로세스 컨트롤을 비롯하여 컨트롤 형식 간에 일관되게 구현되는지 확인합니다.

- [Azure의 구분 전략에 대한 지침(동영상)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Azure의 구분 전략에 대한 지침(문서)](/security/compass/governance#enterprise-segmentation-strategy)

- [엔터프라이즈 구분 전략에 맞춰 네트워크 구분 조정](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: 보안 태세 관리 전략 정의

**지침**: 개별 자산과 해당 자산이 호스트되는 환경에 대한 위험을 지속적으로 측정하고 완화합니다. 게시된 애플리케이션, 네트워크 수신 및 송신 지점, 사용자 및 관리자 엔드포인트 등과 같은 고가치 자산과 노출이 많은 공격 노출 영역에 우선 순위를 지정합니다.

- [Azure Security Benchmark - 태세 및 취약성 관리](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: 조직 역할, 책임, 의무 조정

**지침**: 보안 조직의 역할 및 책임에 대한 명확한 전략을 문서화하고 전달하는지 확인합니다. 보안 의사 결정에 대한 명확한 책임을 제시하고, 공유 책임 모델을 모두에게 교육하고, 클라우드를 보호하는 기술에 관해 기술 팀을 교육하는 일에 우선 순위를 지정합니다.

- [Azure 보안 모범 사례 1 – 사용자: 클라우드 보안 경험에 대한 팀 교육](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure 보안 모범 사례 2 – 사용자: 클라우드 보안 기술에 대한 팀 교육](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure 보안 모범 사례 3 – 프로세스: 클라우드 보안 결정에 대한 책임 할당](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-5-define-network-security-strategy"></a>GS-5: 네트워크 보안 전략 정의

**지침**: 조직의 전반적인 보안 액세스 제어 전략의 일부로 Azure 네트워크 보안 방법을 설정합니다.  

이 전략에는 다음 요소에 대한 문서화된 지침, 정책 및 표준이 포함되어야 합니다. 

-   중앙 집중식 네트워크 관리 및 보안 책임

-   엔터프라이즈 구분 전략에 맞춰 조정된 가상 네트워크 구분 모델

-   다양한 위협 및 공격 시나리오에서 수정 전략

-   인터넷 에지 및 송수신 전략 /azure/security/benchmarks/security-controls-v2-logging-threat-detection
-   하이브리드 클라우드 및 온-프레미스 상호 연결 전략

-   최신 네트워크 보안 아티팩트(예: 네트워크 다이어그램, 참조 네트워크 아키텍처)

자세한 내용은 다음 참조 문서를 참조하세요.
- [Azure 보안 모범 사례 11 – 아키텍처 단일 통합 보안 전략](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark - 네트워크 보안](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure 네트워크 보안 개요](../security/fundamentals/network-overview.md)

- [엔터프라이즈 네트워크 아키텍처 전략](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: ID 및 권한 있는 액세스 전략 정의

**지침**: 조직의 전반적인 보안 액세스 제어 전략의 일부로 Azure ID 및 권한 있는 액세스 방법을 설정합니다.  

이 전략에는 다음 요소에 대한 문서화된 지침, 정책 및 표준이 포함되어야 합니다. 

-   중앙 집중식 ID 및 인증 시스템과 다른 내부 및 외부 ID 시스템 간의 상호 연결

-   다양한 사용 사례 및 조건에서 강력한 인증 방법

-   권한이 높은 사용자 보호

-   변칙 사용자 활동 모니터링 및 처리  

-   사용자 ID 및 액세스 검토와 조정 프로세스

자세한 내용은 다음 참조 문서를 참조하세요.

- [Azure Security Benchmark - ID 관리](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure Security Benchmark - 권한 있는 액세스](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Azure 보안 모범 사례 11 – 아키텍처 단일 통합 보안 전략](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure ID 관리 보안 개요](../security/fundamentals/identity-management-overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: 로깅 및 위협 대응 전략 정의

**지침**: 규정 준수 요구 사항을 충족하면서 신속하게 위협을 탐지하고 수정할 수 있는 로깅 및 위협 대응 전략을 수립합니다. 통합 및 수동 단계가 아닌 위협에 집중할 수 있도록 분석가에게 고품질 경고 및 원활한 환경을 제공하는 것에 우선 순위를 지정합니다. 

이 전략에는 다음 요소에 대한 문서화된 지침, 정책, 표준이 포함되어야 합니다. 

-   보안 운영(SecOps) 조직의 역할 및 책임 

-   NIST 또는 다른 업계 프레임워크를 사용하여 잘 정의된 인시던트 응답 프로세스 

-   위협 탐지, 인시던트 응답 및 규정 준수 요구 사항을 지원하는 로그 캡처 및 보존

-   SIEM, 네이티브 Azure 기능, 기타 소스를 사용하여 위협에 대한 중앙 집중식 가시성 및 상관 관계 정보 

-   고객, 공급자 및 공공 당사자와의 통신 및 알림 계획

-   로깅 및 위협 탐지, 포렌식, 공격 수정, 제거와 같은 인시던트 처리를 위해 Azure 네이티브 및 타사 플랫폼 사용

-   확인한 상황 및 증거 보존을 포함하여 인시던트 및 인시던트 후 활동을 처리하는 프로세스

자세한 내용은 다음 참조 문서를 참조하세요.

- [Azure Security Benchmark - 로깅 및 위협 탐지](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark - 인시던트 응답](../security/benchmarks/security-controls-v2-incident-response.md)

- [Azure 보안 모범 사례 4 - 프로세스 클라우드에 대한 인시던트 응답 프로세스 업데이트](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure 채택 프레임워크, 로깅, 보고 의사 결정 가이드](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 엔터프라이즈 규모, 관리, 모니터링](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](../security/benchmarks/overview.md)를 참조하세요.
- [Azure 보안 기준](../security/benchmarks/security-baselines-overview.md)에 대해 자세히 알아보세요.