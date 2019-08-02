---
title: Azure Automation 계정을 다른 구독으로 이동
description: 이 문서에서는 Automation 계정을 다른 구독으로 이동 하는 방법을 설명 합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8187e4c6f2c7dc721c178bad50b6c3ada2a65367
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717236"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Azure Automation 계정을 다른 구독으로 이동

Azure는 일부 리소스를 새 리소스 그룹 또는 구독으로 이동 하는 기능을 제공 합니다. Azure Portal, PowerShell, Azure CLI 또는 REST API를 통해 리소스를 이동할 수 있습니다. 프로세스에 대해 자세히 알아보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/resource-group-move-resources.md)을 참조 하세요.

Azure Automation 계정은 이동할 수 있는 리소스 중 하나입니다. 이 문서에서는 Automation 계정을 다른 리소스 또는 구독으로 이동 하는 단계에 대해 알아봅니다.

Automation 계정을 이동 하는 개략적인 단계는 다음과 같습니다.

1. 솔루션을 제거 합니다.
2. 작업 영역 연결을 해제 합니다.
3. Automation 계정을 이동 합니다.
4. 실행 계정을 삭제 하 고 다시 만듭니다.
5. 솔루션을 다시 사용 하도록 설정 합니다.

## <a name="remove-solutions"></a>솔루션 제거

Automation 계정에서 작업 영역의 연결을 끊으려면 이러한 솔루션을 작업 영역에서 제거 해야 합니다.
- **변경 내용 추적 및 인벤토리**
- **업데이트 관리**
- **업무 시간 외 Vm 시작/중지**

리소스 그룹에서 각 솔루션을 찾고 **삭제**를 선택 합니다. **리소스 삭제** 페이지에서 제거할 리소스를 확인 하 고 **삭제**를 선택 합니다.

![Azure Portal에서 솔루션 삭제](../media/move-account/delete-solutions.png)

다음 예제와 같이 [move-azurermresource](/powershell/module/azurerm.resources/remove-azurermresource) cmdlet을 사용 하 여 동일한 작업을 수행할 수 있습니다.

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>Vm 시작/중지에 대 한 추가 단계

**Vm 시작/중지** 솔루션의 경우 솔루션에서 만든 경고 규칙도 제거 해야 합니다.

Azure Portal에서 리소스 그룹으로 이동 하 고 **모니터링** > **경고** > **경고 규칙 관리**를 선택 합니다.

![경고 관리 규칙의 선택 항목을 보여 주는 경고 페이지](../media/move-account/alert-rules.png)

**규칙** 페이지에 해당 리소스 그룹에 구성 된 경고 목록이 표시 됩니다. **Vm 시작/중지** 솔루션은 세 가지 경고 규칙을 만듭니다.

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

이 세 가지 경고 규칙을 선택한 다음 **삭제**를 선택 합니다. 이 작업을 수행 하면 이러한 경고 규칙이 제거 됩니다.

![선택한 규칙에 대 한 삭제 확인을 요청 하는 규칙 페이지](../media/move-account/delete-rules.png)

> [!NOTE]
> **규칙** 페이지에서 경고 규칙이 표시 되지 않는 경우 사용 하지 않도록 설정 했을 수 있으므로 **사용 하지 않도록 설정** 된 경고 표시로 **상태** 를 변경 합니다.

경고 규칙이 제거 되 면 **Vm 시작/중지** 솔루션 알림에 대해 만든 작업 그룹을 제거 합니다.

Azure Portal에서 **모니터** > **경고** > **관리 작업 그룹**을 선택 합니다.

목록에서 **StartStop_VM_Notification** 를 선택 합니다. 작업 그룹 페이지에서 **삭제**를 선택 합니다.

![작업 그룹 페이지에서 삭제를 선택 합니다.](../media/move-account/delete-action-group.png)

마찬가지로, 다음 예제와 같이 [AzureRmActionGroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) cmdlet을 사용 하 여 PowerShell을 사용 하 여 작업 그룹을 삭제할 수 있습니다.

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>작업 영역 연결 해제

Azure Portal에서 **Automation 계정** > **관련 리소스** > **연결 된 작업 영역**을 선택 합니다. **작업 영역 연결 해제** 를 선택 하 여 Automation 계정에서 작업 영역의 연결을 해제 합니다.

