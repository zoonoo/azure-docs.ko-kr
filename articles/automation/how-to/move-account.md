---
title: Azure Automation 계정을 다른 구독으로 이동
description: 이 문서에서는 Automation 계정을 다른 구독으로 이동하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 562ea5e0e9e4851ed59bd3ef917be2f9c48cd2a7
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185554"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Azure Automation 계정을 다른 구독으로 이동

Azure Automation에서는 일부 리소스를 새 리소스 그룹 또는 구독으로 이동할 수 있습니다. Azure Portal, Azure PowerShell, Azure CLI 또는 REST API를 통해 리소스를 이동할 수 있습니다. 프로세스에 대한 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조하세요.

Automation 계정은 이동할 수 있는 리소스 중 하나입니다. 이 문서에서는 Automation 계정을 다른 리소스 또는 구독으로 이동하는 방법을 학습합니다. Automation 계정을 이동하기 위한 개략적인 단계는 다음과 같습니다.

1. 기능을 사용하지 않도록 설정합니다.
2. 작업 영역 연결을 해제합니다.
3. Automation 계정을 이동합니다.
4. 실행 계정을 삭제하고 다시 만듭니다.
5. 기능을 다시 사용하도록 설정합니다.

## <a name="remove-features"></a>기능 제거

Automation 계정에서 작업 영역의 연결을 해제하려면 작업 영역에서 기능 리소스를 제거해야 합니다.

- 변경 내용 추적 및 인벤토리
- 업데이트 관리
- 작업이 없는 동안 VM 시작/중지

1. Azure Portal에서 리소스 그룹을 찾습니다.
2. 각 기능을 찾고 **리소스 삭제** 페이지에서 **삭제**를 선택합니다.

    ![Azure Portal에서 기능 리소스를 삭제하는 스크린샷](../media/move-account/delete-solutions.png)

원하는 경우 다음과 같이 [AzResource](/powershell/module/Az.Resources/Remove-AzResource?view=azps-3.7.0) cmdlet을 사용하여 리소스를 삭제할 수 있습니다.

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="remove-alert-rules-for-startstop-vms-during-off-hours"></a>작업 시간 외 VM 시작/중지에 대한 경고 규칙 제거

작업 시간 외 VM 시작/중지의 경우 기능에서 만든 경고 규칙도 제거해야 합니다.

1. Azure Portal에서 리소스 그룹으로 이동하고 **모니터링** > **경고s** > **경고 규칙 관리**를 선택합니다.

   ![경고 규칙 관리 선택 항목을 보여 주는 경고 페이지 스크린샷](../media/move-account/alert-rules.png)

2. 규칙 페이지에 해당 리소스 그룹에 구성된 경고 목록이 됩니다. 이 기능은 다음 규칙을 만듭니다.

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. 규칙을 한 번에 하나씩 선택하고 **삭제**를 선택하여 제거합니다.

    ![선택한 규칙에 대해 삭제 확인을 요청하는 규칙 페이지의 스크린샷](../media/move-account/delete-rules.png)

    > [!NOTE]
    > 규칙 페이지에 경고 규칙이 표시되지 않으면 **상태** 필드를 **사용 안 함**으로 변경하여 사용하지 않는 경고를 표시합니다. 

4. 경고 규칙을 제거할 때는 작업 시간 외 VM 시작/중지에 대해 만든 작업 그룹을 제거해야 합니다. Azure Portal에서 **모니터** > **경고** > **작업 그룹 관리**를 선택합니다.

5. **StartStop_VM_Notification**을 선택합니다. 

6. 작업 그룹 페이지에서 **삭제**를 선택합니다.

    ![작업 그룹 페이지의 스크린샷](../media/move-account/delete-action-group.png)

원하는 경우 다음과 같이 [Remove-AzActionGroup](/powershell/module/az.monitor/remove-azactiongroup?view=azps-3.7.0) cmdlet을 사용하여 작업 그룹을 삭제할 수 있습니다.

