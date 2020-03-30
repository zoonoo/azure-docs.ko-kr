---
title: Azure 자동화 계정을 다른 구독으로 이동
description: 이 문서에서는 자동화 계정을 다른 구독으로 이동하는 방법에 대해 설명합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1aa759a2984764169eb28935e095d0f7c0f90c08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969838"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Azure 자동화 계정을 다른 구독으로 이동

Azure는 일부 리소스를 새 리소스 그룹 또는 구독으로 이동하는 기능을 제공합니다. Azure 포털, PowerShell, Azure CLI 또는 REST API를 통해 리소스를 이동할 수 있습니다. 프로세스에 대한 자세한 내용은 [리소스를 새 리소스 그룹 또는 구독으로 이동을](../../azure-resource-manager/management/move-resource-group-and-subscription.md)참조합니다.

Azure 자동화 계정은 이동할 수 있는 리소스 중 하나입니다. 이 문서에서는 자동화 계정을 다른 리소스 또는 구독으로 이동하는 단계를 배웁니다.

자동화 계정을 이동하는 고급 단계는 다음과 같습니다.

1. 솔루션을 제거합니다.
2. 작업 영역의 연결을 해제합니다.
3. 자동화 계정을 이동합니다.
4. 계정을 삭제하고 다시 만듭니다.
5. 솔루션을 다시 활성화합니다.

## <a name="remove-solutions"></a>솔루션 제거

자동화 계정에서 작업 영역의 연결을 해제하려면 다음 솔루션을 작업 영역에서 제거해야 합니다.
- **변경 내용 추적 및 인벤토리**
- **업데이트 관리**
- **영업 외 시간 동안 VM 시작/중지**

리소스 그룹에서 각 솔루션을 찾아 **삭제를**선택합니다. 리소스 **삭제** 페이지에서 제거할 리소스를 확인하고 **삭제를**선택합니다.

![Azure 포털에서 솔루션 삭제](../media/move-account/delete-solutions.png)

다음 예제와 같이 [제거-AzureRmResource](/powershell/module/azurerm.resources/remove-azurermresource) cmdlet을 사용 하 고 동일한 작업을 수행할 수 있습니다.

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>VM 시작/중지를 위한 추가 단계

**VM 시작/중지** 솔루션의 경우 솔루션에서 만든 경고 규칙을 제거해야 합니다.

Azure 포털에서 리소스 그룹으로 이동하여 **경고 모니터링** > **경고 규칙**관리를**선택합니다.** > 

![경고 관리 규칙 의 선택을 보여주는 경고 페이지](../media/move-account/alert-rules.png)

**규칙** 페이지에 해당 리소스 그룹에 구성된 경고 목록이 표시됩니다. **VM 시작/중지** 솔루션은 세 가지 경고 규칙을 만듭니다.

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

이 세 가지 경고 규칙을 선택한 다음 **삭제를**선택합니다. 이 작업을 수행하면 이러한 경고 규칙이 제거됩니다.

![선택한 규칙에 대한 삭제 확인을 요청하는 규칙 페이지](../media/move-account/delete-rules.png)

> [!NOTE]
> **규칙** 페이지에 경고 규칙이 표시되지 않으면 비활성화되었을 수 있으므로 **상태를** 변경하여 **사용 중지된** 경고를 표시합니다.

경고 규칙이 제거되면 **시작/중지 VMs** 솔루션 알림에 대해 만들어진 작업 그룹을 제거합니다.

Azure 포털에서 경고 **관리** > **작업 그룹을****선택합니다.** > 

목록에서 **StartStop_VM_Notification** 선택합니다. 작업 그룹 페이지에서 **삭제를**선택합니다.

![작업 그룹 페이지, 삭제 선택](../media/move-account/delete-action-group.png)

마찬가지로 다음 예제와 같이 [제거-AzureRmActionGroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) cmdlet을 사용하여 작업 그룹을 삭제할 수 있습니다.

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>작업 영역 연결 해제

Azure 포털에서 **자동화 계정** > **관련 리소스** > **연결된 작업 영역을**선택합니다. 작업 **영역을 연결 해제를** 선택하여 자동화 계정에서 작업 영역의 연결을 해제합니다.

