---
title: "자동화 계정 및 리소스 마이그레이션 | Microsoft Docs"
description: "이 문서에서는 Azure 자동화의 자동화 계정 및 관련 리소스를 구독 간에 이동하는 방법을 설명합니다."
services: automation
documentationcenter: 
author: MGoedtel
manager: jwhit
editor: tysonn
ms.assetid: 9c2db4a2-f324-48dc-8ce7-3343bf7230d5
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/21/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 4fbfb24a2e9d55d718902d468bd25e12f64e7d24
ms.openlocfilehash: 687da15bdaf854254321b59350f47549781676f5


---
# <a name="migrate-automation-account-and-resources"></a>자동화 계정 및 리소스 마이그레이션
자동화 계정 및 연결된 된 리소스 (즉, 자산, runbook, 모듈 등)를 Azure 포털에서 만든 다른 또는 하나의 구독에서 다른 한 리소스 그룹에서 마이그레이션에 대해 손쉽게 수행할 수 있습니다 [리소스 이동](../azure-resource-manager/resource-group-move-resources.md) Azure Portal에서 사용할 수 있는 기능입니다. 그러나 이 작업을 계속하기 전에 다음 [리소스를 이동하기 전의 검사 목록](../azure-resource-manager/resource-group-move-resources.md#checklist-before-moving-resources)과 아래의 자동화 관련 목록을 검토해야 합니다.   

1. 대상 구독/리소스 그룹은 원본이 있는 동일한 지역에 있어야 합니다.  즉, 자동화 계정을 여러 지역 간에 이동할 수 없습니다.
2. 리소스(예: Runbook, 작업 등)를 이동할 때 원본 그룹과 대상 그룹은 작업 기간 동안 잠겨 있습니다. 쓰기 및 삭제 작업은 이동이 완료될 때까지 그룹에서 차단됩니다.  
3. 기존 구독의 리소스 또는 구독 ID를 참조하는 모든 Runbook 또는 변수는 마이그레이션이 완료된 후에 업데이트해야 합니다.   

> [!NOTE]
> 이 기능은 클래식 자동화 리소스 이동을 지원하지 않습니다.
>
>

## <a name="to-move-the-automation-account-using-the-portal"></a>포털을 사용하여 자동화 계정을 이동하려면
1. 자동화 계정에서 블레이드 위쪽에 있는 **이동**을 클릭합니다.<br> ![이동 옵션](media/automation-migrate-account-subscription/automation-menu-move.png)<br>
2. **리소스 이동** 블레이드에는 자동화 계정 및 리소스 그룹 둘 다에 관련된 리소스가 표시됩니다.  드롭다운 목록에서 **구독** 및 **리소스 그룹**을 선택하거나 **새 리소스 그룹 만들기** 옵션을 선택한 후 제공된 필드에 새 리소스 그룹 이름을 입력합니다.  
3. 검토한 후 *리소스를 이동한 후 새 리소스 ID를 사용하려면 도구 및 스크립트를 업데이트해야 한다는 사실을 이해*했음을 확인하는 확인란을 선택한 후 **확인**을 클릭합니다.<br> ![리소스 이동 블레이드](media/automation-migrate-account-subscription/automation-move-resources-blade.png)<br>   

이 작업을 완료하는 데는 몇 분 정도 걸릴 수 있습니다.  **알림**에 유효성 검사 및 마이그레이션을 비롯한 각 작업의 상태와 최종 완료 시기가 표시됩니다.     

## <a name="to-move-the-automation-account-using-powershell"></a>PowerShell을 사용하여 자동화 계정을 이동하려면
기존 자동화 리소스를 다른 리소스 그룹 또는 구독으로 이동하려면 **Get-AzureRmResource** cmdlet을 사용하여 특정 자동화 계정을 가져온 다음 **Move-AzureRmResource** cmdlet을 사용하여 이동을 수행합니다.

첫 번째 예제는 자동화 계정을 새 리소스 그룹으로 이동하는 방법을 보여 줍니다.

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup"
   ```

위의 코드 예제를 실행하면 이 작업을 수행할지 묻는 메시지가 나타납니다.  **예** 를 클릭하고 스크립트가 진행되도록 허용하면 마이그레이션을 수행하는 동안 알림이 수신되지 않습니다.  

새 구독으로 이동하려면 *DestinationSubscriptionId* 매개 변수 값을 포함합니다.

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup" -DestinationSubscriptionId "SubscriptionId"
   ```

앞의 예제에서와 마찬가지로 이동할지 묻는 메시지가 표시됩니다.  

## <a name="next-steps"></a>다음 단계
* 리소스를 새 리소스 그룹이나 구독으로 이동하는 방법에 대한 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/resource-group-move-resources.md)
* Azure 자동화의 역할 기반 액세스 제어에 대한 자세한 내용은 [Azure 자동화에서 역할 기반 액세스 제어](automation-role-based-access-control.md)를 참조하세요.
* 구독을 관리하기 위한 PowerShell cmdlet에 대한 자세한 내용은 [Resource Manager에서 Azure PowerShell 사용](../azure-resource-manager/powershell-azure-resource-manager.md)
* 구독을 관리하기 위한 포털 기능에 대한 자세한 내용은 [Azure 포털을 사용하여 리소스 관리](../azure-resource-manager/resource-group-portal.md)를 참조하세요.



<!--HONumber=Dec16_HO3-->


