---
title: Azure Monitor의 데이터 원본 | Microsoft Docs
description: Azure 리소스 및 리소스에서 실행되는 애플리케이션의 성능과 상태를 모니터링할 수 있는 데이터를 설명합니다.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2018
ms.author: bwren
ms.openlocfilehash: 5896083c9159c30e6b66009f43073391eec936ed
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2019
ms.locfileid: "56003706"
---
# <a name="sources-of-data-in-azure-monitor"></a>Azure 모니터의 데이터 원본
이 문서에서는 Azure Monitor에서 수집한 데이터 원본에 대해 설명하여 리소스 및 해당 리소스에서 실행 중인 애플리케이션의 상태와 성능을 모니터링합니다. 이러한 리소스는 Azure, 다른 클라우드 또는 온-프레미스에 있을 수 있습니다.  이 데이터를 저장한 방법 및 이 데이터를 볼 수 있는 방법에 대한 자세한 내용은 [Azure Monitor에서 수집한 데이터](data-collection.md)를 참조하세요.

Azure의 모니터링 데이터는 계층, 즉 애플리케이션과 운영 체제인 가장 높은 계층 및 Azure 플랫폼의 구성 요소인 가장 낮은 계층으로 구성될 수 있는 다양한 원본에서 제공됩니다. 이 내용은 다음 섹션에서 자세히 설명하는 각 계층이 포함된 다음 다이어그램에 나와 있습니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

![모니터링 데이터의 계층](media/data-sources/monitoring-tiers.png)

## <a name="azure-tenant"></a>Azure 테넌트
Azure 테넌트와 관련된 원격 분석은 Azure Active Directory와 같은 테넌트 전체 서비스에서 수집됩니다.

![Azure 테넌트 컬렉션](media/data-sources/tenant-collection.png)

### <a name="azure-active-directory-audit-logs"></a>Azure Active Directory 감사 로그
[Azure Active Directory 보고](../../active-directory/reports-monitoring/overview-reports.md)는 로그인 활동 및 특정 테넌트 내의 변경 감사 내역에 대한 기록을 포함합니다. 이러한 감사 로그는 Azure Monitor 로그에 기록되어 다른 로그 데이터로 분석할 수 있습니다.


## <a name="azure-platform"></a>Azure 플랫폼
Azure 자체의 상태 및 운영과 관련된 원격 분석은 Azure 구독의 운영 및 관리에 대한 데이터를 포함합니다. Azure 활동 로그에 저장된 서비스 상태 데이터 및 Azure Active Directory의 감사 로그를 포함합니다.

![Azure 구독 컬렉션](media/data-sources/azure-collection.png)

### <a name="azure-service-health"></a>Azure Service Health
[Azure Service Health](service-notifications.md)는 애플리케이션 및 리소스가 의존하는 구독에서 Azure 서비스의 상태에 대한 정보를 제공합니다. 애플리케이션에 영향을 줄 수 있는 현재 및 예상되는 중요한 문제에 대한 알림을 받기 위한 경고를 만들 수 있습니다. Service Health 레코드는 [Azure 활동 로그](activity-logs-overview.md)에 저장되므로 활동 로그 탐색기에서 레코드를 보고 Azure Monitor 로그에 복사할 수 있습니다.

### <a name="azure-activity-log"></a>Azure 동작 로그
[Azure 활동 로그](activity-logs-overview.md)는 Azure 리소스에 한 모든 구성 변경에 대한 레코드와 함께 서비스 상태 레코드를 포함합니다. 활동 로그는 모든 Azure 리소스에 대해 사용할 수 있으며 해당 _외부_ 보기를 나타냅니다. 활동 로그에서 특정 유형의 레코드는 [Azure 활동 로그 이벤트 스키마](activity-log-schema.md)에 설명돼 있습니다.

Azure Portal의 해당 페이지에서 특정 리소스에 대한 활동 로그를 보거나, [활동 로그 탐색기](activity-logs-overview.md)의 여러 리소스에서 로그를 볼 수 있습니다. 특히 다른 모니터링 데이터와 결합하려면 Azure Monitor에 로그 항목을 복사하는 것이 유용합니다. [Event Hubs](activity-logs-stream-event-hubs.md)를 사용하여 다른 위치로 보낼 수 있습니다.



