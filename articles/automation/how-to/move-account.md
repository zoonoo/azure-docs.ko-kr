---
title: 다른 구독으로 Azure Automation 계정 이동
description: 이 문서에서는 Automation 계정을 다른 구독으로 이동 하는 방법 설명
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 01f3995a80375f2deada13c36c48600243a17623
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2019
ms.locfileid: "57733094"
---
# <a name="move-your-automation-account-to-another-subscription"></a>다른 구독으로 Automation 계정 이동

Azure 새 리소스 그룹 또는 Azure portal, PowerShell, Azure CLI 또는 REST API를 통해 고유 하 게 동일한 테 넌 트를 사용 하 여 구독에 일부 리소스를 이동 하는 기능을 제공 합니다. 프로세스에 대 한 자세한 내용은 참조 하세요 [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/resource-group-move-resources.md)합니다. Automation 계정을 이동할 수 있는 리소스 중 하나는 되지만 Automation 계정 이동 하는 경우 필요한 특별 한 단계입니다.

Automation 계정 이동 하는 대략적인 단계는 다음과 같습니다.

* 솔루션 제거
* 작업 영역 연결 해제
* Automation 계정 이동
* 삭제 및 다시 실행 계정 만들기
* 솔루션을 다시 사용 하도록 설정

## <a name="remove-solutions"></a>솔루션 제거

Automation 계정, 변경 및 인벤토리에서 작업 영역 연결을 끊을 업데이트 관리 및 시간 솔루션 해제 하는 동안 Vm 시작/중지 작업 영역에서 제거 되어야 합니다.

각 리소스 그룹 선택 **솔루션** 누릅니다 **삭제**합니다. 에 **리소스 삭제** 페이지에서 확인 하는 리소스를 제거 하 고 클릭 **삭제**합니다.

![Azure portal에서 솔루션을 삭제 합니다.](../media/move-account/delete-solutions.png)

사용 하 여 동일한 작업을 수행할 수 있습니다 합니다 [Remove-azurermresource](/powershell/module/azurerm.resources/remove-azurermresource) 다음 예제에서와 같이 cmdlet:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>Vm 시작/중지에 대 한 추가 단계

에 대 한 합니다 **VM 시작/중지** 솔루션도 해야 솔루션에서 만든 경고 규칙을 제거 합니다.

Azure portal에서 리소스 그룹으로 이동 및 선택 **경고** 아래에서 **모니터링**합니다. 에 **경고** 페이지에서 **경고 규칙 관리**

![경고 관리 규칙에 대해 클릭 하면 경고 페이지 표시](../media/move-account/alert-rules.png)

에 **규칙** 페이지에서 모든 해당 리소스 그룹에 구성 된 경고 목록이 표시 됩니다. 합니다 **Vm 시작/중지** 솔루션은 3 개의 경고 규칙을 만듭니다

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

이러한 3 개의 경고 규칙을 선택 하 고 클릭 **삭제**합니다. 이 작업은 이러한 경고 규칙을 제거 합니다.

![선택한 규칙을 사용 하 여 페이지 및 개방형 규칙 삭제](../media/move-account/delete-rules.png)

> [!NOTE]
> 모든 경고 규칙에 표시 되지 않으면를 **규칙** 페이지에서 변경 합니다 **상태** 표시할 **사용 하지 않도록 설정** 있습니다 수 있는 비활성화 된 것으로 해당 경고.

경고 규칙 제거 되 면 VM 시작/중지 솔루션에 대 한 알림이 생성 된 작업 그룹을 제거 해야 합니다.

Azure portal로 이동 **모니터**를 선택 **경고**를 클릭 하 고 **작업 그룹 관리**합니다.

작업 그룹 목록에서 선택, 이름 더 **StartStop_VM_Notification**합니다. 작업 그룹 페이지에서 클릭 **삭제**

![작업 그룹 페이지에서 삭제를 클릭](../media/move-account/delete-action-group.png)

마찬가지로, PowerShell 사용 하 여 작업 그룹을 삭제할 수 있습니다. 이 작업은 작업을 완료 합니다 [Remove-azurermactiongroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) 다음 예제와 같이 cmdlet:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>작업 영역 연결 해제

Azure portal로 이동 하 **Automation 계정**합니다. 아래 **관련 된 리소스**, 클릭 **연결 된 작업 영역**합니다. 클릭 **작업 영역 연결 해제** Automation 계정에서 작업 영역 연결 해제 합니다.

