---
title: Azure Monitor의 데이터 원본 | Microsoft Docs
description: Azure 리소스 및 리소스에서 실행되는 애플리케이션의 성능과 상태를 모니터링할 수 있는 데이터를 설명합니다.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: bwren
ms.openlocfilehash: b77fb3ab5651147c59b9f0afd22a2d6d0159c90e
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357454"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Azure Monitor에 대 한 모니터링 데이터의 원본
Azure Monitor 기반를 [일반적인 모니터링 데이터 플랫폼](data-platform.md) 포함 하는 [로그](data-platform-logs.md) 하 고 [메트릭](data-platform-metrics.md). 이 플랫폼으로 데이터를 수집 합니다. Azure Monitor에서 공통 도구 집합을 사용 하 여 함께 분석 하려는 여러 리소스의에서 데이터를 수 있습니다. 모니터링 데이터도 전송할 수 있습니다 다른 위치로 특정 시나리오를 지원 하 고 일부 리소스를 로그 또는 메트릭 수집 되기 전에 다른 위치에 쓸 수 있습니다.

이 문서에서는 Azure 리소스에서 만들어지고 모니터링 데이터 외에도 Azure Monitor에서 수집한 데이터를 모니터링의 다양 한 소스를 설명 합니다. 링크는 다른 위치에이 데이터를 수집 하는 데 필요한 구성에 대 한 자세한 정보에 제공 됩니다.

## <a name="application-tiers"></a>애플리케이션 계층

Azure 응용 프로그램에서 데이터를 모니터링 하는 원본 계층, 응용 프로그램 자체를 가장 높은 계층이 및 Azure 플랫폼의 구성 요소 중 더 낮은 계층으로 구성할 수 있습니다. 각 계층에서 데이터를 액세스 하는 방법은 다릅니다. 아래 표에 요약 되어 응용 프로그램 계층 및 각 계층에서 모니터링 데이터의 소스는 다음 섹션에 표시 됩니다. 참조 [Azure에서 데이터 위치를 모니터링](data-locations.md) 각 데이터 위치 및 해당 데이터를 액세스 하는 방법에 대 한 합니다.


![계층 모니터링](../media/overview/overview.png)


### <a name="azure"></a>Azure
다음 표에서 Azure에 관련 된 응용 프로그램 계층을 간략하게 설명 합니다. 추가 대 한 링크를 따라 각 아래 섹션에 자세히 설명 합니다.