## <a name="azure-services"></a>Azure 서비스
메트릭 및 리소스 수준 진단 로그는 Azure 리소스의 _내부_ 작업에 대한 정보를 제공합니다. 이것은 대부분의 Azure 서비스에 대해 사용할 수 있으며, 관리 솔루션은 특정 서비스에 대한 추가 인사이트를 제공합니다.

![Azure 리소스 컬렉션](media/data-sources/azure-resource-collection.png)


### <a name="metrics"></a>메트릭
대부분의 Azure 서비스는 성능 및 작업을 반영하는 [플랫폼 메트릭](data-collection.md#metrics)을 생성합니다. 특정 [메트릭은 리소스의 각 형식마다 다릅니다](metrics-supported.md).  메트릭 분석에서 액세스할 수 있으며, Log Analytics를 사용하여 추세 및 기타 분석을 위해 로그에 복사할 수 있습니다.


### <a name="resource-diagnostic-logs"></a>리소스 진단 로그
활동 로그가 Azure 리소스에서 수행된 작업에 대한 정보를 제공하는 반면 리소스 수준 [진단 로그](diagnostic-logs-overview.md)는 리소스 자체 작업에 대한 인사이트를 제공합니다.   구성 요구 사항 및 이러한 로그의 콘텐츠는 [리소스 유형에 따라 다릅니다](diagnostic-logs-schema.md).

Azure Portal에서 진단 로그를 직접 볼 수는 없지만 [보관을 위해 Azure Storage로 보내고](archive-diagnostic-logs.md), 다른 서비스로 리디렉션하기 위해 [Event Hub](../../event-hubs/event-hubs-about.md)로 내보내거나 분석을 위해 [Azure Monitor](diagnostic-logs-stream-log-store.md)로 내보낼 수 있습니다. 일부 리소스는 Azure Monitor에 직접 쓸 수 있는 반면 다른 리소스는 [Log Analytics로 가져오기](azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage) 전에 스토리지 계정에 쓸 수 있습니다.

### <a name="monitoring-solutions"></a>모니터링 솔루션
 [모니터링 솔루션](../../azure-monitor/insights/solutions.md)은 특정 서비스 또는 애플리케이션의 작업에 대한 추가 인사이트를 제공하는 데이터를 수집합니다. 솔루션에 일반적으로 포함되는 [보기](view-designer.md) 또는 [쿼리 언어](../../azure-monitor/log-query/log-query-overview.md)를 사용하여 분석될 수 있는 경우 Azure Monitor 로그에 데이터를 수집합니다.


## <a name="guest-operating-system"></a>게스트 운영 체제
Azure, 다른 클라우드 및 온-프레미스의 계산 리소스에는 모니터링할 게스트 운영 체제가 있습니다. 하나 이상의 에이전트를 설치하여 게스트에서 Azure 서비스와 동일한 모니터링 도구에 원격 분석을 수집할 수 있습니다.

![Azure 계산 리소스 컬렉션](media/data-sources/compute-resource-collection.png)

### <a name="azure-diagnostic-extension"></a>Azure 진단 확장
Azure 진단 확장을 사용하여 Azure 계산 리소스의 클라이언트 운영 체제에서 로그 및 성능 데이터를 수집하여 기본 수준의 모니터링을 제공합니다.   

### <a name="log-analytics-agent"></a>Log Analytics 에이전트
Windows 또는 Linux 가상 머신 또는 물리적 컴퓨터의 포괄적인 모니터링 및 관리는 Log Analytics 에이전트를 사용하여 전달됩니다. 가상 머신은 Azure, 다른 클라우드 또는 온-프레미스에서 실행될 수 있으며 에이전트는 직접 또는 System Center Operations Manager를 통해 Azure Monitor에 연결되고, 구성하는 [데이터 원본](agent-data-sources.md)에서 또는 가상 머신에서 실행되는 애플리케이션에 대한 추가 인사이트를 제공하는 [모니터링 솔루션](../../azure-monitor/insights/solutions.md)에서 데이터를 수집할 수 있습니다.


### <a name="dependency-agent"></a>종속성 에이전트
[서비스 맵](../insights/service-map.md) 및 [VM용 Azure Monitor](../../azure-monitor/insights/vminsights-overview.md)는 Windows 및 Linux 가상 머신에서 Dependency Agent가 필요합니다. Log Analytics 에이전트와 통합하여 가상 머신에서 실행되는 프로세스에 대한 검색된 데이터 및 외부 프로세스 종속성을 수집합니다. Azure Monitor에 이 데이터를 저장하고 검색되는 상호 연결 구성 요소를 시각화합니다.  

모니터링 요구 사항에 따라 에이전트와 사용할 에이전트 간의 차이점을 더 이해하려면 [모니터링 에이전트 개요](agents-overview.md)를 참조하세요.

## <a name="applications"></a>애플리케이션
애플리케이션이 게스트 운영 체제에 쓸 수 있는 원격 분석 외에도 자세한 애플리케이션 모니터링은 [Application Insights](https://docs.microsoft.com/azure/application-insights/)를 사용하여 완료됩니다. Application Insights는 다양한 플랫폼에서 실행되는 애플리케이션에서 데이터를 수집할 수 있습니다. 애플리케이션은 Azure, 다른 클라우드 또는 온-프레미스에서 실행될 수 있습니다.

![애플리케이션 데이터 수집](media/data-sources/application-collection.png)


### <a name="application-data"></a>애플리케이션 데이터
계측 패키지를 설치하여 애플리케이션에 대한 Application Insights를 사용하도록 설정하는 경우 애플리케이션의 성능 및 작업과 관련된 메트릭 및 로그를 수집합니다. 페이지 보기, 애플리케이션 요청 및 예외에 대한 자세한 정보가 포함됩니다. Application Insights는 수집한 데이터를 Azure Monitor에 저장합니다. 이 데이터를 분석하기 위한 광범위한 도구를 포함하지만 메트릭 분석 및 로그 분석과 같은 도구를 사용하여 다른 원본의 데이터로 분석할 수 있습니다.

[사용자 지정 메트릭을 만들](../../application-insights/app-insights-api-custom-events-metrics.md)려면 Application Insights를 사용할 수도 있습니다.  숫자 값을 계산한 다음, 메트릭 탐색기에서 액세스하고 [자동 크기 조정](autoscale-custom-metric.md) 및 메트릭 경고에 사용할 수 있는 다른 메트릭으로 해당 값을 저장하기 위한 사용자 고유의 논리를 정의할 수 있습니다.


### <a name="dependencies"></a>종속성
애플리케이션의 다른 논리 작업을 모니터링하려면 [여러 구성 요소에 걸쳐 원격 분석을 수집](../../application-insights/app-insights-transaction-diagnostics.md)해야 합니다. Application Insights는 함께 분석하도록 허용하는 구성 요소 간 종속성을 식별하는 [분산 원격 분석 상관 관계](../../application-insights/application-insights-correlation.md)를 지원합니다.

### <a name="availability-tests"></a>가용성 테스트
Application Insights의 [가용성 테스트](../../application-insights/app-insights-monitor-web-app-availability.md)는 공용 인터넷의 다른 위치에서 애플리케이션의 가용성 및 응답성을 테스트할 수 있습니다. 사용자 시나리오를 시뮬레이션하는 웹 테스트를 만들려면 애플리케이션이 활성 상태이거나 Visual Studio를 사용하는지 확인하는 간단한 ping 테스트를 할 수 있습니다.  가용성 테스트는 애플리케이션에서 계측이 필요하지 않습니다.

## <a name="custom-sources"></a>사용자 지정 원본
애플리케이션의 표준 계층 외에 다른 데이터 원본과 함께 수집할 수 없는 원격 분석이 있는 다른 리소스를 모니터링해야 할 수도 있습니다. 해당 리소스의 경우 Azure Monitor API를 사용하여 이 데이터를 기록해야 합니다.

![사용자 지정 데이터 컬렉션](media/data-sources/custom-collection.png)

### <a name="data-collector-api"></a>데이터 수집기 API
Azure Monitor는 [데이터 수집기 API](../../azure-monitor/platform/data-collector-api.md)를 사용하여 REST 클라이언트에서 로그 데이터를 수집할 수 있습니다. 그러면 사용자 정의 모니터링 시나리오를 작성하고 다른 소스를 통해 원격 분석을 표시하지 않는 리소스까지 모니터링을 확장할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Monitor에서 수집한 모니터링 데이터의 유형](data-collection.md)과 이 데이터를 보고 분석하는 방법에 대해 자세히 알아보세요.
