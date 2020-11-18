---
title: App Service에 대 한 Azure 보안 기준
description: App Service 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: app-service
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 640d51de6dac1d175f9b46f327b72f293cf025cb
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94849191"
---
# <a name="azure-security-baseline-for-app-service"></a>App Service에 대 한 Azure 보안 기준

App Service에 대 한 Azure 보안 기준에는 배포의 보안 상태를 개선 하는 데 도움이 되는 권장 사항이 포함 되어 있습니다. 이 서비스의 기준은 [Azure Security Benchmark 버전 1.0](../security/benchmarks/overview-v1.md)에서 가져왔으며, 모범 사례 지침을 통해 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 추천 사항을 제공합니다. 콘텐츠는 Azure 보안 벤치 마크에 정의 된 **보안 컨트롤** 및 App Service에 적용 되는 관련 지침에 따라 그룹화 됩니다. App Service에 적용할 수 없는 **컨트롤** 은 제외 되었습니다.

App Service 완전히 Azure 보안 벤치 마크에 매핑되는 방법을 보려면 [전체 App Service 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조 하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [Azure 보안 벤치 마크: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조 하세요.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: 격리 된 가격 책정 계층에서 App Service를 사용 하는 경우 Azure Virtual Network 내에서 서브넷에 직접 배포할 수 있는 APP SERVICE ENVIRONMENT (ASE) 라고도 합니다. 네트워크 보안 그룹을 사용 하 여 가상 네트워크의 리소스에 대 한 인바운드 및 아웃 바운드 트래픽을 차단 하거나 App Service Environment 앱에 대 한 액세스를 제한 하 여 Azure App Service Environment를 보호 합니다.

기본적으로 네트워크 보안 그룹은 가장 낮은 우선 순위의 암시적 거부 규칙을 포함 하며, 허용 규칙을 명시적으로 추가 해야 합니다. 최소 권한 네트워킹 접근 방식에 따라 네트워크 보안 그룹에 대 한 허용 규칙을 추가 합니다. App Service Environment를 호스트 하는 데 사용 되는 기본 가상 머신은 Microsoft에서 관리 하는 구독에 있기 때문에 직접 액세스할 수 없습니다.

WAF (웹 응용 프로그램 방화벽) 사용 Azure 애플리케이션 게이트웨이를 통해 트래픽을 라우팅 하 여 App Service Environment를 보호 합니다. Application Gateway와 함께 서비스 끝점을 사용 하 여 앱에 대 한 인바운드 게시 트래픽을 보호 합니다.  

다중 테 넌 트 App Service (격리 계층에 없는 앱)에서 네트워크 보안 그룹을 사용 하 여 앱에서 아웃 바운드 트래픽을 차단 합니다. Virtual Network 통합 기능을 사용 하 여 앱이 Virtual Network 또는에서 리소스에 액세스할 수 있도록 합니다. 이 기능은 앱에서 공용 주소로 아웃 바운드 트래픽을 차단 하는 데도 사용할 수 있습니다. Virtual Network 통합을 사용 하 여 앱에 대 한 인바운드 액세스를 제공할 수 없습니다.  

다음을 사용 하 여 앱에 대 한 인바운드 트래픽 보안
- 액세스 제한-인바운드 액세스를 제어 하는 일련의 허용 또는 거부 규칙
- 서비스 끝점-지정 된 가상 네트워크 또는 서브넷 외부에서 인바운드 트래픽을 거부할 수 있습니다.
- 개인 끝점-개인 IP 주소를 사용 하 여 Virtual Network에 앱을 노출 합니다. 앱에서 개인 끝점을 사용 하도록 설정 하면 더 이상 인터넷에 액세스할 수 없습니다.

동일한 지역의 가상 네트워크와 Virtual Network 통합 기능을 사용 하는 경우 네트워크 보안 그룹을 사용 하 고 사용자 정의 경로를 사용 하 여 테이블을 라우팅합니다. 사용자 정의 경로를 통합 서브넷에 배치 하 여 원하는 대로 아웃 바운드 트래픽을 보낼 수 있습니다.  

Azure 방화벽을 구현 하 여 구독 및 가상 네트워크에서 응용 프로그램 및 네트워크 연결 정책을 중앙에서 만들고 적용 하 고 기록 하는 것이 좋습니다. Azure 방화벽은 가상 네트워크 리소스에 대 한 고정 공용 IP 주소를 사용 하므로 외부 방화벽은 가상 네트워크에서 발생 하는 트래픽을 식별할 수 있습니다. 

- [App Service Environment 잠금](environment/firewall-integration.md)

- [OWASP (Web Application Security Project) 상위 10 개 취약점 보호를 엽니다.](https://owasp.org/www-project-top-ten/)

- [네트워크 보안 그룹](/azure/virtual-network/security-overview)

- [Azure 가상 네트워크에 앱 통합](web-sites-integrate-with-vnet.md)

- [App Service Environment에 대한 네트워킹 고려 사항](environment/network-info.md)

- [외부 ASE를 만드는 방법](environment/create-external-ase.md)

- [내부 ASE를 만드는 방법](environment/create-ilb-ase.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: 가상 네트워크, 서브넷 및 네트워크 인터페이스의 구성 및 트래픽을 모니터링 하 고 기록 합니다.

**지침**: Azure Security Center에서 네트워크 보호 권장 사항을 구현 하 여 App Service 앱 및 api와 관련 된 네트워크 리소스 및 구성을 보호 합니다.

Azure 방화벽을 사용 하 여 트래픽을 전송 하 고, 구독 및 가상 네트워크에서 응용 프로그램 및 네트워크 연결 정책을 중앙에서 만들고 적용 하 고 기록 합니다. Azure 방화벽은 가상 네트워크 리소스에 대 한 고정 공용 IP 주소를 사용 하므로 외부 방화벽은 Virtual Network에서 시작 된 트래픽을 식별할 수 있습니다. 또한 Azure 방화벽 서비스는 로깅 및 분석을 위해 Azure Monitor와 완전히 통합 됩니다.

- [Azure 방화벽 개요](../firewall/overview.md)

- [Azure Security Center에서 제공 하는 네트워크 보안 이해](../security-center/security-center-network-recommendations.md)

- [App Service의 모니터링 및 보호를 사용 하도록 설정 하는 방법](/azure/security-center/security-center-app-services)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: 다음을 수행 하 여 ASE (App Service Environment)에서 인터넷에 액세스할 수 있는 앱을 보호 합니다.
- 인터넷 연결 앱 앞에 Azure 애플리케이션 Gateway를 사용 하 여 WAF (웹 응용 프로그램 방화벽) 배포
- 액세스 제한을 사용 하 여 Application Gateway에 대 한 인바운드 트래픽 보안
- 인증을 보장 하기 위해 Azure Active Directory (Azure AD)를 사용 하 여 앱 보호
- 최소 TLS 버전을 1.2으로 설정 합니다.
- 앱을 HTTPS로만 설정

Azure 방화벽 장치를 통해 모든 응용 프로그램 트래픽 아웃 바운드를 구동 하 고 로그를 모니터링 합니다. 

다중 테 넌 트 App Service에서 인터넷에 액세스할 수 있는 앱을 보호 하려면 (예: 격리 된 계층에 없음)
- 앱 앞에 웹 응용 프로그램 방화벽 사용 장치 배포
- 액세스 제한 또는 서비스 끝점을 사용 하 여 WAF (웹 응용 프로그램 방화벽) 장치에 대 한 인바운드 트래픽 보안
- 인증을 위해 Azure AD를 사용 하 여 앱 보호
- 최소 TLS 버전을 1.2으로 설정 합니다.
- 앱을 HTTPS로만 설정
- 가상 네트워크 통합 및 앱 설정 WEBSITE_VIRTUAL NETWORK_ROUTE_ALL를 사용 하 여 모든 아웃 바운드 트래픽이 통합 서브넷의 네트워크 보안 그룹 및 사용자 정의 경로에 적용 되도록 할 수 있습니다.

응용 프로그램 서비스 환경 앱과 마찬가지로 모든 응용 프로그램 트래픽을 Azure 방화벽 장치를 통해 아웃 바운드 하 고 앱의 로그를 모니터링 합니다.

또한 App Service Environment 문서 잠금에 대 한 권장 사항을 검토 하 고 따릅니다.

- [App Service Environment 잠금](environment/firewall-integration.md)

- [Azure 애플리케이션 게이트웨이의 Azure 웹 응용 프로그램 방화벽](../web-application-firewall/ag/ag-overview.md)

- [Azure App Service 액세스 제한](app-service-ip-restrictions.md)

- [Azure Monitor를 사용 하 여 WAF 경고를 추적 하 고 추세를 쉽게 모니터링 ](../azure-monitor/overview.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: App Service Environment 설명서 잠금에 설명 된 대로 App Service Environment를 보호 합니다. Azure Security Center의 통합 위협 인텔리전스 기능을 적용 하 여 알려진 악성 또는 사용 되지 않는 공용 IP 주소와의 통신을 거부 합니다. 액세스 제한을 사용 하 여 Application Gateway에 대 한 인바운드 트래픽을 보호 합니다. 

공용 인터넷 연결 끝점을 사용 하 여 다중 테 넌 트 App Service (격리 된 계층에 없는 앱)의 보안을 유지 합니다. Virtual Network 내에서 특정 서브넷의 트래픽만 허용 하 고 다른 모든 항목은 차단 합니다. 액세스 제한을 사용 하 여 허용 되는 인바운드 트래픽을 잠글 네트워크 Access Control 목록 (IP 제한)을 구성 합니다.

앱에 대 한 네트워크 액세스를 관리 하려면 정렬 된 허용 또는 거부 목록 간의 우선 순위를 정의 합니다. 이 목록에는 IP 주소 또는 Virtual Network 서브넷이 포함 될 수 있습니다. 하나 이상의 항목이 포함 된 경우 목록의 끝에 암시적 "모두 거부" 규칙이 있습니다. 이 기능은, Web Apps, API Apps, Linux 앱, Linux 컨테이너 앱 및 함수를 비롯 한 모든 App Service 호스팅된 작업 로드와 함께 작동 합니다. 

서비스 끝점을 사용 하 여 Azure Virtual Network에서 웹 앱에 대 한 액세스를 제한 합니다. 선택한 서브넷에서 서비스 끝점을 사용 하 여 다중 테 넌 트 App Service (격리 계층에 있지 않은 앱)에 대 한 액세스를 제한 합니다. 

- [Azure App Service 고정 IP 제한](app-service-ip-restrictions.md)

- [Azure 애플리케이션 게이트웨이의 Azure 웹 응용 프로그램 방화벽](../web-application-firewall/ag/ag-overview.md)

- [App Service Environment에 대해 WAF (웹 응용 프로그램 방화벽)를 구성 하는 방법](environment/app-service-app-service-environment-web-application-firewall.md)

- [App Service Environment 잠금에 설명 된 대로 ASE를 보호 합니다.](environment/firewall-integration.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷을 기록 합니다.

**지침**: Security Center를 사용 하 여 App Service 앱에 전송 되는 요청 및 응답을 모니터링 합니다. 웹 응용 프로그램 방화벽을 사용 하 고 웹 응용 프로그램 방화벽 경고를 추적 하 고 추세를 쉽게 모니터링 하도록 Azure Monitor 설정 된 웹 응용 프로그램 방화벽이 있는 실시간 Application Gateway를 사용 하 여 웹 응용 프로그램에 대 한 공격을 모니터링할 수 있습니다.

- [Azure Application Gateway의 Azure 웹 애플리케이션 방화벽](../web-application-firewall/ag/ag-overview.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침**: App Service Environment에서 앱에 대 한 트래픽을 관리 합니다.

- App Service Environment 잠금에 설명 된 대로 App Service Environment를 보호 합니다.
- 인터넷 연결 앱 앞에 Azure 웹 응용 프로그램 방화벽이 있는 Application Gateway 배포
- HTTPS를 통해서만 앱에 액세스할 수 있도록 설정

다중 테 넌 트 App Service에서 인터넷에 액세스할 수 있는 앱에 대 한 트래픽 관리 (격리 계층에 없음): 

- 인터넷 연결 앱 앞에서 Azure 웹 응용 프로그램 방화벽이 사용 하도록 설정 된 Application Gateway 배포
- 액세스 제한 또는 서비스 끝점을 사용 하 여 웹 응용 프로그램 방화벽에 대 한 인바운드 트래픽을 보호 합니다. 액세스 제한 기능은 Web Apps, API Apps, Linux 앱, Linux 컨테이너 앱 및 함수를 비롯 한 모든 App Service 호스팅된 작업 로드와 함께 작동 합니다.

- HTTPS를 통해서만 앱에 액세스할 수 있도록 설정
- 고정 IP 제한을 사용 하 여 App Service 앱에 대 한 액세스를 제한 하 여 응용 프로그램 게이트웨이의 VIP에서 액세스 권한이 있는 유일한 주소로만 트래픽을 수신할 수 있도록 합니다.

참조 된 링크에서 추가 정보를 검토 합니다.

- [Azure App Service 고정 IP 제한](app-service-ip-restrictions.md)

- [Azure 애플리케이션 게이트웨이의 Azure 웹 응용 프로그램 방화벽](../web-application-firewall/ag/ag-overview.md)

- [포털에서 Application Gateway를 사용 하 여 종단 간 TLS를 구성 하는 방법](../application-gateway/end-to-end-ssl-portal.md)

- [App Service 잠금에 설명 된 대로 ASE를 보호 합니다.](https://review.docs.microsoft.com/azure/app-service/environment/firewall-integrationEnvironment:)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: App Service에는 서비스를 관리 하는 데 사용 되는 여러 끝점이 있습니다. 이러한 끝점 주소는 AppServiceManagement IP 서비스 태그에도 포함 됩니다. AppServiceManagement 태그는 이러한 트래픽을 허용 하는 App Service Environment에만 사용 됩니다. 

규칙의 적절 한 원본 또는 대상 필드에서 서비스 태그 이름을 지정 하 여 해당 서비스에 대 한 트래픽을 허용 하거나 거부할 수 있습니다. App Service 인바운드 주소는 AppService IP 서비스 태그에서 추적 됩니다. App Service에서 사용 하는 아웃 바운드 주소를 포함 하는 IP 서비스 태그는 없습니다.

Microsoft는 서비스 태그에 포함되는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트합니다.

- [가상 네트워크 서비스 태그](../virtual-network/service-tags-overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지 관리

**지침**: App Service apps와 관련 된 네트워크 설정에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. 

"Microsoft 웹" 및 "Microsoft. 네트워크" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 보안 구성을 유지 합니다. 사용자 지정 정책을 만들어 App Service 앱의 네트워크 구성을 감사 하거나 적용 합니다. 

다음과 같이 App Service에 대 한 기본 제공 정책 정의를 사용 합니다.
- 앱은 가상 네트워크 서비스 끝점을 사용 해야 합니다.
- HTTPS를 통해서만 앱에 액세스할 수 있어야 합니다.
- 최소 TLS 버전을 현재 버전으로 설정 합니다.

참조 된 링크에서 추가 정보를 검토 합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [포털에서 Application Gateway를 사용 하 여 종단 간 TLS를 구성 하는 방법](../application-gateway/end-to-end-ssl-portal.md)

- [App Service 잠금에 설명 된 대로 ASE를 보호 합니다.](https://review.docs.microsoft.com/azure/app-service/environment/firewall-integrationEnvironment:)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: 트래픽 구성 규칙 문서화

**지침**: App Service의 트래픽 흐름을 비롯 하 여 네트워크 보안 그룹 및 기타 관련 리소스에 대 한 태그를 사용 합니다.

개별 네트워크 보안 그룹 규칙에 대 한 네트워크 트래픽을 허용 하는 모든 규칙에 대 한 "설명" 필드를 사용 하 여 비즈니스 요구, 기간 등을 지정 합니다.

태그가 포함 된 모든 리소스를 만들고 태그가 지정 되지 않은 기존 리소스를 알리도록 하려면 "태그 및 해당 값 필요"와 같은 태그 지정 효과와 관련 된 기본 제공 Azure Policy 정의를 적용 합니다. Azure PowerShell 또는 Azure CLI를 사용 하 여 태그를 기준으로 리소스에 대 한 작업을 조회 하거나 수행할 수 있습니다.

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

- [Azure App Service 액세스 제한](/azure/app-service/app-service-ip-restriction)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용 하 여 네트워크 리소스 구성을 모니터링 하 고 네트워크 설정 및 App Service 관련 된 모든 리소스에 대 한 변경 내용을 검색 합니다. 

가상 네트워크 끝점 서비스 사용을 위해 앱을 감사 하는 정책과 같이 App Service에 대 한 몇 가지 Azure Policy 기본 제공 정의 중 하나를 적용 합니다. 중요 한 네트워크 설정 또는 리소스에 대 한 변경 내용이 발생 하는 경우 트리거할 Azure Monitor 내에서 경고를 만듭니다. 

포털에서 또는 프로그래밍 방식 도구를 통해 Security Center에서 자세한 보안 경고 및 권장 사항을 검토 합니다. 이 정보를 내보내거나 사용자 환경의 다른 모니터링 도구로 보냅니다. 도구를 사용 하 여 경고 및 권장 사항을 수동으로 또는 지속적으로 지속적으로 내보낼 수 있습니다. 이러한 도구를 사용 하 여 다음을 수행할 수 있습니다.
 
- Log Analytics 작업 영역으로 지속적으로 내보내기
- Azure Event Hubs로 계속 내보내기 (타사 SIEMs와 통합)
- CSV 파일로 내보내기 (한 번)

자동 도구를 사용 하 여 네트워크 리소스 구성을 모니터링 하 고 변경 내용을 신속 하 게 검색 하는 프로세스를 만드는 것이 좋습니다.

- [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](/azure/azure-monitor/platform/activity-log-view)

- [Azure Monitor에서 경고를 만드는 방법](../azure-monitor/platform/alerts-activity-log.md)

- [보안 경고 및 권장 사항 내보내기](../security-center/continuous-export.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure 보안 벤치 마크: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조 하세요.*

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure Monitor와 APP SERVICE ENVIRONMENT (ASE)를 통합 하 여 Azure Storage, Azure Event Hubs 또는 Log Analytics에 로그를 보냅니다. 제어 평면 감사 로깅에 대해 Azure 활동 로그 진단 설정을 사용 하도록 설정 합니다. Security Center의 보안 경고는 Azure 활동 로그에 게시 됩니다. Azure 활동 로그 데이터를 감사 하 여 Azure App Service 및 기타 Azure 리소스에 대 한 제어 평면 수준에서 수행 되는 모든 쓰기 작업 (PUT, POST, DELETE)에 대해 "무엇을, 누가, 언제"를 확인할 수 있습니다. 나중에 사용 하기 위해 쿼리를 저장 하 고, 쿼리 결과를 Azure 대시보드에 고정 하 고, 로그 경고를 만듭니다. 또한 Application Insights의 데이터 액세스 REST API를 사용 하 여 프로그래밍 방식으로 원격 분석에 액세스할 수 있습니다.

비즈니스 요구 사항에 따라 다양 한 데이터 원본 및 커넥터에 연결 하는 데 사용할 수 있는 확장 가능한 클라우드 네이티브, SIEM (보안 정보 이벤트 관리)의 Microsoft Azure 센티널을 사용 합니다. 또한 Azure Marketplace의 Barracuda와 같은 타사 SIEM (보안 정보 이벤트 관리) 시스템에 대해 및 온보드 데이터를 사용 하도록 설정할 수 있습니다.

- [ASE 작업 로깅](environment/using-an-ase.md#logging)

- [Azure App Service에 대 한 진단 설정을 사용 하도록 설정 하는 방법](troubleshoot-diagnostic-logs.md)

- [Application Insights를 사용 하도록 설정 하는 방법](../azure-monitor/app/app-insights-overview.md)

- [Application Insights에서 원격 분석 내보내기](../azure-monitor/app/export-telemetry.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: App Service의 제어 평면 감사 로깅에 대해 Azure 활동 로그 진단 설정을 사용 하도록 설정 합니다. 로그를 Log Analytics 작업 영역, Azure 이벤트 허브 또는 Azure Storage 계정으로 보냅니다.
제어 평면 수준에서 수행 되는 모든 쓰기 작업 (PUT, POST, DELETE)에 대 한 "무엇을, 누가, 언제"는 App Service 및 기타 Azure 리소스에 대 한 Azure 활동 로그 데이터를 사용 하 여 확인할 수 있습니다.

또한 Azure Key Vault는 액세스 정책 및 감사 기록을 통해 중앙 집중화 된 비밀 관리를 제공 합니다. 

- [진단 설정을 Azure 활동 로그에 사용하도록 설정하는 방법](/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Azure App Service에 대 한 진단 설정을 사용 하도록 설정 하는 방법](troubleshoot-diagnostic-logs.md)

- [리소스 관리자 작업](../role-based-access-control/resource-provider-operations.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침**: Azure Monitor에서 조직의 규정 준수 규정에 따라 App Service 리소스와 연결 된 Log Analytics 작업 영역에 대 한 로그 보존 기간을 설정 합니다.
- [로그 보존 매개 변수를 설정 하는 방법](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그를 모니터링 하 고 검토 합니다.

**지침**: Log Analytics 작업 영역으로 전송 되는 로그를 사용 하 여 App Service 리소스의 Azure 활동 로그 진단 설정을 검토 합니다. Log Analytics에서 쿼리를 수행하여 용어를 검색하고, 추세를 파악하며, 패턴을 분석하고, 수집된 데이터를 기반으로 하여 다양한 인사이트를 제공합니다.

App Service 앱에 대 한 Application Insights를 사용 하 고 로그, 성능 및 오류 데이터를 수집 합니다. Azure Portal 내 Application Insights에서 수집한 원격 분석 데이터를 확인 합니다.

WAF (웹 응용 프로그램 방화벽)를 배포한 경우 실시간 웹 응용 프로그램 방화벽 로그를 사용 하 여 App Service 앱에 대 한 공격을 모니터링할 수 있습니다. 이 로그는 웹 응용 프로그램 방화벽 경고를 추적 하 고 추세를 쉽게 모니터링 하기 위해 Azure Monitor와 통합 됩니다.

확장 가능한 클라우드 기본 SIEM (보안 정보 이벤트 관리) 인 Azure 센티널을 사용 하 여 요구 사항에 따라 다양 한 데이터 원본 및 커넥터와 통합할 수 있습니다. 필요에 따라 Azure Marketplace의 타사 보안 정보 이벤트 관리 솔루션에 및 온보드 데이터를 사용 하도록 설정 합니다.

- [진단 설정을 Azure 활동 로그에 사용하도록 설정하는 방법](/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Application Insights를 사용 하도록 설정 하는 방법](../azure-monitor/app/app-insights-overview.md)

- [App Service Environment를 WAF (Azure 애플리케이션 Gateway)와 통합 하는 방법](environment/integrate-with-application-gateway.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: Azure 구독에 Security Center를 구성 하 고 생성 된 경고를 검토 합니다. Azure Monitor를 사용 하 여 Azure 센티널과 같은 SIEM (security information Event management) 솔루션에서 읽을 수 있는 이벤트 허브로 활동 로그 데이터를 가져올 수 있습니다. 

배포 된 Azure WAF (웹 응용 프로그램 방화벽)에서 실시간 웹 응용 프로그램 방화벽 로그를 사용 하 여 App Service 앱에 대 한 공격을 모니터링 합니다. 이 로그는 WAF (웹 응용 프로그램 방화벽) 경고를 추적 하 고 추세를 쉽게 모니터링 하기 위해 Azure Monitor와 통합 됩니다.

- [App Service Environment를 WAF (Azure 애플리케이션 Gateway)와 통합 하는 방법](environment/integrate-with-application-gateway.md)

- [보안 경고 및 권장 사항 내보내기](../security-center/continuous-export.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [Azure 보안 벤치 마크: id 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조 하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**지침**: Azure Active Directory (Azure AD)에는 명시적으로 할당 되 고 쿼리가 가능 해야 하는 기본 제공 역할이 있습니다. Azure AD PowerShell 모듈을 사용 하 여 임시 쿼리를 수행 하 여 관리 그룹의 구성원 인 계정을 검색 합니다.

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

- [App Service 및 Azure Functions에 대한 관리 ID를 사용하는 방법](overview-managed-identity.md?context=azure%2Factive-directory%2Fmanaged-identities-azure-resources%2Fcontext%2Fmsi-context&amp;tabs=dotnet)

- [Azure Portal을 사용하여 Azure 역할 할당 추가 또는 제거](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

**참고**: Azure Active Directory (Azure AD)에는 기본 암호 개념이 없습니다. App Service에 대 한 제어 평면 액세스를 제공 합니다.

일반적으로 사용자의 앱을 빌드할 때 사용자 액세스를 위한 기본 암호를 구현 하지 마십시오. Azure AD, Microsoft 계정, Facebook, Google, Twitter 등의 App Service에 대해 기본적으로 제공 되는 id 공급자 중 하나를 사용 합니다.

지원 해야 하는 경우가 아니면 익명 액세스를 사용 하지 않도록 설정 합니다. 

- [Azure App Service에서 기본적으로 사용할 수 있는 id 공급자](overview-authentication-authorization.md#identity-providers)

- [Azure App Service 및 Azure Functions의 인증 및 권한 부여](overview-authentication-authorization.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다. Security Center의 Id 및 액세스 관리 기능을 사용 하 여 관리 계정 수를 모니터링 하 고 추적 합니다. 

Security Center 또는 기본 제공 Azure 정책의 권장 사항을 사용 합니다. 예를 들면 다음과 같습니다.

- 구독에 둘 이상의 소유자가 할당 되어야 합니다. 
- 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.
- 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.

네트워크 리소스 구성을 모니터링 하 고 관리 계정에 대 한 변경 내용을 검색 하는 프로세스를 만듭니다.

- [Azure Security Center를 사용 하 여 id 및 액세스를 모니터링 하는 방법](../security-center/security-center-identity-access.md)

- [Azure Policy를 사용하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [사용자에 게 응용 프로그램 액세스 권한 부여에 대 한 자세한 정보](../role-based-access-control/overview.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: SSO (Azure Active Directory Single Sign-On)를 사용 하십시오.

**지침**: Azure Active Directory (Azure AD)를 통해 App Service을 인증 합니다. Id 공급자에 대 한 OAuth 2.0 서비스를 제공 하 고 모바일 및 웹 응용 프로그램에 대 한 권한 있는 액세스를 가능 하 게 합니다. 

App Service 앱은 타사 id 공급자가 사용자 id와 인증 흐름을 관리 하는 페더레이션된 id를 사용 합니다. 이러한 id 공급자는 기본적으로 사용할 수 있습니다.

- Azure AD
- Microsoft 계정

- Facebook

- Google

- Twitter

이러한 공급자 중 하나를 사용 하 여 인증 및 권한 부여를 사용 하도록 설정 하면 사용자 인증 및 공급자의 인증 토큰 유효성 검사에 해당 로그인 끝점이 제공 됩니다.

- [Azure App Service의 인증 및 권한 부여 이해](overview-authentication-authorization.md#identity-providers)

- [Azure App Service의 인증 및 권한 부여에 대해 알아보기](overview-authentication-authorization.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 대해 multi-factor authentication을 사용 하십시오.

**지침**: Azure Active Directory (Azure AD)에서 다단계 인증 기능을 사용 하도록 설정 하 고 Security Center의 Id 및 액세스 관리 권장 사항을 따릅니다.

Azure AD에 대 한 다단계 인증을 구현 합니다. 관리자는 포털의 구독 계정이 보호 되는지 확인 해야 합니다. 구독은 만든 리소스를 관리 하기 때문에 공격에 취약 합니다. 

- [Azure 보안 MFA](/azure/security/develop/secure-aad-app)

- [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: 관리 작업에는 안전한 Azure 관리 워크스테이션 사용

**지침**: 다단계 인증을 사용 하는 PAW (권한 있는 액세스 워크스테이션)를 사용 하 여 Azure 리소스에 로그인 하 고 구성 합니다.

- [Privileged Access Workstation에 대한 자세한 정보](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정에서 의심 스러운 활동에 대 한 로그 및 경고

**지침**: 환경에서 의심 스러운 활동이 나 안전 하지 않은 활동이 발생 하는 경우 로그 및 경고를 생성 하는 데는 Azure Active Directory (Azure AD)에서 PRIVILEGED IDENTITY MANAGEMENT (PIM)를 사용 합니다.

또한 Azure AD 위험 탐지를 사용하여 위험한 사용자 동작에 대한 경고 및 보고서를 봅니다.

Security Center의 위협 방지는 Windows 컴퓨터, Linux 컴퓨터, App Service 및 Azure 컨테이너와 같은 Azure 계산 리소스에 대 한 위협 방지를 포함 하 여 사용자 환경에 대 한 포괄적인 방어 기능을 제공 합니다.

- [PIM(Privileged Identity Management)을 배포하는 방법](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure AD 위험 탐지 이해](/azure/active-directory/reports-monitoring/concept-risk-events)

- [Azure 계산 리소스에 대 한 위협 방지](/azure/security-center/threat-protection)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명 된 위치를 사용 하 여 IP 주소 범위, 국가 또는 지역의 특정 논리적 그룹 에서만 Azure Portal에 액세스할 수 있도록 합니다.

- [Azure에서 명명된 위치를 구성하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: App Service 앱에 대 한 중앙 인증 및 권한 부여 시스템으로 Azure Active Directory (Azure AD)를 사용 합니다. Azure AD는 미사용 데이터 및 전송 중인 데이터에 대해 강력한 암호화를 사용 하 여 데이터를 보호 하 고 사용자 자격 증명을 salts, 해시 및 안전 하 게 저장 합니다.

- [Azure AD 로그인을 사용 하도록 Azure App Service 앱을 구성 하는 방법](configure-authentication-provider-aad.md)

- [Azure AD 인스턴스를 만들고 구성 하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure Active Directory (Azure AD)에서 제공 하는 로그를 사용 하 여 부실 계정을 검색 합니다. Azure Id 액세스 검토를 사용 하 여 그룹 멤버 자격 및 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자 액세스를 주기적으로 검토 하 여 의도 한 사용자만 계속 액세스할 수 있도록 합니다. 

- [Azure AD 보고 이해](/azure/active-directory/reports-monitoring)

- [Azure ID 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화 되는 자격 증명에 대 한 액세스 시도를 모니터링 합니다.

**지침**: App Service 앱에 대 한 중앙 인증 및 권한 부여 시스템으로 Azure Active Directory (Azure AD)를 사용 합니다. Azure AD는 미사용 데이터 및 전송 중인 데이터에 대해 강력한 암호화를 사용 하 여 데이터를 보호 하 고, salts, 해시 하 고, 사용자 자격 증명을 안전 하 게 저장 합니다.

Azure AD 로그인 활동, 감사 및 위험 이벤트 로그 원본에 액세스 하 여 Azure 센티널 또는 타사 SIEM (보안 정보 이벤트 관리) 솔루션과 통합할 수 있습니다. Azure AD 사용자 계정에 대 한 진단 설정을 만들고 Log Analytics 작업 영역에 감사 및 로그인 로그를 전송 하 여 프로세스를 간소화 합니다. Log Analytics 내에서 원하는 로그 경고를 구성할 수 있습니다.

- [Azure AD 로그인을 사용 하도록 Azure App Service 앱을 구성 하는 방법](configure-authentication-provider-aad.md)

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대 한 경고

**지침**: App Service 앱에 대 한 중앙 인증 및 권한 부여 시스템으로 Azure Active Directory (Azure AD)를 사용 합니다. 

Azure AD ID 보호를 사용 하 여 사용자 id와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성할 수 있습니다 (예: Azure Portal를 사용 하 여 제어 평면의 계정 로그인 동작 편차). 추가 조사를 위해 데이터를 Azure Sentinel로 수집할 수도 있습니다. 

- [Azure AD 로그인을 사용 하도록 Azure App Service 앱을 구성 하는 방법](configure-authentication-provider-aad.md)

- [Azure AD 위험한 로그인을 확인하는 방법](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오에서 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**지침**: App Service에는 사용할 수 없습니다. Azure App Service에 대 한 고객 Lockbox 지원 되지 않습니다.

- [Customer Lockbox 지원 서비스 목록](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [Azure 보안 벤치 마크: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조 하세요.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용 하 여 중요 한 정보를 저장 하거나 처리 하는 App Service 리소스를 추적 하는 데 도움을 줍니다.

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: App Service Environment의 경우 개발, 테스트 및 프로덕션 환경에 대해 별도의 구독, 관리 그룹 또는 둘 다를 구현 합니다. 동일한 방식으로 다른 앱에서 중요 한 정보를 처리 하는 앱을 격리할 수 있습니다. Virtual Network에 App Service 앱을 배포 합니다. 추가 응용 프로그램 격리를 위해 네트워크 보안 그룹 및 서브넷을 사용 합니다. 

ASE (App Service 환경)에는 두 가지 배포 유형이 있습니다. 둘 다 비즈니스 요구 사항에 따라 트래픽을 격리할 수 있습니다.

- 외부 응용 프로그램 서비스 환경-인터넷에 액세스할 수 있는 IP 주소에 호스트 된 앱 App Service Environment를 노출 합니다.

-  ILB (내부 부하 분산 장치) 응용 프로그램 서비스 환경-Virtual Network 내의 IP 주소에 호스트 된 앱 App Service Environment를 노출 합니다. 내부 끝점이 ilb (내부 부하 분산 장치) 이기 때문에 ILB ASE 라고 합니다. 

다중 테 넌 트 App Service (격리 된 계층에 없는 앱)의 경우 가상 네트워크의 리소스에 대 한 앱 액세스에 Virtual Network 통합을 사용 합니다.  개인 사이트 액세스를 사용 하 여 Azure 가상 네트워크 내에서와 같은 개인 네트워크 에서만 앱에 액세스할 수 있도록 합니다. Virtual Network 통합은 앱에서 Virtual Network 아웃 바운드 호출을 수행 하는 데만 사용 됩니다. Virtual Network 통합 기능은 동일한 지역의 가상 네트워크 및 다른 지역의 가상 네트워크와 함께 사용 하는 경우 다르게 동작 합니다. 
 
- [App Service Environment에 대한 네트워킹 고려 사항](environment/network-info.md)

- [외부 ASE를 만드는 방법](environment/create-external-ase.md)

- [내부 ASE를 만드는 방법](environment/create-ilb-ase.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: App Service에 대해 데이터 id, 분류 및 손실 방지 기능을 아직 사용할 수 없지만, 대상이 인터넷 또는 Azure 서비스에 대해 ' tag '를 사용 하는 모든 규칙을 제거 하 여 가상 네트워크에서 데이터 반출 위험을 줄일 수 있습니다. 

Microsoft는 App Service에 대 한 기본 인프라를 관리 하 고, 데이터 손실 또는 노출을 방지 하기 위해 엄격한 컨트롤을 구현 했습니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: 전송 중인 모든 정보를 암호화 하는 데 TLS/SSL 설정에서 구성 된 기본 최소 버전의 tls 1.2을 사용 합니다. 또한 모든 HTTP 연결 요청이 HTTPS로 리디렉션 되는지 확인 합니다.

- [Azure App Service 웹 앱에 대 한 전송 암호화 이해](security-recommendations.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: 현재 사용할 수 없습니다. 데이터 식별, 분류 및 손실 방지 기능은 아직 App Service 사용할 수 없습니다. 

중요 한 정보를 처리 하는 App Service 앱에 태그를 지정할 수 있습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현 합니다.

Microsoft는 기본 플랫폼을 관리 하 고 모든 고객 데이터를 중요 한 것으로 간주 하 고 고객 데이터 손실 및 노출을 방지 하기 위해 좋은 길이로 이동 합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: 역할 기반 액세스 제어를 사용 하 여 리소스에 대 한 액세스를 제어 합니다.

**지침**: Azure Active Directory (azure AD)의 역할 기반 액세스 제어 (azure RBAC)를 사용 하 여 Azure Portal의 App Service 제어 평면에 대 한 액세스를 제어 합니다.

- [Azure에서 RBAC를 구성 하는 방법](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: 파일과 같은 App Service 앱의 웹 사이트 콘텐츠는 미사용 콘텐츠를 자동으로 암호화 하는 Azure Storage에 저장 됩니다. Key Vault에 응용 프로그램 암호를 저장 하 고 런타임에 검색 하도록 선택 합니다.

고객이 제공한 암호는 App Service 구성 데이터베이스에 저장 되어 있는 동안 암호화 됩니다.

로컬에 연결 된 디스크는 선택적으로 웹 사이트에서 임시 저장소 (예: D:\local 및% TMP%)로 사용할 수 있지만 미사용 상태로 암호화 되지 않습니다.

- [Azure App Service에 대 한 데이터 보호 컨트롤 이해](app-service-security-controls.md)

- [휴지 상태의 암호화 Azure Storage 이해](../storage/common/storage-service-encryption.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고

**지침**: Azure 활동 로그와 함께 Azure Monitor를 사용 하 여 프로덕션 App Service 앱 및 기타 중요 또는 관련 된 리소스에 대 한 변경 내용에 대 한 경고를 만듭니다.

- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="vulnerability-management"></a>취약점 관리

*자세한 내용은 [Azure 보안 벤치 마크: 취약성 관리](../security/benchmarks/security-control-vulnerability-management.md)를 참조 하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행

**지침**: App Service 앱의 보안을 유지 하 고 수명 주기 동안 보안을 유지 하기 위해 DevSecOps 사례를 채택 합니다. DevSecOps는 조직의 보안 팀과 해당 기능을 DevOps 사례에 통합 하 여 팀의 모든 사용자가 보안을 책임 지 게 합니다.

App Service 앱을 보호 하기 위한 Security Center의 권장 사항을 검토 하 고 따릅니다.

- [CI/CD 파이프라인에 지속적인 보안 유효성 검사를 추가 하는 방법](https://docs.microsoft.com/azure/devops/migrate/security-validation-cicd-pipeline?view=azure-devops&amp;preserve-view=true)

- [Azure Security Center 취약성 평가 권장 사항을 구현 하는 방법](/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**지침**: Microsoft는 App Service을 지 원하는 기본 시스템에서 취약성 관리를 수행 합니다. 그러나 Security Center 내에서 권장 사항의 심각도 뿐만 아니라 보안 점수를 사용 하 여 사용자 환경 내에서 위험을 측정할 수 있습니다. 보안 점수는 완화 된 Security Center 권장 사항의 수를 기준으로 합니다. 권장 사항에 우선 순위를 지정 하려면 각각의 심각도를 고려 합니다.

- [보안 권장 사항 참조 가이드](../security-center/recommendations-reference.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure 보안 벤치 마크: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조 하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화 된 asset discovery 솔루션 사용

**지침**: Azure 리소스 그래프를 사용 하 여 구독 내의 모든 리소스 (예: 계산, 저장소, 네트워크, 포트, 프로토콜 등)를 쿼리하거나 검색 합니다. 적절 한 권한이 테 넌 트에 적용 되는지 확인 하 고 모든 Azure 구독 및 구독 내의 리소스를 열거할 수 있습니다.

클래식 Azure 리소스는 Resource Graph를 통해 검색할 수 있지만 앞으로 Azure Resource Manager 리소스를 만들어 사용하는 것이 좋습니다.

- [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

- [Azure 구독을 확인하는 방법](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Azure RBAC 이해](../role-based-access-control/overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타 데이터를 사용 하 여 Azure 리소스에 태그를 적용 하 여 논리적으로 분류로 구성 합니다.

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 태깅, 관리 그룹 및 별도의 구독을 적절 하 게 사용 하 여 Azure 리소스를 구성 하 고 추적 합니다. 정기적으로 인벤토리를 조정 하 고이 프로세스의 일부로 승인 되지 않은 리소스가 구독에서 제거 되도록 합니다.

다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정 하려면 Azure Policy을 선택 합니다.

- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

참조 된 링크에서 추가 정보를 검토 합니다.

- [추가 Azure 구독을 만드는 방법](/azure/billing/billing-create-subscription)

- [관리 그룹을 만드는 방법](/azure/governance/management-groups/create)

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: 승인 된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 조직의 요구 사항에 따라, 승인 된 Azure 리소스 및 계산 리소스에 대 한 승인 된 소프트웨어의 인벤토리를 만듭니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy을 사용 하 여 구독에 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.

Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리하거나 검색 합니다.  환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다. 

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: Azure 리소스 그래프를 사용 하 여 구독 내의 리소스를 쿼리하거나 검색 하 고 검색 된 Azure 리소스가 조직 정책에 따라 승인 되었는지 확인 합니다.

App Service에서 WebJobs를 사용 하 여 계산 리소스 내에 배포 된 승인 되지 않은 소프트웨어 응용 프로그램을 모니터링할 수 있습니다. WebJobs를 사용 하 여 웹 앱, API 앱 또는 모바일 앱과 동일한 인스턴스에서 프로그램 또는 스크립트를 실행 합니다. 로그를 사용 하 여 WebJob 구성 및 모니터링을 정의 합니다. WebJob 실행 세부 정보 페이지에서 토글 출력을 선택하여 로그 내용의 텍스트를 확인합니다. WebJobs는 Linux의 App Service에 대해 아직 지원 되지 않습니다.

- [Azure App Service에서 WebJobs로 백그라운드 작업 실행](webjobs-create.md)

- [자습서-규정 준수를 적용 하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)

- [빠른 시작-Azure 리소스 그래프 탐색기를 사용 하 여 첫 번째 리소스 그래프 쿼리 실행](../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: 환경에 있는 모든 Azure 리소스가 승인 되었는지 확인 합니다. Azure Policy를 사용 하 여 구독에 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다. 조직 정책에 따라 승인 되지 않은 배포 된 소프트웨어 응용 프로그램을 모두 제거 합니다.

- [Azure App Service에서 WebJobs로 백그라운드 작업 실행](webjobs-create.md)

- [자습서-규정 준수를 적용 하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)

- [빠른 시작-Azure 리소스 그래프 탐색기를 사용 하 여 첫 번째 리소스 그래프 쿼리 실행](../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 애플리케이션만 사용

**지침**: 환경에 있는 모든 Azure 리소스가 승인 되었는지 확인 합니다. Azure Policy를 사용 하 여 구독에 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다. 조직 정책에 따라 승인 되지 않은 배포 된 소프트웨어 응용 프로그램을 모두 제거 합니다. 

- [Azure App Service에서 WebJobs로 백그라운드 작업 실행](webjobs-create.md)

- [자습서-규정 준수를 적용 하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)

- [빠른 시작-Azure 리소스 그래프 탐색기를 사용 하 여 첫 번째 리소스 그래프 쿼리 실행](../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: 권한 있는 azure 서비스를 정기적으로 검토 하는 프로세스를 만들어 권한 있는 azure 서비스만 구독에서 사용 되도록 합니다.

이 프로세스 내에서 Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리하거나 검색 합니다. 환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다.

다음 기본 제공 정책 정의를 사용 하 여 구독에 만들 수 있는 리소스 유형에 대 한 제한을 설정 하도록 Azure Policy를 구성 합니다.

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

App Service에서 WebJobs를 사용 하 여 컴퓨터 리소스 내에 배포 된 승인 되지 않은 소프트웨어 응용 프로그램을 모니터링할 수 있습니다. WebJobs를 사용 하 여 웹 앱, API 앱 또는 모바일 앱과 동일한 인스턴스에서 프로그램 또는 스크립트를 실행 합니다. 로그를 사용 하 여 WebJob 구성 및 모니터링을 정의 합니다. WebJob 실행 세부 정보 페이지에서 토글 출력을 선택하여 로그 내용의 텍스트를 확인합니다. WebJobs는 Linux의 App Service에 대해 아직 지원 되지 않습니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](../governance/policy/samples/built-in-policies.md#general)

- [Azure App Service에서 WebJobs로 백그라운드 작업 실행](webjobs-create.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: 승인 된 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: 구독에서 승인 된 Azure 서비스만 사용 되도록 정기적으로 구독에서 소프트웨어 타이틀을 인벤토리 및 검토 하는 프로세스를 구현 합니다.

이 프로세스 내에서 Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리하거나 검색 합니다. 환경에서 검색 된 모든 Azure 리소스가 승인 되었는지 확인 합니다.

다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정 하도록 Azure Policy를 구성 합니다.

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

마찬가지로, App Service에서 WebJobs를 사용 하 여 컴퓨터 리소스 내에 배포 된 승인 되지 않은 소프트웨어 응용 프로그램을 인벤토리에 넣습니다. 로그를 사용 하 여 구성 및 모니터링을 정의 합니다. WebJob 실행 세부 정보 페이지에서 토글 출력을 선택하여 로그 내용의 텍스트를 확인합니다. WebJobs는 Linux의 App Service에 대해 아직 지원 되지 않습니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](../governance/policy/samples/built-in-policies.md#general)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침**: "Microsoft Azure 관리" 앱에 대 한 "액세스 차단"을 구성 하 여 사용자가 Azure Resource Manager와 상호 작용 하는 기능을 제한 하도록 Azure 조건부 액세스를 구성 합니다.

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 사용자가 컴퓨팅 리소스 내에서 스크립트를 실행하는 기능 제한

**지침**: WebJobs App Service의를 사용 하면 고객이 웹 앱, API 앱 또는 모바일 앱과 동일한 인스턴스에서 프로그램 또는 스크립트를 실행할 수 있습니다. 조직에서 허용 하지 않는 스크립트를 제한 하거나 제한 하는 구성을 정의 해야 합니다. App Service는 기본적으로 스크립트 실행을 제한 하는 메커니즘을 제공 하지 않습니다. WebJobs는 Linux의 App Service에 대해 아직 지원 되지 않습니다.

- [Azure App Service에서 WebJobs로 백그라운드 작업 실행](webjobs-create.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**지침**: 별도의 구독 또는 관리 그룹을 구현 하 여 높은 위험 App Service 앱에 대 한 격리를 제공 합니다. App Service의 경계 보안이 가상 네트워크를 사용 하 여 달성 되기 때문에 더 높은 위험 수준 앱을 자체 Virtual Network 배포 합니다. App Service Environment은 Azure Virtual Network의 서브넷에 App Service를 배포 하는 것입니다. 

응용 프로그램 서비스 환경, 외부 응용 프로그램 서비스 환경 및 ILB (내부 Load Balancer) 응용 프로그램 서비스 환경의 두 가지 유형이 있습니다. 요구 사항에 따라 가장 적합 한 아키텍처를 선택 합니다.

- [App Service Environment에 대한 네트워킹 고려 사항](environment/network-info.md)

- [외부 App Service Environment 만들기](environment/create-external-ase.md)

- [내부 Load Balancer App Service Environment 만들기 및 사용](environment/create-ilb-ase.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure 보안 벤치 마크: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조 하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Policy를 사용 하 여 App Service 배포 된 앱에 대 한 표준 보안 구성을 정의 하 고 구현 합니다.

"Microsoft 웹" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 App Service Web Apps 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다.

다음과 같은 기본 제공 정책 정의를 적용 합니다.
- App Service는 가상 네트워크 서비스 엔드포인트를 사용해야 함
- 웹 응용 프로그램은 HTTPS를 통해서만 액세스할 수 있어야 합니다.

- 앱에서 최신 TLS 버전 사용

표준화 된 사용에 대 한 기본 제공 정책 정의를 적용 하는 프로세스를 문서화 하는 것이 좋습니다.   

- [사용 가능한 Azure 정책 별칭을 확인하는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: Azure Policy [거부] 및 [없는 경우 배포] 효과를 사용 하 여 Azure App Service 앱에서 보안 설정을 적용 합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: 사용자 지정 Azure Policy 정의를 사용 하는 경우 코드를 안전 하 게 저장 하 고 관리 하려면 Azure devops 또는 Azure Repos를 선택 합니다.

기존 CI (지속적인 통합) 및 CD (지속적인 업데이트) 파이프라인을 사용 하 여 알려진 보안 구성을 배포 합니다.

- [Azure DevOps에 코드를 저장하는 방법](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Azure Repos 설명서](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스에 대 한 구성 관리 도구 배포

**지침**: 기본 제공 Azure Policy Azure Policy 정의 및 "Microsoft 웹" 네임 스페이스의 별칭을 사용 하 여 시스템 구성을 경고, 감사 및 적용 하기 위한 사용자 지정 정책을 만들 수 있습니다. 정책 예외를 관리 하기 위한 프로세스 및 파이프라인을 개발 합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대 한 자동화 된 구성 모니터링 구현

**지침**: 기본 제공 Azure Policy Azure Policy 정의 및 "Microsoft 웹" 네임 스페이스의 별칭을 사용 하 여 시스템 구성을 경고, 감사 및 적용 하기 위한 사용자 지정 정책을 만들 수 있습니다. 

Azure 리소스에 대 한 구성을 자동으로 적용 하는 효과, [감사], [deny] 및 [없는 경우 배포] Azure Policy 적용 합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: 관리 되는 id를 사용 하 여 App Service 앱에 Azure Active Directory (Azure AD)에서 자동으로 관리 되는 id를 제공 합니다. 관리 되는 Id를 사용 하면 코드에 자격 증명 없이 Key Vault를 포함 하 여 Azure AD 인증을 지 원하는 모든 서비스에 앱을 인증할 수 있습니다. Azure Key Vault에서 일시 삭제를 사용 하도록 설정 했는지 확인 합니다.

- [Azure Key Vault에서 일시 삭제를 사용 하도록 설정 하는 방법](/azure/key-vault/key-vault-soft-delete-powershell)

- [App Service에 관리 id를 사용 하는 방법](overview-managed-identity.md)

- [관리 id를 사용 하 여 Key Vault 인증을 제공 하는 방법](/azure/key-vault/managed-identity)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: 관리 되는 id를 사용 하 여 Azure Active Directory (Azure AD)에서 자동으로 관리 되는 id로 App Service 배포 된 앱을 제공 합니다. 관리 되는 Id를 사용 하면 코드에 자격 증명 없이 Key Vault를 포함 하 여 Azure AD 인증을 지 원하는 모든 서비스에 앱을 인증할 수 있습니다.

- [App Service에 관리 id를 사용 하는 방법](overview-managed-identity.md)

- [관리 id를 사용 하 여 Key Vault 인증을 제공 하는 방법](/azure/key-vault/managed-identity)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure 보안 벤치 마크: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조 하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 정기 자동 백업 확인

**지침**: App Service의 백업 및 복원 기능을 사용 하 여 수동으로 또는 일정에 따라 앱 백업을 쉽게 만들 수 있습니다. 백업이 무기한 보존되도록 구성할 수 있습니다. 기존 앱을 덮어쓰거나 다른 앱으로 복원하여 앱을 이전 상태의 스냅샷으로 복원할 수 있습니다.

앱에서 사용 하도록 구성한 Azure storage 계정 및 컨테이너에 다음 정보를 백업할 수 App Service.
- 앱 구성
- 파일 콘텐츠
- 앱에 연결된 데이터베이스

정기적이 고 자동화 된 백업이 조직 정책에 정의 된 빈도로 발생 하는지 확인 합니다.

- [Azure App Service 백업 기능 이해](manage-backup.md)

- [Azure Storage 암호화를 위한 고객 관리 키](https://docs.microsoft.com/azure/storage/common/customer-managed-keys-overview?toc=/azure/storage/blobs/toc.json)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업을 수행 하 고 고객 관리 키를 백업 합니다.

**지침**: App Service의 백업 및 복원 기능을 사용 하 여 응용 프로그램을 백업 합니다. 백업 기능에는 응용 프로그램의 백업 정보를 저장 하는 Azure Storage 계정이 필요 합니다.

- 미사용 암호화 기능을 제공 하는 Azure Storage는 시스템 제공 키 또는 고객이 관리 하는 고유한 키를 사용 합니다. Azure의 웹 앱에서 실행 되 고 있지 않을 때 응용 프로그램 데이터가 저장 되는 위치입니다.
- 배포 패키지에서 실행은 App Service의 배포 기능입니다. SAS (공유 액세스 서명) URL을 사용 하 여 Azure Storage 계정에서 사이트 콘텐츠를 배포할 수 있습니다.

- Key Vault 참조는 App Service의 보안 기능입니다. 이를 통해 런타임에 응용 프로그램 설정으로 암호를 가져올 수 있습니다. 이를 사용 하 여 Azure Storage 계정의 SAS URL을 암호화 합니다.

참조 된 링크에서 자세한 내용을 확인할 수 있습니다.

- [Azure에서 앱 백업](manage-backup.md)

- [Azure App Service에서 실행 되는 앱 복원](web-sites-restore.md)

- [Azure에서 미사용 암호화 이해](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [암호화 모델 및 키 관리 테이블](../security/fundamentals/encryption-atrest.md)

- [고객 관리 키를 사용 하 여 미사용 암호화](configure-encrypt-at-rest-using-cmk.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 비롯 한 모든 백업 유효성 검사

**지침**: App Service 응용 프로그램의 모든 백업에 대 한 복원 프로세스를 주기적으로 테스트 합니다.

- [Azure에서 앱 백업](manage-backup.md)

- [Azure App Service 웹 앱을 복원 하는 방법](web-sites-restore.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객이 관리 하는 키를 보호 해야 합니다.

**지침**: App Service 백업은 Azure Storage 계정 내에 저장 됩니다. Azure Storage의 데이터는 256 비트 AES 암호화를 사용 하 여 투명 하 게 암호화 되 고 해독 되며, 사용 가능한 가장 강력한 블록 암호화 중 하나 이며 FIPS 140-2 규격입니다. Azure Storage 암호화는 Windows의 BitLocker 암호화와 비슷합니다.

Azure Storage 암호화는 리소스 관리자 및 클래식 저장소 계정을 포함 하 여 모든 저장소 계정에 대해 사용 하도록 설정 됩니다. Azure Storage 암호화를 사용 하지 않도록 설정할 수 없습니다. 기본적으로 데이터 보안을 유지 하기 때문에 Azure Storage 암호화를 활용 하기 위해 코드 또는 응용 프로그램을 수정할 필요가 없습니다.

기본적으로 저장소 계정의 데이터는 Microsoft 관리 키를 사용 하 여 암호화 됩니다. Microsoft에서 관리 하는 키를 사용 하 여 데이터를 암호화 하거나 자신의 키를 사용 하 여 암호화를 관리할 수 있습니다. Azure Key Vault에서 일시 삭제를 사용 하도록 설정 했는지 확인 합니다.

- [휴지 상태의 데이터에 대 한 Azure Storage 암호화 이해](../storage/common/storage-service-encryption.md)

- [Azure Key Vault에서 일시 삭제를 사용 하도록 설정 하는 방법](/azure/key-vault/key-vault-soft-delete-powershell)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [Azure 보안 벤치 마크: 인시던트 응답](../security/benchmarks/security-control-incident-response.md)을 참조 하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 검색에서 사후 검토에 이르는 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할을 정의하는 인시던트 대응 계획이 있는지 확인합니다.

- [Azure Security Center 내에서 워크플로 자동화를 구성하는 방법](../security-center/security-center-planning-and-operations-guide.md)

- [자체 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [또한 고객은 NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용 하 여 고유한 인시던트 대응 계획을 만드는 데 도움이 될 수 있습니다.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center에서 심각도를 각 경고에 할당하여 먼저 조사해야 하는 경고에 대한 우선 순위를 지정합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용된 결과 또는 분석의 신뢰도 및 경고가 발생된 활동의 배후에 악의적인 의도가 있었음에 대한 신뢰 수준을 기준으로 합니다.

또한 구독 (예: 프로덕션, 비프로덕션)을 명확 하 게 표시 하 고 Azure 리소스를 명확 하 게 식별 하 고 범주화 하기 위한 이름 지정 시스템을 만듭니다.

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 정기적으로 시스템의 인시던트 응답 기능을 테스트 하는 연습을 수행 합니다. 약점과 결함을 식별하고 필요에 따라 계획을 수정합니다.

- [IT 계획 및 기능에 대 한 테스트, 학습 및 연습 프로그램은 NIST의 게시 가이드를 참조 하세요.](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 고객 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다.  문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: 연속 내보내기 기능을 사용 하 여 Security Center 경고 및 권장 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Security Center data connector를 사용 하 여 비즈니스 요구에 따라 경고 센티널을 스트리밍합니다.

- [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Security Center의 워크플로 자동화 기능을 사용 하 여 보안 경고 및 권장 사항에 대 한 "Logic Apps"를 통해 응답을 자동으로 트리거합니다.

- [워크플로 자동화 및 Logic Apps를 구성하는 방법](../security-center/workflow-automation.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure 보안 벤치 마크: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조 하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대 한 정기적인 침투 테스트를 수행 하 고 모든 중요 한 보안 결과를 수정 하세요.

**지침**: 다음 Microsoft 시행 규칙에 따라 침투 테스트에서 Microsoft 정책을 위반하지 않는지 확인합니다. https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Microsoft에서 관리 하는 클라우드 인프라, 서비스 및 응용 프로그램에 대 한 레드 팀 및 라이브 사이트 침투 테스트에 대 한 자세한 정보를 확인할 수 있습니다.

- [Microsoft 클라우드 Red 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