![Automation 계정에서 작업 영역 연결 해제](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Automation 계정 이동

이전 항목을 모두 제거 되었거나, Automation 계정 및 해당 runbook을 제거 하려면 계속할 수 있습니다. Azure portal에서 Automation 계정 리소스 그룹으로 이동 합니다. 선택 **이동** 차례로 **다른 구독으로 이동**합니다.

![다른 구독으로 이동을 선택 하는 리소스 그룹 페이지](../media/move-account/move-resources.png)

이동 하려는 리소스 그룹에서 리소스를 선택 합니다. 포함 해야 하면 **Automation 계정**, **Runbook**, 및 **Log Analytics 작업 영역** 리소스.

이동이 완료 되 면에 모든 기능을 사용 하려면 수행 해야 하는 추가 단계가 있습니다.

## <a name="recreate-run-as-accounts"></a>실행을 계정으로 다시 만듭니다.

[실행 계정](../manage-runas-account.md) Azure 리소스를 사용 하 여 인증 하도록 Azure Active Directory에 서비스 주체를 만듭니다. 구독을 변경 하면 기존 실행 계정을 Automation 계정에서 사용할 수 있는 더 이상.

선택한 Automation 계정에서 새 구독으로 이동 **계정으로 실행** 아래에서 **계정 설정**합니다. 이제는 실행 계정 표시으로 불완전 한 볼 수 있습니다.

![실행 계정 표시 완료 안 됨](../media/move-account/run-as-accounts.png)

계정으로 이동 하 고에서 각 실행을 클릭 합니다 **속성** 페이지에서 클릭 **삭제** 실행 계정을 삭제 하려면.

> ! [참고] 만들거나 다음 메시지가 표시 됩니다 실행 계정을 볼 수 있는 권한이 없는 경우 `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.`합니다. 실행 계정을 구성 하는 데 필요한 권한에 대 한 자세한 참조 [실행 계정을 구성 하는 데 필요한 권한을](../manage-runas-account.md#permissions)합니다.

실행 계정 삭제 되 면 클릭 **Create** 에 **Azure 실행 계정**합니다. 에 **Azure 실행 계정 추가** 페이지에서 클릭 **만들기** 실행 계정 및 서비스 주체를 만들려면. 사용 하 여 위의 단계를 반복 합니다 **Azure 클래식 실행 계정**합니다.

## <a name="enable-solutions"></a>솔루션 사용

실행 계정을 만들었습니다, 후 이동 하기 전에 제거 하는 솔루션을 다시 활성화할 수 있습니다. 사용할 수 있도록 **변경 내용 추적 및 인벤토리** 하 고 **업데이트 관리**, Automation 계정에 해당 하는 기능을 선택 합니다. 위로 이동 하 여 Log Analytics 작업 영역을 선택 하 고 클릭 **사용**합니다.

![다시 이동된 Automation 계정에 솔루션을 사용 하도록 설정](../media/move-account/reenable-solutions.png)

솔루션을 사용 하 여 등록 된 컴퓨터 다시 표시 됩니다 기존 Log Analytics 작업 영역을 연결 하는 이후.

Vm 시작/중지 작업이 없는 동안을 다시 활성화 하려면 솔루션을 다시 배포 해야 합니다. 아래 **관련 된 리소스**를 선택 **VM 시작/중지**합니다. 클릭 **자세한 정보 및 솔루션을 사용 하도록 설정** 누릅니다 **만들기** 배포를 시작 합니다.

에 **솔루션 추가** 페이지, Log Analytics 작업 영역 및 Automation 계정을 선택 합니다.  

![실행 계정 표시 완료 안 됨](../media/move-account/add-solution-vm.png)

솔루션 구성에 대 한 자세한 내용은 참조 하세요. [Azure Automation의 작업이 없는 동안 Vm 시작/중지](../automation-solution-vm-management.md)

## <a name="post-move-verification"></a>사후 이동 확인

이동이 완료 되 면 모든 항목이 예상 대로 작동 되도록 Automation 계정에서 다양 한 시나리오를 확인 해야 합니다. 다음 표에서 이동이 완료 된 후 확인 해야 하는 작업의 목록을 보여 줍니다.

|기능|테스트|연결 문제 해결|
|---|---|---|
|Runbook|성공적으로 Runbook을 실행 하 고 Azure 리소스에 연결할 수 있습니다.|[Runbook 문제 해결](../troubleshoot/runbooks.md)
| 소스 제어|원본 제어 리포지토리에에서 수동 동기화를 실행할 수 있습니다.|[소스 제어 통합](../source-control-integration.md)|
|변경 내용 추적 및 인벤토리|컴퓨터에서 현재 인벤토리 데이터를 표시를 확인 합니다.|[변경 내용 추적 문제 해결](../troubleshoot/change-tracking.md)|
|업데이트 관리|컴퓨터를 표시 하 정상 상태 확인</br>테스트 소프트웨어 업데이트 배포를 실행 합니다.|[업데이트 관리 문제 해결](../troubleshoot/update-management.md)|

## <a name="next-steps"></a>다음 단계

Azure에서 리소스를 이동 하는 방법에 대 한 자세한 내용은를 참조 하세요. [Azure에서 리소스를 이동 합니다.](../../azure-resource-manager/move-support-resources.md)
