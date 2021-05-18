---
title: Site Recovery에 대한 Azure 보안 기준
description: Site Recovery 보안 기준은 Azure Security Benchmark에 지정된 보안 권장 사항을 구현하기 위한 절차 지침과 리소스를 제공합니다.
author: msmbaldwin
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 429bb1ffdf40ed9906082e00d4ffd1156a4e5e0b
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967849"
---
# <a name="azure-security-baseline-for-site-recovery"></a>Site Recovery에 대한 Azure 보안 기준

이 보안 기준은 [Azure Security Benchmark 버전 1.0](../security/benchmarks/overview-v1.md)에서 Site Recovery에 대한 지침을 적용합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 Azure Security Benchmark 및 Site Recovery에 적용되는 관련 지침에서 정의하는. **보안 컨트롤** 에 따라 그룹화됩니다. Site Recovery에 적용되지 않거나 Microsoft의 책임인 **컨트롤** 은 제외되었습니다.

Site Recovery가 Azure Security Benchmark에 완전히 매핑되는 방법을 확인하려면 [전체 Site Recovery 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조하세요.

## <a name="network-security"></a>네트워크 보안

자세한 내용은 [Azure Security Benchmark: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조하세요.

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: Microsoft Azure Site Recovery는 Azure Virtual Network로의 배포를 지원하지 않습니다. 네트워크를 통해 보안 통신을 적용하려면 Azure 프라이빗 엔드포인트를 사용하여 Site Recovery 서비스를 구성합니다.

- [Azure Site Recovery 개인 링크 지원](azure-to-azure-how-to-enable-replication-private-endpoints.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: Site Recovery 서비스는 고객이 특정 서비스 및 포트에 대한 트래픽만 열 수 있도록 하는 서비스 태그를 지원합니다. 고객은 방화벽 또는 네트워크 보안 그룹에 대한 "AzureSiteRecovery" 서비스 태그를 허용하여 Site Recovery 서비스에 대한 아웃바운드 액세스를 허용해야 합니다.

- [서비스 태그를 사용하여 아웃바운드 연결](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-using-service-tags)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

**지침**: 네트워크 보안 그룹 및 네트워크 보안/트래픽 흐름과 관련된 기타 리소스에 태그를 사용합니다. 개별 네트워크 보안 그룹 규칙의 경우 "설명" 필드를 사용하여 네트워크에서 보내고 받는 트래픽을 허용하는 규칙을 문서화합니다. 

태그 지정과 관련된 기본 제공 Azure Policy 정의(예: "태그 및 해당 값 필요")를 통합하여 모든 리소스에서 태그를 포함하도록 만들고 태그가 없는 기존 리소스를 알립니다. 

Azure PowerShell 또는 Azure CLI를 사용하여 태그를 기준으로 리소스에 대한 작업을 조회하거나 수행할 수 있습니다. 

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

- [Azure Virtual Network를 만드는 방법](../virtual-network/quick-create-portal.md) 

- [네트워크 보안 그룹 규칙을 사용하여 네트워크 트래픽을 필터링하는 방법](../virtual-network/tutorial-filter-network-traffic.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용하여 Site Recovery 서비스와 관련된 네트워크 리소스 구성에 대한 변경 내용을 모니터링합니다. Azure Monitor에서 경고를 만들어서 중요한 Site Recovery 네트워크 리소스가 변경될 때 알려줍니다.

- [Azure 활동 로그 이벤트를 보고 검색하기](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Azure Monitor를 사용하여 활동 로그 경고 만들기, 보기 및 관리하기](../azure-monitor/alerts/alerts-activity-log.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure Security Benchmark: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조하세요.*

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: 감사 로깅을 위해 Azure 활동 로그 진단 설정을 사용하도록 설정하고, 보관을 위해 로그를 Log Analytics 작업 영역, Azure Storage 계정 또는 Azure Event Hub로 보냅니다.

Azure 활동 로그 데이터를 사용하면 Azure 리소스에서 수행되는 모든 쓰기 작업(PUT, POST, DELETE)에 대한 "무엇을, 누가, 언제"를 판단할 수 있습니다.

Azure Monitor에서 Site Recovery 로그를 수집하여 생성된 보안 데이터를 집계합니다. Azure Monitor 내에서 Log Analytics 작업 영역을 사용하여 분석을 쿼리 및 수행하고, 스토리지 계정을 장기 또는 보관 스토리지에 사용할 수 있습니다. 또한 Azure Sentinel 또는 타사 SIEM(Security Incident and Event Management) 솔루션을 사용하도록 설정하고 데이터를 온보딩할 수 있습니다.

- [Azure 활동 로그에 대한 진단 설정을 사용하도록 설정하는 방법](../azure-monitor/essentials/activity-log.md)

- [Azure Monitor 로그를 사용하여 Site Recovery 모니터링](monitor-log-analytics.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: 감사 로깅을 위해 Azure 활동 로그 진단 설정을 사용하도록 설정하고, 보관을 위해 로그를 Log Analytics 작업 영역, Azure 스토리지 계정 또는 Azure 이벤트 허브로 보냅니다. 

Azure 활동 로그 데이터를 사용하면 Azure 리소스에서 수행되는 모든 쓰기 작업(PUT, POST, DELETE)에 대한 "무엇을, 누가, 언제"를 판단할 수 있습니다.

Azure Monitor에서 Site Recovery 로그를 수집하여 생성된 보안 데이터를 집계합니다. Azure Monitor 내에서 Log Analytics 작업 영역을 사용하여 분석을 쿼리 및 수행하고, 스토리지 계정을 장기/보관 스토리지에 사용할 수 있습니다. 데이터를 Azure Sentinel 또는 타사 SIEM(Security Incident and Event Management) 솔루션에 사용하도록 설정하고 온보딩합니다.

- [Azure 활동 로그에 대한 진단 설정을 사용하도록 설정하는 방법](../azure-monitor/essentials/activity-log.md)

- [Azure Monitor 로그를 사용하여 Site Recovery 모니터링](monitor-log-analytics.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침**: Azure Monitor를 사용하여 조직의 규정 준수 규정에 따라 Azure Recovery Services 자격 증명 모음과 연결된 Log Analytics 작업 영역에 대한 로그 보존 기간을 설정합니다. 

- [로그 보존 매개 변수를 설정하는 방법](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: Azure 활동 로그 진단 설정을 사용하도록 설정하고, 로그를 Log Analytics 작업 영역에 보냅니다. 

Log Analytics에서 쿼리를 수행하여 용어를 검색하고, 추세를 식별하고, 패턴을 분석하고, Recovery Services 자격 증명 모음에서 수집된 활동 로그 데이터에 대한 정보를 얻을 수 있습니다.

- [Site Recovery 모니터링](site-recovery-monitor-and-troubleshoot.md)

- [Azure 활동 로그에 대한 진단 설정을 사용하도록 설정하는 방법](../azure-monitor/essentials/activity-log.md)

- [Azure Monitor의 Log Analytics 작업 영역에서 Azure 활동 로그를 수집하고 분석하는 방법](../azure-monitor/essentials/activity-log.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상 활동에 대한 경고 사용

**지침**: Azure Monitor 로그 및 Log Analytics를 사용하여 Azure Site Recovery가 복제한 머신을 모니터링합니다. Azure Monitor 내에서 Log Analytics를 사용하여 로그 쿼리를 작성 및 테스트하고, 로그 데이터를 대화형으로 분석할 수 있습니다. Azure Monitor는 다른 모니터링 데이터와 함께 활동 및 리소스 로그를 수집합니다. 

로그 결과를 시각화하고 쿼리하며, 모니터링되는 데이터에 따라 작업을 수행하도록 경고를 구성합니다. SOAR(보안 오케스트레이션 자동화 응답) 솔루션을 제공하므로 Log Analytics 작업 영역의 경고를 Azure Sentinel에 설정합니다. 이를 통해 플레이북과 같은 자동화된 솔루션을 만들어 보안 문제를 수정하는 데 사용할 수 있습니다. Azure Monitor를 사용하여 Log Analytics 작업 영역에서 사용자 지정 로그 경고를 만듭니다. 

- [Site Recovery 모니터링](site-recovery-monitor-and-troubleshoot.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

- [Azure Monitor를 사용하여 로그 경고 만들기, 보기 및 관리](../azure-monitor/alerts/alerts-log.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [Azure Security Benchmark: ID 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정 인벤토리 유지 관리

**지침**: 기본적으로 역할은 할당되지 않습니다. 비즈니스 요구에 따라 명시적으로 할당해야 합니다. PowerShell CLI 또는 Azure AD(Azure Active Directory)를 통해 역할 할당을 확인하여 관리 그룹의 멤버인 계정을 검색할 수 있습니다.

- [PowerShell을 사용하여 Azure AD에서 디렉터리 역할을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrole)

- [PowerShell을 사용하여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrolemember)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다. Security Center의 ID 및 액세스 관리 기능을 사용하여 관리 계정 수를 모니터링합니다.

또한 전용 관리 계정을 추적하는 데 도움이 되도록 다음과 같은 Security Center 또는 기본 제공 Azure Policy의 권장 사항을 사용합니다. 

- 구독에 둘 이상의 소유자를 할당해야 합니다. 
- 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다. 

- 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.

관리 계정의 ID 및 액세스 제어를 추적하고 주기적으로 검토하는 프로세스를 만듭니다.

- [Azure Security Center를 사용하여 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

- [Azure Policy를 사용하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 SSO(Single Sign-On) 사용

**지침**: 서비스 사용자로 Azure 앱 등록을 사용하여 API 호출을 통해 Recovery Services 자격 증명 모음과 상호 작용하는 데 사용할 토큰을 검색합니다.

- [Azure REST API를 호출하는 방법](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Azure AD(Azure Active Directory)를 사용하여 클라이언트 애플리케이션(서비스 사용자)을 등록하는 방법](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Azure Recovery Services API 정보](/rest/api/recoveryservices)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure Active Directory 다단계 인증을 사용하도록 설정하고, Security Center의 ID 및 액세스 권장 사항을 따릅니다.

- [Azure Active Directory Multi-Factor Authentication 배포 계획](../active-directory/authentication/howto-mfa-getstarted.md)

- [ID 및 액세스 모니터링](../security-center/security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

**지침**: 관리 작업을 위한 Azure Active Directory Multi-Factor Authentication과 함께 안전한 Azure 관리 워크스테이션(PAW(Privileged Access Workstation)라고도 함)을 사용하여 Site Recovery 리소스에 대한 권한 있는 작업을 수행합니다. 

- [권한 있는 액세스 워크스테이션](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [클라우드 기반 Azure Active Directory Multi-Factor Authentication 배포 계획](../active-directory/authentication/howto-mfa-getstarted.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

**지침**: 환경에서 의심스럽거나 안전하지 않은 활동이 발생하는 경우 로그와 경고를 생성하기 위해 Azure AD(Azure Active Directory)의 PIM(Privileged Identity Management) 기능을 사용합니다.

Azure AD 위험 검색 기능을 사용하여 위험한 사용자 동작에 대한 경고 및 보고서를 봅니다.

- [PIM(Privileged Identity Management)을 배포하는 방법](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure AD 위험 탐지 이해](../active-directory/identity-protection/overview-identity-protection.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명된 위치를 사용하여 IP 주소 범위, 지역 또는 국가의 특정 논리적 그룹화에서만 Azure Portal에 액세스할 수 있도록 허용합니다.
- [Azure에서 명명된 위치를 구성하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Site Recovery에 대한 중앙 인증 및 권한 부여 시스템으로 Azure AD(Azure Active Directory)를 사용합니다. Azure AD는 미사용 데이터와 전송 중인 데이터에 강력한 암호화를 사용하여 데이터를 보호하며 사용자 자격 증명을 솔트 및 해시하고 안전하게 저장합니다.

- [Azure AD 인스턴스를 만들고 구성하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure AD(Azure Active Directory) 로그를 사용하여 부실 계정을 검색할 수 있습니다.

Azure AD의 ID 및 액세스 검토를 사용하여 그룹 멤버 자격, 엔터프라이즈 애플리케이션에 대한 액세스 및 역할 할당을 효율적으로 관리합니다.

액세스 검토가 완료된 사용자만 계속 액세스할 수 있도록 정기적으로 사용자 액세스를 검토하는 프로세스를 만듭니다.

- [Azure AD 보고 이해](/azure/active-directory/reports-monitoring/)

- [Azure ID 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화된 자격 증명에 대한 액세스 시도 모니터링

**지침**: Site Recovery 리소스에 대한 중앙 인증 및 권한 부여 시스템으로 Azure AD(Azure Active Directory)를 사용합니다. Azure AD는 미사용 데이터와 전송 중인 데이터에 강력한 암호화를 사용하여 데이터를 보호하며 사용자 자격 증명을 솔트 및 해시하고 안전하게 저장합니다.

Azure AD 로그인 활동, 감사 및 위험 이벤트 로그 원본에 액세스할 수 있으며, 이를 통해 Azure Marketplace에서 사용할 수 있는 Azure 센티널 또는 SIEM 또는 모니터링 도구와 통합할 수 있습니다.

Azure AD 사용자 계정에 대한 진단 설정을 만들고 감사 및 로그인 로그를 Log Analytics 작업 영역으로 보내서 이 프로세스를 간소화합니다. Log Analytics 작업 영역 내에서 원하는 경고를 구성할 수 있습니다.

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure AD(Azure Active Directory)를 Recovery Services 자격 증명 모음의 중앙 인증 및 권한 부여 시스템으로 사용합니다.

계정 로그인 동작 감지를 위해 Azure AD의 ID 보호 기능을 사용하고 사용자 ID와 관련하여 감지된 의심스러운 동작에 대한 자동 응답을 구성합니다. 또한 추가 조사를 위해 데이터를 Azure Sentinel에 수집합니다.

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

**지침**: 개발, 테스트 및 프로덕션 Recovery Services 자격 증명 모음에 대한 별도의 구독 또는 관리 그룹을 구현합니다. 리소스를 가상 네트워크 또는 서브넷으로 구분하고, 적절하게 태그를 지정하고, 네트워크 보안 그룹 또는 Azure Firewall로 보호합니다. 

사용하지 않을 때는 민감한 데이터를 저장하거나 처리하는 가상 머신을 끕니다. 이를 되풀이 프로세스로 설정하려면 정책과 프로시저를 구현합니다. 

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [Site Recovery 개요](site-recovery-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: 개인 링크 또는 프라이빗 엔드포인트, 네트워크 보안 그룹 및 서비스 태그를 사용하여 Site Recovery를 지원하는 가상 머신에서 데이터 반출 기회를 줄입니다.

Microsoft는 Site Recovery에서 사용하는 기본 플랫폼을 관리하고 모든 고객 콘텐츠를 중요하게 취급하며 고객 데이터 손실 및 노출을 방지합니다. Microsoft는 Azure 내의 고객 데이터를 안전하게 유지하기 위해 강력한 데이터 보호 제어 및 기능 제품군을 구현하고 유지합니다. 

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

- [Azure 프라이빗 엔드포인트를 사용하여 가상 머신 복제](azure-to-azure-how-to-enable-replication-private-endpoints.md)

- [Azure Site Recovery 서비스 태그를 사용하여 가상 머신 복제](azure-to-azure-about-networking.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: Site Recovery는 Azure 워크로드 서버에서 Recovery Services 자격 증명 모음 뒤에서 호스트되는 Site Recovery 서비스에 이르기까지, AES 256(Advanced Encryption Standard)을 사용하여 암호화된 보안 https 채널을 사용합니다.

Site Recovery에 지원되는 현재 TLS 버전은 TLS 1.0, TLS 1.1, TLS 1.2(2019년 말까지 활성화됨)입니다. TLS 1.2는 어떤 새 지역에서든 지원되는 유일한 TLS 버전입니다.

- [Azure Site Recovery에 대한 전송 시 암호화 이해](physical-azure-set-up-source.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: 데이터 식별, 분류, 손실 방지 기능은 Site Recovery에서 아직 사용할 수 없습니다. 

규정 준수를 위해 필요한 경우 타사 솔루션을 구현합니다.

Microsoft는 Site Recovery에서 사용하는 기본 플랫폼을 관리하고 모든 고객 콘텐츠를 중요하게 취급하며 고객 데이터 손실 및 노출을 방지합니다. Azure 내의 고객 데이터를 안전하게 유지하기 위해 강력한 데이터 보호 제어 및 기능 제품군을 구현하고 유지합니다. 

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 관리

**지침**: Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 Site Recovery 리소스와 관련된 데이터 및 리소스에 대한 액세스를 관리합니다. 

Azure RBAC로 업무를 분리하고 필요한 적절한 액세스 권한을 부여합니다. 기본 제공 Site Recovery 역할을 사용하여 Site Recovery 관리 작업을 제어합니다.

- [Azure RBAC를 구성하는 방법](../role-based-access-control/role-assignments-portal.md)

- [역할 기반 액세스 제어를 사용하여 Azure Site Recovery 관리](site-recovery-role-based-linked-access-control.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: 플랫폼과 고객 관리형 키를 모두 사용하여 이중 암호화를 사용하도록 설정합니다. 이 기능은 Site Recovery에서 사용할 수 있습니다. 

Site Recovery는 데이터에 대한 미사용 데이터 암호화를 지원합니다. Azure IaaS 워크로드의 경우 SSE(스토리지 서비스 암호화)를 사용하여 미사용 데이터가 암호화됩니다. 

고객 관리형 키로 암호화된 Recovery Services 자격 증명 모음을 사용하는 동안에는 고객만 암호화 키에 액세스할 수 있습니다. Microsoft는 복사본을 유지 관리하지 않고, 키에 대한 액세스 권한이 없으며, 언제든지 주 위치에서 재해 복구 위치로 전송된 데이터의 암호를 해독하지 않습니다. 

- [Azure Site Recovery에 대한 고객 관리형 키 지원](azure-to-azure-how-to-enable-replication-cmk-disks.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 기록 및 경고

**지침**: Azure 활동 로그에 Azure Monitor를 사용하여 중요한 리소스가 변경될 때 경고를 생성합니다. 이러한 리소스에는 Recovery Services 자격 증명 모음의 프로덕션 인스턴스, Site Recovery 서비스 및 관련 리소스가 포함될 수 있습니다.
- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](../azure-monitor/alerts/alerts-activity-log.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure Security Benchmark: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화된 자산 검색 솔루션 사용

**지침**: Azure Resource Graph를 사용하여 구독 내의 모든 리소스(Recovery Services 자격 증명 모음 포함)를 쿼리하고 발견합니다. 테넌트에서 적절한 읽기 권한을 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거합니다.

Resource Graph를 통해 클래식 Azure 리소스를 검색할 수 있지만 앞으로는 Azure Resource Manager 리소스를 만들어서 사용하는 것이 좋습니다.

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

- [Azure 구독을 확인하는 방법](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC 이해](../role-based-access-control/overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: Site Recovery에서 메타데이터와 함께 사용하는 Recovery Services 자격 증명 모음 및 기타 관련 리소스에 태그를 적용하여 논리적으로 분류 체계로 구성합니다.
- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 해당하는 경우 태그 지정, 관리 그룹, 별도의 구독을 사용하여 Site Recovery(Recovery Services 자격 증명 모음) 및 기타 관련 리소스를 구성하고 추적합니다. 

또한 Azure Policy에서 다음과 같은 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다. 

- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: 승인된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 고객의 조직 요구 사항에 따라 컴퓨팅 리소스에 대해 승인된 Azure 리소스 및 승인된 소프트웨어의 인벤토리를 만듭니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy에서 다음의 기본 제공 정책 정의를 사용해 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다. 

- 허용되지 않는 리소스 종류 
- 허용되는 리소스 유형

Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리하고 검색합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy에서 다음의 기본 제공 정책 정의를 사용해 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.

- 허용되지 않는 리소스 종류 
- 허용되는 리소스 유형

회사 표준 및 서비스 수준 계약의 준수를 유지하는 데 있어 Azure에서 정책을 만들고 관리하는 방법을 이해하는 것이 중요합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](/azure/governance/policy/samples)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Azure Resource Manager와 상호 작용하는 사용자 기능 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성함으로써 Azure 조건부 액세스를 사용하여 Azure Resource Manager와 상호 작용하는 사용자 기능을 제한합니다. 이렇게 하면 높은 보안 환경 내의 리소스 생성 및 변경을 방지할 수 있습니다.

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure Security Benchmark: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Policy를 사용하여 Recovery Services 자격 증명 모음에 대한 표준 보안 구성을 정의하고 구현합니다. 

“Microsoft.RecoveryServices” 네임스페이스에서 Azure Policy 별칭을 사용하여 Site Recovery 서비스의 Recovery Services 자격 증명 모음 구성을 감사 또는 적용하는 사용자 지정 정책을 만듭니다.
- [사용 가능한 Azure Policy 별칭을 보는 방법](/powershell/module/az.resources/get-azpolicyalias)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: [거부] 및 [존재하지 않으면 배포] Azure Policy 효과를 사용하여 보안 설정을 Azure 리소스 전체에 적용합니다.
- [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: Recovery Services 자격 증명 모음 및 관련 리소스에 대한 사용자 지정 Azure Policy 정의를 사용하는 경우 코드를 안전하게 저장하고 관리하려면 Azure Repos을 선택합니다.

- [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow)

- [Azure Repos 설명서](/azure/devops/repos/)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스를 위한 구성 관리 도구 배포

**지침**: "Microsoft.RecoveryServices" 네임스페이스의 Azure Policy 별칭과 함께 기본 제공된 Azure Policy 정의를 사용하여 시스템 구성을 경고, 감사 및 적용하는 사용자 지정 정책을 만듭니다. 

또한 정책 예외를 관리하는 프로세스와 파이프라인을 개발합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대한 자동화된 구성 모니터링 구현

**지침**: "Microsoft.RecoveryServices" 네임스페이스의 Azure Policy 별칭과 함께 기본 제공된 Azure Policy 정의를 사용하여 시스템 구성을 경고, 감사 및 적용하는 사용자 지정 정책을 만듭니다. 

Azure Policy [감사], [거부] 및 [존재하지 않으면 배포] 효과를 사용하여 Azure 리소스에 대한 구성을 자동으로 적용합니다.
- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: 고객은 Azure Key Vault와 통합된 Site Recovery 암호를 관리하고 Azure Disk Encryption 지원 가상 머신에 대한 재해 복구를 사용하도록 설정해야 합니다. 

- [를 만드는 방법](../key-vault/general/quick-create-portal.md)

- [Key Vault에 인증하는 방법](../key-vault/general/authentication.md)

- [Key Vault 액세스 정책을 할당하는 방법](../key-vault/general/assign-access-policy-portal.md)

- [Site Recovery를 사용하여 Azure Disk Encryption 지원 가상 머신에 대해 DR을 사용하도록 설정하는 방법](azure-to-azure-how-to-enable-replication-ade-vms.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: Site Recovery는 고객이 Recovery Services 자격 증명 모음에서 시스템 관리 ID를 사용하도록 설정할 수 있는 시스템 관리 ID만 지원합니다. 액세스 경계를 정의하기 위해 Disaster Recovery 제품에서 사용되는 리소스에 동일한 방법론이 적용됩니다.

관리 ID를 사용하여 Azure AD(Azure Active Directory)에서 자동으로 관리 ID를 Azure 서비스에 제공합니다.

관리 ID를 사용하면 코드에 자격 증명 없이 Key Vault를 포함하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있습니다.

- [Azure 관리 ID와 통합하는 방법](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity?tabs=core2x)

- [Recovery Services 자격 증명 모음에서 시스템 관리 ID를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-private-endpoints#enable-the-managed-identity-for-the-vault)

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

**지침**: Microsoft Antimalware는 Azure 서비스(예: Site Recovery)를 지원하는 기본 호스트에서 사용하도록 설정되어 있지만 콘텐츠에서 실행되지 않습니다. 비컴퓨팅 Azure 리소스(예: App Service, Data Lake Storage 및 Blob Storage)로 업로드되는 모든 파일을 미리 검사합니다.

Data Services를 위한 Security Center의 위협 탐지를 사용하여 스토리지 계정에 업로드된 맬웨어를 탐지합니다.

- [Azure Cloud Services 및 Virtual Machines용 Microsoft Antimalware 이해](../security/fundamentals/antimalware.md)

- [데이터 서비스에 대한 Azure Security Center의 위협 탐지 이해](../security-center/azure-defender.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure Security Benchmark: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조하세요.*

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업 수행 및 고객 관리형 키 백업

**지침**: Site Recovery는 고객이 워크로드에 대해 구성된 대로 Disaster Recovery 솔루션의 상태를 유지하기 위해 내부적으로 Azure Storage 계정을 사용합니다.

유형 구성에서 Site Recovery services 메타데이터에 사용되는 모든 스토리지 리소스(RA-GRS(읽기 액세스 지역 중복 스토리지))입니다. GRS 이상의 유형의 스토리지 계정(예: RAGRS, RAG-ZRS)은 데이터를 보조 지역(원본 데이터의 주 위치에서 수백 마일 떨어진 위치)에 복제하여 중단 중에도 고객에게 재해 복구 서비스를 계속 제공합니다.

이는 고객 범위를 벗어난 것이므로 Site Recovery 팀이 내부적으로 처리합니다. 고객은 Azure에서 Key Vault 키를 백업할 수 있습니다.

- [Azure에서 키 자격 증명 모음 키를 백업하는 방법](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 컨트롤과 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft RecoveryServices**:

[!INCLUDE [Resource Policy for Microsoft.RecoveryServices 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.recoveryservices-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리형 키를 포함한 모든 백업의 유효성 검사

**지침**: 백업된 고객 관리형 키의 복원을 주기적으로 테스트합니다.

- [Azure에서 키 자격 증명 모음 키를 복원하는 방법](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리형 키 보호 보장

**지침**: 미사용 데이터는 Azure의 IaaS(Infrastructure as a Service) 기반 가상 머신과 함께 SSE(Storage Service Encryption)를 사용하여 암호화됩니다. Key Vault에서 일시 삭제를 사용하도록 설정하여 실수로 또는 악의적으로 삭제되지 않도록 키를 보호합니다.

- [Key Vault에서 일시 삭제를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/storage/blobs/soft-delete-blob-overview?tabs=azure-portal)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../security/benchmarks/security-control-incident-response.md)을 참조하세요.

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 

탐지에서 인시던트 사후 검토까지의 인시던트 처리 또는 관리 단계뿐만 아니라 담당자의 모든 역할도 정의하는 인시던트 대응 계획이 작성되어 있는지 확인합니다.

- [Azure Security Center 내에서 워크플로 자동화를 구성하는 방법](../security-center/security-center-planning-and-operations-guide.md)

- [자체 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [고객이 NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용하여 사용자 고유의 인시던트 대응 계획을 만들 수도 있음](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center의 할당된 경고-심각도에 따라 먼저 조사해야 하는 경고의 우선 순위를 지정합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용되는 결과 또는 분석의 신뢰도 및 경고가 발생한 활동에 악의적인 의도가 있었다는 신뢰 수준을 기준으로 합니다.

구독(예: 프로덕션, 비프로덕션)을 명확하게 표시하고 Azure 리소스를 명확하게 식별 및 범주화하는 명명 시스템을 만듭니다.

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md) 

- [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md).

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 결함을 식별하고 필요에 따라 계획을 수정합니다.

- [NIST의 게시물을 참조하세요. IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 안내](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 고객 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 

인시던트를 검토하고 발생 후 문제가 해결되었는지 확인하는 프로세스를 만듭니다.

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: 연속 내보내기 기능을 사용하여 Security Center 경고 및 추천 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. 

필요에 따라 Azure Security Center 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림합니다.
- [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Security Center의 워크플로 자동화 기능을 사용하여 "Logic Apps"를 통해 보안 경고 및 권장 사항에 대한 응답을 자동으로 트리거합니다.
- [워크플로 자동화와 Logic Apps를 구성하는 방법](../security-center/workflow-automation.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure Security Benchmark: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대한 침투 테스트를 정기적으로 수행 및 모든 중요한 보안 결과를 수정

**지침**: Microsoft 클라우드 침투 테스트 참여 규칙을 따라서 침투 테스트가 Microsoft 정책을 위반하지 않도록 하세요. Microsoft의 전략과 Microsoft에서 관리하는 클라우드 인프라, 서비스, 애플리케이션에 대한 레드 팀 실행 및 실시간 사이트 침투 테스트를 사용합니다.

- [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud 레드 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