| 계층 | 설명 | 컬렉션 메서드 |
|:---|:---|:---|
| [Azure Tenant](#azure-tenant) | Azure Active Directory 등, 테넌트 수준 Azure 서비스의 작업에 대한 데이터입니다. | 포털에서 AAD 데이터를 보거나 테 넌 트 진단 설정을 사용 하 여 Azure Monitor에는 컬렉션을 구성 합니다. |
| [Azure 구독](#azure-subscription) | 상태 및 Resource Manager 및 서비스 상태와 같은 Azure 구독에서 리소스 간 서비스 관리와 관련 된 데이터입니다. | 포털에서 보거나 로그 프로필을 사용 하 여 Azure Monitor에는 컬렉션을 구성 합니다. |
| [Azure 리소스](#azure-resources) |  작업 및 각 Azure 리소스의 성능에 대 한 데이터입니다. | 자동으로 수집 된 메트릭을 메트릭 탐색기에서 봅니다.<br>Azure Monitor의 로그를 수집할 진단 설정을 구성 합니다.<br>솔루션 및 특정 리소스 종류에 대 한 자세한 모니터링에 사용할 수 있는 정보를 모니터링 합니다. |

### <a name="azure-other-cloud-or-on-premises"></a>Azure, 다른 클라우드 또는 온-프레미스 
다음 표에서 Azure, 다른 클라우드 또는 온-프레미스에 있을 수 있는 응용 프로그램 계층을 간략하게 설명 합니다. 추가 대 한 링크를 따라 각 아래 섹션에 자세히 설명 합니다.

| 계층 | 설명 | 컬렉션 메서드 |
|:---|:---|:---|
| [운영 체제 (게스트)](#operating-system-guest) | 계산 리소스의 운영 체제에 대 한 데이터입니다. | Azure Monitor 및 종속성 수집 하도록 에이전트를 Vm에 대 한 Azure Monitor를 지 원하는 종속성으로 클라이언트 데이터 원본 수집 하도록 Log Analytics 에이전트를 설치 합니다.<br>Azure virtual machines에 대 한 로그 및 메트릭을 Azure Monitor로 수집 하도록 Azure 진단 확장을 설치 합니다. |
| [응용 프로그램 코드](#application-code) | 성능 및 실제 응용 프로그램 성능 추적, 응용 프로그램 로그 및 사용자 원격 분석을 포함 하 여 코드의 기능에 대 한 데이터입니다. | Application Insights로 데이터를 수집 하기 위해 코드를 계측 합니다. |
| [사용자 지정 원본](#custom-sources) | 외부 서비스 또는 다른 구성 요소 또는 장치에서 데이터를 제공 합니다. | 모든 REST 클라이언트에서 Azure Monitor로 로그 또는 메트릭 데이터를 수집 합니다. |

## <a name="azure-tenant"></a>Azure 테넌트
Azure 테넌트와 관련된 원격 분석은 Azure Active Directory와 같은 테넌트 전체 서비스에서 수집됩니다.

![Azure 테넌트 컬렉션](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Azure Active Directory 감사 로그
[Azure Active Directory 보고](../../active-directory/reports-monitoring/overview-reports.md)는 로그인 활동 및 특정 테넌트 내의 변경 감사 내역에 대한 기록을 포함합니다. 

| 대상 | 설명 | 참조 |
|:---|:---|:---|
| Azure Monitor 로그 | 기타 모니터링 데이터를 사용 하 여 분석 하는 Azure Monitor에서 수집할 로그 Azure AD를 구성 합니다. | [Azure Monitor 로그 (미리 보기)를 사용 하 여 Azure AD 로그 통합](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Azure Storage에 보관 하는 것에 대 한 Azure AD 로그를 내보냅니다. | [자습서: Azure storage 계정 (미리 보기)에 Azure AD 로그 보관](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| 이벤트 허브 | Azure AD Stream Event Hubs를 사용 하 여 다른 위치에 기록 합니다. | [자습서: Azure 이벤트 허브 (미리 보기)를 Azure Active Directory 로그 Stream](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)합니다. |



## <a name="azure-subscription"></a>Azure 구독
상태 및 Azure 구독 작업에 관련 된 원격 분석 합니다.

![Azure 구독](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Azure 활동 로그 
합니다 [Azure 활동 로그](activity-logs-overview.md) Azure 구독의 리소스에 대 한 구성 변경 내용에 대 한 레코드와 함께 서비스 상태 레코드를 포함 합니다. 활동 로그는 모든 Azure 리소스에 대해 사용할 수 있으며 해당 _외부_ 보기를 나타냅니다.

| 대상 | 설명 | 참조 |
|:---|:---|
| 활동 로그 | 활동 로그는 Azure Monitor 메뉴에서 보기 또는 활동 로그 경고를 만드는 데 사용할 수 있는 자체 데이터 저장소에 수집 됩니다. | [Azure portal에서 활동 로그 쿼리](activity-log-view.md#azure-portal) |
| Azure Monitor 로그 | 기타 모니터링 데이터를 사용 하 여 분석에 활동 로그를 수집 하려면 Azure Monitor 로그를 구성 합니다. | [Azure Monitor에서 Log Analytics 작업 영역에서 Azure 활동 로그 수집 및 분석](activity-log-collect.md) |
| Azure Storage | Azure Storage에 보관 하는 것에 대 한 활동 로그를 내보냅니다. | [활동 로그 보관](activity-log-export.md#archive-activity-log)  |
| Event Hubs | Stream Event Hubs를 사용 하 여 다른 위치에 활동 로그 | [활동 로그 이벤트 허브를 Stream](activity-log-export.md#stream-activity-log-to-event-hub)합니다. |

### <a name="azure-service-health"></a>Azure Service Health
[Azure Service Health](../../service-health/service-health-overview.md)는 애플리케이션 및 리소스가 의존하는 구독에서 Azure 서비스의 상태에 대한 정보를 제공합니다.

| 대상 | 설명 | 참조 |
|:---|:---|:---|
| 활동 로그<br>Azure Monitor 로그 | 서비스 상태 레코드는 활동 로그로 수행할 수 있는 다른 작업을 수행 하거나 Azure portal에서 볼 수 있도록 Azure 활동 로그에 저장 됩니다. | [Azure Portal을 사용하여 서비스 상태 알림 보기](service-notifications.md) |


## <a name="azure-resources"></a>Azure 리소스
메트릭 및 리소스 수준 진단 로그는 Azure 리소스의 _내부_ 작업에 대한 정보를 제공합니다. 이러한 대부분의 Azure 서비스에 사용할 수 있으며 모니터링 솔루션과 insights 특정 서비스에 대 한 추가 데이터를 수집 합니다.

![Azure 리소스 컬렉션](media/data-sources/azure-resources.png)


### <a name="platform-metrics"></a>플랫폼 메트릭 
대부분의 Azure 서비스를 송신할 [플랫폼 메트릭](data-platform-metrics.md) 해당 성능 및 작업 메트릭 데이터베이스에 직접 반영 하는 합니다. 특정 [메트릭은 리소스의 각 형식마다 다릅니다](metrics-supported.md). 

| 대상 | 설명 | 참조 |
|:---|:---|:---|
| Azure Monitor 메트릭 | 플랫폼 메트릭 구성 없이 Azure Monitor 메트릭 데이터베이스에 작성 합니다. 메트릭 탐색기에서 플랫폼 메트릭에 액세스 합니다.  | [Azure 메트릭 탐색기를 사용 하 여 시작](metrics-getting-started.md)<br>[Azure Monitor에서 지원되는 메트릭](metrics-supported.md) |
| Azure Monitor 로그 | 로그를 Log Analytics를 사용 하 여 추세 및 다른 분석 플랫폼 메트릭에 복사 합니다. | [Log Analytics에 직접 azure 진단](collect-azure-metrics-logs.md#azure-diagnostics-direct-to-log-analytics) |
| Event Hubs | Event Hubs를 사용 하 여 다른 위치에 Stream 메트릭입니다. |[Azure 모니터링 데이터를 이벤트 허브로 스트리밍하여 외부 도구에서 사용](stream-monitoring-data-event-hubs.md) |

### <a name="diagnostic-logs"></a>진단 로그
[진단 로그](diagnostic-logs-overview.md) 에 대 한 정보를 제공 합니다 _내부_ Azure 리소스의 작업입니다.  진단 로그는 기본적으로 사용할 수 없습니다. 사용 하도록 설정 하 고 각 리소스에 대 한 대상을 지정 해야 합니다. 

구성 요구 사항 및 진단 로그의 콘텐츠를 리소스 유형에 따라 다르며 일부 서비스는 진단 로그를 아직 생성. 참조 [Azure 진단 로그에 대 한 서비스, 스키마 및 범주를 지 원하는](diagnostic-logs-schema.md) 각 서비스 및 자세한 구성 프로시저에 대 한 링크에 대 한 자세한 내용은 합니다. 이 문서에서 서비스 목록에 없으면 해당 서비스 진단 로그에 쓸 현재 하지 않습니다.

| 대상 | 설명 | 참조 |
|:---|:---|:---|
| Azure Monitor 로그 | 다른 수집 된 로그 데이터를 분석 하기 위해 Azure Monitor 로그에 진단 로그를 보냅니다. 일부 리소스는 다른 Log Analytics 작업 영역으로 가져오기 전에 저장소 계정에 작성 하는 동안 Azure Monitor에 직접 쓸 수 있습니다. | [Azure Monitor에서 Log Analytics 작업 영역에 Stream Azure 진단 로그](diagnostic-logs-stream-log-store.md)<br>[Azure portal을 사용 하 여 Azure Storage에서 로그를 수집 하려면](azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage)  |
| Storage | 진단 전송 보관에 대 한 Azure Storage에 로그 합니다. | [Azure 진단 로그 보관](archive-diagnostic-logs.md) |
| Event Hubs | Stream 진단 Event Hubs를 사용 하 여 다른 위치에 기록 합니다. |[Stream 이벤트 허브로 Azure 진단 로그](diagnostic-logs-stream-event-hubs.md) |

## <a name="operating-system-guest"></a>운영 체제 (게스트)
Azure, 다른 클라우드 및 온-프레미스의 계산 리소스에는 모니터링할 게스트 운영 체제가 있습니다. 하나 이상의 에이전트를 설치를 사용 하 여 Azure 서비스 자체와 동일한 모니터링 도구를 사용 하 여 분석 하는 Azure Monitor로 게스트에서 원격 분석을 수집할 수 있습니다.

![Azure 계산 리소스 컬렉션](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Azure 진단 확장
Azure 게스트 운영 체제에서 메트릭을 계산 가상 머신 (클래식) Azure 클라우드 서비스 웹 및 작업자 역할, Virtual Machines를 비롯 한 리소스 및 로그를 수집 하려면 Azure Virtual machines를 사용 하면 Azure 진단 확장을 사용 하도록 설정 scale sets 및 Service Fabric입니다.

| 대상 | 설명 | 참조 |
|:---|:---|:---|
| Storage | 진단 확장을 사용 하도록 설정 하면 저장소 계정에 기본적으로 기록 합니다. | [Azure Storage에서 진단 데이터 저장 및 보기](diagnostics-extension-to-storage.md) |
| Azure Monitor 메트릭 | 성능 카운터를 수집 하려면 진단 확장을 구성 하면 Azure Monitor 메트릭 데이터베이스에 기록 되며. | [Azure Monitor 메트릭에 대 한 게스트 OS 메트릭 Resource Manager 템플릿을 사용 하 여 Windows 가상 머신에 대 한 저장 보내기](collect-custom-metrics-guestos-resource-manager-vm.md) |
| Application Insights 로그 | 다른 응용 프로그램 데이터와 분석 응용 프로그램을 지 원하는 계산 리소스에서 로그 및 성능 카운터를 수집 합니다. | [Application Insights에 클라우드 서비스, 가상 컴퓨터 또는 Service Fabric 진단 데이터 보내기](diagnostics-extension-to-application-insights.md) |
| Event Hubs | Event Hubs를 사용 하 여 다른 위치로 데이터를 스트리밍하는 진단 확장을 구성 합니다.  | [Event Hubs를 사용 하 여 실행 부하 과다 경로에서 Azure 진단 데이터 스트리밍](diagnostics-extension-stream-event-hubs.md) |

### <a name="log-analytics-agent"></a>Log Analytics 에이전트 
포괄적인 모니터링 및 관리에 Windows 또는 Linux virtual machines에 대 한 Log Analytics 에이전트를 설치 합니다. 가상 머신은 Azure, 다른 클라우드 또는 온-프레미스에서 실행될 수 있습니다.

| 대상 | 설명 | 참조 |
|:---|:---|:---|
| Azure Monitor 로그 | Log Analytics 에이전트를 Azure에 연결할 직접적으로 또는 System Center Operations Manager를 통해 모니터링 하 고 구성 하는 데이터 원본에서 또는 모니터링 응용 프로그램에 대 한 추가 정보를 제공 하는 솔루션에서 데이터를 수집할 수 있습니다 가상 머신에서 실행 합니다. | [Azure Monitor의 데이터 원본 에이전트](agent-data-sources.md)<br>[Azure Monitor에 Operations Manager 연결](om-agents.md) |


### <a name="azure-monitor-for-vms"></a>VM용 Azure Monitor 
[Vm에 대 한 azure Monitor](../insights/vminsights-overview.md) 핵심 Azure Monitor 기능, 서비스 상태 및 VM 상태를 포함 하 여 보다 더 뛰어난 기능을 제공 하는 가상 머신에 대 한 사용자 지정된 모니터링 환경을 제공 합니다. 가상 머신과 외부 프로세스 종속성에서 실행 중인 프로세스에 대 한 검색 된 데이터를 수집 하도록 Log analytics와 통합 되는 Windows 및 Linux virtual machines에서 종속성 에이전트가 필요 합니다.

| 대상 | 설명 | 참조 |
|:---|:---|:---|
| Azure Monitor 로그 | 에이전트에서 프로세스 및 종속성에 대 한 데이터를 저장합니다. | [Azure Monitor를 사용 하 여 vm (미리 보기) 응용 프로그램 구성 요소를 이해 하는 맵](../insights/vminsights-maps.md) |
| VM 저장소 | Vm에 대 한 azure Monitor는 사용자 지정 위치에 heath 상태 정보를 저장 합니다. 이 외에 Azure portal에서 Vm에 대 한 Azure Monitor를 사용할 수 있습니다만 합니다 [Azure Resource health REST API](/rest/api/resourcehealth/)합니다. | [Azure virtual machines의 상태를 이해 합니다.](../insights/vminsights-health.md)<br>[Azure Resource health REST API](https://docs.microsoft.com/rest/api/resourcehealth/) |



## <a name="application-code"></a>응용 프로그램 코드
Azure Monitor의 자세한 응용 프로그램 모니터링 작업을 완료 [Application Insights](https://docs.microsoft.com/azure/application-insights/) 다양 한 플랫폼에서 실행 중인 응용 프로그램에서 데이터를 수집 하는 합니다. 애플리케이션은 Azure, 다른 클라우드 또는 온-프레미스에서 실행될 수 있습니다.

![애플리케이션 데이터 수집](media/data-sources/applications.png)


### <a name="application-data"></a>애플리케이션 데이터
계측 패키지를 설치하여 애플리케이션에 대한 Application Insights를 사용하도록 설정하는 경우 애플리케이션의 성능 및 작업과 관련된 메트릭 및 로그를 수집합니다. Application Insights는 다른 데이터 원본에서 사용 하는 동일한 Azure Monitor 데이터 플랫폼에서 수집한 데이터를 저장 합니다. 이 데이터를 분석 하는 것에 대 한 광범위 한 도구 포함 하지만 메트릭 탐색기 및 Log Analytics와 같은 도구를 사용 하 여 다른 원본에서 데이터도 분석할 수 있습니다.

| 대상 | 설명 | 참조 |
|:---|:---|:---|
| Azure Monitor 로그 | 페이지 보기, 응용 프로그램 요청, 예외 및 추적을 포함 하 여 응용 프로그램에 대 한 운영 데이터입니다. | [Azure Monitor의 로그 데이터 분석](../log-query/log-query-overview.md) |
|                    | 원격 분석 상관 관계 및 응용 프로그램 맵을 지원 하도록 응용 프로그램 구성 요소 간의 종속성 정보입니다. | [Application Insights에서 원격 분석 상관 관계](../app/correlation.md) <br> [애플리케이션 맵](../app/app-map.md) |
|            | 가용성과 공용 인터넷에서 서로 다른 위치에서 응용 프로그램의 응답성을 테스트 하는 가용성 테스트의 결과입니다. | [웹 사이트의 가용성 및 응답성 모니터링](../app/monitor-web-app-availability.md) |
| Azure Monitor 메트릭 | Application Insights는 성능 및 Azure Monitor 메트릭 데이터베이스로 응용 프로그램에서 정의 하는 사용자 지정 메트릭 외에도 응용 프로그램의 작업을 설명 하는 메트릭을 수집 합니다. | [Application Insights에서 로그 기반 및 미리 집계 된 메트릭](../app/pre-aggregated-metrics-log-metrics.md)<br>[사용자 지정 이벤트 및 메트릭 용 application Insights API](../app/api-custom-events-metrics.md) |
| Azure Storage | Azure Storage에 보관 하는 것에 대 한 응용 프로그램 데이터를 보냅니다. | [Application Insights에서 원격 분석 내보내기](../app/export-telemetry.md) |
|            | 가용성 테스트의 세부 정보는 Azure Storage에 저장 됩니다. 로컬 분석을 위해 다운로드 하려면 Azure portal에서 Application Insights를 사용 합니다. 가용성 테스트의 결과 Azure Monitor 로그에 저장 됩니다. | [웹 사이트의 가용성 및 응답성 모니터링](../app/monitor-web-app-availability.md) |
|            | Profiler 추적 데이터는 Azure Storage에 저장 됩니다. 로컬 분석을 위해 다운로드 하려면 Azure portal에서 Application Insights를 사용 합니다.  | [프로덕션 응용 프로그램 프로 파일링 Azure에서 Application Insights를 사용 하 여](../app/profiler-overview.md) 
|            | 디버그 스냅숏 예외 하위 집합에 대 한 캡처된 데이터를 Azure Storage에 저장 됩니다. 로컬 분석을 위해 다운로드 하려면 Azure portal에서 Application Insights를 사용 합니다.  | [스냅숏 작동 방식](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>솔루션 및 정보를 모니터링합니다.
[모니터링 솔루션](../insights/solutions.md) 하 고 [Insights](../insights/insights-overview.md) 특정 서비스나 응용 프로그램의 작업에 대 한 추가 정보를 제공 하는 데이터를 수집 합니다. 다른 응용 프로그램 계층 및 여러 계층에서 리소스를 해결할 수 있습니다.

### <a name="monitoring-solutions"></a>모니터링 솔루션

| 대상 | 설명 | 참조
|:---|:---|:---|
| Azure Monitor 로그 | 모니터링 솔루션 데이터를 수집할 Azure Monitor 로그로 수행 될 수 있는 쿼리 언어를 사용 하 여 분석 또는 [뷰](view-designer.md) 는 일반적으로 솔루션에 포함 합니다. | [Azure에서 솔루션을 모니터링 하는 것에 대 한 데이터 수집 세부 정보](../insights/solutions-inventory.md) |


### <a name="azure-monitor-for-containers"></a>컨테이너에 대 한 azure Monitor
[컨테이너에 대 한 azure Monitor](../insights/container-insights-overview.md) 에 대 한 사용자 지정된 모니터링 환경을 제공 [Azure Kubernetes Service (AKS)](/azure/aks/)합니다. 다음 표에 설명 된 이러한 리소스에 대 한 추가 데이터를 수집 합니다.

| 대상 | 설명 | 참조 |
|:---|:---|:---|
| Azure Monitor 로그 | 인벤토리, 로그 및 이벤트를 포함 하 여 AKS에 대 한 모니터링 데이터를 저장 합니다. 메트릭 데이터는 포털에서 분석 기능을 활용 하기 위해 로그에도 저장 됩니다. | [컨테이너용 Azure Monitor를 사용하여 AKS 클러스터 성능 이해](../insights/container-insights-analyze.md) |
| Azure Monitor 메트릭 | 메트릭 데이터는 메트릭 드라이브 시각화 및 경고 데이터베이스에 저장 됩니다. | [메트릭 탐색기에서 컨테이너 메트릭 보기](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes Service | 거의 실시간으로 환경 순서로 컨테이너에 대 한 Azure Monitor는 Azure portal에서 Azure Kubernetes 서비스에서 직접 데이터를 표시합니다. | [Azure Monitor에서 컨테이너 로그를 실시간으로 보는 방법(미리 보기)](../insights/container-insights-live-logs.md) |

### <a name="azure-monitor-for-vms"></a>VM용 Azure Monitor
[Vm에 대 한 azure Monitor](../insights/vminsights-overview.md) 가상 머신 모니터링에 대 한 사용자 지정된 된 환경을 제공 합니다. 에 포함 된 Vm에 대 한 Azure Monitor에서 수집한 데이터에 대 한 설명을 합니다 [운영 체제 (게스트)](#operating-system-guest) 위의 섹션입니다.

## <a name="custom-sources"></a>사용자 지정 원본
애플리케이션의 표준 계층 외에 다른 데이터 원본과 함께 수집할 수 없는 원격 분석이 있는 다른 리소스를 모니터링해야 할 수도 있습니다. 이러한 리소스에 대 한 메트릭 또는 로그는 Azure Monitor API를 사용 하 여이 데이터를 씁니다.

![사용자 지정 컬렉션](media/data-sources/custom.png)

| 대상 | 방법 | 설명 | 참조 |
|:---|:---|:---|:---|
| Azure Monitor 로그 | 데이터 수집기 API | 모든 REST 클라이언트에서 로그 데이터를 수집 하 고 Log Analytics 작업 영역에 저장 합니다. | [HTTP 데이터 수집기 API (공개 미리 보기)를 사용 하 여 Azure Monitor 데이터 로그 보내기](data-collector-api.md) |
| Azure Monitor 메트릭 | 사용자 지정 메트릭 API | 모든 REST 클라이언트 및 Azure Monitor 메트릭 데이터베이스의 저장소에서 메트릭 데이터를 수집 합니다. | [REST API를 사용 하 여 Azure Monitor 메트릭 저장소는 Azure 리소스에 대 한 사용자 지정 메트릭 보내기](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>기타 서비스
Azure의 기타 서비스는 Azure Monitor 데이터 플랫폼으로 데이터를 씁니다. 이 통해 Azure Monitor에서 수집한 데이터를 사용 하 여 이러한 서비스에 수집 된 데이터를 분석 하 고 동일한 분석 및 시각화 도구를 활용할 수 있습니다.

| 서비스 | 대상 | 설명 | 참조 |
|:---|:---|:---|:---|
| [Azure Security Center](/azure/security-center/) | Azure Monitor 로그 | Azure Security Center는 Azure Monitor에서 수집한 다른 로그 데이터를 사용 하 여 분석할 수 있도록 Log Analytics 작업 영역에서 수집 하는 보안 데이터를 저장 합니다.  | [Azure Security Center에서 데이터 수집](../../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](/azure/sentinel/) | Azure Monitor 로그 | Azure Sentinel Azure Monitor에서 수집한 다른 로그 데이터를 사용 하 여 분석할 수 있도록 Log Analytics 작업 영역에서 다른 데이터 원본에서 수집한 데이터를 저장 합니다.  | [데이터 원본 연결](/azure/sentinel/quickstart-onboard) |


## <a name="next-steps"></a>다음 단계

- [Azure Monitor에서 수집한 모니터링 데이터의 유형](data-platform.md)과 이 데이터를 보고 분석하는 방법에 대해 자세히 알아보세요.
- 목록 합니다 [Azure 리소스 데이터를 저장할 다른 위치](data-locations.md) / 액세스 방법도 있습니다. 
