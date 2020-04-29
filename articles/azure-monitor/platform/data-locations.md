---
title: Azure Monitor의 모니터링 데이터 위치 | Microsoft Docs
description: Azure Monitor 데이터 플랫폼을 포함 하 여 Azure에 모니터링 데이터가 저장 되는 다양 한 위치에 대해 설명 합니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/21/2019
ms.openlocfilehash: 7d4459867081d920fefb9471b1a682d21040da9d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77666618"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Azure Monitor에서 데이터 위치 모니터링

Azure Monitor은 [Azure Monitor data platform](data-platform.md)에 설명 된 대로 [로그](data-platform-logs.md) 및 [메트릭의](data-platform-metrics.md) [데이터 플랫폼](data-platform.md) 을 기반으로 합니다. Azure 리소스의 모니터링 데이터를 Azure Monitor 복사 하기 전이나 추가 시나리오를 지원 하기 위해 다른 위치에 쓸 수 있습니다. 

## <a name="monitoring-data-locations"></a>데이터 위치 모니터링

다음 표에서는 Azure의 모니터링 데이터를 전송 하는 다양 한 위치 및 해당 데이터에 액세스 하는 다른 방법을 보여 줍니다.

| 위치 | Description | 액세스 방법 |
|:---|:---|:---|:--|
| Azure Monitor 메트릭 | 시간이 찍힌 데이터를 분석 하기 위해 최적화 된 시계열 데이터베이스입니다. | [메트릭 탐색기](metrics-getting-started.md)<br>[Azure Monitor 메트릭 API](/rest/api/monitor/metrics) |
| Azure Monitor 로그    | Azure 데이터 탐색기를 기반으로 하는 Log Analytics 작업 영역으로, 강력한 분석 엔진과 풍부한 쿼리 언어를 제공 합니다. | [Log Analytics](../log-query/portals.md)<br>[Log Analytics API](https://dev.loganalytics.io/)<br>[Application Insights API](https://dev.applicationinsights.io/reference/get-query) |
| 활동 로그 | 활동 로그의 데이터는 다른 데이터를 사용 하 여 분석 하기 위해 Azure Monitor 로그에 보낼 때 가장 유용 하지만 Azure Portal에서 직접 볼 수 있도록 자체적으로 수집 됩니다. | [Azure Portal](activity-log-view.md#azure-portal)<br>[Azure Monitor 이벤트 API](/rest/api/monitor/eventcategories) |
| Azure Storage | 일부 데이터 원본은 Azure storage에 직접 기록 하 고 데이터를 로그로 이동 하는 구성이 필요 합니다. 또한 보관을 위해 Azure storage에 데이터를 보내고 외부 시스템과 통합할 수 있습니다.  | [스토리지 분석](/rest/api/storageservices/storage-analytics)<br>[서버 탐색기](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Event Hubs | 데이터를 다른 위치로 스트리밍하려면 Azure Event Hubs에 보냅니다. | [저장소에 캡처](../../event-hubs/event-hubs-capture-overview.md)  |
| VM용 Azure Monitor | VM용 Azure Monitor은 Azure Portal의 모니터링 환경에서 사용 하는 사용자 지정 위치에 워크 로드 상태 데이터를 저장 합니다. | [Azure Portal](../insights/vminsights-overview.md)<br>[작업 모니터 REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[Azure 리소스 상태 REST API](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| 경고 | Azure Monitor에서 생성 된 경고입니다. | [Azure Portal](alerts-managing-alert-instances.md)<br>[경고 관리 REST API](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>다음 단계

- [Azure Monitor에 의해 수집 된 여러 모니터링 데이터](data-sources.md)원본을 참조 하세요.
- [Azure Monitor에서 수집 하는 모니터링 데이터 유형과](data-platform.md) 이 데이터를 보고 분석 하는 방법에 대해 알아봅니다.
