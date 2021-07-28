---
title: Stream Analytics에 대한 Azure 보안 기준
description: Stream Analytics 보안 기준은 Azure Security Benchmark에 지정된 보안 추천 사항을 구현하기 위한 절차 지침과 리소스를 제공합니다.
author: msmbaldwin
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 88e27b839e3f8baf9a1df8a6a6d936aecd602b23
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285119"
---
# <a name="azure-security-baseline-for-stream-analytics"></a>Stream Analytics에 대한 Azure 보안 기준

이 보안 기준은 [Azure Security Benchmark 버전 1.0](../security/benchmarks/overview-v1.md)의 지침을 Stream Analytics에 적용합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 Azure Security Benchmark에서 정의한 **보안 컨트롤** 및 Stream Analytics에 적용되는 관련 지침에 따라 그룹화됩니다. 

> [!NOTE]
> Stream Analytics에 적용되지 않거나 Microsoft의 책임인 **컨트롤** 은 제외되었습니다. Stream Analytics가 Azure Security Benchmark에 완전히 매핑되는 방식을 확인하려면 **[전체 Stream Analytics 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/stream-analytics-security-baseline-v1.1.xlsx)** 을 참조하세요.

## <a name="network-security"></a>네트워크 보안

자세한 내용은 [Azure Security Benchmark: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조하세요.

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: Azure Stream Analytics는 NSG(네트워크 보안 그룹) 및 Azure Firewall의 사용을 지원하지 않습니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: 가상 네트워크, 서브넷 및 네트워크 인터페이스의 구성 및 트래픽을 모니터링 및 기록

**지침**: Azure Stream Analytics는 가상 네트워크 및 서브넷의 사용을 지원하지 않습니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호 

**지침**: 해당 없음. 이 추천 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: Azure Security Center 위협 보호를 사용하여 알려진 악성 또는 사용하지 않는 인터넷 IP 주소를 통한 통신을 탐지하고 경고합니다.

- [Azure Security Center에서 Azure 서비스 계층에 대한 위협 방지](../security-center/azure-defender.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷 기록

**지침**: Azure Stream Analytics는 네트워크 보안 그룹을 사용하지 않으며, Azure Key Vault에 대한 흐름 로그는 캡처되지 않습니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 IDS/IPS(침입 탐지/침입 방지 시스템) 배포

**지침**: Azure Security Center 위협 보호를 사용하여 Azure 구독 환경의 비정상적이거나 잠재적으로 유해한 작업을 탐지합니다.

- [Azure Security Center에서 Azure 서비스 계층에 대한 위협 방지](../security-center/azure-defender.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침**: 해당 없음. 이 추천 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: Azure Stream Analytics는 가상 네트워크 및 네트워크 규칙 사용을 지원하지 않습니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지 

**지침**: Azure Stream Analytics는 가상 네트워크 및 네트워크 디바이스 사용을 지원하지 않습니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙 

**지침**: Azure Stream Analytics는 가상 네트워크 및 트래픽 구성 규칙의 사용을 지원하지 않습니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색 

**지침**: Azure 활동 로그를 사용하여 리소스 구성을 모니터링하고 Stream Analytics 리소스의 변경 내용을 검색합니다. Azure Monitor 내에서 중요한 리소스가 변경되면 트리거되는 경고를 만듭니다.

- [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Azure Monitor에서 경고를 만드는 방법](../azure-monitor/alerts/alerts-activity-log.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure Security Benchmark: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조하세요.*

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성 

**지침**: 감사 이벤트 및 요청과 같은 보안 데이터를 집계하기 위해 Azure Monitor를 통해 로그를 수집합니다. Azure Monitor 내에서 Log Analytics 작업 영역을 사용하여 분석을 쿼리 및 수행하고, 장기/보관 스토리지에 Azure Storage 계정을 사용하고, 필요에 따라 변경 불가능한 스토리지 및 적용된 보존 보류와 같은 보안 기능을 사용합니다. 

- [Azure Monitor를 사용하여 플랫폼 로그 및 메트릭을 수집하는 방법](../azure-monitor/essentials/diagnostic-settings.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: 관리, 보안 및 진단 로그에 액세스하기 위해 Azure Stream Analytics에서 진단 설정을 사용하도록 설정합니다. Azure 활동 로그 진단 설정을 사용하도록 설정하고, 로그를 동일한 Log Analytics 작업 영역 또는 스토리지 계정에 보낼 수도 있습니다.

- [Azure Stream Analytics는 검토할 진단 로그 및 작업 데이터를 제공합니다.](stream-analytics-job-diagnostic-logs.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.StreamAnalytics**:

[!INCLUDE [Resource Policy for Microsoft.StreamAnalytics 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.streamanalytics-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성 

**지침**: Azure Storage 계정 또는 Log Analytics 작업 영역에 보안 이벤트 로그를 저장하는 경우 조직의 요구 사항에 따라 보존 정책을 설정할 수 있습니다. 

- [Azure Stream Analytics는 검토할 진단 로그 및 작업 데이터를 제공합니다.](stream-analytics-job-diagnostic-logs.md)

- [Azure Storage 계정 로그에 대한 보존 정책을 구성하는 방법](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging) 

- [Log Analytics에서 데이터 보존 기간 변경](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: 비정상적인 동작에 대한 로그를 분석 및 모니터링하고 Stream Analytics 리소스에 대한 결과를 정기적으로 검토합니다. Azure Monitor의 Log Analytics 작업 영역을 사용하여 로그를 검토하고 로그 데이터에 대한 쿼리를 수행합니다. 또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다. 

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md) 

- [Log Analytics 작업 영역에 대한 자세한 정보](../azure-monitor/logs/log-analytics-tutorial.md)

- [Azure Monitor에서 사용자 지정 쿼리를 수행하는 방법](../azure-monitor/logs/get-started-queries.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상 활동에 대한 경고 사용

**지침**: Stream Analytics에 대한 진단 설정을 사용하도록 설정하고 Log Analytics 작업 영역으로 로그를 보냅니다. SOAR(보안 오케스트레이션 자동화 응답) 솔루션을 제공하므로 Log Analytics 작업 영역을 Azure Sentinel에 온보딩합니다. 이를 통해 플레이북(자동화된 솔루션)을 만들어 보안 문제를 수정하는 데 사용할 수 있습니다. 

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md) 

- [로그 분석 로그 데이터에 경고하는 방법](../azure-monitor/alerts/tutorial-response.md)  

- [Azure Stream Analytics는 검토할 진단 로그 및 작업 데이터를 제공합니다.](stream-analytics-job-diagnostic-logs.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화 

**지침**: 해당 없음. Stream Analytics는 맬웨어 방지 관련 로그를 처리하거나 생성하지 않습니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용 

**지침**: Azure Monitor의 Azure DNS 분석(미리 보기) 솔루션은 보안, 성능 및 작업 관련 정보를 DNS 인프라에 수집합니다. 현재는 Azure Stream Analytics를 지원하지 않지만 타사 DNS 로깅 솔루션을 사용할 수 있습니다. 

- [DNS 분석 미리 보기 솔루션으로 DNS 인프라에 대한 인사이트 수집](../azure-monitor/insights/dns-analytics.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용 

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [Azure Security Benchmark: ID 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정 인벤토리 유지 관리 

**지침**: Azure AD(Azure Active Directory)에는 명시적으로 할당되어야 하는 기본 제공 역할이 포함되어 있습니다. 멤버 자격을 검색하기 위해 역할을 쿼리할 수 있습니다. Azure AD PowerShell 모듈을 사용하여 임시 쿼리를 수행해서 관리 그룹의 멤버인 계정을 검색합니다.

- [PowerShell을 사용하여 Azure AD에서 디렉터리 역할을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrole)

- [PowerShell을 사용하여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrolemember)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우) 

**지침**: 서비스 관리를 위해 인증은 Azure AD(Azure Active Directory)와 함께 제공되고 Azure RBAC(Azure 역할 기반 액세스 제어)로 보호되므로 Stream Analytics는 기본 암호의 개념이 없습니다. 삽입 스트림 서비스와 출력 서비스에 따라 작업에 구성된 자격 증명을 순환해야 합니다.

- [Stream Analytics 작업에서 입력 및 출력을 위한 로그인 자격 증명 순환](stream-analytics-login-credentials-inputs-outputs.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용 

**지침**: 관리자 역할에 대한 최소 권한 액세스 원칙을 비롯한 모범 사례에 따라 ID 관리 및 역할 보안 계획을 만듭니다. Azure PIM(Privileged Identity Management)을 사용하여 Azure AD(Azure Active Directory) 및 Azure 리소스에 대한 Just-In-Time 권한 액세스를 제공합니다. Azure PIM 경고 및 감사 기록을 사용하여 관리 계정의 활동을 모니터링합니다. 손상되었을 수 있는 관리 계정을 식별하는 데 도움이 되도록 Azure AD 보안 보고서를 사용합니다.

- [자세한 정보](/azure/active-directory/privileged-identity-management/)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory Single Sign-On(SSO) 사용

**지침**: 가능하면 서비스별로 개별 독립 실행형 자격 증명을 구성하는 대신 Azure AD(Azure Active Directory) SSO를 사용합니다. Azure Security Center의 ID&amp; 및 액세스 권장 사항을 구현합니다.

- [Azure AD를 사용하는 SSO 이해](../active-directory/manage-apps/what-is-single-sign-on.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure AD(Azure Active Directory) 다단계 인증을 사용하도록 설정하고, Azure Security Center ID 및 액세스 관리 권장 사항에 따라 Stream Analytics 리소스를 보호합니다.

- [Azure에서 다단계 인증을 사용하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: 관리 작업에 안전한 Azure 관리 워크스테이션 사용

**지침**: 구성된 다단계 인증으로 PAW(권한 있는 액세스 워크스테이션)를 사용하여 Stream Analytics 리소스에 로그인하고 구성합니다.

- [Privileged Access Workstation에 대한 자세한 정보](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure에서 다단계 인증을 사용하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

**지침**: Azure AD(Azure Active Directory) 보안 보고서를 사용하여 환경에서 의심스럽거나 안전하지 않은 활동이 발생하면 로그 및 경고를 생성합니다. Azure Security Center를 사용하여 ID 및 액세스 활동을 모니터링합니다.

- [위험한 활동에 대해 플래그가 지정된 Azure AD 사용자를 식별하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center에서 사용자의 ID 및 액세스 활동을 모니터링하는 방법](../security-center/security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리 그룹에서만 액세스하도록 허용합니다. 

- [Azure에서 명명된 위치를 구성하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure AD(Azure Active Directory)를 중앙 인증 및 권한 부여 시스템으로 사용합니다. Azure AD는 Stream Analytics 리소스에 대한 클라이언트 액세스를 미세 제어하기 위해 Azure RBAC(역할 기반 액세스 제어)를 제공합니다.

- [Azure AD 인스턴스를 만들고 구성하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure AD(Azure Active Directory) 로그를 검토하여 스토리지 계정 관리 역할이 있는 계정을 비롯한 부실 계정을 검색할 수 있습니다. 또한 Azure ID 액세스 검토를 사용하여 그룹 멤버 자격, 엔터프라이즈 애플리케이션에 대한 액세스, 역할 할당을 효율적으로 관리합니다. 사용자 액세스를 정기적으로 검토하여 적합한 사용자만 계속 액세스할 수 있도록 합니다.

- [Azure AD 보고 이해](/azure/active-directory/reports-monitoring/)

- [Azure ID 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화된 자격 증명에 대한 액세스 시도 모니터링

**지침**: Azure Stream Analytics 및 Azure AD(Azure Active Directory)에 대한 진단 설정을 사용하도록 설정하여 모든 로그를 Log Analytics 작업 영역으로 보냅니다. Log Analytics 내에서 원하는 경고(예: 사용할 수 없는 비밀에 대한 액세스 시도)를 구성합니다.

- [Azure Monitor 로그에 Azure AD 로그 통합](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure AD(Azure Active Directory)의 위험 및 ID 보호 기능을 사용하여 Stream Analytics 리소스와 관련하여 검색된 의심스러운 동작에 대한 자동 응답을 구성합니다. 조직의 보안 대응을 구현하기 위해 Azure Sentinel을 통한 자동화된 대응을 사용하도록 설정해야 합니다.

- [Azure AD 위험한 로그인을 확인하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오 중 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**지침**: 해당 없음. 고객 Lockbox는 아직 Azure Stream Analytics에서 지원되지 않습니다.

- [일반 공급에서 지원되는 서비스 및 시나리오](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-protection"></a>데이터 보호

자세한 내용은 [Azure Security Benchmark: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조하세요.

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용하여 중요한 정보를 저장하거나 처리하는 Stream Analytics 리소스를 추적할 수 있도록 지원합니다. 

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 입력, 출력 및 스토리지 계정을 동일한 구독에 배치하여 Stream Analytics 작업을 격리합니다. 애플리케이션 및 엔터프라이즈 환경에서 요구하는 Stream Analytics 리소스에 대한 액세스 수준을 제어하도록 Stream Analytics를 제한할 수 있습니다. Azure AD(Azure Active Directory) RBAC를 통해 Azure Stream Analytics에 대한 액세스를 제어할 수 있습니다.

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

- [Azure Stream Analytics의 입력 이해](stream-analytics-add-inputs.md)

- [Azure Stream Analytics의 출력 이해](stream-analytics-define-outputs.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단 

**지침**: 데이터 손실 방지 기능은 아직 Azure Stream Analytics 리소스에서 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고, 고객 데이터 손실 및 노출로부터 보호합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

- [Azure Storage 계정을 보호하는 방법](../storage/blobs/security-recommendations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: 데이터 식별 기능은 아직 Azure Stream Analytics 리소스에서 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현합니다. 

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: 역할 기반 액세스 제어를 사용하여 리소스에 대한 액세스 제어

**지침**: Azure RBAC(역할 기반 액세스 제어)를 사용하여 사용자가 서비스와 상호 작용하는 방식을 제어합니다.

- [Azure RBAC를 구성하는 방법](../role-based-access-control/role-assignments-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: 모든 처리는 메모리 내에서 수행되므로 Stream Analytics는 들어오는 데이터를 저장하지 않습니다.  Stream Analytics에서 지속되어야 하는 쿼리 및 함수를 비롯한 모든 프라이빗 데이터는 구성된 스토리지 계정에 저장됩니다.  CMK(고객 관리형 키)를 사용하여 스토리지 계정에서 미사용 출력 데이터를 암호화합니다. CMK가 없는 경우에도 Stream Analytics는 인프라에서 최상의 암호화 표준을 자동으로 사용하여 데이터를 암호화하고 보호합니다.

- [Azure Stream Analytics의 데이터 보호](data-protection.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 기록 및 경고

**지침**: Azure 활동 로그와 함께 Azure Monitor를 사용하여 Azure Stream Analytics 리소스의 프로덕션 인스턴스가 변경되는 시점에 대한 경고를 생성합니다.

- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](../azure-monitor/alerts/alerts-activity-log.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="vulnerability-management"></a>취약성 관리

*자세한 내용은 [Azure Security Benchmark: 취약성 관리](../security/benchmarks/security-control-vulnerability-management.md)를 참조하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행

**지침**: Azure Stream Analytics 리소스 보안에 대한 Azure Security Center의 권장 사항에 따릅니다.

Microsoft는 Azure Stream Analytics를 지원하는 기본 시스템에서 취약성 관리를 수행합니다.

- [Azure Security Center 권장 사항 이해](../security-center/recommendations-reference.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화된 운영 체제 패치 관리 솔루션 배포

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: 타사 소프트웨어 타이틀을 위한 자동화된 패치 관리 솔루션 배포

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 연속 취약성 검색 비교

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**참고 자료**: Azure Security Center에서 제공하는 기본 위험 등급(보안 점수)을 사용합니다. 

- [Azure Security Center 보안 점수의 이해](../security-center/secure-score-security-controls.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure Security Benchmark: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화된 자산 검색 솔루션 사용

**지침**: Azure Resource Graph를 사용하여 구독 내 모든 리소스(예: 컴퓨팅, 스토리지, 네트워크, 포트, 프로토콜 등)를 쿼리/검색합니다. 테넌트에서 적절한 권한(읽기)을 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거합니다.

클래식 Azure 리소스는 Resource Graph를 통해 검색할 수 있지만 앞으로 Azure Resource Manager 리소스를 만들어 사용하는 것이 좋습니다.

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

**지침**: 해당하는 경우 태그 지정, 관리 그룹 및 별도의 구독을 사용하여 Azure Stream Analytics 리소스를 구성하고 추적합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

또한 Azure Policy에서 다음과 같은 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 유형을 제한합니다.

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: 승인된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스와 Azure 전체를 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

또한 Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리/검색합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스와 Azure 전체를 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 애플리케이션만 사용

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: 승인된 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Azure Resource Manager와 상호 작용하는 사용자 기능 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성하여 사용자가 Azure Resource Manager와 상호 작용하는 기능을 제한하도록 Azure 조건부 액세스를 구성합니다.

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 사용자가 컴퓨팅 리소스 내에서 스크립트를 실행하는 기능 제한

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**지침**: 해당 없음. 이 추천 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure Security Benchmark: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: “Microsoft.StreamAnalytics” 네임스페이스에서 Azure Policy 별칭을 사용하여 Azure Stream Analytics 구성을 감사하거나 적용하기 위한 사용자 지정 정책을 만듭니다. 다음과 같이 Azure Stream Analytics와 관련된 기본 제공 정책 정의를 활용할 수도 있습니다.

-Azure Stream Analytics에서 진단 로그를 사용하도록 설정해야 함

- [사용 가능한 Azure Policy 별칭을 보는 방법](/powershell/module/az.resources/get-azpolicyalias)

- [Azure Policy 기본 제공 정책 정의](../governance/policy/samples/built-in-policies.md)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: [거부] 및 [존재하지 않으면 배포] Azure 정책을 사용하여 보안 설정을 Azure 리소스 전체에 적용합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지 관리

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: Azure Repos를 사용하여 사용자 지정 Azure 정책, Azure Resource Manager 템플릿, Desired State Configuration 스크립트, 사용자 정의 함수, 쿼리를 비롯하여 코드를 안전하게 저장하고 관리할 수 있습니다. Azure DevOps에서 관리하는 리소스에 액세스하려면 Azure DevOps와 통합된 경우 Azure AD(Azure Active Directory) 또는 TFS와 통합된 경우 Azure AD에 정의된 특정 사용자, 기본 제공 보안 그룹 또는 하나 이상의 그룹에 권한을 부여하거나 거부할 수 있습니다.

- [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow)

- [Azure DevOps의 그룹 및 사용 권한 정보](/azure/devops/organizations/security/about-permissions)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스를 위한 구성 관리 도구 배포

**지침**: “Microsoft.StreamAnalytics” 네임스페이스에서 Azure Policy 별칭을 사용하여 시스템 구성을 경고, 감사 및 적용하는 사용자 지정 정책을 만듭니다. 또한 정책 예외를 관리하는 프로세스와 파이프라인을 개발합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 구성 관리 도구 배포

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대한 자동화된 구성 모니터링 구현

**지침**: “Microsoft.StreamAnalytics” 네임스페이스에서 Azure Policy 별칭을 사용하여 시스템 구성을 경고, 감사 및 적용하는 사용자 지정 정책을 만듭니다. Azure Policy의 [감사], [거부], [존재하지 않으면 배포]를 사용하여 Azure Stream Analytics 리소스에 대한 구성을 자동으로 적용합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대한 자동화된 구성 모니터링 구현

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: Stream Analytics 작업에서 사용 되는 입력 또는 출력 리소스의 연결 세부 정보는 구성된 스토리지 계정에 저장됩니다. 모든 데이터를 보호하기 위해 스토리지 계정을 암호화합니다.  또한 Stream Analytics 작업의 입력 또는 출력에 대한 자격 증명을 정기적으로 순환합니다.

- [Azure Stream Analytics의 데이터 보호](data-protection.md)

- [Stream Analytics 작업에서 입력 및 출력을 위한 로그인 자격 증명 순환](stream-analytics-login-credentials-inputs-outputs.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: 출력에 대한 관리되는 ID 인증은 연결 문자열을 사용하는 대신 Power BI, 스토리지 계정을 포함한 서비스에 Stream Analytics 작업 직접 액세스 권한을 제공합니다.

- [관리 ID를 사용하여 Azure Data Lake Storage Gen1에 대해 Stream Analytics 인증](stream-analytics-managed-identities-adls.md)

- [관리 ID를 사용하여 Azure Blob Storage 출력에 Azure Stream Analytics 작업 인증](blob-output-managed-identity.md)

- [관리 ID를 사용하여 Power BI에 Azure Stream Analytics 작업 인증](powerbi-output-managed-identity.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [Azure Security Benchmark: 맬웨어 방어](../security/benchmarks/security-control-malware-defense.md)를 참조하세요.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: 중앙 관리형 맬웨어 방지 소프트웨어 사용

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: Microsoft 맬웨어 방지는 Azure 서비스(예: Azure Stream Analytics)를 지원하는 기본 호스트에서 사용하도록 설정되지만 고객 콘텐츠에서는 실행되지 않습니다.

Azure 리소스(예: App Service, Stream Analytics, Blob Storage 등)로 업로드되는 콘텐츠를 미리 검사합니다. Microsoft는 이러한 인스턴스의 데이터에 액세스할 수 없습니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명이 업데이트되었는지 확인

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure Security Benchmark: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**지침**: 선택한 출력 서비스의 형식에 따라 달라집니다. 출력 서비스에 권장되는 지침에 따라 출력 데이터의 자동화된 백업을 수행할 수 있습니다.  사용자 정의 함수, 쿼리, 데이터 스냅샷을 포함하는 내부 데이터는 정기적으로 백업할 수 있는 구성된 스토리지 계정에 저장됩니다.

Microsoft Azure 스토리지 계정의 데이터는 내구성 및 고가용성을 보장하기 위해 항상 자동으로 복제됩니다. Azure Storage는 계획된 이벤트 그리고 일시적인 하드웨어 오류, 네트워크 또는 정전, 대규모 자연 재해 등의 계획되었거나 계획되지 않은 이벤트로부터 데이터를 보호하기 위해 데이터를 복사합니다. 동일한 데이터 센터, 동일한 지역 내의 영역 데이터 센터 또는 지리적으로 분리된 지역 간에 데이터를 복제하도록 선택할 수 있습니다. 

수명 주기 관리 기능을 사용하여 데이터를 보관 계층에 백업할 수도 있습니다.  또한 일시 삭제를 스토리지 계정에 저장된 백업에 사용하도록 설정합니다.

- [Azure Stream Analytics의 데이터 보호](https://docs.microsoft.com/azure/stream-analytics/data-protection#private-data-assets-that-are-stored)

- [Azure Storage 중복성 및 서비스 수준 계약 이해](../storage/common/storage-redundancy.md)

- [Azure Blob Storage 수명 주기 관리](../storage/blobs/storage-lifecycle-management-concepts.md)

- [Azure Storage Blob에 대한 일시 삭제](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업 수행 및 고객 관리형 키 백업

**지침**: 사용자 정의 함수, 쿼리, 데이터 스냅샷을 포함하는 내부 데이터는 정기적으로 백업할 수 있는 구성된 스토리지 계정에 저장됩니다.

스토리지 계정 지원 서비스에서 데이터를 백업하기 위해 azcopy 또는 타사 도구를 사용하는 등 여러 가지 방법을 사용할 수 있습니다. Azure Blob Storage에 대한 변경 불가능한 스토리지를 사용하면 사용자가 중요 비즈니스용 데이터 개체를 WORM(Write Once, Read Many) 상태로 저장할 수 있습니다. 이 상태는 사용자가 지정한 간격 동안 데이터를 지울 수 없고 수정할 수 없게 만듭니다.

- [Azure Stream Analytics의 데이터 보호](https://docs.microsoft.com/azure/stream-analytics/data-protection#private-data-assets-that-are-stored)

- [AzCopy 시작](../storage/common/storage-use-azcopy-v10.md)

- [Blob Storage에 대한 불변성 정책 설정 및 관리](../storage/blobs/storage-blob-immutability-policies-manage.md)

Azure CLI 또는 PowerShell을 사용하여 Azure Key Vault 내에서 고객 관리/제공 키를 백업할 수 있습니다.

- [Azure에서 키 자격 증명 모음 키를 백업하는 방법](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리형 키를 포함한 모든 백업의 유효성 검사

**지침**: 정기적으로 백업 데이터의 데이터 복원을 수행하여 데이터의 무결성을 테스트합니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리형 키 보호 보장

**지침**: Azure Storage 내에 저장된 Stream Analytics 백업은 기본적으로 암호화를 지원하며 해제할 수 없습니다.  백업을 중요한 데이터로 처리하고 이 기준의 일부로 관련 액세스 및 데이터 보호 제어를 적용해야 합니다.  

- [Azure Stream Analytics의 데이터 보호](https://docs.microsoft.com/azure/stream-analytics/data-protection#private-data-assets-that-are-stored)

- [Azure Storage의 데이터에 대한 액세스 권한 부여](../storage/common/storage-auth.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../security/benchmarks/security-control-incident-response.md)을 참조하세요.

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 탐지에서 인시던트 사후 검토까지의 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할도 정의하는 인시던트 대응 계획이 작성되어 있는지 확인합니다.

- [사용자 고유의 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [고객은 NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용하여 사용자 고유의 인시던트 대응 계획을 만들 수도 있음](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center에서 심각도를 각 경고에 할당하여 먼저 조사해야 하는 경고에 대한 우선 순위를 지정합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용되는 결과 또는 메트릭의 신뢰도 및 경고가 발생한 활동 배후에 악의적인 의도가 있었다는 신뢰 수준을 기준으로 합니다. 

또한 태그를 사용하여 구독(예: 프로덕션, 비 프로덕션)을 명확하게 표시하고 Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 명확하게 식별하고 분류하는 명명 시스템을 만듭니다.  인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다.

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md)

- [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md).

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: Azure 리소스를 보호하는 데 도움이 되도록 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 격차를 식별하고 필요에 따라 계획을 수정합니다.

- [NIST의 게시물을 참조하세요. IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 안내](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: Azure 리소스에 대한 위험을 식별하는 데 도움이 되도록 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다.

- [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 관한 "Logic Apps"를 통해 응답을 자동으로 트리거함으로써 Azure 리소스를 보호합니다.

- [워크플로 자동화와 Logic Apps를 구성하는 방법](../security-center/workflow-automation.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure Security Benchmark: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대한 침투 테스트를 정기적으로 수행 및 모든 중요한 보안 결과를 수정

**지침**: 다음 Microsoft 시행 규칙에 따라 침투 테스트에서 Microsoft 정책을 위반하지 않는지 확인합니다. https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 

- [다음 백서에서는 Microsoft 관리형 클라우드 인프라, 서비스 및 애플리케이션에 대한 Microsoft의 Red Teaming 및 라이브 사이트 침투 테스트 전략 및 실행에 대한 자세한 내용을 확인할 수 있습니다.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
