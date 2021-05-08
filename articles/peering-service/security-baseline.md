---
title: Microsoft Azure Peering Service에 대한 Azure 보안 기준
description: Microsoft Azure Peering Service 보안 기준은 Azure Security Benchmark에 지정된 보안 권장 사항을 구현하기 위한 절차 지침과 리소스를 제공합니다.
author: msmbaldwin
ms.service: peering-service
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 854c669861c6aff71861481843bff97fdb7157ab
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315359"
---
# <a name="azure-security-baseline-for-microsoft-azure-peering-service"></a>Microsoft Azure Peering Service에 대한 Azure 보안 기준

이 보안 기준은 [Azure Security Benchmark 버전 2.0](../security/benchmarks/overview.md)의 지침을 Microsoft Azure Peering Service에 적용합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 Azure Security Benchmark에서 정의한 **보안 제어** 및 Microsoft Azure Peering Service에 적용되는 관련 지침에 따라 그룹화됩니다.

> [!NOTE]
> Microsoft Azure Peering Service에 적용되지 않거나 Microsoft의 책임이 있는 **제어** 는 제외되었습니다. Microsoft Azure Peering Service가 Azure Security Benchmark에 완전히 매핑되는 방법을 보려면 **[전체 Microsoft Azure Peering Service 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)** 을 참조하세요.

## <a name="network-security"></a>네트워크 보안

자세한 내용은 [Azure Security Benchmark: 네트워크 보안](/azure/security/benchmarks/security-controls-v2-network-security)을 참조하세요.

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: IDS/IPS(침입 검색 시스템/침입 방지 시스템) 배포

**지침**: Peering Service는 기본 침입 검색 또는 방지 서비스를 제공하지 않습니다. 고객은 Azure Firewall의 위협 인텔리전스 기반 필터링을 사용하여 비즈니스 요구 사항에 따라 알려진 악의적인 IP 주소 및 도메인에 대한 트래픽을 경고하고 차단하는 등의 보안 모범 사례를 따라야 합니다. 이러한 IP 주소 및 도메인은 Microsoft 위협-인텔리전스 피드에서 제공됩니다. 

페이로드 검사가 필요한 경우 Azure Marketplace에서 페이로드 검사 기능이 있는 타사 IDS/IPS(침입 검색 시스템 또는 방지 시스템)를 배포합니다.  
또는 네트워크 기반 침입 검색 또는 방지 시스템과 함께(또는 대신) 호스트 기반 EDR(엔드포인트 감지 및 응답) 솔루션을 사용합니다.  

침입 검색 또는 침입 방지 시스템을 활용하는 규정 요구 사항이 있는 경우 항상 고품질 경고를 사용하거나 제공하도록 튜닝되어 있는지 확인합니다. 

