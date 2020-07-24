---
title: PowerShell cmdlet 참조
description: Azure Scheduler용 PowerShell cmdlet에 대해 알아봅니다.
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 5b82dba923db16e96cc0884b629723c4e8496c3a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080916"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Azure Scheduler용 PowerShell cmdlet 참조

> [!IMPORTANT]
> [Azure Scheduler](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)는 조만간 사용 중지되고 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)로 대체됩니다. 스케줄러에 설정 된 작업을 계속 하려면 가능한 한 빨리 [Azure Logic Apps로 마이그레이션](../scheduler/migrate-from-scheduler-to-logic-apps.md) 하세요. 
>
> Scheduler는 더 이상 Azure Portal에서 사용할 수 없지만 [REST API](/rest/api/scheduler) 및 [Azure Scheduler PowerShell cmdlet](scheduler-powershell-reference.md)은 현재 사용 가능하므로 작업 및 작업 컬렉션을 관리할 수 있습니다.

Scheduler 작업 및 작업 컬렉션을 만들고 관리하는 데 사용할 스크립트를 작성하려는 경우 PowerShell cmdlet을 사용할 수 있습니다. 이 문서에서는 중요한 Azure Scheduler용 PowerShell cmdlet 및 각 cmdlet의 참조 문서 링크를 제공합니다. Azure 구독용으로 Azure PowerShell을 설치하려면 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/)을 참조하세요. [Azure Resource Manager cmdlet](/powershell/azure/)에 대한 자세한 내용은 [Azure Resource Manager로 Azure PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

| cmdlet | Description |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |작업 컬렉션을 비활성화합니다. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |작업 컬렉션을 활성화합니다. |
| [AzSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Scheduler 작업을 가져옵니다. |
| [AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |작업 컬렉션을 가져옵니다. |
| [AzSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |작업 내역을 가져옵니다. |
| [AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |HTTP 작업을 만듭니다. |
| [AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |작업 컬렉션을 만듭니다. |
| [AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Service Bus 큐 작업을 만듭니다. |
| [AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Service Bus 항목 작업을 만듭니다. |
| [AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Storage 큐 작업을 만듭니다. |
| [AzSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Scheduler 작업을 삭제합니다. |
| [AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |작업 컬렉션을 삭제합니다. |
| [AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Scheduler HTTP 작업을 수정합니다. |
| [AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |작업 컬렉션을 수정합니다. |
| [AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Service Bus 큐 작업을 수정합니다. |
| [AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Service Bus 항목 작업을 수정합니다. |
| [AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Storage 큐 작업을 수정합니다. |
||| 

다음 cmdlet 중 하나를 실행하면 자세한 내용을 확인할 수 있습니다. 

```text
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="next-steps"></a>다음 단계

* [Azure Scheduler 개념, 용어 및 엔터티 계층 구조](scheduler-concepts-terms.md)
* [Azure Scheduler 제한, 기본값 및 오류 코드](scheduler-limits-defaults-errors.md)
* [Azure Scheduler REST API 참조](/rest/api/scheduler)