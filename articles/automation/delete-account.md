---
title: Azure Automation 계정 삭제
description: 이 문서에서는 다양한 구성 시나리오에서 Automation 계정을 삭제하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
ms.date: 06/04/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 00401c7afd4fff1fcea7c5097d31ccf440e09049
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111572505"
---
# <a name="how-to-delete-your-azure-automation-account"></a>Azure Automation 계정 삭제 방법

IT 또는 비즈니스 프로세스를 자동화하는 데 도움이 되도록 Azure Automation 계정을 사용으로 설정하거나 업데이트 관리와 같은 Azure 및 비Azure 머신의 작업 관리를 지원하기 위해 다른 기능을 사용으로 설정한 다음, Automation 계정 사용을 중지할 수 있습니다. Azure Monitor Log Analytics 작업 영역과의 통합을 사용하는 기능을 사용하도록 설정한 경우, 이 작업을 완료하는 데 필요한 추가 단계가 있습니다.

지원되는 배포 모델에 따라 다음 방법 중 하나를 사용하여 Automation 계정을 제거할 수 있습니다.

* Automation 계정이 포함된 리소스 그룹을 삭제합니다.
* 다음과 같은 경우 Automation 계정 및 연결된 Azure Monitor Log Analytics 작업 영역을 포함하는 리소스 그룹을 삭제합니다.

    * 계정 및 작업 영역은 업데이트 관리, 변경 내용 추적, 인벤토리, 작업 시간 외 VM 시작/중지를 지원하는 데 전적으로 사용합니다.
    * 이 계정은 프로세스 자동화 전용이며 Runbook 작업 상태와 작업 스트림을 보내기 위해 작업 영역과 통합됩니다.

* Automation 계정에서 Log Analytics 작업 영역의 연결을 해제하고 Automation 계정을 삭제합니다.
* 연결된 작업 영역에서 기능을 삭제하고 작업 영역에서 계정 연결을 해제한 다음, Automation 계정을 삭제합니다.

이 문서에서는 Azure Portal에서 Azure PowerShell, Azure CLI 또는 REST API를 사용해 Automation 계정을 완전히 제거하는 방법을 설명합니다.

## <a name="prerequisite"></a>필수 요소
중요한 리소스를 실수로 삭제하거나 수정하지 못하도록 하는 [Resource Manager 잠금](../azure-resource-manager/management/lock-resources.md)이 구독, 리소스 그룹 또는 리소스에서 적용되지 않는지 확인합니다. 작업 시간 외 VM 시작/중지 솔루션을 배포할 경우 Automation 계정의 여러 종속 리소스(특히 해당 Runbook 및 변수)에 대해 잠금 수준을 **CanNotDelete** 로 설정합니다. Automation 계정을 삭제하기 전에 잠금을 제거합니다.

