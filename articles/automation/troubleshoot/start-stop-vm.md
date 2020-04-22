---
title: 외근무 솔루션 중 VM 시작/중지 문제 해결
description: 이 문서에서는 VM 시작/중지 솔루션 문제 해결에 대한 정보를 제공합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 003c2c5a2c09957e7a3a4ac0a26b87a9ac43dace
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679158"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>작업 시간 외 VM 시작/중지 문제 해결

이 문서에서는 업무 외 솔루션 중에 VM 시작/중지 작업 중에 발생하는 문제 해결에 대한 정보를 제공합니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 모듈을 업데이트하는 방법을](../automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="scenario-the-startstop-vms-during-off-hours-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>시나리오: 영업 시간 동안 VM 시작/중지 솔루션이 제대로 배포되지 못합니다.

### <a name="issue"></a>문제

[작업 시간 외 VM 시작/중지 솔루션](../automation-solution-vm-management.md)을 배포할 때 다음 오류 중 하나가 표시됩니다.

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

다음과 같은 이유 중 하나로 인해 배포가 실패할 수 있습니다.

1. 선택한 리전에서 이름이 같은 자동화 계정이 이미 있습니다.
2. 정책이 영업외 솔루션 동안 VM 시작/중지 배포를 허용하지 않습니다.
3. 의 `Microsoft.OperationsManagement` `Microsoft.Insights`에 `Microsoft.Automation` 대해 또는 리소스 유형이 등록되지 않았습니다.
4. 로그 분석 작업 영역이 잠겨 있습니다.
5. 오래된 버전의 AzureRM 모듈 또는 영업외 솔루션 중에 VM 시작/중지가 있습니다.

### <a name="resolution"></a>해결 방법

문제에 대한 잠재적인 해결 방법은 다음과 같은 수정 사항을 검토합니다.

* Automation 계정은 다른 리소스 그룹에 있는 경우에도 Azure 지역 내에서 고유해야 합니다. 대상 지역의 기존 자동화 계정을 확인합니다.
* 기존 정책은 배포할 오프시간 솔루션 동안 VM 시작/중지에 필요한 리소스를 방지합니다. Azure Portal의 정책 할당으로 이동하여 이 리소스의 배포를 허용하지 않는 정책 할당이 있는지 여부를 확인합니다. 이에 대한 자세한 내용은 [RequestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md)를 참조하세요.
* 시작/중지 VM 솔루션을 배포하려면 구독을 다음 Azure 리소스 네임스페이스에 등록해야 합니다.

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   공급자를 등록할 때 오류에 대해 자세히 알아보려면 [리소스 공급자 등록에 대한 오류 해결을](../../azure-resource-manager/templates/error-register-resource-provider.md) 참조하십시오.
* Log Analytics 작업 영역에 잠금이 설정된 경우 Azure Portal에서 해당 작업 영역으로 이동하여 리소스에 대한 잠금을 제거합니다.
* 위의 해결 방법이 문제를 해결하지 못하면 [솔루션 업데이트](../automation-solution-vm-management.md#update-the-solution) 의 지침에 따라 시작/중지 솔루션을 다시 배포합니다.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>시나리오: 모든 VM이 시작 또는 중지되지 않습니다.

### <a name="issue"></a>문제

영업외 솔루션 중에 VM 시작/중지를 구성했지만 모든 VM이 시작되거나 중지되지는 않습니다.

### <a name="cause"></a>원인

이 오류는 다음과 같은 원인 중 하나로 인해 발생할 수 있습니다.

1. 일정이 올바르게 구성되지 않았습니다.
2. 로 실행 계정이 올바르게 구성되지 않았을 수 있습니다.
3. Runbook에 오류가 발생할 수 있습니다.
4. VM이 제외되었을 수 있습니다.

### <a name="resolution"></a>해결 방법

문제에 대한 잠재적인 해결 방법은 다음 목록을 검토하십시오.

* 영업외 솔루션 중에 VM 시작/중지 일정을 올바르게 구성했는지 확인합니다. 일정을 구성하는 방법을 알아보려면 [일정](../automation-schedules.md) 문서를 참조하세요.

* 작업 [스트림을](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) 확인하여 오류를 찾습니다. 다음 Runbook 중 하나에서 작업을 찾습니다.

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* [실행 As 계정에](../manage-runas-account.md) 시작 또는 중지하려는 VM에 대한 적절한 사용 권한이 있는지 확인합니다. 리소스의 사용 권한을 확인하는 방법을 알아보려면 [Azure 포털을 사용하여 사용자에게 할당된 역할 보기 빠른 시작을](../../role-based-access-control/check-access.md)참조하십시오. Run As 계정에서 사용하는 서비스 주체에 대한 응용 프로그램 ID를 제공해야 합니다. Azure 포털의 자동화 계정으로 이동하여 **계정 설정에서** **계정으로 실행을** 선택하고 해당 계정으로 실행을 클릭하여 이 값을 검색할 수 있습니다.

* VM을 명시적으로 제외한 경우에는 시작 또는 중지되지 않을 수 있습니다. 제외된 VM은 `External_ExcludeVMNames` 솔루션이 배포되는 자동화 계정의 변수에 설정됩니다. 다음 예제에서는 PowerShell을 통해 해당 값을 쿼리하는 방법을 보여 주며 있습니다.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>시나리오: 일부 VM이 시작 또는 중지되지 않습니다.

### <a name="issue"></a>문제

영업외 솔루션 중에 VM 시작/중지를 구성했지만 구성된 일부 VM이 시작되거나 중지되지 는 않습니다.

### <a name="cause"></a>원인

이 오류는 다음과 같은 원인 중 하나로 인해 발생할 수 있습니다.

1. 시퀀스 시나리오에서 태그가 없거나 올바르지 않을 수 있습니다.
2. VM은 제외될 수 있습니다.
3. 실행 As 계정에 VM에 대한 사용 권한이 충분하지 않을 수 있습니다.
4. VM에서 VM을 시작하거나 중지하지 못하게 하는 문제가 있을 수 있습니다.

### <a name="resolution"></a>해결 방법

다음 목록에서 문제의 잠재적인 해결 방법이나 검색 위치를 확인합니다.

* 영업외 솔루션 중에 VM 시작/중지의 [시퀀스 시나리오를](../automation-solution-vm-management.md) 사용하는 경우 시작하거나 중지하려는 각 VM에 적절한 태그가 있는지 확인해야 합니다. 시작하려는 VM에는 `sequencestart` 태그가 있는지, 중지하려는 VM에는 `sequencestop` 태그가 있는지 확인합니다. 두 태그 값은 모두 양의 정수여야 합니다. 다음 예제와 비슷한 쿼리를 사용하여 모든 VM과 태그 및 해당 값을 검색할 수 있습니다.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* VM이 명시적으로 제외되는 경우 VM이 시작되거나 중지되지 않을 수 있습니다. 제외된 VM은 `External_ExcludeVMNames` 솔루션이 배포되는 자동화 계정의 변수에 설정됩니다. 다음 예제에서는 PowerShell을 통해 해당 값을 쿼리하는 방법을 보여 주며 있습니다.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* VM을 시작하고 중지하려면 자동화 계정에 대한 실행 계정의 VM에 대한 적절한 권한이 있어야 합니다. 리소스의 사용 권한을 확인하는 방법을 알아보려면 [Azure 포털을 사용하여 사용자에게 할당된 역할 보기 빠른 시작을](../../role-based-access-control/check-access.md)참조하십시오. Run As 계정에서 사용하는 서비스 주체에 대한 응용 프로그램 ID를 제공해야 합니다. Azure 포털의 자동화 계정으로 이동하여 **계정 설정에서** **계정으로 실행을** 선택하고 해당 계정으로 실행을 클릭하여 이 값을 검색할 수 있습니다.

* VM이 시작 또는 할당 지정에 문제가 있는 경우 VM 자체에 문제가 있을 수 있습니다. 예를 들어 VM이 종료하려고 할 때 업데이트가 적용되고 서비스가 중단되는 등의 경우 VM 리소스로 이동한 후 **활동 로그**를 확인하여 로그에 오류가 있는지 검토합니다. VM에 로그인하여 이벤트 로그에 오류가 있는지 확인할 수도 있습니다. VM 문제 해결에 대한 자세한 내용은 [Azure 가상 컴퓨터 문제 해결을](../../virtual-machines/troubleshooting/index.yml) 참조하세요.

* 작업 [스트림을](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) 확인하여 오류를 찾습니다. 포털에서 자동화 계정으로 이동하여 프로세스 **자동화**에서 **작업을** 선택합니다.

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>시나리오: 사용자 지정 Runbook이 VM을 시작하거나 중지하지 못합니다.

### <a name="issue"></a>문제

사용자 지정 Runbook을 작성했거나, PowerShell 갤러리에서 다운로드했으나 제대로 작동하지 않습니다.

### <a name="cause"></a>원인

오류의 원인은 여러 가지가 있을 수 있습니다. Azure 포털의 자동화 계정으로 이동하여 프로세스 자동화 에서 **작업을** **선택합니다.** 작업 페이지에서 Runbook의 작업을 찾아 작업 실패를 확인합니다.

### <a name="resolution"></a>해결 방법

다음을 권장합니다.

* [외무 성 솔루션 동안 VM 시작/중지를](../automation-solution-vm-management.md) 사용하여 Azure 자동화에서 VM을 시작하고 중지합니다. 이 솔루션은 Microsoft에서 작성한 것입니다. 

* Microsoft는 사용자 지정 런북을 지원하지 않습니다. [Runbook 문제 해결에서](runbooks.md)사용자 지정 Runbook에 대한 솔루션을 찾을 수 있습니다. 작업 [스트림을](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) 확인하여 오류를 찾습니다. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>시나리오: VM이 올바른 순서로 시작하거나 중지되지 않습니다.

### <a name="issue"></a>문제

솔루션에서 구성한 VM이 올바른 순서로 시작 또는 중지되지 않습니다.

### <a name="cause"></a>원인

이 문제는 VM에 잘못 태그지정으로 인해 발생합니다.

### <a name="resolution"></a>해결 방법

다음 단계를 수행하여 솔루션이 올바르게 구성되었는지 확인합니다.

1. 시작 또는 중지하려는 모든 VM에 상황에 따라 `sequencestart` 또는 `sequencestop` 태그가 있는지 확인합니다. 이러한 태그 값으로 양의 정수가 필요합니다. VM은 이 값의 오름차순으로 처리됩니다.
2. 시작 또는 중지할 VM의 리소스 그룹이 상황에 따라 `External_Start_ResourceGroupNames` 또는 `External_Stop_ResourceGroupNames` 변수에 있는지 확인합니다.
3. 변경 내용을 미리 보기 위해 `SequencedStartStop_Parent` True로 `WHATIF` 설정된 매개 변수로 Runbook을 실행하여 변경 내용을 테스트합니다.
4. 솔루션 사용에 대해 자세히 알아보려면 [VM을 순서대로 시작하고 중지합니다.](../automation-solution-vm-management.md)

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>시나리오: 403 개의 금지 된 오류로 작업 실패 하는 오프 시간 동안 VM 시작/중지

### <a name="issue"></a>문제

영업외 솔루션 런북 `403 forbidden` 중에 VM 시작/중지에 대한 오류로 실패한 작업을 찾을 수 있습니다.

### <a name="cause"></a>원인

이 문제는 잘못 구성되거나 만료된 Run As 계정으로 인해 발생할 수 있습니다. 또한 실행 As 계정에서 VM 리소스에 대한 권한이 부족하기 때문일 수도 있습니다.

### <a name="resolution"></a>해결 방법

로 실행 계정이 제대로 구성되어 있는지 확인하려면 Azure 포털의 자동화 계정으로 이동하여 **계정 설정에서** **계정으로 실행을** 선택합니다. 로 실행 계정이 잘못 구성되거나 만료된 경우 상태가 표시됩니다.

로 실행 계정이 잘못 구성된 경우 계정을 삭제하고 다시 만들어야 합니다. [Azure 자동화 실행 을 계정으로 관리 를](../manage-runas-account.md)참조하십시오.

실행 As 계정에 대한 인증서가 만료된 경우 [자체 서명된 인증서 갱신](../manage-runas-account.md#cert-renewal) 단계를 참조하여 인증서를 갱신합니다.

누락된 사용 권한이 있는 경우 [Azure 포털을 사용하여 사용자에게 할당된 역할 보기 빠른 시작을](../../role-based-access-control/check-access.md)참조하십시오. Run As 계정에서 사용하는 서비스 주체에 대한 응용 프로그램 ID를 제공해야 합니다. Azure 포털의 자동화 계정으로 이동하여 **계정 설정에서** **계정으로 실행을** 선택하고 해당 계정으로 실행을 클릭하여 이 값을 검색할 수 있습니다.

## <a name="scenario-my-problem-isnt-listed-above"></a><a name="other"></a>시나리오: 내 문제가 위에 나열되지 않습니다.

### <a name="issue"></a>문제

이 페이지에 나열되지 않은 오프 시간 솔루션 중에 시작/중지 VM을 사용할 때 문제 또는 예기치 않은 결과가 발생합니다.

### <a name="cause"></a>원인

이전 및 오래된 버전의 솔루션을 사용하면 많은 시간 오류가 발생할 수 있습니다.

> [!NOTE]
> 운영 외 시간 동안 VM 시작/중지 솔루션은 솔루션을 배포할 때 자동화 계정으로 가져온 Azure 모듈을 사용 하 고 테스트 되었습니다. 이 솔루션은 현재 Azure 모듈의 최신 버전에서 작동하지 않습니다. 이는 영업외 솔루션 동안 VM 시작/중지를 실행하는 데 사용하는 자동화 계정에만 영향을 줍니다. Azure 자동화에서 Azure PowerShell 모듈을 업데이트하는 방법에 설명된 대로 다른 자동화 계정에서 최신 버전의 [Azure 모듈을](../automation-update-azure-modules.md) 계속 사용할 수 있습니다.

### <a name="resolution"></a>해결 방법

많은 오류를 해결하려면 [영업 시간 외 솔루션 중에 VM 시작/중지를](../automation-solution-vm-management.md#update-the-solution)제거하고 업데이트하는 것이 좋습니다. 또한 [작업 스트림을](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) 확인하여 오류를 찾을 수 있습니다. 

## <a name="next-steps"></a>다음 단계

위에 문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 통해 추가 지원을 시도해 보십시오.

* Azure 포럼을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/forums/)
* [@AzureSupport](https://twitter.com/azuresupport)Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.
* Azure 지원 인시던트 제출 [Azure 지원 사이트로](https://azure.microsoft.com/support/options/) 이동하여 **지원 받기를**선택합니다.