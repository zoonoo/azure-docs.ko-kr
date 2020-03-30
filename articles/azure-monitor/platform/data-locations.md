---
title: Azure 모니터의 데이터 위치 모니터링 | 마이크로 소프트 문서
description: Azure Monitor 데이터 플랫폼을 포함하여 모니터링 데이터가 Azure에 저장되는 다양한 위치에 대해 설명합니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/21/2019
ms.openlocfilehash: 7d4459867081d920fefb9471b1a682d21040da9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666618"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Azure 모니터의 데이터 위치 모니터링

Azure Monitor는 Azure Monitor [데이터 플랫폼에](data-platform.md) 설명된 대로 [로그](data-platform-logs.md) 및 [메트릭의](data-platform-metrics.md) 데이터 [플랫폼을](data-platform.md)기반으로 합니다. Azure 리소스의 모니터링 데이터는 Azure Monitor로 복사되기 전에 또는 추가 시나리오를 지원하기 위해 다른 위치에 기록될 수 있습니다. 

## <a name="monitoring-data-locations"></a>데이터 위치 모니터링

다음 표에서는 Azure에서 모니터링 데이터가 전송되는 위치와 데이터에 액세스하기 위한 다양한 방법을 식별합니다.

| 위치 | 설명 | 액세스 방법 |
|:---|:---|:---|:--|
| Azure 모니터 메트릭 | 타임스탬프 데이터 분석에 최적화된 타임계 데이터베이스입니다. | [메트릭 탐색기](metrics-getting-started.md)<br>[Azure 모니터 메트릭 API](/rest/api/monitor/metrics) |
| Azure Monitor 로그    | 강력한 분석 엔진과 풍부한 쿼리 언어를 제공하는 Azure Data Explorer를 기반으로 하는 로그 분석 작업 영역입니다. | [Log Analytics](../log-query/portals.md)<br>[로그 분석 API](https://dev.loganalytics.io/)<br>[Application Insights API](https://dev.applicationinsights.io/reference/get-query) |
| 활동 로그 | 활동 로그의 데이터는 다른 데이터로 분석하기 위해 Azure Monitor Logs로 전송될 때 가장 유용하지만 Azure Portal에서 직접 볼 수 있도록 자체적으로 수집됩니다. | [Azure 포털](activity-log-view.md#azure-portal)<br>[Azure Monitor 이벤트 API](/rest/api/monitor/eventcategories) |
| Azure Storage | 일부 데이터 원본은 Azure 저장소에 직접 쓰고 데이터를 로그로 이동하려면 구성이 필요합니다. 보관 및 외부 시스템과의 통합을 위해 Azure 저장소로 데이터를 보낼 수도 있습니다.  | [스토리지 분석](/rest/api/storageservices/storage-analytics)<br>[서버 탐색기](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Event Hubs | Azure 이벤트 허브로 데이터를 전송하여 다른 위치로 스트리밍합니다. | [스토리지로 캡처](../../event-hubs/event-hubs-capture-overview.md)  |
| VM용 Azure Monitor | VM용 Azure Monitor는 Azure 포털의 모니터링 환경에서 사용되는 사용자 지정 위치에 워크로드 상태 데이터를 저장합니다. | [Azure 포털](../insights/vminsights-overview.md)<br>[워크로드 모니터 REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[Azure 리소스 상태 REST API](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| 경고 | Azure 모니터에서 만든 경고입니다. | [Azure 포털](alerts-managing-alert-instances.md)<br>[경고 관리 REST API](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>다음 단계

- [Azure Monitor에서 수집한 모니터링 데이터의](data-sources.md)다양한 소스를 확인합니다.
- [Azure Monitor에서 수집한 모니터링 데이터의 유형과](data-platform.md) 이 데이터를 보고 분석하는 방법에 대해 알아봅니다.