```azurepowershell-interactive
Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>작업 영역 연결 해제

이제 작업 영역 연결을 해제할 수 있습니다.

1. Azure Portal에서 **Automation 계정** > **관련 리소스** > **연결된 작업 영역**을 선택합니다. 

2. **작업 영역 연결 해제**를 선택하여 Automation 계정에서 작업 영역의 연결을 해제합니다.

    ![Automation 계정에서 작업 영역 연결 해제의 스크린샷](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Automation 계정 이동

이제 Automation 계정 및 Runbook을 이동할 수 있습니다. 

1. Azure Portal에서 Automation 계정의 리소스 그룹으로 이동합니다. **이동** > **다른 구독으로 이동**을 선택합니다.

    ![리소스 그룹 페이지, 다른 구독으로 이동의 스크린샷](../media/move-account/move-resources.png)

2. 이동하려는 리소스 그룹에서 리소스를 선택합니다. Automation 계정, Runbook 및 Log Analytics 작업 영역 리소스를 포함하는지 확인합니다.

## <a name="re-create-run-as-accounts"></a>실행 계정 다시 만들기

[실행 계정](../manage-runas-account.md)은 Azure Active Directory에서 서비스 주체를 만들어 Azure 리소스에 인증합니다. 구독을 변경하면 Automation 계정에서 기존 실행 계정을 더 이상 사용하지 않습니다. 실행 계정을 다시 만들려면 다음을 수행합니다.

1. 새 구독의 Automation 계정에서 **계정 설정** 아래의 **실행 계정**을 선택합니다. 지금은 실행 계정이 불완전하게 표시됩니다.

    ![불완전하게 표시되는 실행 계정의 스크린샷](../media/move-account/run-as-accounts.png)

2. **속성** 페이지에서 **삭제**를 선택하여 실행 계정을 한 번에 하나씩 삭제합니다. 

    > [!NOTE]
    > 실행 계정을 만들거나 볼 권한이 없는 경우 다음과 같은 메시지가 표시됩니다. `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` 자세한 내용은 [실행 계정을 구성하는 데 필요한 권한](../manage-runas-account.md#permissions)을 참조하세요.

3. 실행 계정을 삭제한 후 **Azure 실행 계정**에서 **만들기**를 선택합니다. 

4. Azure 실행 계정 추가 페이지에서 **만들기**를 선택하여 실행 계정 및 서비스 주체를 만듭니다. 

5. Azure 클래식 실행 계정으로 위의 단계를 반복합니다.

## <a name="enable-features"></a>기능 사용

실행 계정을 다시 만든 후에는 이동 전에 제거한 기능을 다시 사용하도록 설정해야 합니다.

1. 변경 내용 추적 및 인벤토리를 켜려면 Automation 계정에서 **변경 내용 추적 및 인벤토리**를 선택합니다. 이동한 Log Analytics 작업 영역을 선택하고 **사용**을 선택합니다.

2. 업데이트 관리에 대해 1단계를 반복합니다.

    ![이동된 Automation 계정의 기능을 다시 사용하도록 설정하는 스크린샷](../media/move-account/reenable-solutions.png)

3. 기능을 사용하도록 설정된 머신은 기존 Log Analytics 작업 영역을 연결할 때 표시됩니다. 작업 시간 외 VM 시작/중지 기능을 켜려면 해당 기능을 다시 사용하도록 설정해야 합니다. **관련 리소스**에서 **VM 시작/중지** > **솔루션 자세히 보기 및 사용** > **만들기**를 선택하여 배포를 시작합니다.

4. 솔루션 추가 페이지에서 Log Analytics 작업 영역 및 Automation 계정을 선택합니다.

    ![솔루션 추가 메뉴의 스크린샷](../media/move-account/add-solution-vm.png)

5. [작업 시간 외 VM 시작/중지 개요](../automation-solution-vm-management.md)에 설명된 대로 기능을 구성합니다.

## <a name="verify-the-move"></a>이동 확인

이동이 완료되면 아래 나열된 기능을 사용할 수 있는지 확인합니다. 

|기능|테스트|문제 해결|
|---|---|---|
|Runbook|Runbook을 성공적으로 실행하고 Azure 리소스에 연결할 수 있습니다.|[Runbook 문제 해결](../troubleshoot/runbooks.md)
|원본 제어|원본 제어 리포지토리에서 수동 동기화를 실행할 수 있습니다.|[원본 제어 통합](../source-control-integration.md)|
|변경 내용 추적 및 인벤토리|머신에서 현재 인벤토리 데이터가 표시되는지 확인합니다.|[변경 내용 추적 문제 해결](../troubleshoot/change-tracking.md)|
|업데이트 관리|머신이 표시되며 정상 상태인지 확인합니다.</br>소프트웨어 업데이트 배포 테스트를 실행합니다.|[업데이트 관리 문제 해결](../troubleshoot/update-management.md)|
|공유 리소스|[자격 증명](../shared-resources/credentials.md) 및 [변수](../shared-resources/variables.md)와 같은 모든 공유 리소스가 표시되는지 확인합니다.|

## <a name="next-steps"></a>다음 단계

Azure에서의 리소스 이동에 대한 자세한 내용은 [Azure에서 리소스 이동](../../azure-resource-manager/management/move-support-resources.md)을 참조하세요.
