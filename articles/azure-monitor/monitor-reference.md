---
title: Azure 모니터에서 모니터링하는 내용
description: Azure Monitor에서 모니터링하는 모든 서비스 및 기타 리소스에 대한 참조입니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2020
ms.openlocfilehash: 3cd330e9c4ceba2feeb7a74cafe9f094fd03d690
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249166"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Azure Monitor에서 모니터링하는 항목
이 문서에서는 Azure Monitor에서 모니터링하는 다양한 응용 프로그램 및 서비스에 대해 설명합니다. 

## <a name="insights-and-core-solutions"></a>인사이트 및 핵심 솔루션
핵심 인사이트 및 솔루션은 Azure Monitor의 일부로 간주되며 Azure에 대한 지원 및 서비스 수준 계약을 따릅니다. Azure 모니터를 사용할 수 있는 모든 Azure 지역에서 지원됩니다.

### <a name="insights"></a>자세한 정보

Insights는 특정 응용 프로그램 및 서비스에 대한 맞춤형 모니터링 환경을 제공합니다. 로그와 메트릭을 모두 수집하고 분석합니다.

| 인사이트 | 설명 |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | 모든 플랫폼에서 라이브 웹 응용 프로그램을 모니터링하는 확장 가능한 응용 프로그램 성능 관리(APM) 서비스입니다. |
| [컨테이너용 Azure 모니터](insights/container-insights-overview.md) | Azure 컨테이너 인스턴스 또는 관리되는 Kubernetes 클러스터에 배포된 컨테이너 워크로드의 성능을 모니터링합니다. |
| [코스모스 DB용 Azure 모니터(미리 보기)](insights/cosmosdb-insights-overview.md) | 통합대화형 환경에서 모든 Azure Cosmos DB 리소스의 전반적인 성능, 실패, 용량 및 운영 상태를 볼 수 있습니다. |
| [네트워크를 위한 Azure 모니터(미리 보기)](insights/network-insights-overview.md) | 모든 네트워크 리소스에 대한 상태 및 메트릭에 대한 포괄적인 보기를 제공합니다. 고급 검색 기능을 사용하면 리소스 종속성을 식별할 수 있으므로 웹 사이트 이름을 검색하기만 하면 웹 사이트를 호스팅하는 리소스를 식별하는 등의 시나리오가 가능합니다. |
[리소스 그룹에 대한 Azure 모니터(미리 보기)](insights/resource-group-insights.md) |  리소스 그룹의 상태 및 성능에 대한 컨텍스트를 제공하면서 개별 리소스가 발생하는 문제를 분류하고 진단합니다. |
| [저장소용 Azure 모니터(미리 보기)](insights/storage-insights-overview.md) | Azure Storage 서비스 성능, 용량 및 가용성에 대한 통합보기를 제공하여 Azure Storage 계정에 대한 포괄적인 모니터링을 제공합니다. |
| [VM용 Azure 모니터(미리 보기)](insights/container-insights-overview.md) | Azure 가상 컴퓨터(VM) 및 가상 시스템 규모 집합을 대규모로 모니터링합니다. 또한 Windows 및 Linux VM의 성능과 상태를 분석하고, 프로세스와 다른 리소스 및 외부 프로세스에 대한 종속성을 모니터링합니다. |

### <a name="core-solutions"></a>핵심 솔루션

솔루션은 특정 응용 프로그램 또는 서비스에 맞게 사용자 지정된 로그 쿼리 및 보기를 기반으로 합니다. 로그만 수집하고 분석하며 시간이 지남에 따라 통찰력을 위해 더 이상 사용되지 않습니다.

| 해결 방법 | 설명 |
|:---|:---|
| [에이전트 상태](insights/solution-agenthealth.md) | 로그 분석 에이전트의 상태 및 구성을 분석합니다. |
| [경고 관리](platform/alert-management-solution.md) | 시스템 센터 운영 관리자, Nagios 또는 Zabbix에서 수집한 경고를 분석합니다. |
| [서비스 맵](insights/service-map.md) | Windows 및 Linux 시스템에서 응용 프로그램 구성 요소를 자동으로 검색하고 서비스 간의 통신을 매핑합니다. |



## <a name="azure-services"></a>Azure 서비스
다음 표에는 Azure 서비스와 Azure 모니터에 수집하는 데이터가 나열되어 있습니다. 

