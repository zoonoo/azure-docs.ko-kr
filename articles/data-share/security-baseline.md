---
title: Azure 데이터 공유에 대 한 azure 보안 기준
description: Azure 데이터 공유 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: data-share
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7bf78194b50e2e2335d807504ebca5f8a0e9f0eb
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94849213"
---
# <a name="azure-security-baseline-for-azure-data-share"></a>Azure 데이터 공유에 대 한 azure 보안 기준

이 보안 기준은 [Azure Security 벤치 마크 버전 1.0](../security/benchmarks/overview-v1.md) 의 지침을 Microsoft Azure 데이터 공유에 적용 합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다.
콘텐츠는 azure 보안 벤치 마크에 정의 된 **보안 컨트롤과** Azure 데이터 공유에 적용 되는 관련 지침에 따라 그룹화 됩니다. Azure 데이터 공유에 적용할 수 없는 **컨트롤이** 제외 되었습니다.

 
Azure 데이터 공유가 Azure 보안 벤치 마크에 완전히 매핑되는 방식을 보려면 [전체 Azure 데이터 공유 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조 하세요.

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure 보안 벤치 마크: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조 하세요.*

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure Monitor를 통해 Azure 데이터 공유와 관련 된 로그를 수집 하 여 끝점 장치, 네트워크 리소스 및 기타 보안 시스템에 의해 생성 된 보안 데이터를 집계 합니다. Azure Monitor에서 Log Analytics 작업 영역을 사용 하 여 분석을 쿼리하고 수행 하 고 장기 및 보관 저장소에 Azure Storage 계정을 사용할 수 있습니다.

또는이 데이터를 Azure 센티널 또는 타사 SIEM에 대해 사용 하도록 설정 하거나 온보드 할 수 있습니다.

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md) 

- [Azure Monitor를 사용 하 여 플랫폼 로그 및 메트릭을 수집 하는 방법](../azure-monitor/platform/diagnostic-settings.md) 

