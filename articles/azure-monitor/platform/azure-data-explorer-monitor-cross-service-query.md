---
title: Azure Monitor와 Azure 데이터 탐색기 간의 크로스 서비스 쿼리 (미리 보기)
description: Azure Log Analytics 도구를 통해 Azure 데이터 탐색기 데이터를 쿼리하여 모든 데이터를 한 곳에서 조인 하 고 분석 합니다.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: 2b68afcb9d200970ca4ea29b13175223f52c77e0
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730992"
---
# <a name="cross-service-query---azure-monitor-and-azure-data-explorer-preview"></a>크로스 서비스 쿼리-Azure Monitor 및 Azure 데이터 탐색기 (미리 보기)
[Azure 데이터 탐색기](/azure/data-explorer/), [Application Insights](../app/app-insights-overview.md)및 [Log Analytics](./data-platform-logs.md)간에 서비스 간 쿼리를 만듭니다.
## <a name="azure-monitor-and-azure-data-explorer-cross-service-querying"></a>Azure Monitor 및 Azure 데이터 탐색기 서비스 간 쿼리
이러한 환경을 통해 [azure 데이터 탐색기와 Azure Monitor 간에 크로스 서비스 쿼리를 만들고](/azure/data-explorer/query-monitor-data) [Azure Monitor와 azure 데이터 탐색기 간에 크로스 서비스 쿼리를 만들](./azure-monitor-data-explorer-proxy.md)수 있습니다.

예: (Log Analytics에서 Azure 데이터 탐색기 쿼리):
```kusto
CustomEvents | where aField == 1
| join (adx("Help/Samples").TableName | where secondField == 3) on $left.Key == $right.key
```
외부 쿼리에서 작업 영역에 있는 테이블을 쿼리 한 다음 새 "adx ()" 함수를 사용 하 여 Azure 데이터 탐색기 클러스터의 다른 테이블과 조인 (이 경우에는 clustername = help, databasename = samples) 하 여 쿼리 텍스트 내에서 다른 작업 영역을 쿼리 하는 것과 같은 작업을 수행할 수 있습니다.

> [!NOTE]
> * Azure 데이터 탐색기 클라이언트 도구에서 직접 또는 azure 데이터 탐색기 클러스터에서 쿼리를 실행 하 여 간접적으로 Azure 데이터 탐색기에서 Azure Monitor 데이터를 쿼리 하는 기능은 미리 보기 모드입니다.
> * [서비스 간 쿼리](mailto:adxproxy@microsoft.com) 팀에 질문을 합니다.

## <a name="query-exported-log-analytics-data-from-azure-blob-storage-account"></a>Azure Blob storage 계정에서 내보낸 Log Analytics 데이터 쿼리

Azure Monitor에서 Azure storage 계정으로 데이터를 내보내면 저렴 한 보존 및 여러 지역에 로그를 다시 할당할 수 있습니다.

Azure 데이터 탐색기를 사용 하 여 Log Analytics 작업 영역에서 내보낸 데이터를 쿼리할 수 있습니다. 구성 된 후에는 작업 영역에서 Azure storage 계정으로 전송 된 지원 테이블을 Azure 데이터 탐색기의 데이터 원본으로 사용할 수 있습니다. [Azure 데이터 탐색기 (미리 보기)를 사용 하 여 Azure Monitor에서 내보낸 데이터를 쿼리](./azure-data-explorer-query-storage.md)합니다.

:::image type="content" source="media\azure-data-explorer-query-storage\exported-data-query.png" alt-text="저장소 흐름의 Azure 데이터 탐색기 쿼리입니다.":::

>[!tip] 
> * Log Analytics 작업 영역의 모든 데이터를 Azure storage 계정 또는 이벤트 허브로 내보내려면 Azure Monitor 로그의 Log Analytics 작업 영역 데이터 내보내기 기능을 사용 합니다. [Azure Monitor에서 Log Analytics 작업 영역 데이터 내보내기 (미리 보기)를 참조](/azure/data-explorer/query-monitor-data)하세요.

## <a name="next-steps"></a>다음 단계
다음에 대해 자세히 알아봅니다.
* [Azure 데이터 탐색기와 Azure Monitor 간에 서비스 간 쿼리를 만듭니다](/azure/data-explorer/query-monitor-data). Azure 데이터 탐색기에서 Azure Monitor 데이터 쿼리
* [Azure Monitor와 Azure 데이터 탐색기 사이에서 서비스 간 쿼리를 만듭니다](./azure-monitor-data-explorer-proxy.md). Azure Monitor에서 Azure 데이터 탐색기 데이터 쿼리
* [Azure Monitor에서 작업 영역 데이터 내보내기 (미리 보기)를 Log Analytics](/azure/data-explorer/query-monitor-data)합니다. Log Analytics 내보낸 데이터를 사용 하 여 Azure Blob storage 계정을 연결 하 고 쿼리 합니다.