- 메트릭 - 서비스는 자동으로 Azure 모니터 메트릭으로 메트릭을 수집합니다. 
- 로그 - 서비스는 Azure Monitor Log에 대한 플랫폼 로그 및 메트릭을 수집할 수 있는 진단 설정을 지원합니다.
- 인사이트 - 서비스에 대한 사용자 지정 모니터링 환경을 제공하는 서비스에 사용할 수 있는 인사이트가 있습니다.

| 서비스 | 메트릭 | 로그 | 인사이트 | 메모 |
|:---|:---|:---|:---|:---|
|Active Directory | 예 | yes | [예](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | 예 | 예 | 예 |  |
|Active Directory Domain Services | 예 | yes | 예 |  |
|활동 로그 | 예 | yes | 예 | |
|고급 위협 보호 | 예 | 예 | 예 |  |
|Advisor | 예 | 예 | 예 |  |
|AI 작성기 | 예 | 예 | 예 |  |
|Analysis Services | yes | yes | 예 |  |
|API for FHIR | 예 | 예 | 예 |  |
|API Management | yes | yes | 예 |  |
|App Service | yes | yes | 예 |  |
|AppConfig | 예 | 예 | 예 |  |
|Application Gateway | yes | yes | 예 |  |
|증명 서비스 | 예 | 예 | 예 |  |
|Automation | yes | yes | 예 |  |
|Azure 서비스 관리자(RDFE) | 예 | 예 | 예 |  |
|Backup | 예 | yes | 예 |  |
|Bastion | 예 | 예 | 예 |  |
|Batch | yes | yes | 예 |  |
|Batch AI | 예 | 예 | 예 |  |
|Blockchain Service | 예 | yes | 예 |  |
|Blueprints | 예 | 예 | 예 |  |
|Bot 서비스 | 예 | 예 | 예 |  |
|Cloud Services | yes | yes | 예 | 게스트 운영 체제 및 워크플로를 모니터링하는 데 필요한 에이전트입니다.  |
|Cloud Shell | 예 | 예 | 예 |  |
|Cognitive Services | yes | yes | 예 |  |
|Container Instances | yes | 예 | 예 |  |
|Container Registry | yes | yes | 예 |  |
|CDN(콘텐츠 배달 네트워크) | 예 | yes | 예 |  |
|Cosmos DB | yes | yes | [예](insights/cosmosdb-insights-overview.md) |  |
|Cost Management | 예 | 예 | 예 |  |
|Data Box | 예 | 예 | 예 |  |
|데이터 카탈로그 Gen2 | 예 | 예 | 예 |  |
|데이터 탐색기 | yes | yes | 예 |  |
|Data Factory | yes | yes | 예 |  |
|데이터 팩토리 v2 | 예 | yes | 예 |  |
|Data Share | 예 | 예 | 예 |  |
|Database for MariaDB | yes | yes | 예 |  |
|Database for MySQL | yes | yes | 예 |  |
|Database for PostgreSQL | yes | yes | 예 |  |
|Database Migration Service | 예 | 예 | 예 |  |
|Databricks | 예 | yes | 예 |  |
|DDoS Protection | yes | yes | 예 |  |
|DevOps | 예 | 예 | 예 |  |
|DNS | yes | 예 | 예 |  |
|도메인 이름 | 예 | 예 | 예 |  |
|DPS | 예 | 예 | 예 |  |
|역학 365 고객 참여 | 예 | 예 | 예 |  |
|역학 365 재무 및 운영 | 예 | 예 | 예 |  |
|Event Grid | yes | 예 | 예 |  |
|Event Hubs | yes | yes | 예 |  |
|ExpressRoute | yes | yes | 예 |  |
|방화벽 | yes | yes | 예 |  |
|Front Door | yes | yes | 예 |  |
|함수 | yes | yes | 예 |  |
|HDInsight | 예 | yes | 예 |  |
|HPC Cache | 예 | 예 | 예 |  |
|정보 보호 | 예 | yes | 예 |  |
|Intune | 예 | yes | 예 |  |
|IoT Central | 예 | 예 | 예 |  |
|IoT Hub | yes | yes | 예 |  |
|Key Vault | yes | yes | 예 |  |
|AKS(Azure Kubernetes Service) | 예 | 예 | [예](insights/container-insights-overview.md)  |  |
|Load Balancer | yes | yes | 예 |  |
|Logic Apps | yes | yes | 예 |  |
|Machine Learning Service | 예 | 예 | 예 |  |
|Managed Applications  | 예 | 예 | 예 |  |
|지도  | 예 | 예 | 예 |  |
|Media Services | yes | yes | 예 |  |
|Microsoft Flow | 예 | 예 | 예 |  |
|Microsoft Managed Desktop | 예 | 예 | 예 |  |
|Microsoft PowerApps | 예 | 예 | 예 |  |
|Microsoft Social Engagement | 예 | 예 | 예 |  |
|Microsoft Stream | yes | yes | 예 |  |
|마이그레이션 | 예 | 예 | 예 |  |
|Multi-Factor Authentication | 예 | yes | 예 |  |
|Network Watcher | yes | yes | 예 |  |
|Notification Hubs | yes | 예 | 예 |  |
|공개 데이터 세트 | 예 | 예 | 예 |  |
|정책 | 예 | 예 | 예 |  |
|Power BI | yes | yes | 예 |  |
|Power BI Embedded | 예 | 예 | 예 |  |
|Private Link | 예 | 예 | 예 |  |
|프로젝트 스풀 커뮤니케이션 플랫폼 | 예 | 예 | 예 |  |
|Red Hat OpenShift | 예 | 예 | 예 |  |
|Redis Cache | yes | yes | 예 |  |
|Resource Graph | 예 | 예 | 예 |  |
|리소스 관리자 | 예 | 예 | 예 |  |
|소매 검색 – 빙에 의해 | 예 | 예 | 예 |  |
|검색 | yes | yes | 예 |  |
|Service Bus | yes | yes | 예 |  |
|Service Fabric | 예 | yes | 예 | 게스트 운영 체제 및 워크플로를 모니터링하는 데 필요한 에이전트입니다.  |
|가입 포털 | 예 | 예 | 예 |  |
|Site Recovery | 예 | yes | 예 |  |
|스프링 클라우드 서비스 | 예 | 예 | 예 |  |
|SQL Data Warehouse | yes | yes | 예 |  |
|SQL Database | yes | yes | 예 |  |
|SQL Server Stretch Database | yes | yes | 예 |  |
|스택 | 예 | 예 | 예 |  |
|스토리지 | yes | 예 | [예](insights/storage-insights-overview.md) |  |
|스토리지 캐시 | 예 | 예 | 예 |  |
|스토리지 동기화 서비스 | 예 | 예 | 예 |  |
|Stream Analytics | yes | yes | 예 |  |
|Time Series Insights | yes | yes | 예 |  |
|TINA | 예 | 예 | 예 |  |
|Traffic Manager | yes | yes | 예 |  |
|범용 인쇄 | 예 | 예 | 예 |  |
|Virtual Machine Scale Sets | 예 | yes | [예](insights/vminsights-overview.md) | 게스트 운영 체제 및 워크플로를 모니터링하는 데 필요한 에이전트입니다. |
|Virtual Machines | yes | yes | [예](insights/vminsights-overview.md) | 게스트 운영 체제 및 워크플로를 모니터링하는 데 필요한 에이전트입니다. |
|Virtual Network | yes | yes | [예](insights/network-insights-overview.md) |  |
|가상 네트워크 - NSG 흐름 로그 | 예 | yes | 예 |  |
|VPN Gateway | yes | yes | 예 |  |
|Windows Virtual Desktop | 예 | 예 | 예 |  |


## <a name="product-integrations"></a>제품 통합
다음 표의 서비스 및 솔루션은 Azure Monitor에서 수집한 다른 로그 데이터로 분석할 수 있도록 데이터를 Log Analytics 작업 영역에 저장합니다.

| 제품/서비스 | 설명 |
|:---|:---|
| [Azure 자동화](/azure/automation/) | 운영 체제 업데이트를 관리하고 Windows 및 Linux 컴퓨터에서 변경 내용을 추적합니다. [변경 정보 추적](../automation/change-tracking.md) 및 업데이트 [관리를](../automation/automation-update-management.md)참조하십시오. |
| [Azure 정보 보호](https://docs.microsoft.com/azure/information-protection/) | 문서와 전자 메일을 분류하고 선택적으로 보호합니다. [Azure 정보 보호를 위한 중앙 보고를](https://docs.microsoft.com/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports)참조하십시오. |
| [Azure 보안 센터](/azure/security-center/) | 보안 이벤트를 수집 및 분석하고 위협 분석을 수행합니다. [Azure 보안 센터에서 데이터 수집](/azure/security-center/security-center-enable-data-collection) 참조 |
| [푸른 파수꾼](/azure/sentinel/) | Office 365 및 Amazon 웹 서비스 클라우드 트레일을 비롯한 다양한 소스에 연결합니다. [데이터 원본 연결을](/azure/sentinel/connect-data-sources)참조하십시오. |
| [Key Vault 분석](insights/azure-key-vault.md) | Azure 키 볼트 감사이벤트 로그를 분석합니다. |
| [Microsoft Intune](https://docs.microsoft.com/intune/) | Azure Monitor에 로그를 보내는 진단 설정을 만듭니다. [Intune(미리 보기)에서 로그 데이터를 저장소, 이벤트 허브 또는 로그 분석으로 보내기](https://docs.microsoft.com/intune/fundamentals/review-logs-using-azure-monitor)를 참조하십시오.  |
| 네트워크  | [네트워크 성능 모니터](insights/network-performance-monitor.md) - 서비스 및 애플리케이션 엔드포인트에 대한 네트워크 연결 및 성능을 모니터링합니다.<br>[Azure 응용 프로그램 게이트웨이](insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) - Azure 응용 프로그램 게이트웨이에서 로그 및 메트릭을 분석합니다.<br>[트래픽 분석](/azure/network-watcher/traffic-analytics) - NSG(네트워크 감시자 네트워크 보안 그룹) 흐름 로그를 분석하여 Azure 클라우드의 트래픽 흐름에 대한 통찰력을 제공합니다. |
| [사무실 365](insights/solution-office-365.md) | Office 365 환경을 모니터링합니다. Azure Sentinel을 통해 사용할 수 있는 향상된 온보딩버전으로 업데이트되었습니다. |
| [SQL 분석](insights/azure-sql.md) | 규모및 여러 구독에서 Azure SQL 데이터베이스, 탄력적 풀 및 관리되는 인스턴스의 성능을 모니터링합니다. |
| [Surface Hub](insights/surface-hubs.md) | Surface Hub 장치의 상태 및 사용량을 추적합니다. |
| [System Center Operations Manager](https://docs.microsoft.com/system-center/scom) | 관리 그룹을 Azure Monitor에 연결하여 Operations Manager 에이전트에서 데이터를 수집합니다. [Azure 모니터에 작업 관리자 연결](platform/om-agents.md) 참조<br> [운영 관리자 평가](insights/scom-assessment.md) 솔루션을 통해 시스템 센터 운영 관리자 관리 그룹의 위험 및 상태를 평가합니다. |
| [마이크로소프트 팀 룸](https://docs.microsoft.com/microsoftteams/room-systems/azure-monitor-deploy) | Microsoft Teams Rooms 장치의 통합된 엔드 투 엔드 관리 |
| [Visual Studio App Center](https://docs.microsoft.com/appcenter/) | 응용 프로그램을 빌드, 테스트 및 배포한 다음 응용 프로그램의 상태 및 사용량을 모니터링합니다. [앱 센터 및 응용 프로그램 인사이트를 통해 모바일 앱 분석 시작을 참조하세요.](learn/mobile-center-quickstart.md) |
| Windows | [Windows 업데이트 규정 준수](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started) - Windows 데스크톱 업그레이드를 평가합니다.<br>[데스크톱 분석](https://docs.microsoft.com/configmgr/desktop-analytics/overview) - 구성 관리자와 통합하여 Windows 클라이언트의 업데이트 준비에 대한 정보에 입각한 의사 결정을 내릴 수 있는 통찰력과 인텔리전스를 제공합니다. |



## <a name="other-solutions"></a>기타 솔루션
다른 솔루션은 다른 응용 프로그램 및 서비스를 모니터링하는 데 사용할 수 있지만 활성 개발이 중지되어 모든 지역에서 사용하지 못할 수 있습니다. Azure Log Analytics 데이터 수집 서비스 수준 계약에 의해 처리됩니다.

| 해결 방법 | 설명 |
|:---|:---|
| [Active 디렉터리 상태 확인](insights/ad-assessment.md) | Active Directory 환경의 위험과 상태를 평가합니다. |
| [활성 디렉터리 복제 상태](insights/ad-replication-status.md) | Active Directory 환경을 정기적으로 모니터링하여 복제 실패가 있는지 확인합니다. |
| [활동 로그 분석](platform/activity-log-view.md#activity-logs-analytics-monitoring-solution) | 미리 정의된 로그 쿼리 및 보기를 사용하여 활동 로그 항목을 분석합니다. |
| [DNS 분석(미리 보기)](insights/dns-analytics.md) | DNS 서버에서 Windows DNS 분석 및 감사 로그 및 기타 관련 데이터를 수집, 분석 및 상호 연관시다. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | 여러 배포에서 클라우드 파운드리 시스템 상태 및 성능 메트릭을 수집, 보기 및 분석합니다. |
| [컨테이너](insights/containers.md) | Docker 및 Windows 컨테이너 호스트를 보고 관리합니다. |
| [온디맨드 평가](https://docs.microsoft.com/services-hub/health/getting_started_with_on_demand_assessments) | 온-프레미스, 하이브리드 및 클라우드 Microsoft 기술 환경의 가용성, 보안 및 성능을 평가하고 최적화합니다. |
| [SQL 상태 확인](insights/sql-assessment.md) | SQL Server 환경의 위험과 상태를 평가합니다.  |
| [실시간 데이터](insights/wire-data.md) | 로그 분석 에이전트와 Windows 연결 및 Linux 연결 컴퓨터에서 수집된 통합 네트워크 및 성능 데이터입니다. |


## <a name="third-party-integration"></a>타사 통합

| 해결 방법 | 설명 |
|:---|:---|
| [ITSM](platform/itsmc-overview.md) | ITSMC(IT 서비스 관리 커넥터)를 사용하면 Azure와 지원되는 ITSM(IT 서비스 관리) 제품/서비스를 연결할 수 있습니다.  |


## <a name="resources-outside-of-azure"></a>Azure 외부리소스
Azure Monitor는 다음 표에 나열된 메서드를 사용하여 Azure 외부의 리소스에서 데이터를 수집할 수 있습니다.

| 리소스 | 방법 |
|:---|:---|
| 애플리케이션 | 응용 프로그램 인사이트를 사용하여 Azure 외부의 웹 응용 프로그램을 모니터링합니다. [응용 프로그램 인사이트는 무엇입니까?](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). |
| 가상 머신 | Log Analytics 에이전트를 사용하여 다른 클라우드 환경 또는 온-프레미스에 있는 가상 시스템의 게스트 운영 체제에서 데이터를 수집합니다. [로그 분석 에이전트를 사용하여 로그 데이터 수집을](platform/log-analytics-agent.md)참조하십시오. |
| REST API 클라이언트 | REST API 클라이언트에서 Azure 모니터 로그 및 메트릭에 데이터를 쓸 수 있는 별도의 API를 사용할 수 있습니다. 로그에 대한 [HTTP 데이터 수집API를 사용하여 Azure 모니터로 로그 데이터 보내기 데이터 보내기](platform/data-collector-api.md) 및 메트릭에 대한 REST API를 사용하여 Azure [리소스에 대한 사용자 지정 메트릭을 Azure Monitor 메트릭 저장소로 보냅니다.](platform/metrics-store-custom-rest-api.md) |



## <a name="next-steps"></a>다음 단계

- [인사이트 및 솔루션에서 수집한 로그 및 메트릭을 저장하는 Azure Monitor 데이터 플랫폼에](platform/data-platform.md)대해 자세히 알아보기.
- Azure [리소스 모니터링에 대한 자습서를](learn/tutorial-resource-logs.md)완료합니다.
- Azure [모니터 로그에서 데이터를 분석하기 위해 로그 쿼리 작성에 대한 자습서를 완료합니다.](learn/tutorial-resource-logs.md)
- Azure [모니터 메트릭의 데이터를 분석하는 메트릭 차트 만들기에 대한 자습서를 완료합니다.](learn/tutorial-metrics-explorer.md)

 
