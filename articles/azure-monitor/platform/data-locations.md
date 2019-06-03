---
title: Azure Monitor의 데이터 위치를 모니터링 합니다. | Microsoft Docs
description: Azure Monitor 데이터 플랫폼을 비롯 하 여 Azure에서 모니터링 데이터 저장은 다른 위치를 설명 합니다.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: bwren
ms.openlocfilehash: 1d92973e32e9c694b1d0488753b9a701e7d71a5d
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416901"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Azure Monitor의 모니터링 데이터 위치

Azure Monitor 기반을 [데이터 플랫폼](data-platform.md) 의 [로그](data-platform-logs.md) 하 고 [메트릭](data-platform-metrics.md) 에 설명 된 대로 [Azure Monitor 데이터 플랫폼](data-platform.md)합니다. Azure 리소스에서 모니터링 데이터 기록할 수 있는 다른 위치를 하나 추가 시나리오를 지원 하기 위해 또는 Azure Monitor로 복사 하기 전에 합니다. 

## <a name="monitoring-data-locations"></a>모니터링 데이터 위치

다음 표에서 Azure에서 모니터링 데이터를 전송 되는 다른 위치 및 액세스에 대 한 여러 가지 방법을 식별 합니다.

| Location | 설명 | 액세스 방법 |
|:---|:---|:---|:--|
| Azure Monitor 메트릭 | 시계열 데이터베이스 타임 스탬프 데이터 분석을 위해 최적화 됩니다. | [메트릭 탐색기](metrics-getting-started.md)<br>[Azure Monitor 메트릭 API](/rest/api/monitor/metrics) |
| Azure Monitor 로그    | Log Analytics 작업 영역 강력한 분석 엔진 및 다양 한 쿼리 언어를 제공 하는 Azure 데이터 탐색기를 기반으로 하는 합니다. | [Log Analytics](../log-query/portals.md)<br>[Log Analytics API](https://dev.loganalytics.io/)<br>[Application Insights API](https://dev.applicationinsights.io/reference/get-query) |
| 활동 로그 | 활동 로그에서 데이터를 다른 데이터를 사용 하 여 분석을 Azure Monitor 로그를 보낼 때 가장 유용 하지만 Azure portal에서 직접 볼 수 있도록 자체적으로 수집도 됩니다. | [Azure Portal](activity-log-view.md#azure-portal)<br>[Azure Monitor 이벤트 API](/rest/api/monitor/eventcategories) |
| Azure Storage | 일부 데이터 원본은 Azure storage에 직접 작성 하 고 로그에 데이터를 이동 하는 구성이 필요 합니다. 또한 외부 시스템과 통합 및 보관에 대 한 Azure storage에 데이터를 보낼 수 있습니다.  | [저장소 분석](/rest/api/storageservices/storage-analytics)<br>[서버 탐색기](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Storage 탐색기](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Event Hubs | 다른 위치에 스트리밍하려면 Azure Event Hubs에 데이터를 보냅니다. | [Storage에 캡처를](../../event-hubs/event-hubs-capture-overview.md)  |
| VM용 Azure Monitor | Vm에 대 한 azure Monitor는 Azure portal에서 해당 모니터링 환경에서 사용 되는 사용자 지정 위치에 작업 상태 데이터를 저장 합니다. | [Azure Portal](../insights/vminsights-overview.md)<br>[작업 모니터를 REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[Azure Resource health REST API](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| 경고 | Azure Monitor에서 생성 된 경고입니다. | [Azure Portal](alerts-managing-alert-instances.md)<br>[경고 관리 REST API](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>다음 단계

- 다른 소스를 참조 하십시오 [Azure Monitor에서 수집한 모니터링 데이터](data-sources.md)입니다.
- 에 대 한 자세한 합니다 [Azure Monitor에서 수집한 모니터링 데이터 유형의](data-platform.md) 및이 데이터 보기 및 분석 하는 방법입니다.