- [Azure Monitor 및 타사 SIEM 통합을 시작하는 방법](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**참고**: 자동으로 사용할 수 있는 활동 로그에는 읽기 작업 (GET)을 제외 하 고 Azure 데이터 공유 리소스에 대 한 모든 쓰기 작업 (PUT, POST, DELETE)이 포함 됩니다. 활동 로그를 사용 하 여 문제를 해결할 때 오류를 찾거나 조직의 사용자가 리소스를 수정한 방법을 모니터링할 수 있습니다.

Azure 데이터 공유에 대 한 진단 로그, 특히 MicrosoftDataShareSentShareSnapshotsLog MicrosoftDataShareReceivedShareSnapshotsLog에 대 한 진단 로그를 사용 하도록 설정 &amp; 합니다. 이러한 로그를 통해 동기화 시작 시간, 종료 시간, 상태 및 기타 세부 정보와 같은 주요 정보를 캡처할 수 있습니다. 이러한 로그는 나중에 보안 인시던트를 조사 하 고 법정 연습을 수행 하는 데 중요할 수 있습니다.

- [Azure Monitor를 사용 하 여 플랫폼 로그 및 메트릭을 수집 하는 방법](../azure-monitor/platform/diagnostic-settings.md) 

- [Azure의 로깅 및 다른 로그 유형 이해](../azure-monitor/platform/platform-logs-overview.md)

- [Azure 활동 로그에 대 한 진단 설정을 구성 하는 방법](../azure-monitor/platform/activity-log.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침**: Azure 데이터 공유 로그를 저장 하는 데 사용 되는 저장소 계정 또는 Log Analytics 작업 영역에는 조직의 규정 준수 규정에 따라 설정 된 로그 보존 기간이 있는지 확인 합니다.

- [Log Analytics 작업 영역 보존 기간을 구성 하는 방법](../azure-monitor/platform/manage-cost-storage.md)

- [Azure Storage 계정에 리소스 로그 저장](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: 비정상적인 동작에 대 한 Azure 데이터 공유 리소스와 관련 된 로그를 분석 및 모니터링 하 고 결과를 정기적으로 검토 합니다. Azure Monitor 및 Log Analytics 작업 영역을 사용 하 여 로그를 검토 하 고 로그 데이터에 대 한 쿼리를 수행할 수 있습니다.

또는 Azure 센티널 또는 타사 SIEM에 대 한 온보드 데이터를 사용 하도록 설정할 수 있습니다.

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md) 

- [Log Analytics 쿼리 시작](/azure/azure-monitor/log-query/get-started-portal) 

- [Azure Monitor에서 사용자 지정 쿼리를 수행하는 방법](../azure-monitor/log-query/get-started-queries.md) 

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: 보안 로그 및 이벤트에서 발견 된 비정상적인 활동을 모니터링 하 고 경고 하기 위해 Log Analytics 작업 영역에서 Azure Security Center를 사용 합니다. 또는 온보드 데이터를 Azure 센티널로 사용 하도록 설정할 수 있습니다.

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md) 

- [Azure Security Center에서 경고를 관리 하는 방법](../security-center/security-center-managing-and-responding-alerts.md) 

- [Log analytics 로그 데이터를 경고 하는 방법](../azure-monitor/learn/tutorial-response.md) 

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [Azure 보안 벤치 마크: id 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조 하세요.*

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 SSO(Single Sign-On) 사용

**지침**: Azure 데이터 공유는 AZURE ACTIVE DIRECTORY로 SSO 인증을 지원 합니다. 조직의 기존 id를 사용 하 여 서비스에 대해 SSO를 사용 하도록 설정 하 여 사용자가 관리 해야 하는 id 및 자격 증명의 수를 줄입니다.

- [Azure AD를 사용 하 여 SSO 이해](/azure/active-directory/manage-apps/what-is-single-sign-o)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: AZURE AD MFA를 사용 하도록 설정 하 고 Azure Security Center id 및 액세스 권장 사항에 따릅니다.

- [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md) 

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

**지침**: 높은 권한이 필요한 관리 작업에 대해 Azure에서 관리 하는 안전한 워크스테이션 (권한 있는 액세스 워크스테이션 또는 PAW 라고도 함)을 사용 합니다.

- [안전 하 고 Azure로 관리 되는 워크스테이션 이해](../active-directory/devices/concept-azure-managed-workstation.md)
 

- [Azure AD MFA를 사용 하도록 설정 하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure Active Directory (Azure AD)를 중앙 인증 및 권한 부여 시스템으로 사용 합니다. Azure AD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다.

- [Azure AD 인스턴스를 만들고 구성 하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Azure 데이터 공유는 일반적인 Azure 역할에서 작동 합니다. ](../role-based-access-control/built-in-roles.md#general)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure AD는 부실 계정을 검색하는 데 유용한 로그를 제공합니다. 또한 Azure AD id 및 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리 합니다. 사용자 액세스를 정기적으로 검토 하 여 적절 한 사용자만 계속 액세스할 수 있도록 할 수 있습니다.

- [Azure AD 보고 이해](/azure/active-directory/reports-monitoring/) 

- [Azure AD id 및 액세스 검토를 사용 하는 방법](../active-directory/governance/access-reviews-overview.md) 

- [Azure 데이터 공유는 일반적인 Azure 역할에서 작동 합니다. ](../role-based-access-control/built-in-roles.md#general)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화 되는 자격 증명에 대 한 액세스 시도를 모니터링 합니다.

**지침**: siem/모니터링 도구와 통합할 수 있도록 하는 Azure AD 로그인 활동, 감사 및 위험 이벤트 로그 원본에 액세스할 수 있습니다.

Azure AD 사용자 계정에 대 한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 전송 하 여이 프로세스를 간소화할 수 있습니다. Log Analytics 작업 영역 내에서 원하는 경고를 구성할 수 있습니다.

- [Azure Monitor와 Azure 활동 로그를 통합 하는 방법](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics) 

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure AD ID 보호 기능을 사용 하 여 사용자 id와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성 합니다. 추가 조사를 위해 데이터를 Azure Sentinel로 수집할 수도 있습니다.

- [Azure AD 위험한 로그인을 확인하는 방법](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [Azure 보안 벤치 마크: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조 하세요.*

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Azure RBAC를 사용 하 여 리소스에 대 한 액세스 관리

**지침**: azure RBAC (역할 기반 액세스 제어)를 사용 하 여 Azure 데이터 공유 리소스와 관련 된 데이터 및 리소스에 대 한 액세스를 관리 하 고, 그렇지 않은 경우 서비스 특정 액세스 제어 방법을 사용 합니다.

- [Azure에서 RBAC를 구성 하는 방법](../role-based-access-control/role-assignments-portal.md) 

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고

**지침**: Azure 활동 로그와 함께 Azure Monitor를 사용 하 여 중요 한 Azure 리소스에 변경 내용이 발생 하는 경우에 대 한 Azure Monitor 경고를 만듭니다.

- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](../azure-monitor/platform/alerts-activity-log.md) 

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="vulnerability-management"></a>취약점 관리

*자세한 내용은 [Azure 보안 벤치 마크: 취약성 관리](../security/benchmarks/security-control-vulnerability-management.md)를 참조 하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행

**지침**: Azure 데이터 공유는 해당 리소스에 대 한 취약성 검색 도구를 설치할 수 없습니다.

일반적으로 Azure 가상 컴퓨터, 컨테이너 이미지 및 SQL server에서 취약점 평가를 수행 하는 Azure Security Center의 권장 사항을 따릅니다.

타사 솔루션을 사용 하 여 네트워크 장치 및 웹 응용 프로그램에 대 한 취약성 평가를 수행 합니다. 원격 검색을 수행할 때 단일 영구 관리자 계정을 사용 하지 마십시오. 검색 계정에 대 한 JIT 프로 비전 방법론을 구현 하는 것이 좋습니다. 검색 계정에 대 한 자격 증명을 보호 하 고, 모니터링 하 고, 취약성 검색에만 사용 해야 합니다.

- [Azure Security Center 취약성 평가 권장 사항을 구현 하는 방법](/azure/security-center/security-center-vulnerability-assessment-recommendations) 

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure 보안 벤치 마크: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조 하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화 된 asset discovery 솔루션 사용

**지침**: Azure 리소스, 리소스 그룹 및 구독에 태그를 적용 하 여 논리적으로 분류로 구성 합니다. 각 태그는 이름과 값 쌍으로 이루어져 있습니다. 예를 들어 프로덕션의 모든 리소스에 "환경" 이름과 "프로덕션" 값을 적용할 수 있습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: Azure 리소스, 리소스 그룹 및 구독에 태그를 적용 하 여 논리적으로 분류로 구성 합니다. 각 태그는 이름과 값 쌍으로 이루어져 있습니다. 예를 들어 프로덕션의 모든 리소스에 "환경" 이름과 "프로덕션" 값을 적용할 수 있습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 해당 하는 경우 태그 지정, 관리 그룹 및 별도의 구독을 사용 하 여 자산을 구성 하 고 추적 합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

- [추가 Azure 구독을 만드는 방법](/azure/billing/billing-create-subscription) 

- [관리 그룹을 만드는 방법](/azure/governance/management-groups/create) 

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: 승인 된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 조직 요구 사항에 따라 승인 된 Azure 리소스 및 계산 리소스에 대 한 승인 된 소프트웨어의 인벤토리를 만듭니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy을 사용 하 여 구독에 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.
Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리하고 검색 합니다. 환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md) 

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: Azure 리소스는 더 이상 필요 하지 않을 때 제거 합니다 .이 작업은 Azure Portal, POWERSHELL 또는 CLI를 통해 수행할 수 있습니다.

- [Azure 리소스 그룹 및 리소스 삭제](https://docs.microsoft.com/azure/azure-resource-manager/management/delete-resource-group?tabs=azure-powershell)

Azure 데이터 공유는 OS를 노출 하지 않거나 해당 리소스에 타사 소프트웨어 응용 프로그램을 설치할 수 있도록 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy을 사용 하 여 사용자 환경에서 프로 비전 할 수 있는 서비스를 제한 합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](../governance/policy/samples/built-in-policies.md#general) 

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침**: Azure 조건부 액세스를 사용 하 여 "Microsoft Azure 관리" 앱에 대 한 "액세스 차단"을 구성 함으로써 사용자의 Azure 리소스 관리자와 상호 작용 하는 기능을 제한 합니다.

- [Azure 리소스 관리자에 대 한 액세스를 차단 하도록 조건부 액세스를 구성 하는 방법](../role-based-access-control/conditional-access-azure-management.md) 

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure 보안 벤치 마크: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조 하세요.*

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: Azure devops를 사용 하 여 사용자 지정 Azure Policy 정의, Azure Resource Manager 템플릿 및 필요한 상태 구성 스크립트와 같은 코드를 안전 하 게 저장 하 고 관리할 수 있습니다. Azure DevOps에서 관리 하는 리소스에 액세스 하려면 Azure DevOps와 통합 된 경우 Azure Active Directory (Azure AD)에 정의 된 특정 사용자, 기본 제공 보안 그룹 또는 그룹에 대 한 권한을 부여 하거나 거부할 수 있습니다. 또는 TFS와 통합 된 경우 Active Directory 합니다.

- [Azure DevOps에 코드를 저장하는 방법](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Azure DevOps의 사용 권한 및 그룹 정보](/azure/devops/organizations/security/about-permissions) 

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스에 대 한 구성 관리 도구 배포

**지침**: "DataShare" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 시스템 구성을 경고, 감사 및 적용 하기 위한 사용자 지정 정책을 만듭니다. 또한 정책 예외를 관리하는 프로세스와 파이프라인을 개발합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md) 

- [별칭을 사용 하는 방법](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: 관리 되는 id를 사용 하 여 azure AD에서 자동으로 관리 되는 Id를 azure 서비스에 제공 합니다. 관리 id를 사용 하면 코드에 자격 증명 없이 Key Vault를 포함 하 여 Azure AD 인증을 지 원하는 모든 서비스에 인증할 수 있습니다.

- [Azure 리소스에 대 한 관리 id를 구성 하는 방법](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
