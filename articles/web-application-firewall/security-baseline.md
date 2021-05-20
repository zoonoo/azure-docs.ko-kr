---
title: Azure Web Application Firewall에 대한 Azure 보안 기준
description: Azure Web Application Firewall 보안 기준은 Azure Security Benchmark에 지정된 보안 권장 사항을 구현하기 위한 절차 지침과 리소스를 제공합니다.
author: msmbaldwin
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ae6b4f38772cd6c6755ece78fb5c47834a616204
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211637"
---
# <a name="azure-security-baseline-for-azure-web-application-firewall"></a>Azure Web Application Firewall에 대한 Azure 보안 기준

이 보안 기준은 [Azure Security Benchmark 버전 1.0](../security/benchmarks/overview-v1.md)의 지침을 Azure Web Application Firewall에 적용합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 Azure Security Benchmark에서 정의된 **보안 제어** 및 Azure Web Application Firewall에 적용되는 관련 지침에 따라 그룹화됩니다. Azure Web Application Firewall에 적용할 수 없는 **컨트롤** 은 제외되었습니다. 

Azure Web Application Firewall이 Azure Security Benchmark에 완전히 매핑되는 방법을 확인하려면 [전체 Cognitive Services 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [Azure Security Benchmark: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조하세요.*

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: Microsoft Azure WAF(Web Application Firewall)를 사용하여 SQL 삽입 및 교차 사이트 스크립팅과 같은 일반적인 악용 및 취약성으로부터 웹 애플리케이션을 중앙 집중식으로 보호합니다. 

- 검색 모드: 이 모드를 사용하여 네트워크 트래픽을 파악하고 가양성을 이해하고 검토합니다. 모든 위협 경고를 모니터링하고 기록합니다. 진단 및 WAF 로그가 선택되어 있고 설정되어 있는지 확인합니다. WAF는 검색 모드에서 작동할 때 들어오는 요청을 차단하지 않습니다.
- 방지 모드: 규칙에서 탐지하는 침입 및 공격을 차단합니다. 공격자는 "403 무단 액세스" 예외를 받고, 연결이 닫힙니다. 방지 모드는 이러한 공격을 WAF 로그에 기록합니다.

WAF의 검색 모드를 사용하여 네트워크 트래픽을 기준으로 합니다. 트래픽 요구 사항이 확인되면 원하지 않는 트래픽을 차단하도록 WAF를 방지 모드로 구성합니다.

WAF로 보호되지 않는 웹 사용 리소스에 대해 Security Center의 높은 심각도 권장 사항을 따릅니다.  

- [웹 애플리케이션 방화벽 CRS 규칙 그룹 및 규칙](ag/application-gateway-crs-rulegroups-rules.md) 

- [Application Gateway의 WAF 모드](ag/ag-overview.md#waf-modes)

- [Front Door의 WAF 모드](afds/afds-overview.md#waf-modes)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: Azure WAF(Web Application Firewall)에서 사용자 지정 규칙을 사용하여 트래픽을 허용하고 차단합니다. 예를 들어 IP 주소 범위에서 들어오는 모든 트래픽을 차단할 수 있습니다. 규칙에서 탐지한 침입 및 공격을 차단하는 방지 모드에서 실행되도록 Azure WAF를 구성합니다. 공격자는 "403 무단 액세스" 예외를 받고, 연결이 닫힙니다. 방지 모드는 이러한 공격을 WAF 로그에 기록합니다.

- [Application Gateway의 WAF 모드](ag/ag-overview.md#waf-modes)

- [Front Door의 WAF 모드](afds/afds-overview.md#waf-modes)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침**: Azure WAF(Web Application Firewall)는 Azure의 웹 애플리케이션 보호의 핵심 구성 요소입니다. Azure WAF를 사용하여 OWASP 상위 10개 범주의 알려진 공격 서명에 대해 미리 구성된 관리 규칙 집합을 사용하여 일반적인 악용 및 취약성으로부터 웹 애플리케이션을 중앙 집중식으로 보호합니다.

애플리케이션 요구 사항에 맞게 규칙 및 규칙 그룹을 사용하여 Azure WAF를 사용자 지정하고 가양성을 제거합니다. 사이트별로 구성할 수 있도록 WAF 뒤의 각 사이트에 대한 Azure WAF 정책을 연결합니다. Azure WAF는 지역 필터링, 속도 제한, Azure에서 관리하는 기본 규칙 집합 규칙을 지원합니다. 그리고 사용자 지정 규칙을 애플리케이션 요구 사항에 맞게 만들 수 있습니다. 

결정된 기간 동안 네트워크 트래픽의 기준을 방지 모드로 지정한 후 방지 모드에서 실행되도록 Azure WAF를 구성합니다. Azure WAF는 방지 모드의 규칙에서 탐지하는 침입 및 공격을 차단합니다. 공격자는 "403 무단 액세스" 예외를 받고, 연결이 닫힙니다. 방지 모드는 이러한 공격을 WAF 로그에 기록합니다.

- [Application Gateway의 WAF 모드](ag/ag-overview.md#waf-modes)

- [Front Door의 WAF 모드](afds/afds-overview.md#waf-modes)

- [웹 애플리케이션 방화벽 CRS 규칙 그룹 및 규칙](ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: 일반적인 애플리케이션(예: Apache 및 IIS)의 잘못된 구성을 검색하기 위한 태그를 사용하여 Azure 구독에서 리소스를 만들고, 연결하고, 논리적으로 구성합니다. 

적용된 태그 메타데이터에 따라 규칙 및 규칙 그룹을 Azure WAF(Web Application Firewall) 정책에 적용합니다.

- [Application Gateway의 WAF 정책](/cli/azure/network/application-gateway/waf-policy) 

- [Front Door의 WAF 정책](/cli/azure/ext/front-door/network/front-door/waf-policy)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

**지침**: Azure Application Gateway 서브넷의 Azure WAF(Web Application Firewall)와 연결된 네트워크 보안 그룹 및 트래픽 흐름과 관련된 다른 리소스에 대한 태그를 사용합니다. 개별 네트워크 보안 그룹 규칙의 경우 "설명" 필드를 사용하여 네트워크에서 보내고 받는 트래픽을 허용하는 모든 규칙에 대한 비즈니스 요구 사항, 기간 등을 지정합니다.

태그 지정과 관련된 기본 제공 Azure Policy 정의(예: "태그 및 해당 값 필요")를 사용하여 모든 리소스를 태그가 지정된 상태로 만들고 태그가 지정되지 않은 기존 리소스를 알려줍니다.

Azure PowerShell 또는 Azure CLI를 선택하여 태그를 기준으로 리소스에 대한 작업을 조회하거나 수행합니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

- [Virtual Network를 만드는 방법](../virtual-network/quick-create-portal.md)

- [보안 구성을 사용하여 NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용하여 네트워크 리소스 구성을 모니터링하고, Azure WAF(Web Application Firewall) 배포와 관련된 네트워크 설정 및 리소스에 대한 변경 내용을 검색합니다. Azure Monitor 내에서 중요한 네트워크 리소스가 변경될 때 트리거되는 경고를 만듭니다.

- [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Azure Monitor에서 경고를 만드는 방법](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure Security Benchmark: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조하세요.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 원본 사용

**지침**: UDP를 통한 123 포트와 같은 적절한 포트 및 프로토콜을 사용하여 NTP 서버에 액세스할 수 있도록 Azure WAF(Web Application Firewall)에 대한 네트워크 규칙을 만듭니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure WAF(Web Application Firewall) 로그를 중앙 보안 로그 관리 솔루션(예: Azure Sentinel 또는 타사 SIEM)에 보도록 구성합니다. 이러한 로그에는 Azure 활동, 진단 및 실시간 WAF 로그가 포함되며, 다양한 도구(예: Azure Monitor, Excel 및 Power BI)에서 이러한 로그를 볼 수 있습니다. Azure Web Application Firewall 로그는 Azure WAF에서 평가, 일치 및 차단하는 데이터에 대한 인사이트를 제공합니다.

Azure Sentinel에는 Azure WAF의 보안 이벤트에 대한 개요를 제공하는 기본 제공 Azure WAF 통합 문서가 있습니다. 이 통합 문서에는 이벤트, 일치하는 규칙, 차단된 규칙 및 방화벽 로그에 기록되는 기타 모든 항목이 포함됩니다. 이 원격 분석은 플레이북 자동화를 시작하여 Azure Sentinel에서 수집한 WAF 이벤트에 따라 알림을 받거나 수정 작업을 수행하는 데 사용할 수 있습니다.

- [활동 로그 보기](../azure-resource-manager/management/view-activity-logs.md)

- [Application Gateway에 대한 진단 로그](../application-gateway/application-gateway-diagnostics.md)

- [Microsoft 웹 애플리케이션 방화벽의 데이터를 Azure Sentinel에 연결](../sentinel/connect-azure-waf.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: 감사, 보안 및 진단 로그에 액세스할 수 있도록 Azure WAF(Web Application Firewall) 리소스에 대한 로깅을 사용하도록 설정합니다. Azure Web Application Firewall은 구성된 진단 로그에서 사용할 수 있는 탐지된 각 위협에 대한 자세한 보고를 제공합니다. 자동으로 사용할 수 있는 활동 로그에는 이벤트 원본, 날짜, 사용자, 타임스탬프, 원본 주소, 대상 주소 및 기타 유용한 요소가 포함됩니다.

- [로깅 개요](ag/ag-overview.md#logging)

- [Azure Monitor 로그 쿼리 개요](../azure-monitor/logs/log-query-overview.md)

- [Azure 플랫폼 로그 개요](../azure-monitor/essentials/platform-logs-overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침**: Azure WAF(Web Application Firewall) 로그를 사용자 지정 스토리지 계정에 보내고, 보존 정책을 정의합니다. Azure Monitor를 사용하여 조직의 규정 준수 요구 사항에 따라 Log Analytics 작업 영역 보존 기간을 설정합니다.
- [스토리지 계정에 대한 모니터링 구성](../storage/common/manage-storage-analytics-logs.md#configure-logging)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: Azure WAF(Web Application Firewall)는 탐지된 각 위협에 대한 자세한 보고를 제공합니다. 로깅은 감사 및 문제 해결에 중요한 작업 및 오류에 대한 풍부한 정보를 제공하는 Azure Diagnostics 로그와 통합됩니다. 

Azure WAF 인스턴스는 Security Center와 통합되어 보고에 대한 경고 및 상태 정보를 보냅니다. Security Center의 권장 사항을 사용하여 보호되지 않는 웹 애플리케이션을 검색하고 이러한 취약한 리소스를 보호합니다. 

Azure Sentinel에는 WAF의 보안 이벤트에 대한 개요를 제공하는 기본 제공 WAF - 방화벽 이벤트 통합 문서가 있습니다. 이러한 통합 문서에는 이벤트, 일치하는 규칙, 차단된 규칙 및 방화벽 로그에 기록되는 기타 모든 항목이 포함됩니다.

- [진단 설정을 Azure 활동 로그에 사용하도록 설정하는 방법](../azure-monitor/index.yml) 

- [진단 설정을 Azure Application Gateway에 사용하도록 설정하는 방법](../application-gateway/application-gateway-diagnostics.md)

- [Azure Front Door에서 메트릭 및 로그 모니터링](../frontdoor/front-door-diagnostics.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상 활동에 대한 경고 사용

**지침**: Azure 활동 로그 진단 설정 및 Azure WAF에 대한 진단 설정을 사용하도록 설정하고, 로그를 Log Analytics 작업 영역에 보냅니다. Log Analytics에서 쿼리를 수행하여 용어를 검색하고, 추세를 파악하며, 패턴을 분석하고, 수집된 데이터를 기반으로 하여 다양한 인사이트를 제공합니다. WAF 메트릭을 기반으로 하여 비정상 활동에 대한 경고를 만듭니다. 예를 들어 'X'개를 초과하는 요청 수가 차단되면 'Y'를 수행합니다.

- [진단 설정을 Azure 활동 로그에 사용하도록 설정하는 방법](../azure-monitor/essentials/activity-log.md)

- [Azure 내에서 경고를 만드는 방법](../azure-monitor/alerts/tutorial-response.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: 들어오는 트래픽을 추가로 검사하기 위해 중요한 웹 애플리케이션 앞에 Azure WAF(Web Application Firewall)를 배포합니다. 

Azure WAF는 일반적인 악용 및 취약성으로부터 웹 애플리케이션을 중앙 집중식으로 보호하고, SQL 삽입, 교차 사이트 스크립팅, 맬웨어 업로드 및 DDoS 공격과 같은 공격을 차단하기 위해 인바운드 웹 트래픽을 검사하여 앱을 보호합니다.

- [Azure WAF를 배포하는 방법](ag/create-waf-policy-ag.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [Azure Security Benchmark: ID 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정 인벤토리 유지 관리

**지침**: Azure AD(Azure Active Directory)에는 쿼리 가능하고 명시적으로 할당해야 하는 기본 제공 역할이 있습니다. Azure AD PowerShell 모듈을 사용하여 관리 그룹의 멤버인 계정을 검색하는 임시 쿼리를 수행합니다.

- [PowerShell을 사용하여 Azure AD에서 디렉터리 역할을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [PowerShell을 사용하여 Azure AD에서 디렉터리 역할 멤버를 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: WAF 내에서 사용할 수 있는 로컬 관리자 할당이 없습니다. 그러나 Azure WAF 리소스에 대한 관리를 제어할 수 있는 Azure AD(Azure Active Directory) 관리자 역할이 환경에 있을 수 있습니다.
Azure WAF(Web Application Firewall) 인스턴스에 액세스할 수 있는 전용 관리 계정을 사용하는 방법에 대한 표준 운영 절차를 만드는 것이 좋습니다. Security Center의 ID 및 액세스 관리 기능을 사용하여 관리 계정 수를 모니터링합니다.

- [Azure Security Center ID 및 액세스 이해](../security-center/security-center-identity-access.md)

- [Azure Database for PostgreSQL에서 관리 사용자를 만드는 방법 이해](../postgresql/howto-create-users.md#the-server-admin-account)

- [Azure Policy를 사용하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure AD(Azure Active Directory) MFA(다단계 인증)를 사용하도록 설정하고, Security Center의 ID 및 액세스 관리 권장 사항을 따릅니다.

- [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

**지침**: MFA(다단계 인증)가 구성된 PAW(Privileged Access Workstation)를 사용하여 Azure WAF(Web Application Firewall) 및 관련 리소스에 로그인하고 구성합니다. 

- [Privileged Access Workstation에 대한 자세한 정보](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

**지침**: 환경에서 의심스럽거나 안전하지 않은 활동이 발생하는 경우 로그와 경고를 생성하기 위해 Azure AD(Azure Active Directory) 보안 보고서를 사용합니다. Security Center를 사용하여 ID 및 액세스 활동을 모니터링합니다.

- [위험한 활동에 대해 플래그가 지정된 Azure AD 사용자를 식별하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center에서 사용자의 ID 및 액세스 활동을 모니터링하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 정책의 위치 조건을 구성하고 명명된 위치를 관리합니다. 

명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 논리적 그룹을 만듭니다. Azure Key Vault 비밀과 같은 중요한 리소스에 대한 액세스를 구성된 명명된 위치로 제한합니다.

- [Azure Active Directory 조건부 액세스의 위치 조건이란?](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure AD(Azure Active Directory)를 중앙 인증 및 권한 부여 시스템으로 사용합니다. Azure AD는 강력한 암호화를 미사용 및 전송 중 데이터에 사용하여 데이터를 보호하고, 사용자 자격 증명도 솔트하고, 해시하고, 안전하게 저장합니다.
- [Azure AD 인스턴스를 만들고 구성하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure AD(Azure Active Directory)는 부실 계정을 검색하는 데 도움이 되는 로그를 제공합니다. Azure ID 액세스 검토를 사용하여 그룹 멤버 자격, 엔터프라이즈 애플리케이션에 대한 액세스 및 역할 할당을 효율적으로 관리합니다. 사용자 액세스를 정기적으로 검토하여 활성 사용자만 계속 액세스할 수 있는지 확인합니다.

- [Azure AD 보고 이해](../active-directory/reports-monitoring/index.yml)

- [Azure ID 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화된 자격 증명에 대한 액세스 시도 모니터링

**지침**: Azure AD(Azure Active Directory) 로그인 활동, 감사 및 위험 이벤트 로그 원본을 SIEM 또는 모니터링 도구(예: Azure Sentinel)와 통합합니다.

Azure AD(Azure Active Directory) 사용자 계정에 대한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics 작업 영역에 보내면 이 프로세스를 간소화할 수 있습니다. Log Analytics 작업 영역 내에서 원하는 경고를 구성합니다.

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure AD(Azure Active Directory)의 위험 및 ID 보호 기능을 사용하여 사용자 ID와 관련하여 검색된 의심스러운 작업에 대한 자동화된 응답을 구성합니다. 추가 조사를 위해 데이터를 Azure Sentinel에 수집합니다.

- [Azure AD 위험한 로그인을 확인하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [Azure Security Benchmark: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조하세요.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용하여 중요한 정보를 저장하거나 처리하는 Azure WAF(Web Application Firewall) 및 관련 리소스를 추적할 수 있도록 지원합니다.
- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 별도의 구독 및 관리 그룹을 환경 유형(예 : 개발, 테스트 및 프로덕션 환경) 및 데이터 민감도 수준과 같은 개별 보안 도메인에 사용하여 격리를 구현합니다. 

Azure RBAC(역할 기반 액세스 제어)를 사용하여 Azure 리소스에 대한 액세스를 제어합니다.

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: 전송 중인 모든 중요한 정보를 암호화합니다. Azure WAF(Web Application Firewall) 인스턴스 및 관련 리소스에 연결하는 모든 클라이언트에서 TLS 1.2 이상을 협상할 수 있는지 확인합니다.

해당하는 경우 미사용 암호화 및 전송 중 암호화에 대한 Security Center 권장 사항을 따릅니다.

- [Azure를 사용한 전송 중 암호화 이해](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 제어

**지침**: Azure RBAC(역할 기반 액세스 제어)를 사용하여 Azure 리소스에 대한 액세스를 제어합니다.
- [Azure RBAC를 구성하는 방법](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: Azure WAF(Web Application Firewall) 및 관련 리소스를 포함하여 모든 Azure 리소스에 미사용 암호화를 사용합니다. Microsoft는 Azure에서 암호화 키를 관리할 수 있도록 허용하는 것을 권장하지만, 일부 인스턴스에서 사용자 고유의 키를 관리할 수 있는 옵션이 있습니다.

- [Azure의 저장 데이터 암호화 이해](../security/fundamentals/encryption-atrest.md)

- [고객 관리형 암호화 키를 구성하는 방법](../storage/common/customer-managed-keys-configure-key-vault.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 기록 및 경고

**지침**: 미리 결정된 시간 동안 네트워크 트래픽의 기준을 방지 모드로 지정한 후 방지 모드에서 실행되도록 Azure WAF(Web Application Firewall)를 구성합니다. 

방지 모드의 Azure WAF는 규칙에서 탐지하는 침입 및 공격을 차단합니다. 공격자는 "403 무단 액세스" 예외를 수신하고, 연결이 종료됩니다. 방지 모드는 이러한 공격을 WAF 로그에 기록합니다.

- [Application Gateway와 Azure Security Center 간의 통합 개요](../security-center/security-center-partner-integration.md)

- [Application Gateway의 WAF 모드](ag/ag-overview.md#waf-modes)

- [Front Door의 WAF 모드](afds/afds-overview.md#waf-modes)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure Security Benchmark: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화된 자산 검색 솔루션 사용

**지침**: Azure Resource Graph를 사용하여 구독 내의 모든 리소스(예: 컴퓨팅, 스토리지, 네트워크, 포트 및 프로토콜 등)를 쿼리하거나 검색합니다. 

테넌트에서 적절한(읽기) 권한을 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거합니다. Resource Graph를 통해 클래식 Azure 리소스를 검색할 수 있지만 앞으로는 Azure Resource Manager 리소스를 만들어서 사용하는 것이 좋습니다.

- [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

- [Azure 구독을 확인하는 방법](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Azure RBAC 이해](../role-based-access-control/overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: Azure WAF(Web Application Firewall) 정책에서 태그를 사용합니다. 태그를 리소스와 연결하고 논리적으로 적용하여 고객 구독에서 이러한 리소스에 대한 액세스를 구성할 수 있습니다. 

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 해당하는 경우 태그 지정, 관리 그룹 및 별도의 구독을 사용하여 Azure WAF 및 관련 리소스를 구성하고 추적합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: 승인된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 조직의 요구 사항에 따라 구성을 포함하여 승인된 리소스의 인벤토리를 만듭니다.

Azure Policy를 사용하여 구독에서 만들 수 있는 리소스 종류를 제한합니다. Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리하고 검색합니다. 환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy를 사용하여 구독에서 만들 수 있는 리소스 종류를 제한합니다.
Azure Resource Graph를 사용하여 구독 내에서 Azure WAF(Web Application Firewall) 리소스를 쿼리하거나 검색합니다. 환경에 있는 모든 Azure WAF 및 관련 리소스가 승인되었는지 확인합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: Azure Policy를 사용하여 승인되지 않은 Azure WAF 리소스를 모니터링하고 제거하여 Azure WAF 또는 특정 유형의 WAF(예: Azure WAF v1 및 V2)의 배포를 거부합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy를 사용하여 환경에서 프로비전할 수 있는 서비스를 제한합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](../governance/policy/samples/index.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Azure Resource Manager와 상호 작용하는 사용자 기능 제한

**지침**: Azure 조건부 액세스를 사용하여 "Microsoft Azure Management" 앱에 대한 "액세스 차단"을 구성함으로써 Azure Resource Manager와 상호 작용하는 사용자의 기능을 제한합니다.

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**지침**: Azure WAF(Web Application Firewall)는 네트워크, 리소스 그룹 또는 구독을 통해 다양한 환경과 연결하여 높은 위험 수준의 애플리케이션을 분리할 수 있습니다.

- [Azure Virtual Network 개요](../virtual-network/virtual-networks-overview.md)

- [Azure 관리 그룹으로 리소스 구성](../governance/management-groups/overview.md)

- [구독 관련 결정 가이드](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure Security Benchmark: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure WAF(Web Application Firewall) 배포와 관련된 네트워크 설정에 대한 표준 보안 구성을 정의하고 구현합니다.
“Microsoft.Network” 네임스페이스에서 Azure Policy 별칭을 사용하여 Azure Application Gateway, Virtual Networks, 네트워크 보안 그룹의 네트워크 구성을 감사하거나 적용하고 기본 제공 정책 정의를 사용하는 사용자 지정 정책을 만듭니다.

- [사용 가능한 Azure Policy 별칭을 보는 방법](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: [거부] 및 [존재하지 않으면 배포] Azure Policy 효과를 사용하여 보안 설정을 Azure WAF(Web Application Firewall) 및 관련 리소스에 적용합니다. 

Azure Resource Manager 템플릿을 사용하여 조직에 필요한 Azure WAF 및 관련 리소스의 보안 구성을 유지 관리합니다.

- [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

- [규정 준수를 적용하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager 템플릿 개요](../azure-resource-manager/templates/overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: Azure DevOps를 사용하여 사용자 지정 Azure 정책 및 Azure Resource Manager 템플릿과 같은 코드를 안전하게 저장하고 관리합니다. 

Azure DevOps와 통합된 경우 Azure AD(Azure Active Directory) 또는 TFS(Team Foundation Server)와 통합된 경우 Active Directory에 정의된 특정 사용자, 기본 제공 보안 그룹 또는 하나 이상의 그룹에 권한을 부여하거나 거부합니다.

- [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure DevOps의 권한 및 그룹 정보](/azure/devops/organizations/security/about-permissions)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스를 위한 구성 관리 도구 배포

**지침**: "Microsoft.Network" 네임스페이스에서 기본 제공 Azure Policy 정의 및 Azure Policy 별칭을 사용하여 시스템 구성을 경고, 감사 및 적용하는 사용자 지정 정책을 만듭니다. 정책 예외를 관리하는 프로세스 및 파이프라인을 개발합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy 설명서](../governance/policy/index.yml)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대한 자동화된 구성 모니터링 구현

**지침**: "Microsoft.Network" 네임스페이스에서 기본 제공 Azure Policy 정의 및 Azure Policy 별칭을 사용하여 시스템 구성을 경고, 감사 및 적용하는 사용자 지정 정책을 만듭니다. 

[감사], [거부] 및 [존재하지 않으면 배포] Azure Policy 효과를 사용하여 Azure 리소스에 대한 구성을 자동으로 적용합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy 설명서](../governance/policy/index.yml)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: Azure Key Vault를 사용하여 인증서를 안전하게 저장합니다. Azure Key Vault는 비밀, 키 및 SSL 인증서를 보호하는 데 사용할 수 있는 플랫폼 관리 비밀 저장소입니다. 

Azure Application Gateway는 HTTPS 지원 수신기에 연결된 서버 인증서에 대해 Key Vault와의 통합을 지원합니다. 

- [Azure PowerShell을 사용하여 Key Vault 인증서를 통해 SSL 종료를 구성하는 방법](../application-gateway/configure-keyvault-ps.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 코드 내에서 인증 정보를 식별하는 자격 증명 스캐너를 구현하면 검색된 인증 정보를 Azure Key Vault와 같은 더 안전한 위치로 이동하는 것도 좋습니다.
- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure Security Benchmark: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조하세요.*

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리형 키 보호 보장

**지침**: 일시 삭제가 Azure Key Vault에 사용하도록 설정되어 있는지 확인합니다. 일시 삭제를 사용하면 삭제된 키 자격 증명 모음 및 자격 증명 모음 개체(예: 키, 비밀 및 인증서)를 복구할 수 있습니다.

- [Azure Key Vault의 일시 삭제를 사용하는 방법](../key-vault/general/key-vault-recovery.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [Azure Security Benchmark: 인시던트 대응](../security/benchmarks/security-control-incident-response.md)을 참조하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 개발합니다. 검색에서 인시던트 사후 검토까지의 인시던트 처리 및 관리 단계뿐만 아니라 담당자의 모든 역할도 정의하는 서면 인시던트 대응 계획이 작성되어 있는지 확인합니다. 

- [자체 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [NIST의 컴퓨터 보안 인시던트 처리 가이드를 사용하여 사용자 고유의 인시던트 대응 계획 수립 지원](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center에서 심각도를 각 경고에 할당하여 먼저 조사해야 하는 경고에 대한 우선 순위를 지정합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용된 결과 또는 분석의 신뢰도 및 경고가 발생된 활동의 배후에 악의적인 의도가 있었음에 대한 신뢰 수준을 기준으로 합니다.
구독(예: 프로덕션, 비 프로덕션)을 명확하게 표시하고, Azure 리소스를 명확하게 식별하고 분류할 수 있는 명명 시스템을 만듭니다.

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 격차를 식별하고 필요에 따라 계획을 수정합니다.
- [NIST 게시물 - IT 계획 및 기능 테스트, 학습 및 연습 프로그램에 대한 가이드](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 고객 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.
- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: 연속 내보내기 기능을 사용하여 Security Center 경고 및 권장 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 조직의 요구 사항에 따라 경고를 Azure Sentinel로 스트림합니다.

- [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Security Center의 워크플로 자동화 기능을 사용하여 "Logic Apps"를 통해 보안 경고 및 권장 사항에 대한 응답을 자동으로 트리거합니다.
- [워크플로 자동화와 Logic Apps를 구성하는 방법](../security-center/workflow-automation.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure Security Benchmark: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대한 침투 테스트를 정기적으로 수행 및 모든 중요한 보안 결과를 수정

**지침**: Microsoft Engagement 규칙에 따라 침투 테스트가 Microsoft 정책을 위반하지 않는지 확인합니다. Microsoft 관리형 클라우드 인프라, 서비스 및 애플리케이션에 대한 Microsoft의 Red Teaming 및 라이브 사이트 침투 테스트 전략과 실행을 사용합니다. 

- [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud 레드 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](../security/benchmarks/overview.md)를 참조하세요.
- [Azure 보안 기준](../security/benchmarks/security-baselines-overview.md)에 대해 자세히 알아보세요.