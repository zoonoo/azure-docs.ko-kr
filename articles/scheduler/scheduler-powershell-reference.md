---
title: PowerShell cmdlet 참조 - Azure Scheduler
description: Azure Scheduler용 PowerShell cmdlet에 대해 알아봅니다.
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 9a26c457-d7a1-4e4a-bc79-f26592155218
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 53b68a5dc72277c9fd44b36d346e5b5c91b53a93
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60344379"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Azure Scheduler용 PowerShell cmdlet 참조

> [!IMPORTANT]
> Azure Scheduler는 사용이 중지되며 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)가 대신 제공됩니다. 작업을 예약하려는 경우 [Azure Logic Apps를 대신 사용해 보세요](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Scheduler 작업 및 작업 컬렉션을 만들고 관리하는 데 사용할 스크립트를 작성하려는 경우 PowerShell cmdlet을 사용할 수 있습니다. 이 문서에서는 해당 참조 문서에 대 한 링크를 사용 하 여 Azure Scheduler의 주요 PowerShell cmdlet을 나열합니다. Azure 구독용으로 Azure PowerShell을 설치하려면 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요. [Azure Resource Manager cmdlet](/powershell/azure/overview)에 대한 자세한 내용은 [Azure Resource Manager로 Azure PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

| Cmdlet | 설명 |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |작업 컬렉션을 비활성화합니다. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |작업 컬렉션을 활성화합니다. |
| [Get-AzSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Scheduler 작업을 가져옵니다. |
| [Get-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |작업 컬렉션을 가져옵니다. |
| [Get-AzSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |작업 내역을 가져옵니다. |
| [New-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |HTTP 작업을 만듭니다. |
| [New-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |작업 컬렉션을 만듭니다. |
| [New-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Service Bus 큐 작업을 만듭니다. |
| [New-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Service Bus 항목 작업을 만듭니다. |
| [New-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Storage 큐 작업을 만듭니다. |
| [Remove-AzSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Scheduler 작업을 삭제합니다. |
| [Remove-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |작업 컬렉션을 삭제합니다. |
| [Set-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Scheduler HTTP 작업을 수정합니다. |
| [Set-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |작업 컬렉션을 수정합니다. |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Service Bus 큐 작업을 수정합니다. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Service Bus 항목 작업을 수정합니다. |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Storage 큐 작업을 수정합니다. |
||| 

다음 cmdlet 중 하나를 실행하면 자세한 내용을 확인할 수 있습니다. 

```
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>참고 항목

* [Azure Scheduler란?](scheduler-intro.md)
* [개념, 용어 및 엔터티 계층 구조](scheduler-concepts-terms.md)
* [첫 번째 작업 만들기 및 예약 - Azure Portal](scheduler-get-started-portal.md)
* [Azure Scheduler REST API 참조](https://msdn.microsoft.com/library/mt629143)