![자동화 계정에서 작업 영역 연결 해제](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>자동화 계정 이동

이전 항목을 제거한 후 자동화 계정과 Runbook을 계속 제거할 수 있습니다. Azure 포털에서 자동화 계정의 리소스 그룹을 찾아봅습니다.  > **다른 구독으로 이동** **이동을**선택합니다.

![리소스 그룹 페이지, 다른 구독으로 이동](../media/move-account/move-resources.png)

이동할 리소스 그룹의 리소스를 선택합니다. **자동화 계정,** **Runbook**및 **로그 분석 작업 영역** 리소스를 포함해야 합니다.

이동이 완료되면 모든 작업을 완료하는 데 필요한 추가 단계가 있습니다.

## <a name="re-create-run-as-accounts"></a>계정으로 실행 다시 만들기

[실행 As 계정은](../manage-runas-account.md) Azure Active Directory에서 서비스 주체를 만들어 Azure 리소스를 사용하여 인증합니다. 구독을 변경하면 자동화 계정이 더 이상 기존 Run As 계정을 사용하지 않습니다.

새 구독의 자동화 계정으로 이동하여 계정 **설정에서** **계정으로 실행을** 선택합니다. 현재 로 실행 계정이 불완전으로 표시됩니다.

![계정이 불완전한 것으로 실행](../media/move-account/run-as-accounts.png)

각 계정으로 실행을 선택합니다. **속성** 페이지에서 **삭제를** 선택하여 계정으로 실행(Run As) 계정을 삭제합니다.

> [!NOTE]
> 계정을 만들거나 볼 수 있는 권한이 없는 경우 다음과 같은 메시지가 표시됩니다. `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` [Permissions required to configure Run As accounts](../manage-runas-account.md#permissions)

로 실행 계정이 삭제된 후 **Azure Run 에서 계정으로** **만들기를** 선택합니다. **계정으로 실행추가** 페이지에서 계정으로 실행 **을** 만들기를 선택합니다. **Azure 클래식 실행 을 계정으로**실행하여 이전 단계를 반복합니다.

## <a name="enable-solutions"></a>솔루션 사용

Run As 계정을 다시 만든 후 이동 하기 전에 제거 한 솔루션을 다시 활성화 합니다. **변경 정보 추적 및 인벤토리** 및 업데이트 **관리를**설정하려면 자동화 계정에서 각각의 기능을 선택합니다. 이동한 로그 분석 작업 영역을 선택하고 **사용 을**선택합니다.

![이동한 자동화 계정에서 솔루션 다시 활성화](../media/move-account/reenable-solutions.png)

기존 Log Analytics 작업 영역을 연결하면 솔루션과 함께 온보기되는 컴퓨터가 표시됩니다.

근무 외 솔루션 중에 **VM 시작/중지를** 설정하려면 솔루션을 다시 배포해야 합니다. **관련 리소스에서** **VM** > 시작/중지를 선택하고 솔루션 > **만들기에서** 배포를 시작하도록**설정합니다.**

솔루션 **추가** 페이지에서 로그 분석 작업 영역 및 자동화 계정을 선택합니다.

![솔루션 추가 메뉴](../media/move-account/add-solution-vm.png)

솔루션 구성에 대한 자세한 지침은 [Azure Automation의 근무 외 솔루션 중 VM 시작/중지를](../automation-solution-vm-management.md)참조하십시오.

## <a name="post-move-verification"></a>이동 후 확인

이동이 완료되면 확인해야 하는 다음 작업 목록을 확인합니다.

|기능|테스트|트러블슈팅 링크|
|---|---|---|
|Runbook|Runbook을 성공적으로 실행하고 Azure 리소스에 연결할 수 있습니다.|[런북 문제 해결](../troubleshoot/runbooks.md)
|원본 제어|소스 제어 리포지토리에서 수동 동기화를 실행할 수 있습니다.|[원본 제어 통합](../source-control-integration.md)|
|변경 추적 및 인벤토리|컴퓨터의 현재 인벤토리 데이터가 표시되는지 확인합니다.|[변경 경로 추적 문제 해결](../troubleshoot/change-tracking.md)|
|업데이트 관리|컴퓨터가 표시되고 정상인지 확인합니다.</br>테스트 소프트웨어 업데이트 배포를 실행합니다.|[업데이트 관리 문제 해결](../troubleshoot/update-management.md)|
|공유 리소스|[자격 증명,](../shared-resources/credentials.md)변수 등과 같은 모든 공유 [리소스가 표시되는지 확인합니다.](../shared-resources/variables.md)|

## <a name="next-steps"></a>다음 단계

Azure에서 리소스이동에 대해 자세히 알아보려면 [Azure의 리소스 이동](../../azure-resource-manager/management/move-support-resources.md)을 참조하십시오.
