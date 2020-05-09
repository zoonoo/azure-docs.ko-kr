---
title: Azure Automation 계정을 다른 구독으로 이동
description: 이 문서에서는 Automation 계정을 다른 구독으로 이동 하는 방법을 설명 합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bfb2f2d1d0f6a0d11784847344cd3dbcafdb0959
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900988"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Azure Automation 계정을 다른 구독으로 이동

Azure Automation를 사용 하 여 일부 리소스를 새 리소스 그룹 또는 구독으로 이동할 수 있습니다. Azure Portal, PowerShell, Azure CLI 또는 REST API를 통해 리소스를 이동할 수 있습니다. 프로세스에 대해 자세히 알아보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조 하세요.

Automation 계정은 이동할 수 있는 리소스 중 하나입니다. 이 문서에서는 Automation 계정을 다른 리소스 또는 구독으로 이동 하는 방법을 배웁니다. Automation 계정을 이동 하기 위한 개략적인 단계는 다음과 같습니다.

1. 솔루션을 제거 합니다.
2. 작업 영역 연결을 해제 합니다.
3. Automation 계정을 이동 합니다.
4. 실행 계정을 삭제 하 고 다시 만듭니다.
5. 솔루션을 다시 사용 하도록 설정 합니다.

>[!NOTE]
>이 문서에서는 Azure PowerShell Az module을 사용 하 여 작업 합니다. AzureRM 모듈을 계속 사용할 수 있습니다. Az module 및 AzureRM compatibility에 대해 자세히 알아보려면 [새 Azure PowerShell Az Module 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조 하세요. Hybrid Runbook Worker에 대 한 Az module 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조 하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트 하는 방법을](../automation-update-azure-modules.md)사용 하 여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="remove-solutions"></a>솔루션 제거

Automation 계정에서 작업 영역의 연결을 끊으려면 작업 영역에서 다음 솔루션을 제거 해야 합니다.

- 변경 내용 추적 및 인벤토리
- 업데이트 관리
- 작업 시간 외 VM 시작/중지

1. Azure Portal에서 리소스 그룹을 찾습니다.
2. 각 솔루션을 찾고 **리소스 삭제** 페이지에서 **삭제** 를 선택 합니다.

    ![Azure Portal에서 솔루션을 삭제 하는 스크린샷](../media/move-account/delete-solutions.png)