- [Azure Firewall을 배포하는 방법](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace에는 타사 IDS 기능이 있음](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Microsoft Defender ATP EDR 기능](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="identity-management"></a>ID 관리

자세한 내용은 [Azure Security Benchmark: ID 관리](/azure/security/benchmarks/security-controls-v2-identity-management)를 참조하세요.

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Azure Active Directory를 중앙 ID 및 인증 시스템으로 표준화

**지침**: Microsoft Azure Peering Service 리소스의 제어 영역(예: Azure Portal)에 대한 관리 액세스 권한이 있는 사용자 계정의 인벤토리를 유지합니다.

Azure Portal에서 구독에 대한 ID 및 액세스 제어(IAM) 창을 사용하여 Azure RBAC(Azure 역할 기반 액세스 제어)를 구성합니다. 역할은 Azure AD(Azure Active Directory)의 사용자, 그룹, 서비스 주체 및 관리 ID에 적용됩니다.

- [Azure Portal을 사용하여 Azure 역할 할당 추가 또는 제거](../role-based-access-control/role-assignments-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: 애플리케이션 ID를 안전하게 자동으로 관리

**지침**: Peering Service에는 관리 ID가 지원되지 않습니다. Peering Service와 같은 관리 ID를 지원하지 않는 서비스의 경우 Azure AD(Azure Active Directory)를 사용하여 리소스 수준에서 권한이 제한된 서비스 주체를 대신 만듭니다. 

- [Azure 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)

- [Azure 리소스에 대한 관리 ID를 지원하는 서비스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Azure 서비스 주체](/powershell/azure/create-azure-service-principal-azureps)

- [인증서를 사용하여 서비스 주체 만들기](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: 애플리케이션 액세스에 Azure AD SSO(Single Sign-On) 사용

**지침**: Microsoft Azure Peering Service는 Azure AD(Azure Active Directory)를 사용하여 Azure 리소스에 대한 ID 및 액세스 관리를 제공합니다. 여기에는 엔터프라이즈 ID(예: 직원) 및 외부 ID(예: 파트너, 공급업체 및 공급자)가 포함됩니다. 

SSO(Single Sign-On)를 사용하여 온-프레미스 및 클라우드에서 조직의 데이터 및 리소스에 대한 액세스를 관리하고 보호합니다. 모든 사용자, 애플리케이션, 디바이스를 Azure AD에 연결하여 원활하고 안전하게 액세스하고 가시성과 제어를 강화할 수 있습니다.

- [Azure AD에서의 애플리케이션 SSO 이해](../active-directory/manage-apps/what-is-single-sign-on.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: 모든 Azure Active Directory 기반 액세스에 강력한 인증 제어 사용

**지침**: Azure AD(Azure Active Directory)로 다단계 인증을 사용하도록 설정하고, Azure Security Center의 ID 및 액세스 관리 권장 사항을 따릅니다.

- [Azure에서 다단계 인증을 사용하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: 비정상 계정 활동 모니터링 및 경고

**참고 자료**: 환경에서 의심스럽거나 안전하지 않은 활동이 발생하는 경우 로그와 경고를 생성하기 위해 Azure AD(Azure Active Directory)가 포함된 PIM(Privileged Identity Management)을 사용합니다. 또한 Azure AD 위험 탐지를 사용하여 위험한 사용자 동작에 대한 경고 및 보고서를 봅니다.

- [PIM(Privileged Identity Management)을 배포하는 방법](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure AD 위험 탐지 이해](../active-directory/identity-protection/overview-identity-protection.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: 조건에 따라 Azure 리소스 액세스 제한

**지침**: 다단계 인증을 사용하려면 특정 IP 범위에서 사용자 로그인을 요구하는 것과 같이 사용자 정의 조건을 기반으로 보다 세분화된 액세스 제어를 위해 Azure AD(Azure Active Directory)와 함께 조건부 액세스를 사용합니다. 세분화된 인증 세션 관리는 다양한 사용 사례에 대한 Azure AD 조건부 액세스 정책을 통해 사용할 수도 있습니다. 

- [Azure 조건부 액세스 개요](../active-directory/conditional-access/overview.md)

- [일반 조건부 액세스 정책](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [조건부 액세스를 사용하여 인증 세션 관리를 구성합니다.](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: 의도하지 않은 자격 증명 노출 제거

**지침**: 코드 내에서 사용되는 자격 증명을 식별하기 위해 Azure DevOps 내에서 자격 증명 스캐너를 구현합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

GitHub의 경우 네이티브 암호 검색 기능을 사용하여 코드 내에서 자격 증명 또는 다른 형태의 암호를 식별할 수 있습니다.

- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [GitHub 암호 검색](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="privileged-access"></a>권한 있는 액세스

자세한 내용은 [Azure Security Benchmark: 권한 있는 액세스](/azure/security/benchmarks/security-controls-v2-privileged-access)를 참조하세요.

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: 높은 권한이 있는 사용자 보호 및 제한

**지침**: 높은 권한을 가진 사용자 계정의 수를 제한하고 이러한 계정을 상승된 수준에서 보호합니다. 

Azure AD(Azure Active Directory)에서 가장 중요한 기본 제공 역할은 전역 관리자와 권한 있는 역할 관리자입니다. 이 두 역할에 할당된 사용자는 관리자 역할을 위임할 수 있기 때문입니다. 

이러한 권한을 통해 사용자는 Azure 환경의 모든 리소스를 직접 또는 간접적으로 읽고 수정할 수 있습니다.

- 전역 관리자 / 회사 관리자: 이 역할의 사용자는 Azure AD의 모든 관리 기능뿐만 아니라 Azure AD ID를 사용하는 서비스에도 액세스할 수 있습니다.

- 권한 있는 역할 관리자: 이 역할의 사용자는 Azure AD뿐만 아니라 Azure AD PIM(Privileged Identity Management) 내에서도 역할 할당을 관리할 수 있습니다. 또한 이 역할을 통해 PIM 및 관리 단위의 모든 측면을 관리할 수 있습니다.

특정 권한이 부여된 사용 권한이 할당된 사용자 지정 역할을 사용하는 경우 관리해야 하는 다른 중요한 역할이 있을 수 있습니다. 중요한 비즈니스 자산의 관리자 계정에 유사한 제어를 적용합니다.  

Azure AD PIM(Privileged Identity Management)을 사용하여 Azure 리소스 및 Azure AD에 대한 JIT(Just-In-Time) 권한 있는 액세스를 사용하도록 설정합니다. JIT는 사용자가 필요한 경우에만 권한 있는 작업을 수행할 수 있는 임시 권한을 부여합니다. 또한 PIM은 Azure AD 조직에서 의심스럽거나 안전하지 않은 활동이 있을 때 보안 경고를 생성할 수 있습니다.

- [Azure AD의 관리자 역할 권한](/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

- [Azure Privileged Identity Management 보안 경고 사용](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Azure AD에서 하이브리드 및 클라우드 배포를 위한 권한 있는 액세스 보안](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: 중요 비즈니스용 시스템에 대한 관리 액세스 제한

**지침**: 권한 있는 액세스 권한이 부여된 계정을 해당 계정이 속한 구독 및 관리 그룹으로 제한하여 중요 비즈니스용 시스템에 대한 액세스를 격리합니다. 

중요 비즈니스용 시스템에 설치된 에이전트를 사용하여 Active Directory DC(도메인 컨트롤러), 보안 도구 및 시스템 관리 도구 등의 중요 비즈니스용 자산에 대한 관리 액세스가 있는 관리, ID 및 보안 시스템에 대한 액세스를 제한해야 합니다. 해당 관리 및 보안 시스템을 손상시키는 공격자는 중요 비즈니스용 자산을 손상시키기 위해 시스템을 즉시 무기화할 수 있습니다. 

모든 형식의 액세스 제어는 일관된 액세스 제어를 보장하기 위해 엔터프라이즈 조각화 전략에 맞춰야 합니다. 

이메일, 검색 및 생산성 작업에 사용되는 표준 사용자 계정과는 다른 별도의 권한 있는 계정을 할당해야 합니다.

- [Azure 구성 요소 및 참조 모델](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [관리 그룹 액세스](https://docs.microsoft.com/azure/governance/management-groups/overview#management-group-access)

- [Azure 구독 관리자](../cost-management-billing/manage/add-change-subscription-administrator.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure AD(Active Directory)는 부실 계정을 검색하는 데 도움이 되는 로그를 제공합니다. 또한 Azure ID 액세스 검토를 사용하여 그룹 멤버 자격, 엔터프라이즈 애플리케이션에 대한 액세스 및 역할 할당을 효율적으로 관리합니다. 사용자의 액세스를 정기적으로 검토하여 적합한 사용자만 계속 액세스할 수 있도록 합니다.

- [Azure AD 활동 보고서 찾기](../active-directory/reports-monitoring/howto-find-activity-reports.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Azure AD에서 응급 액세스 설정

**지침**: 일반 관리 계정을 사용할 수 없는 경우 액세스를 위한 긴급 계정을 설정하여 Azure AD(Azure Active Directory) 조직에서 실수로 잠기는 것을 방지합니다. 긴급 액세스 계정은 높은 수준의 권한을 포함할 수 있으며 특정 개인에게 할당해서는 안 됩니다. 긴급 액세스 계정을 긴급 또는 '비상 계정' 시나리오로 제한합니다.

긴급 액세스 계정의 자격 증명(예: 암호, 인증서 또는 스마트 카드)이 안전하게 유지되고 비상 시에 사용할 권한이 있는 개인에게만 알립니다.

- [Azure AD에서 응급 액세스 계정 관리](/azure/active-directory/users-groups-roles/directory-emergency-access)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="pa-5-automate-entitlement-management"></a>PA-5: 권한 관리의 자동화 

**지침**: Azure AD(Azure Active Directory) 권한 관리 기능을 사용하여 액세스 할당, 검토 및 만료를 포함한 액세스 요청 워크플로를 자동화합니다. 이중 또는 다단계 승인도 지원됩니다.

- [Azure AD(Azure Active Directory) 액세스 검토란 무엇인가요?](../active-directory/governance/access-reviews-overview.md)

- [Azure AD(Azure Active Directory) 권한 관리란 무엇인가요?](../active-directory/governance/entitlement-management-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: 권한 있는 액세스 워크스테이션 사용

**지침**: Azure AD(Azure Active Directory)의 다단계 인증과 함께 PAW(Privileged Access Workstation)를 사용하여 Azure Sentinel 관련 리소스에 로그인하고 구성할 수 있습니다.

- [권한 있는 액세스 워크스테이션](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [클라우드 기반 Azure AD 다단계 인증 배포 계획](../active-directory/authentication/howto-mfa-getstarted.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: 충분한 관리 수행(최소 권한 원칙) 

**지침**: Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하면 역할 할당을 통해 Azure 리소스 액세스를 관리할 수 있습니다. 이러한 역할은 사용자, 그룹 서비스 주체 및 관리 ID에 할당할 수 있습니다. 특정 리소스에 대해 미리 정의된 기본 제공 역할이 있으며 이러한 역할은 Azure CLI, Azure PowerShell 및 Azure Portal과 같은 도구를 통해 쿼리하거나 인벤토리에 포함할 수 있습니다.

Azure RBAC를 통해 리소스에 할당하는 권한은 항상 역할에 필요한 권한으로 제한해야 합니다. 제한된 권한은 Azure AD(Azure Active Directory) PIM(Privileged Identity Management )의 JIT(just-in-time) 접근 방식을 보완하며 이러한 권한은 정기적으로 검토해야 합니다.

기본 제공 역할을 사용하여 권한을 할당하고 필요한 경우에만 사용자 지정 역할을 만듭니다.

- [Azure RBAC(Azure 역할 기반 액세스 제어)는 무엇인가요?](../role-based-access-control/overview.md)

- [Azure RBAC를 구성하는 방법](../role-based-access-control/role-assignments-portal.md)

- [Azure AD ID 및 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="asset-management"></a>자산 관리

자세한 내용은 [Azure Security Benchmark: 자산 관리](/azure/security/benchmarks/security-controls-v2-asset-management)를 참조하세요.

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: 보안 팀에서 자산 위험에 대한 가시성을 확보하도록 보장

**지침**: Azure Security Center를 사용하여 보안 위험을 모니터링할 수 있도록 Azure 테넌트 및 구독에서 보안 읽기 권한자 권한을 보안 팀에 부여합니다. 

보안 팀의 책임이 구성된 방식에 따라 보안 위험 모니터링은 중앙 보안 팀 또는 로컬 팀의 책임이 될 수 있습니다. 즉, 보안 인사이트 및 위험이 항상 조직 내의 중앙에서 집계되어야 합니다. 

보안 읽기 권한자 권한은 전체 테넌트(루트 관리 그룹)에 광범위하게 적용하거나 범위를 관리 그룹 또는 특정 구독으로 지정할 수 있습니다. 

워크로드 및 서비스에 대한 가시성을 얻으려면 추가 권한이 필요할 수 있습니다. 

- [보안 읽기 권한자 역할 개요](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader)

- [Azure 관리 그룹 개요](../governance/management-groups/overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: 보안 팀에 자산 인벤토리 및 메타데이터에 대한 액세스 권한이 있는지 확인

**지침**: 보안 팀이 지속적으로 업데이트되는 Azure 자산 인벤토리에 대한 액세스 권한을 가지고 있는지 확인합니다. 보안 팀은 새로운 위험에 대한 조직의 잠재적인 노출을 평가하기 위해, 그리고 지속적으로 보안을 향상하기 위한 입력 정보로서 이 인벤토리가 필요한 경우가 많습니다. 

Azure Security Center 인벤토리 기능 및 Azure Resource Graph는 Azure 서비스, 애플리케이션 및 네트워크 리소스를 포함하여 구독에 있는 모든 리소스를 쿼리하고 검색할 수 있습니다.  

Azure에서 태그뿐만 아니라 다른 메타데이터(이름, 설명, 범주)도 사용하여 조직의 분류법에 따라 자산을 논리적으로 구성합니다.  

- [Azure Resource Graph Explorer를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

- [Azure Security Center 자산 인벤토리 관리](../security-center/asset-inventory.md)

- [자산에 태그를 지정하는 방법에 대한 자세한 내용은 리소스 명명 및 태그 지정 결정 안내를 참조하세요.](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: 승인된 Azure 서비스만 사용

**지침**: Microsoft Azure Peering Service는 'Microsoft.Peering/peerings' 네임스페이스에서 Azure Policy를 사용하여 Azure Resource Manager 기반 배포 및 구성 적용을 지원합니다. Azure Policy를 사용하여 환경에서 사용자가 프로비저닝할 수 있는 서비스를 감사하고 제한합니다. Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리하고 검색합니다. 또한 Azure Monitor를 사용하여 승인되지 않은 서비스가 검색되면 경고를 트리거하는 규칙을 만들 수 있습니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

- [Azure Resource Graph Explorer를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: Azure Resource Manager와 상호 작용하는 사용자 기능 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성하여 사용자가 Azure Resource Manager와 상호 작용하는 기능을 제한하도록 Azure 조건부 액세스를 구성합니다.

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="logging-and-threat-detection"></a>로깅 및 위협 탐지

자세한 내용은 [Azure Security Benchmark: 로깅 및 위협 탐지](/azure/security/benchmarks/security-controls-v2-logging-threat-protection)를 참조하세요.

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Azure 리소스에 대한 위협 탐지 사용

**지침**: 잠재적인 위협 및 비정상 활동에 대해 다양한 유형의 Azure 자산을 모니터링하고 있는지 확인합니다. 분석가가 면밀히 살펴볼 수 있도록 가양성을 줄여 고품질 경고를 얻는 데 중점을 두세요. 경고는 로그 데이터, 에이전트 또는 기타 데이터로부터 제공될 수 있습니다.

Azure 서비스 원격 분석 모니터링 및 서비스 로그 분석을 기반으로 하는 Azure Security Center 기본 제공 위협 탐지 기능을 사용합니다. 데이터는 시스템에서 다양한 보안 관련 구성 및 이벤트 로그를 읽고 분석용으로 작업 영역에 데이터를 복사하는 Log Analytics 에이전트를 사용하여 수집됩니다. 

또한 Azure Sentinel을 사용하여 사용자 환경에서 특정 기준과 일치하는 위협을 헌팅하는 분석 규칙을 빌드합니다. 규칙은 기준이 일치할 때 인시던트를 생성하므로 각 인시던트를 조사할 수 있습니다. 또한 Azure Sentinel은 타사 위협 인텔리전스를 가져와서 위협 검색 기능을 향상시킬 수 있습니다. 

- [위협 방지 및 Azure Security Center](/azure/security-center/threat-protection)

- [Azure Security Center 보안 경고 참조 지침](../security-center/alerts-reference.md)

- [위협 검색을 위한 사용자 지정 분석 규칙 만들기](../sentinel/tutorial-detect-threats-custom.md)

- [Azure Sentinel을 사용한 사이버 위협 인텔리전스](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Azure ID 및 액세스 관리에 위협 탐지 사용

**지침**: Azure AD(Azure Active Directory)는 Azure AD 보고로 보거나 Azure Monitor와 통합할 수 있는 사용자 로그를 제공합니다. 이러한 로그를 Azure Sentinel 또는 다른 SIEM(보안 정보 및 이벤트 관리) 솔루션 또는 모니터링 도구와 통합하여 보다 정교한 모니터링 및 분석 사용 사례를 확인할 수도 있습니다.

- 로그인 – 로그인 보고서는 관리되는 애플리케이션 및 사용자 로그인 활동의 사용에 대한 정보를 제공합니다.

- 감사 로그 - Azure AD 내의 다양한 기능에 의해 수행된 모든 변경 내용에 대한 로그를 통한 추적 기능을 제공합니다. 감사 로그의 예로는 사용자, 앱, 그룹, 역할 및 정책 추가 또는 제거와 같은 Azure AD 내의 모든 리소스에 대한 변경 내용이 있습니다.

- 위험한 로그인 - 위험한 로그인은 사용자 계정의 정당한 소유자가 아닌 사용자에 의해 수행된 로그인 시도에 대한 지표입니다.

- 위험 플래그가 지정된 사용자 - 위험한 사용자는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다.

Azure Security Center는 구독에서 더 이상 사용되지 않는 계정에 의한 과도한 인증 시도 실패와 같은 특정 의심스러운 활동에 대해 경고할 수도 있습니다. 기본 보안 예방 조치 모니터링 외에도 보안 센터의 위협 방지 모듈은 개별 Azure 컴퓨팅 리소스(예: 가상 머신, 컨테이너, 앱 서비스), 데이터 리소스(예: SQL DB 및 스토리지) 및 Azure 서비스 계층에서 추가적으로 심층적인 보안 경고를 수집할 수 있습니다. 해당 기능을 사용하면 개별 리소스 내에서 비정상 계정 활동을 볼 수 있습니다.

- [Azure AD에서 활동 보고서 감사](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure ID 보호 사용](../active-directory/identity-protection/overview-identity-protection.md)

- [위협 방지 및 Azure Security Center](/azure/security-center/threat-protection)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Azure 네트워크 활동에 대한 로깅 사용

**지침**: 연결 위치 간의 연결에 대한 인사이트를 제공하도록 연결 원격 분석을 구성합니다.

Microsoft Azure Peering Service를 통한 Microsoft 네트워크

- [연결 원격 분석 구성](measure-connection-telemetry.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Azure 리소스에 대한 로깅 사용

**지침**: Azure 활동 로그를 사용하도록 설정하고 보관을 위해 로그를 Log Analytics 작업 영역, Azure 이벤트 허브 또는 Azure 스토리지 계정으로 보냅니다. 

활동 로그는 컨트롤 플레인 수준에서 Azure ExpressRoute 리소스에 대해 수행된 작업에 대한 인사이트를 제공합니다. Azure 활동 로그 데이터를 사용하면 리소스의 컨트롤 플레인 수준에서 수행되는 모든 쓰기 작업(PUT, POST, DELETE)에 대한 "무엇을, 누가, 언제"를 판단할 수 있습니다.

- [Azure 활동 로그](/azure/azure-monitor/platform/activity-log)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: 보안 로그 관리 및 분석 중앙 집중화

**지침**: 상관 관계를 사용하도록 설정하기 위해 로깅 스토리지 및 분석을 중앙 집중화합니다. 각 로그 원본에 대해 데이터 소유자, 액세스 지침, 스토리지 위치, 데이터를 처리하고 액세스하는 데 사용되는 도구, 데이터 보존 요구 사항을 할당했는지 확인합니다.

Azure 활동 로그를 중앙 로깅에 통합하고 있는지 확인합니다. Azure Monitor를 통해 로그를 수집하여 엔드포인트 디바이스, 네트워크 리소스, 기타 보안 시스템에 의해 생성된 보안 데이터를 집계합니다. Azure Monitor에서 Log Analytics 작업 영역을 사용하여 분석을 쿼리하고 수행하며, 장기 및 기록 스토리지에 Azure Storage 계정을 사용할 수 있습니다.

또한 데이터를 Azure Sentinel 또는 타사 SIEM에 사용하도록 설정하고 온보딩할 수 있습니다.

많은 조직에서 자주 사용되는 “핫” 데이터에는 Azure Sentinel을 사용하고 덜 자주 사용되는 “콜드” 데이터에는 Azure Storage를 사용합니다.

- [Azure Monitor를 사용한 플랫폼 로그 및 메트릭 수집 방법](../azure-monitor/essentials/diagnostic-settings.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: 로그 스토리지 보존 구성

**지침**: Azure Monitor에서 조직의 규정 준수 규정에 따라 Azure 리소스와 연결된 Log Analytics 작업 영역의 로그 보존 기간을 설정합니다.

- [로그 보존 매개 변수를 설정하는 방법](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](/azure/security/benchmarks/security-controls-v2-incident-response)을 참조하세요.

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: 준비 - Azure에 대한 인시던트 응답 프로세스 업데이트

**지침**: 조직이 보안 인시던트에 대응하는 프로세스를 보유하고, 해당 프로세스를 Azure에 대해 업데이트했으며, 준비 상태를 확인하기 위해 프로세스를 정기적으로 수행하는지 확인합니다.

- [엔터프라이즈 환경에서 보안 구현](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [인시던트 응답 참조 가이드](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: 준비 - 인시던트 알림 설정

**지침**: Azure Security Center에서 보안 인시던트 연락처 정보를 설정합니다. 이 연락처 정보는 MSRC(Microsoft 보안 대응 센터)가 불법적인 당사자나 권한 없는 당사자가 데이터에 액세스한 것을 발견한 경우 Microsoft가 연락하는 데 사용됩니다. 또한 인시던트 응답 요구 사항에 따라 다양한 Azure 서비스에서 인시던트 경고 및 알림을 사용자 지정하는 옵션도 있습니다. 

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: 탐지 및 분석 – 고품질 경고를 기반으로 인시던트 만들기 

**지침**: 고품질 경고를 만들고 경고의 품질을 측정하는 프로세스가 있는지 확인합니다. 이렇게 하면 이전 인시던트에서 상황을 확인하고 분석가를 위한 경고의 우선 순위를 지정할 수 있으므로 가양성으로 인해 시간을 낭비하지 않습니다. 

고품질 경고는 이전 인시던트, 유효성이 검사된 커뮤니티 소스, 다양한 신호 원본을 결합하고 상관 관계를 설정하여 경고를 생성하고 정리하도록 설계된 도구 등에서 얻은 경험을 기반으로 구축할 수 있습니다. 

Azure Security Center는 많은 Azure 자산에 대해 고품질 경고를 제공합니다. ASC 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다. Azure Sentinel을 사용하면 조사를 위해 인시던트를 자동으로 생성하는 고급 경고 규칙을 만들 수 있습니다. 

Azure 리소스에 대한 위험을 식별하는 데 도움이 되도록 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 경고 및 추천 사항을 수동 또는 지속적인 방식으로 내보냅니다.

- [내보내기를 구성하는 방법](../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: 검색 및 분석 – 인시던트 조사

**지침**: 분석가가 잠재적 인사이트를 조사하여 발생한 상황에 대한 전체 보기를 구축할 때 다양한 데이터 원본을 쿼리하고 사용할 수 있도록 합니다. 사각지대를 방지하기 위해 다양한 로그를 수집하여 킬 체인 전체에서 잠재적 공격자의 활동을 추적해야 합니다.  또한 다른 분석가 및 향후 기록 참조를 위해 인사이트 및 습득 지식을 캡처해야 합니다.  

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

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: 탐지 및 분석 – 인시던트 우선 순위 지정

**지침**: 경고 심각도 및 자산 민감도에 따라 먼저 집중해야 하는 인시던트에 대한 컨텍스트를 분석가에게 제공합니다. 

Azure Security Center는 먼저 조사해야 하는 경고의 우선 순위를 지정하는 데 도움이 되도록 심각도를 각 경고에 할당합니다. 심각도는 Security Center에서 경고를 발행하는 데 사용되는 결과 또는 분석의 신뢰도 및 경고가 발생한 활동에 악의적인 의도가 있었다는 신뢰도 수준을 기반으로 합니다.

또한 태그를 사용하여 리소스를 표시하고, Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 식별하고 분류할 수 있는 명명 시스템을 만듭니다.  인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다.

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md)

- [태그를 사용하여 Azure 리소스 구성](/azure/azure-resource-manager/resource-group-using-tags).

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: 차단, 제거, 복구 - 인시던트 처리 자동화

**지침**: 수동 반복 작업을 자동화하여 응답 시간을 단축하고 분석가의 부담을 줄입니다. 수동 작업은 실행하는 데 더 오래 걸려 각 인시던트의 속도를 늦추고 분석가가 처리할 수 있는 인시던트 수를 줄입니다. 또한 수동 작업은 분석가를 지치게 하여 지연을 유발하는 인간 오류의 위험을 높이고, 복잡한 작업에 효과적으로 집중할 수 있는 분석가의 능력을 저하시킵니다. Azure Security Center 및 Azure Sentinel의 워크플로 자동화 기능을 사용하여 작업을 자동으로 트리거하거나 플레이북을 실행하여 들어오는 보안 경고에 대응합니다. 플레이북은 알림 보내기, 계정 사용 안 함 및 문제가 있는 네트워크 격리와 같은 작업을 수행합니다. 

- [Security Center에서 워크플로 자동화 구성](../security-center/workflow-automation.md)

- [Azure Security Center에서 자동화된 위협 대응 설정](https://docs.microsoft.com/azure/security-center/tutorial-security-incident#triage-security-alerts)

- [Azure Sentinel에서 자동화된 위협 응답 설정](../sentinel/tutorial-respond-threats-playbook.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="posture-and-vulnerability-management"></a>태세 및 취약성 관리

자세한 내용은 [Azure Security Benchmark: 태세 및 취약성 관리](/azure/security/benchmarks/security-controls-v2-vulnerability-management)를 참조하세요.

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Azure 서비스에 대한 보안 구성 설정 

**지침**: 인프라 및 DevOps 팀이 사용하는 Azure 서비스를 쉽게 안전하게 구성하여 보안 가드레일을 정의합니다. 

Azure Security Benchmark의 서비스 기준을 사용하여 Azure 서비스의 보안 구성을 시작하고 조직의 필요에 맞게 사용자 지정합니다. 

Azure Security Center를 사용하여 Azure 리소스의 구성을 감사하고 적용하도록 Azure 정책을 구성합니다. 

Azure Blueprints를 사용하여 단일 청사진 정의에서 Azure Resource Manager 템플릿, Azure RBAC 컨트롤 및 정책을 포함하여 서비스 및 애플리케이션 환경의 배포 및 구성을 자동화할 수 있습니다.

- [엔터프라이즈급 랜딩 존의 가드레일 구현에 대한 그림](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Azure Security Center의 보안 정책 작업](../security-center/tutorial-security-policy.md)

- [규정 준수를 적용하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints](../governance/blueprints/overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Azure 서비스에 대한 보안 구성 유지

**지침**: 해당 없음. 이 권장 사항은 컴퓨팅 리소스를 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: 정기적인 공격 시뮬레이션 수행

**지침**: 필요에 따라 Azure 리소스에 대한 침투 테스트 또는 레드 팀 활동을 수행하고 모든 중요한 보안 결과를 수정해야 합니다.
Microsoft Cloud 침투 테스트 시행 규칙에 따라 침투 테스트가 Microsoft 정책을 위반하지 않는지 확인합니다. Microsoft의 전략과 Microsoft에서 관리하는 클라우드 인프라, 서비스, 애플리케이션에 대한 레드 팀 실행 및 실시간 사이트 침투 테스트를 사용합니다.

- [Azure의 침투 테스트](../security/fundamentals/pen-testing.md)

- [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud 레드 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

## <a name="governance-and-strategy"></a>거버넌스 및 전략

자세한 내용은 [Azure Security Benchmark: 거버넌스 및 전략](/azure/security/benchmarks/security-controls-v2-governance-strategy)을 참조하세요.

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
- [Azure 보안 아키텍처 권장 사항 - 스토리지, 데이터, 암호화](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Azure 보안 기본 사항 - Azure 데이터 보안, 암호화, 스토리지](../security/fundamentals/encryption-overview.md)

- [클라우드 채택 프레임워크 - Azure 데이터 보안 및 암호화 모범 사례](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure Security Benchmark - 자산 관리](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Azure Security Benchmark - 데이터 보호](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: 엔터프라이즈 구분 전략 정의 

**지침**: ID, 네트워크, 애플리케이션, 구독, 관리 그룹 및 기타 컨트롤의 조합을 사용하여 자산에 대한 액세스를 구분하는 엔터프라이즈 전체 전략을 수립합니다.

서로 통신하고 데이터에 액세스해야 하는 시스템의 일상 작업을 사용하도록 설정해야 할 필요성과 보안 분리의 필요성을 신중하게 조정해야 합니다.

구분 전략이 네트워크 보안, ID 및 액세스 모델, 애플리케이션 권한/액세스 모델 및 사용자 프로세스 컨트롤을 비롯하여 컨트롤 형식 간에 일관되게 구현되는지 확인합니다.

- [Azure의 구분 전략에 대한 지침(동영상)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Azure의 구분 전략에 대한 지침(문서)](/security/compass/governance#enterprise-segmentation-strategy)

- [엔터프라이즈 구분 전략에 맞춰 네트워크 구분 조정](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: 보안 태세 관리 전략 정의

**지침**: 개별 자산과 해당 자산이 호스트되는 환경에 대한 위험을 지속적으로 측정하고 완화합니다. 게시된 애플리케이션, 네트워크 수신 및 송신 지점, 사용자 및 관리자 엔드포인트 등과 같은 고가치 자산과 노출이 많은 공격 노출 영역에 우선 순위를 지정합니다.

- [Azure Security Benchmark - 태세 및 취약성 관리](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: 조직 역할, 책임, 의무 조정

**지침**: 보안 조직의 역할 및 책임에 대한 명확한 전략을 문서화하고 전달하는지 확인합니다. 보안 의사 결정에 대한 명확한 책임을 제시하고, 공유 책임 모델을 모두에게 교육하고, 클라우드를 보호하는 기술에 관해 기술 팀을 교육하는 일에 우선 순위를 지정합니다.

- [Azure 보안 모범 사례 1 – 사용자: 클라우드 보안 경험에 대한 팀 교육](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure 보안 모범 사례 2 – 사용자: 클라우드 보안 기술에 대한 팀 교육](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure 보안 모범 사례 3 – 프로세스: 클라우드 보안 결정에 대한 책임 할당](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="gs-5-define-network-security-strategy"></a>GS-5: 네트워크 보안 전략 정의

**지침**: 조직의 전반적인 보안 액세스 제어 전략의 일부로 Azure 네트워크 보안 방법을 설정합니다.  

이 전략에는 다음 요소에 대한 문서화된 지침, 정책 및 표준이 포함되어야 합니다. 

-   중앙 집중식 네트워크 관리 및 보안 책임

-   엔터프라이즈 구분 전략에 맞춰 조정된 가상 네트워크 구분 모델

-   다양한 위협 및 공격 시나리오에서 수정 전략

-   인터넷 에지 및 수신/송신 전략

-   하이브리드 클라우드 및 온-프레미스 상호 연결 전략

-   최신 네트워크 보안 아티팩트(예: 네트워크 다이어그램, 참조 네트워크 아키텍처)

자세한 내용은 다음 참조 문서를 참조하세요.
- [Azure 보안 모범 사례 11 – 아키텍처 단일 통합 보안 전략](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark - 네트워크 보안](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Azure 네트워크 보안 개요](../security/fundamentals/network-overview.md)

- [엔터프라이즈 네트워크 아키텍처 전략](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: ID 및 권한 있는 액세스 전략 정의

**지침**: 조직의 전반적인 보안 액세스 제어 전략의 일부로 Azure ID 및 권한 있는 액세스 방법을 설정합니다.  

이 전략에는 다음 요소에 대한 문서화된 지침, 정책 및 표준이 포함되어야 합니다. 

-   중앙 집중식 ID 및 인증 시스템과 다른 내부 및 외부 ID 시스템 간의 상호 연결

-   다양한 사용 사례 및 조건에서 강력한 인증 방법

-   권한이 높은 사용자 보호

-   변칙 사용자 활동 모니터링 및 처리  

-   사용자 ID 및 액세스 검토와 조정 프로세스

자세한 내용은 다음 참조 문서를 참조하세요.

- [Azure Security Benchmark - ID 관리](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure Security Benchmark - 권한 있는 액세스](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure 보안 모범 사례 11 – 아키텍처 단일 통합 보안 전략](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure ID 관리 보안 개요](../security/fundamentals/identity-management-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

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

- [Azure Security Benchmark - 로깅 및 위협 탐지](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure Security Benchmark - 인시던트 응답](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure 보안 모범 사례 4 - 프로세스 클라우드에 대한 인시던트 응답 프로세스 업데이트](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure 채택 프레임워크, 로깅, 보고 의사 결정 가이드](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 엔터프라이즈 규모, 관리, 모니터링](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
