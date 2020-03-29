---
title: 파워쉘 cmdlet 레퍼런스
description: Azure Scheduler용 PowerShell cmdlet에 대해 알아봅니다.
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 577e2128cf7e4e9f914ec5504917053acb3c19d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898492"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Azure Scheduler용 PowerShell cmdlet 참조

> [!IMPORTANT]
> [Azure 논리 앱이](../logic-apps/logic-apps-overview.md) [사용 중지되는](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)Azure 스케줄러를 대체합니다. 스케줄러에서 설정한 작업을 계속 작업하려면 가능한 한 빨리 [Azure Logic Apps로 마이그레이션하십시오.](../scheduler/migrate-from-scheduler-to-logic-apps.md) 
>
> 스케줄러는 Azure 포털에서 더 이상 사용할 수 없지만 작업 및 작업 컬렉션을 관리할 수 있도록 [현재 REST API](/rest/api/scheduler) 및 Azure [스케줄러 PowerShell cmdlet을](scheduler-powershell-reference.md) 사용할 수 있습니다.

Scheduler 작업 및 작업 컬렉션을 만들고 관리하는 데 사용할 스크립트를 작성하려는 경우 PowerShell cmdlet을 사용할 수 있습니다. 이 문서에서는 중요한 Azure Scheduler용 PowerShell cmdlet 및 각 cmdlet의 참조 문서 링크를 제공합니다. Azure 구독용으로 Azure PowerShell을 설치하려면 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요. [Azure Resource Manager cmdlet](/powershell/azure/overview)에 대한 자세한 내용은 [Azure Resource Manager로 Azure PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

| Cmdlet | 설명 |
|--------|-------------|
| [비활성화-아즈스케줄러작업컬렉션](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |작업 컬렉션을 비활성화합니다. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |작업 컬렉션을 활성화합니다. |
| [겟 아즈스케줄러잡](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Scheduler 작업을 가져옵니다. |
| [겟-아즈스케줄러잡컬렉션](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |작업 컬렉션을 가져옵니다. |
| [겟-아즈스케줄러잡히스토리](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |작업 내역을 가져옵니다. |
| [뉴 아즈스케줄러HttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |HTTP 작업을 만듭니다. |
| [뉴 아즈스케줄러잡컬렉션](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |작업 컬렉션을 만듭니다. |
| [뉴 아즈스케줄러서비스버스큐작업](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Service Bus 큐 작업을 만듭니다. |
| [뉴 아즈스케줄러서비스버스토픽작업](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Service Bus 항목 작업을 만듭니다. |
| [New-Az스케줄러스토리지큐작업](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Storage 큐 작업을 만듭니다. |
| [제거-아즈스케줄러작업](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Scheduler 작업을 삭제합니다. |
| [제거-아즈스케줄러잡컬렉션](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |작업 컬렉션을 삭제합니다. |
| [세트-아즈스케줄러HttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Scheduler HTTP 작업을 수정합니다. |
| [세트-아즈스케줄러잡컬렉션](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |작업 컬렉션을 수정합니다. |
| [세트-아즈스케줄러서비스버스큐작업](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Service Bus 큐 작업을 수정합니다. |
| [세트-아즈스케줄러서비스버스토픽작업](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Service Bus 항목 작업을 수정합니다. |
| [세트-아즈스케줄러스토리지큐작업](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Storage 큐 작업을 수정합니다. |
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