![Automation 계정에서 작업 영역 연결 끊기](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Automation 계정 이동

이전 항목을 제거한 후에도 Automation 계정 및 해당 runbook을 계속 제거할 수 있습니다. Azure Portal에서 Automation 계정의 리소스 그룹으로 이동 합니다. **다른 구독으로**이동 이동을 **선택 합니다.**  > 

![리소스 그룹 페이지, 다른 구독으로 이동](../media/move-account/move-resources.png)

이동 하려는 리소스 그룹의 리소스를 선택 합니다. **Automation 계정**, **Runbook**및 **Log Analytics 작업 영역** 리소스를 포함 해야 합니다.

이동이 완료 되 면 모든 작업을 수행 하는 데 필요한 추가 단계가 있습니다.

## <a name="re-create-run-as-accounts"></a>실행 계정 다시 만들기

[실행 계정](../manage-runas-account.md) Azure 리소스를 사용 하 여 인증 하는 Azure Active Directory에서 서비스 주체를 만듭니다. 구독을 변경 하면 Automation 계정에서 기존 실행 계정을 더 이상 사용 하지 않습니다.

새 구독의 Automation 계정으로 이동 하 고 **계정 설정**에서 **실행 계정** 을 선택 합니다. 실행 계정이 불완전 하 게 표시 되는 것을 볼 수 있습니다.

![실행 계정은 불완전 합니다.](../media/move-account/run-as-accounts.png)

각 실행 계정을 선택 합니다. **속성** 페이지에서 **삭제** 를 선택 하 여 실행 계정을 삭제 합니다.

> [!NOTE]
> 실행 계정을 만들거나 볼 수 있는 권한이 없는 경우 다음과 같은 메시지가 표시 됩니다. `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.`실행 계정을 구성 하는 데 필요한 권한에 대 한 자세한 내용은 [실행 계정을 구성 하는 데 필요한 권한](../manage-runas-account.md#permissions)을 참조 하세요.

실행 계정이 삭제 된 후 **Azure 실행 계정**에서 **만들기** 를 선택 합니다. **Azure 실행 계정 추가** 페이지에서 **만들기** 를 선택 하 여 실행 계정 및 서비스 주체를 만듭니다. **Azure 클래식 실행 계정**으로 이전 단계를 반복 합니다.

## <a name="enable-solutions"></a>솔루션 사용

실행 계정을 다시 만든 후에는 이동 하기 전에 제거한 솔루션을 다시 사용 하도록 설정 합니다. **변경 내용 추적 및 인벤토리** 및 **업데이트 관리**를 켜려면 Automation 계정에서 해당 기능을 선택 합니다. 이동한 Log Analytics 작업 영역을 선택 하 고 **사용**을 선택 합니다.

![이동한 Automation 계정에서 솔루션 다시 사용](../media/move-account/reenable-solutions.png)

솔루션과 등록 된 컴퓨터는 기존 Log Analytics 작업 영역에 연결 된 경우 표시 됩니다.

업무 시간 외 **Vm 시작/중지** 솔루션을 켜려면 솔루션을 다시 배포 해야 합니다.  >  **관련 리소스**에서 **vm 시작/중지**를 선택 하 고, 솔루션 > **만들기** 를**사용 하도록 설정**하 여 배포를 시작 합니다.

**솔루션 추가** 페이지에서 Log Analytics 작업 영역 및 Automation 계정을 선택 합니다.

![솔루션 추가 메뉴](../media/move-account/add-solution-vm.png)

솔루션을 구성 하는 방법에 대 한 자세한 내용은 [Azure Automation의 작업 시간 외 Vm 시작/중지 솔루션](../automation-solution-vm-management.md)을 참조 하십시오.

## <a name="post-move-verification"></a>이동 후 확인

이동이 완료 되 면 확인 해야 하는 다음 작업 목록을 확인 합니다.

|기능|테스트|문제 해결 링크|
|---|---|---|
|Runbook|Runbook을 성공적으로 실행 하 고 Azure 리소스에 연결할 수 있습니다.|[Runbook 문제 해결](../troubleshoot/runbooks.md)
|소스 제어|원본 제어 리포지토리에서 수동 동기화를 실행할 수 있습니다.|[소스 제어 통합](../source-control-integration.md)|
|변경 내용 추적 및 인벤토리|컴퓨터의 현재 인벤토리 데이터가 표시 되는지 확인 합니다.|[변경 내용 추적 문제 해결](../troubleshoot/change-tracking.md)|
|업데이트 관리|컴퓨터가 정상 상태 인지 확인 합니다.</br>테스트 소프트웨어 업데이트 배포를 실행 합니다.|[업데이트 관리 문제 해결](../troubleshoot/update-management.md)|
|공유 리소스|[자격 증명](../shared-resources/credentials.md), [변수](../shared-resources/variables.md)등의 모든 공유 리소스가 표시 되는지 확인 합니다.|

## <a name="next-steps"></a>다음 단계

Azure에서 리소스를 이동 하는 방법에 대해 자세히 알아보려면 [azure에서 리소스 이동](../../azure-resource-manager/move-support-resources.md)을 참조 하세요.
