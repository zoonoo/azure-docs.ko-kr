---
title: Batch 이벤트에 대한 진단 로깅 사용 - Azure | Microsoft Docs
description: 풀, 작업 등과 같은 Azure Batch 계정 리소스에 대해 진단 로그 이벤트를 기록 및 분석합니다.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: e14e611d-12cd-4671-91dc-bc506dc853e5
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c41c8c9f8fd9302c610ce356b0485e33ea3c967d
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="log-events-for-diagnostic-evaluation-and-monitoring-of-batch-solutions"></a>Batch 솔루션의 진단 평가 및 모니터링에 대한 로그 이벤트

많은 Azure 서비스와 마찬가지로 Batch 서비스는 리소스 수명 주기 동안 특정 리소스에 대한 로그 이벤트를 내보냅니다. Azure Batch 진단 로그를 사용하여 풀 및 작업 등의 리소스에 대한 이벤트를 기록하고 이 로그를 진단 평가와 모니터링에 사용할 수 있습니다. 풀 만들기, 풀 삭제, 작업 시작, 작업 완료, 기타 등의 이벤트는 Batch 진단 로그에 포함됩니다.

> [!NOTE]
> 이 문서에서는 작업과 작업 출력 데이터가 아닌 Batch 계정 리소스 자체에 대한 이벤트 로깅을 논의합니다. 작업 및 태스크의 출력 데이터 저장에 대한 자세한 내용은 [Azure Batch 작업 및 태스크 보관](batch-task-output.md)을 참조하세요.
> 
> 

## <a name="prerequisites"></a>필수 조건
* [Azure Batch 계정](batch-account-create-portal.md)
* [Azure Storage 계정](../storage/common/storage-create-storage-account.md#create-a-storage-account)
  
  Batch 진단 로그를 유지하려면 Azure가 로드를 저장하는 Azure Storage 계정을 만들어야 합니다. Batch 계정에 [진단 로깅을 사용](#enable-diagnostic-logging)할 때 이 Storage 계정을 지정합니다. 로그 수집을 사용할 때 지정하는 Storage 계정은 [응용 프로그램 패키지](batch-application-packages.md) 및 [작업 출력 지속성](batch-task-output.md) 문서에서 설명한 연결된 Storage 계정과는 다릅니다.
  
  > [!WARNING]
  > Azure Storage 계정에 저장된 데이터에 대해 요금이 **청구**됩니다. 여기에는 이 문서에서 논의하는 진단 로그가 포함됩니다. [로그 보존 정책](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)을 설계할 때는 이 점을 염두에 둡니다.
  > 
  > 

## <a name="enable-diagnostic-logging"></a>진단 로깅 사용
진단 로깅은 Batch 계정에 기본적으로 활성화되지 않습니다. 모니터링할 각각의 Batch 계정에 대해 진단 로그를 명시적으로 사용하도록 설정해야 합니다.

[진단 로그의 컬렉션을 사용하도록 설정하는 방법](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs)

전체 [Azure Diagnostic Logs 개요](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) 문서를 읽고 로깅 사용뿐 아니라 여러 Azure 서비스에서 지원하는 로그 카테고리에 대해서도 파악하는 것이 좋습니다. 예를 들어, 현재 Azure Batch는 **서비스 로그** 카테고리 하나만 지원합니다.

## <a name="service-logs"></a>서비스 로그
Azure Batch 서비스 로그는 풀이나 작업 같은 배치 리소스의 수명 주기 동안 Azure Batch 서비스가 내보낸 이벤트를 포함합니다.  Batch에서 내보낸 각각의 이벤트는 JSON 형식으로 지정된 Storage 계정에 저장됩니다. 예를 들어 샘플 **풀 만들기 이벤트**의 본문은 다음과 같습니다.

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "4",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

각 이벤트 본문은 지정된 Azure Storage 계정의 .json 파일 안에 있습니다. 로그에 직접 액세스하려면 [저장소 계정의 진단 로그 스키마](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account)를 검토할 수 있습니다.

## <a name="service-log-events"></a>서비스 로그 이벤트
Batch 서비스는 현재 다음과 같은 서비스 로그 이벤트를 내보냅니다. 이 목록은 전체가 아니며, 이 문서가 마지막으로 업데이트된 뒤 다른 이벤트가 추가되었을 수 있습니다.

| **서비스 로그 이벤트** |
| --- |
| [풀 만들기][pool_create] |
| [풀 삭제 시작][pool_delete_start] |
| [풀 삭제 완료][pool_delete_complete] |
| [풀 크기 조정 시작][pool_resize_start] |
| [풀 크기 조정 완료][pool_resize_complete] |
| [작업 시작][task_start] |
| [작업 완료][task_complete] |
| [작업 실패][task_fail] |

## <a name="next-steps"></a>다음 단계
Azure Storage 계정에 진단 로그를 저장하는 것 외에도 [Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md)에 Batch 서비스 로그 이벤트를 스트림하고 [Azure Log Analytics](../log-analytics/log-analytics-overview.md)로 보낼 수도 있습니다.

* [Event Hubs로 Azure 진단 로그 스트림](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)
  
  Batch 진단 이벤트를 확장성 높은 데이터 수집 서비스인 Event Hubs에 스트림합니다. Event Hubs는 초당 수백 건의 이벤트를 수집하여 모든 실시간 분석 공급자를 통해 변환 및 저장할 수 있습니다.
* [Log Analytics를 사용하여 Azure 진단 로그 분석 ](../log-analytics/log-analytics-azure-storage.md)
  
  OMS(Operations Management Suite) 포털에서 분석할 수 있게 진단 로그를 Log Analytics로 보내거나, Power BI 또는 Excel에서의 분석을 위해 내보냅니다.

[pool_create]: https://msdn.microsoft.com/library/azure/mt743615.aspx
[pool_delete_start]: https://msdn.microsoft.com/library/azure/mt743610.aspx
[pool_delete_complete]: https://msdn.microsoft.com/library/azure/mt743618.aspx
[pool_resize_start]: https://msdn.microsoft.com/library/azure/mt743609.aspx
[pool_resize_complete]: https://msdn.microsoft.com/library/azure/mt743608.aspx
[task_start]: https://msdn.microsoft.com/library/azure/mt743616.aspx
[task_complete]: https://msdn.microsoft.com/library/azure/mt743612.aspx
[task_fail]: https://msdn.microsoft.com/library/azure/mt743607.aspx