> [!NOTE]
> `The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions`와 유사한 오류 메시지가 표시되면 Automation 계정은 업데이트 관리 및/또는 변경 내용 추적 및 인벤토리 기능을 사용하도록 설정된 Log Analytics 작업 영역에 연결됩니다. 자세한 내용은 아래의 [공유 기능 Automation 계정 삭제](#delete-a-shared-capability-automation-account)를 참조하세요.

## <a name="delete-the-dedicated-resource-group"></a>전용 리소스 그룹 삭제

Automation 계정을 삭제하고, 해당 계정 전용의 리소스 그룹과 같은 그룹에서 만든 계정에 연결된 경우 Log Analytics 작업 영역도 삭제하려면 [Azure Resource Manager 리소스 그룹 및 리소스 삭제](../azure-resource-manager/management/delete-resource-group.md) 문서에 간략히 설명된 단계를 따릅니다.

## <a name="delete-a-standalone-automation-account"></a>독립 실행형 Automation 계정 삭제

Automation 계정이 Log Analytics 작업 영역에 연결되지 않은 경우 다음 단계를 수행하여 삭제합니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

1. [https://portal.azure.com](https://portal.azure.com)에서 Azure에 로그인합니다.

2. Azure Portal에서 **Automation 계정** 으로 이동합니다.

3. Automation 계정을 열고 메뉴에서 **삭제** 를 선택합니다.

정보가 확인되고 계정이 삭제되는 동안 메뉴에서 **알림** 을 선택하여 진행률을 추적할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

이 명령은 유효성 검사 메시지를 표시하지 않고 Automation 계정을 제거합니다.

```powershell
Remove-AzAutomationAccount -Name "automationAccountName" -Force -ResourceGroupName "resourceGroupName"
```

---

## <a name="delete-a-standalone-automation-account-linked-to-workspace"></a>작업 영역에 연결된 독립 실행형 Automation 계정 삭제

Automation 계정이 Log Analytics 작업 영역에 연결되어 작업 스트림 및 작업 로그를 수집하는 경우 다음 단계를 수행하여 계정을 삭제합니다.

Automation 계정에서 Log Analytics 작업 영역 연결을 해제하는 두 가지 옵션이 있습니다. Automation 계정 또는 연결된 작업 영역에서 이 프로세스를 수행할 수 있습니다.

Automation 계정에서 연결을 끊으려면 다음 단계를 수행합니다.

1. Azure Portal에서 **Automation 계정** 으로 이동합니다.

2. Automation 계정을 열고 왼쪽의 **관련 리소스** 아래에서 **연결된 작업 영역** 을 선택합니다.

3. **작업 영역 연결 해제** 페이지에서 **작업 영역 연결 해제** 를 선택하고 프롬프트에 응답합니다.

   ![작업 영역 연결 해제 페이지](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Log Analytics 작업 영역에서 연결을 해제하는 동안 메뉴의 **알림** 에서 진행 상황을 추적할 수 있습니다.

작업 영역에서 연결을 해제하려면 다음 단계를 수행합니다.

1. Azure Portal에서 **Log Analytics 작업 영역** 으로 이동합니다.

2. 작업 영역의 **관련 리소스** 아래에서 **Automation 계정** 을 선택합니다.

3. Automation 계정 페이지에서 **계정 연결 해제** 를 선택하고 프롬프트에 응답합니다.

Automation 계정의 연결을 해제하는 동안 메뉴의 **알림** 에서 진행률을 추적할 수 있습니다.

작업 영역에서 Automation 계정의 연결을 해제한 다음, [독립 실행형 Automation 계정](#delete-a-standalone-automation-account) 섹션의 단계를 수행하여 계정을 삭제합니다.

## <a name="delete-a-shared-capability-automation-account"></a>공유 기능 Automation 계정 삭제

업데이트 관리, 변경 내용 추적 및 인벤토리 및/또는 작업 시간 외 VM 시작/중지를 지원하기 위해 Log Analytics 작업 영역에 연결된 Automation 계정을 삭제하려면 다음 단계를 수행합니다.

### <a name="step-1-delete-the-solution-from-the-linked-workspace"></a>1단계. 연결된 작업 영역에서 솔루션 삭제

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

1. [https://portal.azure.com](https://portal.azure.com)에서 Azure에 로그인합니다.

2. Automation 계정으로 이동하고 **관련 리소스** 에서 **연결된 작업 영역** 을 선택합니다.

3. **작업 영역으로 이동** 을 선택합니다.

4. **일반** 아래에서 **솔루션** 을 선택합니다.

5. 솔루션 페이지에서 계정에 배포된 기능에 따라 다음 중 하나를 선택합니다.

    * 작업 시간 외 VM 시작/중지의 경우 **Start-Stop-VM[workspace name]** 을 선택합니다.
    * 업데이트 관리의 경우 **Updates(workspace name)** 를 선택합니다.
    * 변경 내용 추적 및 인벤토리의 경우 **ChangeTracking(workspace name)** 을 선택합니다.

6. **솔루션** 페이지의 메뉴에서 **삭제** 를 선택합니다. 위에 나열된 기능 중 둘 이상이 Automation 계정 및 연결된 작업 영역에 배포된 경우 계속하기 전에 각 기능을 선택하고 삭제해야 합니다.

7. 정보가 확인되고 기능이 삭제되는 동안 메뉴에서 **알림** 을 선택하여 진행 상황을 추적할 수 있습니다. 제거 프로세스가 끝나면 솔루션 페이지가 표시됩니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell을 사용하여 설치된 솔루션을 제거하려면 [Remove-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution) cmdlet을 사용합니다.

```powershell
Remove-AzMonitorLogAnalyticsSolution -ResourceGroupName "resourceGroupName" -Name "solutionName"
```

---

### <a name="step-2-unlink-workspace-from-automation-account"></a>2단계. Automation 계정에서 작업 영역 연결 해제

Automation 계정에서 Log Analytics 작업 영역 연결을 해제하는 두 가지 옵션이 있습니다. Automation 계정 또는 연결된 작업 영역에서 이 프로세스를 수행할 수 있습니다.

Automation 계정에서 연결을 끊으려면 다음 단계를 수행합니다.

1. Azure Portal에서 **Automation 계정** 으로 이동합니다.

2. Automation 계정을 열고 왼쪽의 **관련 리소스** 아래에서 **연결된 작업 영역** 을 선택합니다.

3. **작업 영역 연결 해제** 페이지에서 **작업 영역 연결 해제** 를 선택하고 프롬프트에 응답합니다.

   ![작업 영역 연결 해제 페이지](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Log Analytics 작업 영역에서 연결을 해제하는 동안 메뉴의 **알림** 에서 진행 상황을 추적할 수 있습니다.

작업 영역에서 연결을 해제하려면 다음 단계를 수행합니다.

1. Azure Portal에서 **Log Analytics 작업 영역** 으로 이동합니다.

2. 작업 영역의 **관련 리소스** 아래에서 **Automation 계정** 을 선택합니다.

3. Automation 계정 페이지에서 **계정 연결 해제** 를 선택하고 프롬프트에 응답합니다.

Automation 계정의 연결을 해제하는 동안 메뉴의 **알림** 에서 진행률을 추적할 수 있습니다.

### <a name="step-3-delete-automation-account"></a>3단계: Automation 계정 삭제

작업 영역에서 Automation 계정의 연결을 해제한 다음, [독립 실행형 Automation 계정](#delete-a-standalone-automation-account) 섹션의 단계를 수행하여 계정을 삭제합니다.

## <a name="next-steps"></a>다음 단계

Azure Portal에서 Automation 계정을 만들려면 [독립 실행형 Azure Automation 계정 만들기](automation-create-standalone-account.md)를 참조하세요. 템플릿을 사용하여 계정을 만들려면 [Azure Resource Manager 템플릿을 사용하여 Automation 계정 만들기](quickstart-create-automation-account-template.md)를 참조하세요.