원한다 면 [AzResource](https://docs.microsoft.com/powershell/module/Az.Resources/Remove-AzResource?view=azps-3.7.0) cmdlet을 사용 하 여 솔루션을 삭제할 수 있습니다.

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="remove-alert-rules-for-the-startstop-vms-during-off-hours-solution"></a>"휴무 시간 동안 Vm 시작/중지" 솔루션에 대 한 경고 규칙을 제거 합니다.

이 솔루션의 경우 솔루션에서 만든 경고 규칙도 제거 해야 합니다.

1. Azure Portal에서 리소스 그룹으로 이동 하 고 **모니터링** > **경고** > **경고 규칙 관리**를 선택 합니다.

   ![경고 규칙 관리 선택 항목을 보여 주는 경고 페이지 스크린샷](../media/move-account/alert-rules.png)

2. **규칙** 페이지에 해당 리소스 그룹에 구성 된 경고 목록이 표시 됩니다. 솔루션은 다음과 같은 규칙을 만듭니다.

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. 규칙을 한 번에 하나씩 선택 하 고 **삭제** 를 선택 하 여 제거 합니다.

    ![선택한 규칙에 대 한 삭제 확인을 요청 하는 규칙 페이지의 스크린샷](../media/move-account/delete-rules.png)

    > [!NOTE]
    > **규칙** 페이지에 경고 규칙이 표시 되지 않으면 **상태** 필드를 **사용 안 함** 으로 변경 하 여 비활성화 된 경고를 표시 합니다. 사용 하지 않도록 설정 했을 수 있습니다.

4. 경고 규칙을 제거 하는 경우 "휴무 시간 동안 Vm 시작/중지" 솔루션 알림을 위해 만든 작업 그룹을 제거 해야 합니다. Azure Portal에서 **모니터** > **경고** > **관리 작업 그룹**을 선택 합니다.

5. **StartStop_VM_Notification**를 선택 합니다. 

6. 작업 그룹 페이지에서 **삭제**를 선택 합니다.

    ![작업 그룹 페이지의 스크린샷](../media/move-account/delete-action-group.png)

원한다 면 [AzActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/remove-azactiongroup?view=azps-3.7.0) cmdlet을 사용 하 여 작업 그룹을 삭제할 수 있습니다.

```azurepowershell-interactive
Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>작업 영역 연결 해제

이제 작업 영역 연결을 끊을 수 있습니다.

1. Azure Portal에서 **Automation 계정** > **관련 리소스** > **연결 된 작업 영역**을 선택 합니다. 

2. **작업 영역 연결 해제** 를 선택 하 여 Automation 계정에서 작업 영역의 연결을 해제 합니다.

    ![Automation 계정에서 작업 영역 연결 끊기의 스크린샷](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Automation 계정 이동

이제 Automation 계정 및 runbook을 이동할 수 있습니다. 

1. Azure Portal에서 Automation 계정의 리소스 그룹으로 이동 합니다.  > **다른 구독으로** **이동 이동을 선택 합니다**.

    ![리소스 그룹 페이지의 스크린샷, 다른 구독으로 이동](../media/move-account/move-resources.png)

2. 이동 하려는 리소스 그룹의 리소스를 선택 합니다. Automation 계정, runbook 및 Log Analytics 작업 영역 리소스를 포함 하는지 확인 합니다.

## <a name="re-create-run-as-accounts"></a>실행 계정 다시 만들기

[실행 계정](../manage-runas-account.md) Azure 리소스를 사용 하 여 인증 하는 Azure Active Directory에서 서비스 주체를 만듭니다. 구독을 변경 하면 Automation 계정에서 기존 실행 계정을 더 이상 사용 하지 않습니다. 실행 계정을 다시 만들려면 다음을 수행 합니다.

1. 새 구독의 Automation 계정으로 이동 하 고 **계정 설정**에서 **실행 계정** 을 선택 합니다. 실행 계정이 불완전 하 게 표시 되는 것을 볼 수 있습니다.

    ![불완전 하 게 표시 되는 실행 계정의 스크린샷](../media/move-account/run-as-accounts.png)

2. **속성** 페이지에서 **삭제** 를 선택 하 여 실행 계정을 한 번에 하나씩 삭제 합니다. 

    > [!NOTE]
    > 실행 계정을 만들거나 볼 수 있는 권한이 없는 경우 다음과 같은 메시지가 표시 됩니다. `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` 자세한 내용은 [실행 계정을 구성 하는 데 필요한 권한](../manage-runas-account.md#permissions)을 참조 하세요.

3. 실행 계정을 삭제 한 후 **Azure 실행 계정**에서 **만들기** 를 선택 합니다. 

4. **Azure 실행 계정 추가** 페이지에서 **만들기** 를 선택 하 여 실행 계정 및 서비스 주체를 만듭니다. 

5. Azure 클래식 실행 계정으로 위의 단계를 반복 합니다.

## <a name="enable-solutions"></a>솔루션 사용

실행 계정을 다시 만든 후에는 이동 전에 제거한 솔루션을 다시 사용 하도록 설정 해야 합니다. 

1. "변경 내용 추적 및 재고" 솔루션을 켜려면 Automation 계정에서 **변경 내용 추적 및 인벤토리** 를 선택 합니다. 이동한 Log Analytics 작업 영역을 선택 하 고 **사용**을 선택 합니다.

2. "업데이트 관리" 솔루션에 대해 1 단계를 반복 합니다.

    ![이동 된 Automation 계정에서 솔루션을 다시 사용 하도록 설정 하는 스크린샷](../media/move-account/reenable-solutions.png)

3. 솔루션과 등록 된 컴퓨터는 기존 Log Analytics 작업 영역에 연결 된 경우에 표시 됩니다. "업무 시간 외 Vm 시작/중지" 솔루션을 설정 하려면 솔루션을 다시 배포 해야 합니다. **관련 리소스**에서 >  **vm 시작/중지**를 선택 하 고, 솔루션 > **만들기** 를**사용 하도록 설정**하 여 배포를 시작 합니다.

4. **솔루션 추가** 페이지에서 Log Analytics 작업 영역 및 Automation 계정을 선택 합니다.

    ![솔루션 추가 메뉴 스크린샷](../media/move-account/add-solution-vm.png)

5. [Azure Automation의 시간 외 Vm 시작/중지 솔루션](../automation-solution-vm-management.md)에 설명 된 대로 솔루션을 구성 합니다.

## <a name="verify-the-move"></a>이동 확인

이동이 완료 되 면 아래 나열 된 기능을 사용할 수 있는지 확인 합니다. 

|기능|테스트|문제 해결|
|---|---|---|
|Runbook|Runbook을 성공적으로 실행 하 고 Azure 리소스에 연결할 수 있습니다.|[Runbook 문제 해결](../troubleshoot/runbooks.md)
|원본 제어|원본 제어 리포지토리에서 수동 동기화를 실행할 수 있습니다.|[소스 제어 통합](../source-control-integration.md)|
|변경 내용 추적 및 인벤토리|컴퓨터의 현재 인벤토리 데이터가 표시 되는지 확인 합니다.|[변경 내용 추적 문제 해결](../troubleshoot/change-tracking.md)|
|업데이트 관리|컴퓨터가 표시 되 고 정상 상태 인지 확인 합니다.</br>테스트 소프트웨어 업데이트 배포를 실행 합니다.|[업데이트 관리 문제 해결](../troubleshoot/update-management.md)|
|공유 리소스|[자격 증명](../shared-resources/credentials.md) 및 [변수와](../shared-resources/variables.md)같은 모든 공유 리소스가 표시 되는지 확인 합니다.|

## <a name="next-steps"></a>다음 단계

Azure에서 리소스를 이동 하는 방법에 대 한 자세한 내용은 [azure에서 리소스 이동](../../azure-resource-manager/management/move-support-resources.md)을 참조 하세요.
