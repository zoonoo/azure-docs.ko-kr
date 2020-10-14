---
title: Azure 웹 응용 프로그램 방화벽에 대 한 azure 보안 기준
description: Azure 웹 응용 프로그램 방화벽 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: edb184fa286eb6212f714c18830540e105ea3305
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92021183"
---
# <a name="azure-security-baseline-for-azure-web-application-firewall"></a>Azure 웹 응용 프로그램 방화벽에 대 한 azure 보안 기준

이 보안 기준은 [Azure Security 벤치 마크 버전 1.0](../security/benchmarks/overview-v1.md) 의 지침을 Azure 웹 응용 프로그램 방화벽에 적용 합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 azure 보안 벤치 마크에 정의 된 **보안 컨트롤** 및 Azure 웹 응용 프로그램 방화벽에 적용 되는 관련 지침에 따라 그룹화 됩니다. Azure 웹 응용 프로그램 방화벽에 적용할 수 없는 **컨트롤이** 제외 되었습니다. 

Azure 웹 응용 프로그램 방화벽이 Azure 보안 벤치 마크에 완전히 매핑되는 방식을 보려면 [전체 Azure 웹 응용 프로그램 방화벽 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조 하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [Azure 보안 벤치 마크: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조 하세요.*

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: 웹 응용 프로그램에 대 한 중앙 집중식 보호를 위해 Waf (웹 응용 프로그램 방화벽)를 사용 하 MICROSOFT AZURE 여 SQL 삽입, 사이트 간 스크립팅 등의 취약점을 해결 합니다. 

- 검색 모드:이 모드를 사용 하 여 네트워크 트래픽을 파악 하 고 가양성을 이해 하 고 검토 합니다. 모든 위협 경고를 모니터링 하 고 기록 합니다. 진단 및 WAF 로그가 선택 되어 있고 켜져 있는지 확인 합니다. WAF는 검색 모드에서 작동할 때 들어오는 요청을 차단 하지 않습니다.
- 방지 모드: 규칙에서 감지한 침입 및 공격을 차단 합니다. 공격자는 "403 권한 없는 액세스" 예외를 수신 하 고 연결을 닫습니다. 방지 모드는 이러한 공격을 WAF 로그에 기록합니다.

WAF의 검색 모드를 사용 하 여 네트워크 트래픽을 기준으로 합니다. 트래픽 요구 사항을 확인 한 후 방지 모드에서 WAF를 원치 않는 원치 않는 트래픽으로 구성 합니다.

WAF로 보호 되지 않는 웹 사용 리소스에 대해 Security Center의 높은 심각도 권장 사항을 따릅니다.  

- [웹 응용 프로그램 방화벽 CRS 규칙 그룹 및 규칙](ag/application-gateway-crs-rulegroups-rules.md) 

- [Application Gateway의 WAF 모드](ag/ag-overview.md#waf-modes)

- [전면 도어의 WAF 모드](afds/afds-overview.md#waf-modes)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: AZURE Waf (웹 응용 프로그램 방화벽)와 함께 사용자 지정 규칙을 사용 하 여 트래픽을 허용 하 고 차단 합니다. 예를 들어 IP 주소 범위에서 들어오는 모든 트래픽을 차단할 수 있습니다. 규칙에서 감지한 침입 및 공격을 차단 하는 방지 모드에서 실행 되도록 Azure WAF를 구성 합니다. 공격자는 "403 권한 없는 액세스" 예외를 수신 하 고 연결을 닫습니다. 방지 모드는 이러한 공격을 WAF 로그에 기록합니다.

- [Application Gateway의 WAF 모드](ag/ag-overview.md#waf-modes)

- [전면 도어의 WAF 모드](afds/afds-overview.md#waf-modes)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침**: AZURE waf (웹 응용 프로그램 방화벽)는 azure의 웹 응용 프로그램 보호를 위한 핵심 구성 요소입니다. Azure WAF를 사용 하 여 OWASP 상위 10 개 범주의 알려진 공격 서명에 대해 미리 구성 된 관리 되는 규칙 집합을 사용 하는 일반적인 악용 및 취약성 으로부터 웹 응용 프로그램에 대 한 중앙 집중식 보호를 제공

웹 응용 프로그램 요구 사항에 맞게 Azure WAF를 규칙 및 규칙 그룹으로 사용자 지정 하 고 가양성을 제거 합니다. WAF 뒤의 각 사이트에 대 한 Azure WAF 정책을 연결 하 여 사이트 관련 구성을 허용 합니다. Azure WAF는 지역 필터링, 요율 제한, Azure에서 관리 하는 기본 규칙 집합 규칙을 지원 합니다. 응용 프로그램의 요구에 맞게 사용자 지정 규칙을 만들 수 있습니다. 

결정 된 기간 동안 검색 모드에서 네트워크 트래픽의 기준선을 지정한 후 방지 모드에서 실행 되도록 Azure WAF를 구성 합니다. Azure WAF는 방지 모드에서 규칙에 의해 검색 된 침입 및 공격을 차단 합니다. 공격자는 "403 권한 없는 액세스" 예외를 수신 하 고 연결을 닫습니다. 방지 모드는 이러한 공격을 WAF 로그에 기록합니다.

- [Application Gateway의 WAF 모드](ag/ag-overview.md#waf-modes)

- [전면 도어의 WAF 모드](afds/afds-overview.md#waf-modes)

- [웹 응용 프로그램 방화벽 CRS 규칙 그룹 및 규칙](ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: 일반적인 응용 프로그램 잘못 된 구성 (예: APACHE 및 IIS)의 검색을 위해 태그를 사용 하 여 Azure 구독에서 리소스를 만들고, 연결 하 고, 논리적으로 구성 합니다. 

적용 된 태그 메타 데이터에 따라 Azure WAF (웹 응용 프로그램 방화벽) 정책에 규칙 및 규칙 그룹을 적용 합니다.

- [Application Gateway의 WAF 정책](https://docs.microsoft.com/cli/azure/network/application-gateway/waf-policy?view=azure-cli-latest) 

- [전면 도어의 WAF 정책](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: 트래픽 구성 규칙 문서화

**지침**: 네트워크 보안 및 트래픽 흐름과 관련 된 기타 리소스 뿐만 아니라 Azure 애플리케이션 게이트웨이 서브넷에서 Azure Waf (웹 응용 프로그램 방화벽)와 연결 된 네트워크 보안 그룹에 대 한 태그를 사용 합니다. 개별 네트워크 보안 그룹 규칙에 대해 "설명" 필드를 사용 하 여 네트워크에서 들어오고 나가는 트래픽을 허용 하는 규칙에 대 한 비즈니스 요구, 기간 등을 지정할 수 있습니다.

태그를 사용 하 여 모든 리소스를 만들고 태그가 지정 되지 않은 기존 리소스를 알리도록 하려면 태그 지정과 관련 된 기본 제공 Azure Policy 정의 (예: "태그 및 해당 값 필요")를 사용 합니다.

Azure PowerShell 또는 Azure CLI을 선택 하 여 태그를 기준으로 리소스에 대 한 작업을 조회 하거나 수행할 수 있습니다.

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

- [Virtual Network를 만드는 방법](../virtual-network/quick-create-portal.md)

- [보안 구성을 사용하여 NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용 하 여 네트워크 리소스 구성을 모니터링 하 고 AZURE Waf (웹 응용 프로그램 방화벽) 배포와 관련 된 네트워크 설정 및 리소스에 대 한 변경 내용을 검색 합니다. 중요 네트워크 설정 또는 리소스가 변경 될 때 트리거되는 Azure Monitor 내에서 경고를 만듭니다.

- [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](/azure/azure-monitor/platform/activity-log-view)

- [Azure Monitor에서 경고를 만드는 방법](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure 보안 벤치 마크: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조 하세요.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 원본 사용

**지침**: AZURE Waf (웹 응용 프로그램 방화벽)의 네트워크 규칙을 만들어 적절 한 포트 및 프로토콜을 사용 하 여 NTP 서버에 대 한 액세스를 허용 합니다 (예: UDP를 통한 포트 123).

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: azure 센티널 또는 타사 siem과 같은 중앙 보안 로그 관리 솔루션에 보낼 Waf (웹 응용 프로그램 방화벽) 로그를 구성 합니다. 이러한 로그에는 Azure 활동, 진단 및 실시간 WAF 로그가 포함 되어 있습니다. 이러한 로그는 Azure Monitor, Excel, Power BI 등의 다른 도구에서 볼 수 있습니다. Azure 웹 응용 프로그램 방화벽 로그는 Azure WAF가 평가, 일치 및 차단 하는 데이터에 대 한 통찰력을 제공 합니다.

Azure 센티널에는 azure WAF의 보안 이벤트에 대 한 개요를 제공 하는 기본 제공 Azure WAF 통합 문서가 있습니다. 이 통합 문서에는 이벤트, 일치 및 차단 된 규칙과 방화벽 로그에 기록 되는 기타 모든 항목이 포함 됩니다. 이 원격 분석을 사용 하 여 플레이 북 자동화를 시작 하 여 센티널에서 수집한 WAF 이벤트에 따라 수정 작업을 알리거나 수정 작업을 수행할 수 있습니다.

- [활동 로그 보기](../azure-resource-manager/management/view-activity-logs.md)

- [Application Gateway에 대 한 진단 로그](../application-gateway/application-gateway-diagnostics.md)

- [Microsoft 웹 응용 프로그램 방화벽의 데이터를 Azure 센티널에 연결](/azure/sentinel/connect-microsoft-waf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: Azure 웹 응용 프로그램 방화벽 (waf) 리소스에 대 한 로깅을 사용 하도록 설정 하 여 감사, 보안 및 진단 로그에 액세스 합니다. Azure 웹 응용 프로그램 방화벽은 구성 된 진단 로그에서 사용할 수 있는 각 검색 된 위협에 대 한 자세한 보고 기능을 제공 합니다. 자동으로 사용할 수 있는 활동 로그에는 이벤트 원본, 날짜, 사용자, 타임스탬프, 원본 주소, 대상 주소 및 기타 유용한 요소가 포함됩니다.

- [로깅 개요](ag/ag-overview.md#logging)

- [Azure Monitor 로그 쿼리 개요](../azure-monitor/log-query/log-query-overview.md)

- [Azure Platform 로그 개요](../azure-monitor/platform/platform-logs-overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침**: AZURE Waf (웹 응용 프로그램 방화벽) 로그를 사용자 지정 저장소 계정으로 보내고 보존 정책을 정의 합니다. Azure Monitor를 사용 하 여 조직의 규정 준수 요구 사항에 따라 Log Analytics 작업 영역 보존 기간을 설정할 수 있습니다.
- [스토리지 계정에 대한 모니터링 구성](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그를 모니터링 하 고 검토 합니다.

**지침**: AZURE Waf (웹 응용 프로그램 방화벽)는 검색 된 각 위협에 대 한 자세한 보고 기능을 제공 합니다. 로깅은 감사 및 문제 해결에 중요 한 작업 및 오류에 대 한 다양 한 정보를 제공 하는 Azure 진단 로그와 통합 됩니다. 

Azure WAF 인스턴스는 보고에 대 한 경고 및 상태 정보를 보내기 위해 Security Center와 통합 됩니다. Security Center 권장 사항을 사용 하 여 보호 되지 않는 웹 응용 프로그램을 검색 하 고 이러한 취약 한 리소스를 보호 합니다. 

Azure 센티널에는 WAF의 보안 이벤트에 대 한 개요를 제공 하는 기본 제공 WAF-방화벽 이벤트 통합 문서가 있습니다. 여기에는 이벤트, 일치 및 차단 된 규칙과 방화벽 로그에 기록 되는 기타 모든 항목이 포함 됩니다.

- [진단 설정을 Azure 활동 로그에 사용하도록 설정하는 방법](/azure/azure-monitor) 

- [Azure 애플리케이션 Gateway에 대 한 진단 설정을 사용 하도록 설정 하는 방법](../application-gateway/application-gateway-diagnostics.md)

- [Azure 전면 도어에서 메트릭 및 로그 모니터링](../frontdoor/front-door-diagnostics.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: Azure 활동 로그 진단 설정 및 AZURE waf의 진단 설정을 사용 하도록 설정 하 고 로그를 Log Analytics 작업 영역으로 보냅니다. Log Analytics에서 쿼리를 수행하여 용어를 검색하고, 추세를 파악하며, 패턴을 분석하고, 수집된 데이터를 기반으로 하여 다양한 인사이트를 제공합니다. WAF 메트릭을 기반으로 비정상적인 활동에 대 한 경고를 만듭니다. 예를 들어, ' X '를 초과 하는 요청 수가 차단 된 경우 ' Y '를 실행 합니다.

- [진단 설정을 Azure 활동 로그에 사용하도록 설정하는 방법](/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Azure 내에서 경고를 만드는 방법](/azure/azure-monitor/learn/tutorial-response)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: 들어오는 트래픽의 추가 검사를 위해 중요 한 웹 응용 프로그램 앞에 AZURE Waf (웹 응용 프로그램 방화벽)를 배포 합니다. 

Azure WAF는 일반적인 악용 및 취약성 으로부터 웹 응용 프로그램에 대 한 중앙 집중식 보호를 제공 하 고, SQL 삽입, 사이트 간 스크립팅, 맬웨어 업로드 및 DDoS 공격과 같은 공격을 차단 하기 위해 인바운드 웹 트래픽을 검사 하 여 앱을 보호 합니다.

- [Azure WAF를 배포 하는 방법](ag/create-waf-policy-ag.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [Azure 보안 벤치 마크: id 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조 하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**참고**: Azure Active Directory (Azure AD)에는 쿼리를 수행할 수 있는 기본 제공 역할이 있으며 명시적으로 할당 되어야 합니다. Azure AD PowerShell 모듈을 사용 하 여 임시 쿼리를 수행 하 여 관리 그룹의 구성원 인 계정을 검색 합니다.

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할을 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: waf 내에서 사용할 수 있는 로컬 관리자 할당은 없습니다. 그러나 Azure WAF 리소스에 대 한 관리 제어를 허용할 수 있는 환경에는 azure AD (Azure Active Directory) 관리자 역할이 있을 수 있습니다.
Azure WAF (웹 응용 프로그램 방화벽) 인스턴스에 액세스할 수 있는 전용 관리 계정을 사용 하는 방법에 대 한 표준 운영 절차를 만드는 것이 좋습니다. Security Center의 Id 및 액세스 관리 기능을 사용 하 여 관리 계정 수를 모니터링 합니다.

- [Azure Security Center Id 및 액세스 이해](../security-center/security-center-identity-access.md)

- [Azure Database for PostgreSQL에서 관리 사용자를 만드는 방법 이해](../postgresql/howto-create-users.md#the-server-admin-account)

- [Azure Policy를 사용하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 대해 multi-factor authentication을 사용 하십시오.

**지침**: Azure Active Directory (Azure AD) MULTI-FACTOR AUTHENTICATION (MFA)를 사용 하도록 설정 하 고 Security Center의 Id 및 액세스 관리 권장 사항을 따릅니다.

- [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

**지침**: MFA (권한 Multi-Factor Authentication 있는 액세스 워크스테이션)를 사용 하 여 AZURE Waf (웹 응용 프로그램 방화벽) 및 관련 리소스에 로그인 하 고이를 구성 합니다. 

- [Privileged Access Workstation에 대한 자세한 정보](/windows-server/identity/securing-privileged-access/privileged-access-workstations) 

- [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정에서 의심 스러운 활동에 대 한 로그 및 경고

**지침**: 환경에서 의심 스러운 활동이 나 안전 하지 않은 활동이 발생 하는 경우 로그 및 경고를 생성 하는 데 Azure AD (Azure Active Directory) 보안 보고서를 사용 합니다. Security Center를 사용 하 여 id 및 액세스 작업을 모니터링할 수 있습니다.

- [위험한 활동에 대해 플래그가 지정된 Azure AD 사용자를 식별하는 방법](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Azure Security Center에서 사용자의 ID 및 액세스 활동을 모니터링하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 정책의 위치 조건을 구성 하 고 명명 된 위치를 관리 합니다. 

명명 된 위치를 사용 하 여 IP 주소 범위 또는 국가 및 지역의 논리적 그룹을 만듭니다. 구성 된 명명 된 위치에 대 한 Azure Key Vault 암호와 같은 중요 한 리소스에 대 한 액세스를 제한 합니다.

- [Azure Active Directory 조건부 액세스의 위치 조건](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure Active Directory (Azure AD)를 중앙 인증 및 권한 부여 시스템으로 사용 합니다. Azure AD는 미사용 데이터 및 전송 중인 데이터에 대해 강력한 암호화를 사용 하 여 데이터를 보호 하 고 사용자 자격 증명을 salts, 해시 및 안전 하 게 저장 합니다.
- [Azure AD 인스턴스를 만들고 구성 하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure Active Directory (Azure AD)는 오래 된 계정을 검색 하는 데 도움이 되는 로그를 제공 합니다. Azure Id 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 정기적으로 사용자 액세스를 검토 하 여 활성 사용자만 계속 액세스할 수 있도록 합니다.

- [Azure AD 보고 이해](/azure/active-directory/reports-monitoring)

- [Azure ID 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화 되는 자격 증명에 대 한 액세스 시도를 모니터링 합니다.

**지침**: azure 센티널과 같은 siem 또는 모니터링 도구를 사용 하 여 Azure Active Directory (azure AD) 로그인 활동, 감사 및 위험 이벤트 로그 소스를 통합 합니다.

Azure Active Directory (Azure AD) 사용자 계정에 대 한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 전송 하 여이 프로세스를 간소화 합니다. Log Analytics 작업 영역 내에서 원하는 경고를 구성 합니다.

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대 한 경고

**지침**: Azure Active Directory (Azure AD) 위험 및 id 보호 기능을 사용 하 여 사용자 id와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성 합니다. 추가 조사를 위해 데이터를 Azure 센티널로 수집 합니다.

- [Azure AD 위험한 로그인을 확인하는 방법](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [Azure 보안 벤치 마크: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조 하세요.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용 하 여 AZURE Waf (웹 응용 프로그램 방화벽) 및 중요 한 정보를 저장 하거나 처리 하는 관련 리소스를 추적 하는 데 도움을 줍니다.
- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 환경 유형 및 데이터 민감도 수준 (예: 개발, 테스트 및 프로덕션 환경)과 같은 개별 보안 도메인에 대해 별도의 구독 및 관리 그룹을 사용 하 여 격리를 구현 합니다. 

Azure AD (Azure Active Directory)를 사용 하 여 azure 리소스에 대 한 액세스를 제어 합니다. azure RBAC (역할 기반 액세스 제어).

- [추가 Azure 구독을 만드는 방법](/azure/billing/billing-create-subscription)

- [관리 그룹을 만드는 방법](/azure/governance/management-groups/create)

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: 전송 중인 모든 중요 한 정보를 암호화 합니다. Azure WAF (웹 응용 프로그램 방화벽) 인스턴스 및 관련 리소스에 연결 하는 모든 클라이언트에서 TLS 1.2 이상을 협상할 수 있는지 확인 합니다.

미사용 암호화 및 암호화 (해당 하는 경우)에 대 한 Security Center 권장 사항을 따릅니다.

- [Azure를 사용 하 여 전송 중인 암호화 이해](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: 역할 기반 액세스 제어를 사용 하 여 리소스에 대 한 액세스를 제어 합니다.

**지침**: azure AD (Azure Active Directory) 역할 기반 액세스 제어 (azure RBAC)를 사용 하 여 azure 리소스에 대 한 액세스를 제어 합니다.
- [Azure에서 RBAC를 구성 하는 방법](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: AZURE Waf (웹 응용 프로그램 방화벽) 및 관련 리소스를 비롯 한 모든 azure 리소스에 미사용 암호화를 사용 합니다. Azure에서 암호화 키를 관리 하도록 허용 하는 것이 좋지만 일부 인스턴스에서는 사용자 고유의 키를 관리할 수 있는 옵션이 있습니다.

- [Azure에서 미사용 암호화 이해](../security/fundamentals/encryption-atrest.md)

- [고객 관리 암호화 키를 구성 하는 방법](/azure/storage/common/storage-encryption-keys-portal)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고

**지침**: 미리 결정 된 시간 동안 검색 모드에서 네트워크 트래픽의 기준선을 지정한 후에 AZURE Waf (웹 응용 프로그램 방화벽)가 방지 모드에서 실행 되도록 구성 합니다. 

Azure WAF는 방지 모드에서 규칙에 의해 검색 되는 침입 및 공격을 차단 합니다. 공격자는 "403 무단 액세스" 예외를 수신하고, 연결이 종료됩니다. 방지 모드는 이러한 공격을 WAF 로그에 기록합니다.

- [Application Gateway와 Azure Security Center 간의 통합 개요](../application-gateway/application-gateway-integration-security-center.md#overview)

- [Application Gateway의 WAF 모드](ag/ag-overview.md#waf-modes)

- [전면 도어의 WAF 모드](afds/afds-overview.md#waf-modes)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure 보안 벤치 마크: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조 하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화 된 asset discovery 솔루션 사용

**지침**: Azure 리소스 그래프를 사용 하 여 구독 내에서 계산, 저장소, 네트워크, 포트, 프로토콜 등의 모든 리소스를 쿼리하거나 검색 합니다. 

테 넌 트에서 적절 한 (읽기) 권한을 확인 하 고 구독 내의 리소스 뿐만 아니라 모든 Azure 구독을 열거 합니다. 클래식 Azure 리소스는 Resource Graph를 통해 검색할 수 있지만 앞으로 Azure Resource Manager 리소스를 만들어 사용하는 것이 좋습니다.

- [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

- [Azure 구독을 확인하는 방법](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Azure RBAC 이해](../role-based-access-control/overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: AZURE Waf (웹 응용 프로그램 방화벽) 정책에 태그를 사용 합니다. 태그를 리소스와 연결 하 여 논리적으로 적용 하 여 고객 구독에서 이러한 리소스에 대 한 액세스를 구성할 수 있습니다. 

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 태그 지정, 관리 그룹 및 별도의 구독 (해당 하는 경우)을 사용 하 여 AZURE waf 및 관련 리소스를 구성 하 고 추적 합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

- [추가 Azure 구독을 만드는 방법](/azure/billing/billing-create-subscription)

- [관리 그룹을 만드는 방법](/azure/governance/management-groups/create)

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: 승인 된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 조직 요구 사항에 따라 구성이 포함 된 승인 된 리소스의 인벤토리를 만듭니다.

Azure Policy를 사용 하 여 구독에 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다. Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리하고 검색 합니다. 환경에 있는 모든 Azure 리소스가 승인 되었는지 확인 합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy을 사용 하 여 구독에 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.
Azure 리소스 그래프를 사용 하 여 구독 내의 Azure WAF (웹 응용 프로그램 방화벽) 리소스를 쿼리하거나 검색 합니다. 환경에 있는 모든 Azure WAF 및 관련 리소스가 승인 되었는지 확인 합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: azure waf 또는 특정 유형의 waf (예: AZURE waf V1 vs V2)의 배포를 거부 하는 Azure Policy으로 승인 되지 않은 azure waf 리소스를 모니터링 하 고 제거 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy을 사용 하 여 사용자 환경에서 프로 비전 할 수 있는 서비스를 제한 합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침**: Azure 조건부 액세스를 사용 하 여 Azure 리소스 관리자와 상호 작용 하는 사용자의 기능을 제한 하 고 "Microsoft Azure Management" 앱에 대 한 "액세스 차단"을 구성 합니다.

- [Azure 리소스 관리자에 대 한 액세스를 차단 하도록 조건부 액세스를 구성 하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**지침**: AZURE Waf (웹 응용 프로그램 방화벽)는 네트워크, 리소스 그룹 또는 구독을 통해 다양 한 환경에 연결 하 여 높은 위험 수준의 응용 프로그램을 분리할 수 있습니다.

- [Azure Virtual Network 개요](../virtual-network/virtual-networks-overview.md)

- [Azure 관리 그룹으로 리소스 구성](../governance/management-groups/overview.md)

- [구독 관련 결정 가이드](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure 보안 벤치 마크: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조 하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: AZURE Waf (웹 응용 프로그램 방화벽) 배포와 관련 된 네트워크 설정에 대 한 표준 보안 구성을 정의 하 고 구현 합니다.
"Microsoft. Network" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 사용자 지정 정책을 만들어 Azure 애플리케이션 게이트웨이, 가상 네트워크, 네트워크 보안 그룹의 네트워크 구성을 감사 하거나 적용 하 고 기본 제공 정책 정의를 사용 합니다.

- [사용 가능한 Azure 정책 별칭을 확인하는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: AZURE Waf (웹 응용 프로그램 방화벽) 및 관련 리소스에서 보안 설정을 적용 하려면 Azure Policy [거부] 및 [없는 경우 배포] 효과를 사용 합니다. 

Azure Resource Manager 템플릿을 사용 하 여 조직에 필요한 Azure WAF 및 관련 리소스의 보안 구성을 유지 합니다.

- [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

- [규정 준수를 적용 하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager 템플릿 개요](../azure-resource-manager/templates/overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: Azure devops를 사용 하 여 사용자 지정 azure 정책 및 Azure Resource Manager 템플릿과 같은 코드를 안전 하 게 저장 하 고 관리 합니다. 

Azure DevOps와 통합 된 경우 Azure Active Directory (Azure AD)에 정의 된 특정 사용자, 기본 제공 보안 그룹 또는 그룹에 대 한 권한을 부여 하거나 거부 하거나, TFS (Team Foundation Server)와 통합 된 경우 Active Directory 합니다.

- [Azure DevOps에 코드를 저장하는 방법](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure DevOps의 사용 권한 및 그룹 정보](/azure/devops/organizations/security/about-permissions)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스에 대 한 구성 관리 도구 배포

**지침**: 기본 제공 Azure Policy Azure Policy 정의 및 "Microsoft 네트워크" 네임 스페이스의 별칭을 사용 하 여 시스템 구성을 경고, 감사 및 적용 하기 위한 사용자 지정 정책을 만들 수 있습니다. 정책 예외를 관리 하기 위한 프로세스 및 파이프라인을 개발 합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy 설명서](/azure/governance/policy)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대 한 자동화 된 구성 모니터링 구현

**지침**: 기본 제공 Azure Policy Azure Policy 정의 및 "Microsoft 네트워크" 네임 스페이스의 별칭을 사용 하 여 시스템 구성을 경고, 감사 및 적용 하기 위한 사용자 지정 정책을 만들 수 있습니다. 

Azure 리소스에 대 한 구성을 자동으로 적용 하려면 Azure Policy [감사], [거부] 및 [없는 경우 배포] 효과를 사용 합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy 설명서](/azure/governance/policy)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: Azure Key Vault을 사용 하 여 인증서를 안전 하 게 저장 합니다. Azure Key Vault는 암호, 키 및 SSL 인증서를 보호 하는 데 사용할 수 있는 플랫폼 관리 암호 저장소입니다. 

Azure Application Gateway는 HTTPS 지원 수신기에 연결된 서버 인증서에 대해 Key Vault와의 통합을 지원합니다. 

- [Azure PowerShell를 사용 하 여 Key Vault 인증서로 SSL 종료를 구성 하는 방법](../application-gateway/configure-keyvault-ps.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현 하 여 코드 내에서 자격 증명을 식별 합니다. 또한 검색 된 자격 증명을 Azure Key Vault 같은 보다 안전한 위치로 이동 하는 것이 좋습니다.
- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure 보안 벤치 마크: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조 하세요.*

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객이 관리 하는 키를 보호 해야 합니다.

**지침**: Azure Key Vault에 대해 일시 삭제를 사용 하도록 설정 되어 있는지 확인 합니다. 일시 삭제는 키, 암호 및 인증서와 같은 삭제 된 key vault 및 자격 증명 모음 개체를 복구할 수 있습니다.

- [Azure Key Vault의 일시 삭제를 사용 하는 방법](/azure/key-vault/key-vault-soft-delete-powershell)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [Azure 보안 벤치 마크: 인시던트 응답](../security/benchmarks/security-control-incident-response.md)을 참조 하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대 한 인시던트 대응 가이드를 개발 합니다. 담당자의 모든 역할을 정의 하는 사고 대응 계획 및 인시던트를 검색 하 고 인시던트를 해결 하는 방법에 대 한 해결 방법을 확인 합니다. 

- [사용자 고유의 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [NIST의 컴퓨터 보안 인시던트 처리 가이드를 사용 하 여 고유한 인시던트 대응 계획을 만드는 데 도움이 됩니다.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center 각 경고에 심각도를 할당 하 여 먼저 조사 해야 하는 경고의 우선 순위를 지정 합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용된 결과 또는 분석의 신뢰도 및 경고가 발생된 활동의 배후에 악의적인 의도가 있었음에 대한 신뢰 수준을 기준으로 합니다.
구독 (예: 프로덕션, 비프로덕션)을 명확 하 게 표시 하 고 Azure 리소스를 명확 하 게 식별 하 고 범주화 하기 위한 이름 지정 시스템을 만듭니다.

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 결함을 식별하고 필요에 따라 계획을 수정합니다.
- [IT 계획 및 기능에 대 한 테스트, 학습 및 연습 프로그램은 NIST의 게시 가이드를 참조 하세요.](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 고객 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.
- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: 연속 내보내기 기능을 사용 하 여 Security Center 경고 및 권장 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center data connector를 사용 하 여 조직의 요구 사항에 따라 Azure 센티널로 경고를 스트리밍합니다.

- [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Security Center의 워크플로 자동화 기능을 사용 하 여 보안 경고 및 권장 사항에 대 한 "Logic Apps"를 통해 응답을 자동으로 트리거합니다.
- [워크플로 자동화 및 Logic Apps를 구성하는 방법](../security-center/workflow-automation.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure 보안 벤치 마크: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조 하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대 한 정기적인 침투 테스트를 수행 하 고 모든 중요 한 보안 결과를 수정 하세요.

**지침**: Microsoft Engagement 규칙에 따라 침투 테스트가 microsoft 정책을 위반 하지 않는지 확인 합니다. Microsoft에서 관리 하는 클라우드 인프라, 서비스 및 응용 프로그램에 대 한 레드 팀 및 라이브 사이트 침투 테스트의 전략과 실행을 사용 합니다. 

- [Engagement의 침투 테스트 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft 클라우드 Red 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
