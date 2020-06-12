---
title: Azure Automation 작업 시간 외 VM 시작/중지 문제 해결
description: 이 문서에서는 작업 시간 외 VM 시작/중지 기능을 사용하는 동안 발생하는 문제를 해결하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 53a7e113d64ea4cf7018d51a44f9488342f1470f
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715633"
---
# <a name="troubleshoot-startstop-vms-during-off-hours-issues"></a>작업 시간 외 VM 중지/시작 문제 해결

이 문서에서는 VM에 Azure Automation 작업 시간 외 VM 시작/중지 기능을 배포할 때 발생하는 문제를 해결하는 방법에 대한 정보를 제공합니다. 

## <a name="scenario-startstop-vms-during-off-hours-fails-to-properly-deploy"></a><a name="deployment-failure"></a>시나리오: 작업 시간 외 VM 시작/중지를 제대로 배포하지 못함

### <a name="issue"></a>문제

[작업 시간 외 VM 시작/중지](../automation-solution-vm-management.md)를 배포할 때 다음 오류 중 하나가 표시됩니다.

```error
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```error
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]".
```

```error
The subscription is not registered to use namespace 'Microsoft.OperationsManagement'.
```

```error
The subscription is not registered to use namespace 'Microsoft.Insights'.
```

```error
The scope '/subscriptions/000000000000-0000-0000-0000-00000000/resourcegroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView' cannot perform write operation because following scope(s) are locked: '/subscriptions/000000000000-0000-0000-0000-00000000/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView'. Please remove the lock and try again
```

```error
A parameter cannot be found that matches parameter name 'TagName'
```

```error
Start-AzureRmVm : Run Login-AzureRmAccount to login
```

### <a name="cause"></a>원인

다음 이유 중 하나로 인해 배포가 실패할 수 있습니다.

- 선택한 지역에 동일한 이름의 Automation 계정이 이미 있습니다.
- 정책이 작업 시간 외 VM 시작/중지 배포를 허용하지 않습니다.
- `Microsoft.OperationsManagement`, `Microsoft.Insights` 또는 `Microsoft.Automation` 리소스 종류가 등록되지 않았습니다.
- Log Analytics 작업 영역이 잠겼습니다.
- 오래된 버전의 AzureRM 모듈 또는 작업 시간 외 VM 시작/중지 기능이 있습니다.

### <a name="resolution"></a>해결 방법

다음과 같은 사용 가능한 해결 방법을 살펴보세요.

* Automation 계정은 다른 리소스 그룹에 있는 경우에도 Azure 지역 내에서 고유해야 합니다. 대상 지역의 기존 Automation 계정을 확인합니다.
* 기존 정책 때문에 작업 시간 외 VM 시작/중지를 배포하는 데 필요한 리소스를 사용할 수 없습니다. Azure Portal의 정책 할당으로 이동하여 이 리소스의 배포를 허용하지 않는 정책 할당이 있는지 여부를 확인합니다. 자세히 알아보려면 [RequestDisallowedByPolicy 오류](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md)를 참조하세요.
* 작업 시간 외 VM 시작/중지를 배포하려면 다음 Azure 리소스 네임스페이스에 구독을 등록해야 합니다.

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   공급자를 등록할 때 발생하는 오류에 대해 자세히 알아보려면 [리소스 공급자 등록 오류 해결](../../azure-resource-manager/templates/error-register-resource-provider.md)을 참조하세요.
* Log Analytics 작업 영역에 잠금이 설정된 경우 Azure Portal에서 해당 작업 영역으로 이동하여 리소스에 대한 잠금을 제거합니다.
* 이러한 해결 방법으로 문제가 해결되지 않으면 [기능 업데이트](../automation-solution-vm-management.md#update-the-feature)의 지침을 따라 작업 시간 외 VM 시작/중지를 다시 배포합니다.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>시나리오: 모든 VM을 시작 또는 중지하지는 못 함

### <a name="issue"></a>문제

작업 시간 외 VM 시작/중지를 구성했지만 모든 VM이 시작 또는 중지되지는 않습니다.

### <a name="cause"></a>원인

이 오류는 다음과 같은 원인 중 하나로 인해 발생할 수 있습니다.

- 일정이 올바르게 구성되지 않았습니다.
- 실행 계정이 올바르게 구성되어 있지 않을 수 있습니다.
- Runbook 실행 중에 오류가 발생한 것일 수 있습니다.
- VM이 제외되었을 수 있습니다.

### <a name="resolution"></a>해결 방법

다음과 같은 사용 가능한 해결 방법 목록을 살펴보세요.

* 작업 시간 외 VM 시작/중지 일정을 올바르게 구성했는지 확인합니다. 일정을 구성하는 방법을 알아보려면 [일정](../automation-schedules.md)을 참조하세요.

* [작업 스트림](../automation-runbook-execution.md#job-statuses)에 오류가 있는지 확인합니다. 다음 Runbook 중 하나에서 작업을 찾아봅니다.

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* [실행 계정](../manage-runas-account.md)에 시작 또는 중지하려는 VM에 대한 적절한 권한이 있는지 확인합니다. 리소스에 대한 권한을 확인하는 방법을 알아보려면 [빠른 시작: Azure Portal을 사용하여 사용자에게 할당된 역할 보기](../../role-based-access-control/check-access.md)를 참조하세요. 실행 계정에서 사용하는 서비스 주체의 애플리케이션 ID를 제공해야 합니다. Azure Portal에서 Automation 계정으로 이동하여 이 값을 검색할 수 있습니다. **계정 설정** 아래에서 **실행 계정**을 선택하고 적절한 실행 계정을 선택합니다.

* VM을 명시적으로 제외한 경우에는 시작 또는 중지되지 않을 수 있습니다. 제외된 VM은 기능이 배포되는 Automation 계정의 `External_ExcludeVMNames` 변수에 설정됩니다. 다음 예제에서는 PowerShell 사용하여 해당 값을 쿼리하는 방법을 보여 줍니다.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>시나리오: VM 중 일부가 시작 또는 중지되지 않음

### <a name="issue"></a>문제

작업 시간 외 VM 시작/중지를 구성했지만 구성된 VM 중 일부가 시작 또는 중지되지 않습니다.

### <a name="cause"></a>원인

이 오류는 다음과 같은 원인 중 하나로 인해 발생할 수 있습니다.

- 시퀀스 시나리오에서는 태그가 누락되거나 올바르지 않을 수 있습니다.
- VM을 제외한 것일 수 있습니다.
- 실행 계정에 VM에 대한 충분한 권한이 없을 수 있습니다.
- VM에 시작 또는 중지를 방해하는 문제가 있을 수 있습니다.

### <a name="resolution"></a>해결 방법

다음과 같은 사용 가능한 해결 방법 목록을 살펴보세요.

* 작업 시간 외 VM 시작/중지의 [시퀀스 시나리오](../automation-solution-vm-management.md)를 사용하는 경우 시작 또는 중지하려는 각 VM에 적절한 태그가 있는지 확인해야 합니다. 시작하려는 VM에는 `sequencestart` 태그가 있는지, 중지하려는 VM에는 `sequencestop` 태그가 있는지 확인합니다. 두 태그 값은 모두 양의 정수여야 합니다. 다음 예제와 비슷한 쿼리를 사용하여 모든 VM과 태그 및 해당 값을 검색할 수 있습니다.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* VM을 명시적으로 제외한 경우에는 시작 또는 중지되지 않을 수 있습니다. 제외된 VM은 기능이 배포되는 Automation 계정의 `External_ExcludeVMNames` 변수에 설정됩니다. 다음 예제에서는 PowerShell 사용하여 해당 값을 쿼리하는 방법을 보여 줍니다.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* VM을 시작 및 중지하려면 Automation 계정의 실행 계정에 VM에 대한 적절한 사용 권한이 있어야 합니다. 리소스에 대한 권한을 확인하는 방법을 알아보려면 [빠른 시작: Azure Portal을 사용하여 사용자에게 할당된 역할 보기](../../role-based-access-control/check-access.md)를 참조하세요. 실행 계정에서 사용하는 서비스 주체의 애플리케이션 ID를 제공해야 합니다. Azure Portal에서 Automation 계정으로 이동하여 이 값을 검색할 수 있습니다. **계정 설정** 아래에서 **실행 계정**을 선택하고 적절한 실행 계정을 선택합니다.
* VM을 시작 또는 할당 취소하는 데 문제가 발생하는 경우 VM 자체에 문제가 있을 수 있습니다. 예를 들어 VM을 종료하려고 할 때 적용되는 업데이트, 중단된 서비스 등이 있습니다. VM 리소스로 이동하고 **활동 로그**를 확인하여 로그에 오류가 있는지 검토합니다. 이벤트 로그에 오류가 있는지 확인하기 위해 VM에 로그인을 시도할 수도 있습니다. VM 문제 해결에 대해 자세히 알아보려면 [Azure 가상 머신 문제 해결](../../virtual-machines/troubleshooting/index.yml)을 참조하세요.
* [작업 스트림](../automation-runbook-execution.md#job-statuses)에 오류가 있는지 확인합니다. 포털에서 Automation 계정으로 이동한 후 **프로세스 자동화**에서 **작업**을 선택합니다.

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>시나리오: 내 사용자 지정 Runbook이 VM을 시작 또는 중지하지 못함

### <a name="issue"></a>문제

사용자 지정 Runbook을 작성하거나, PowerShell 갤러리에서 다운로드했는데 제대로 작동하지 않습니다.

### <a name="cause"></a>원인

이 오류의 원인은 여러 가지가 있을 수 있습니다. Azure Portal에서 Automation 계정으로 이동한 후 **프로세스 자동화**에서 **작업**을 선택합니다. **작업** 페이지에서 Runbook의 작업을 찾아 작업 실패를 확인합니다.

### <a name="resolution"></a>해결 방법

다음을 수행하는 것이 좋습니다.

* [작업 시간 외 VM 시작/중지](../automation-solution-vm-management.md)를 사용하여 Azure Automation의 VM을 시작하고 중지합니다. 
* Microsoft는 사용자 지정 Runbook을 지원하지 않습니다. [Runbook 문제 해결](runbooks.md)에서 사용자 지정 Runbook에 대한 해결 방법을 찾을 수 있습니다. [작업 스트림](../automation-runbook-execution.md#job-statuses)에 오류가 있는지 확인합니다. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>시나리오: VM이 올바른 순서로 시작 또는 중지되지 않음

### <a name="issue"></a>문제

기능이 설정된 VM이 올바른 순서로 시작 또는 중지되지 않습니다.

### <a name="cause"></a>원인

이 문제는 VM의 잘못된 태그 지정 때문에 발생합니다.

### <a name="resolution"></a>해결 방법

기능이 올바르게 설정되었는지 확인하려면 다음 단계를 수행합니다.

1. 시작 또는 중지하려는 모든 VM에 상황에 따라 `sequencestart` 또는 `sequencestop` 태그가 있는지 확인합니다. 이러한 태그 값으로 양의 정수가 필요합니다. VM은 이 값의 오름차순으로 처리됩니다.
1. 시작 또는 중지할 VM의 리소스 그룹이 상황에 따라 `External_Start_ResourceGroupNames` 또는 `External_Stop_ResourceGroupNames` 변수에 있는지 확인합니다.
1. `WHATIF` 매개 변수를 True로 설정하여 **SequencedStartStop_Parent** Runbook을 실행하는 방식으로 변경 내용을 미리 보면서 테스트합니다.

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>시나리오: 작업 시간 외 VM 시작/중지 작업이 실패하고 403 사용 권한 없음 오류가 발생함

### <a name="issue"></a>문제

작업 시간 외 VM 시작/중지 Runbook에 대한 `403 forbidden` 오류와 함께 작업이 실패합니다.

### <a name="cause"></a>원인

이 문제는 잘못 구성되었거나 만료된 실행 계정으로 인해 발생할 수 있습니다. 또한 실행 계정에 VM 리소스에 대한 적절한 권한이 없기 때문일 수 있습니다.

### <a name="resolution"></a>해결 방법

실행 계정이 제대로 구성되어 있는지 확인하려면 Azure Portal에서 Automation 계정으로 이동한 후 **계정 설정** 아래에서 **실행 계정**을 선택합니다. 실행 계정이 잘못 구성되었거나 만료된 경우 상태에 조건이 표시됩니다.

실행 계정이 잘못 구성된 경우 실행 계정을 삭제한 후 다시 만드세요. 자세한 내용은 [Azure Automation 실행 계정 관리](../manage-runas-account.md)를 참조하세요.

실행 계정에 대한 인증서가 만료되면 [자체 서명된 인증서 갱신](../manage-runas-account.md#cert-renewal)의 단계에 따라 인증서를 갱신합니다.

누락된 권한이 있는 경우 [빠른 시작: Azure Portal을 사용하여 사용자에게 할당된 역할 보기](../../role-based-access-control/check-access.md)를 참조하세요. 실행 계정에서 사용하는 서비스 주체의 애플리케이션 ID를 제공해야 합니다. Azure Portal에서 Automation 계정으로 이동하여 이 값을 검색할 수 있습니다. **계정 설정** 아래에서 **실행 계정**을 선택하고 적절한 실행 계정을 선택합니다.

## <a name="scenario-my-problem-isnt-listed-here"></a><a name="other"></a>시나리오: 내 문제가 여기에 나열되지 않음

### <a name="issue"></a>문제

작업 시간 외 VM 시작/중지를 사용할 때 이 페이지에 나열되지 않은 문제 또는 예기치 않은 결과가 발생합니다.

### <a name="cause"></a>원인

이전 및 오래된 버전의 기능을 사용해서 오류가 발생하는 경우가 많습니다.

> [!NOTE]
> 작업 시간 외 VM 시작/중지 기능을 VM에 배포할 때 Automation 계정으로 가져온 Azure 모듈을 사용하여 테스트했습니다. 이 기능은 현재 최신 버전의 Azure 모듈에서 작동하지 않습니다. 이 제한은 작업 시간 외 VM 시작/중지를 실행하는 데 사용하는 Automation 계정에만 영향을 줍니다. [Azure PowerShell 모듈 업데이트](../automation-update-azure-modules.md)에 설명된 대로 다른 Automation 계정에서 최신 버전의 Azure 모듈을 계속 사용할 수 있습니다.

### <a name="resolution"></a>해결 방법

여러 오류를 해결하려면 [작업 시간 외 VM 시작/중지를 제거하고 업데이트합니다](../automation-solution-vm-management.md#update-the-feature). 또한 [작업 스트림](../automation-runbook-execution.md#job-statuses)에 오류가 있는지 확인할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

여기에 문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 통해 추가 지원을 받으세요.

* [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가로부터 답변을 얻습니다.
* 고객 환경을 개선하기 위한 공식 Microsoft Azure 계정인 [@AzureSupport](https://twitter.com/azuresupport)와 연결합니다. Azure 지원은 Azure 커뮤니티를 답변, 지원 및 전문가에게 연결합니다.
* Azure 지원 인시던트 제출 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동하여 **지원 받기**를 선택합니다.