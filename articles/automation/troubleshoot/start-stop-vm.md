---
title: 업무 시간 외 Vm 시작/중지 솔루션 문제 해결
description: 이 문서에서는 업무 시간 외 VM 시작/중지 솔루션 문제를 해결 하는 방법에 대 한 정보를 제공 합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 292c86107ff6d57aa020af746674ed40a7ec39d0
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836600"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>작업 시간 외 VM 시작/중지 문제 해결

이 문서에서는 업무 시간 외 Vm 시작/중지 솔루션을 Azure Automation 사용 하 여 작업할 때 발생 하는 문제 해결에 대 한 정보를 제공 합니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대 한 Az module 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조 하세요. Azure Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트 하는 방법을](../automation-update-azure-modules.md)사용 하 여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="scenario-the-startstop-vms-during-off-hours-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>시나리오: 업무 시간 외 Vm 시작/중지 솔루션을 제대로 배포 하지 못함

### <a name="issue"></a>문제

[끄기 시간 동안 Vm 시작/중지 솔루션](../automation-solution-vm-management.md)을 배포 하는 경우 다음 오류 중 하나가 표시 됩니다.

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

배포는 다음과 같은 이유 중 하나로 인해 실패할 수 있습니다.

- 선택한 지역에 동일한 이름의 Automation 계정이 이미 있습니다.
- 정책은 시간 외 Vm 시작/중지 솔루션의 배포를 허용 하지 않습니다.
- `Microsoft.OperationsManagement`, `Microsoft.Insights`또는 `Microsoft.Automation` 리소스 형식이 등록 되지 않았습니다.
- Log Analytics 작업 영역이 잠겼습니다.
- 오래 된 버전의 AzureRM 모듈 또는 시간 외 Vm 시작/중지 솔루션이 있습니다.

### <a name="resolution"></a>해결 방법

문제에 대 한 잠재적 해결 방법을 확인 하려면 다음 수정 사항을 검토 하세요.

* Automation 계정은 다른 리소스 그룹에 있는 경우에도 Azure 지역 내에서 고유 해야 합니다. 대상 지역에서 기존 Automation 계정을 확인 합니다.
* 기존 정책은 오프 시간에 Vm 시작/중지 솔루션에 필요한 리소스를 배포 하지 못하도록 방지 합니다. Azure Portal에서 정책 할당으로 이동 하 고이 리소스의 배포를 허용 하지 않는 정책 할당이 있는지 확인 합니다. 자세히 알아보려면 [RequestDisallowedByPolicy 오류](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md)를 참조 하세요.
* Vm 시작/중지 솔루션을 배포 하려면 다음과 같은 Azure 리소스 네임 스페이스에 구독을 등록 해야 합니다.

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   공급자를 등록할 때 발생 하는 오류에 대 한 자세한 내용은 [리소스 공급자 등록에 대 한 오류 해결](../../azure-resource-manager/templates/error-register-resource-provider.md)을 참조 하세요.
* Log Analytics 작업 영역에 잠금이 설정된 경우 Azure Portal에서 해당 작업 영역으로 이동하여 리소스에 대한 잠금을 제거합니다.
* 이러한 해결 방법으로 문제가 해결 되지 않으면 [솔루션 업데이트](../automation-solution-vm-management.md#update-the-solution) 의 지침에 따라 오프 시간에 vm 시작/중지 솔루션을 다시 배포 합니다.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>시나리오: 모든 Vm을 시작 또는 중지 하지 못함

### <a name="issue"></a>문제

끄기 시간 동안 Vm 시작/중지 솔루션을 구성 했지만 모든 Vm을 시작 하거나 중지 하지는 않습니다.

### <a name="cause"></a>원인

이 오류는 다음과 같은 원인 중 하나로 인해 발생할 수 있습니다.

- 일정이 올바르게 구성 되지 않았습니다.
- 실행 계정이 올바르게 구성 되지 않았을 수 있습니다.
- Runbook에 오류가 발생 했을 수 있습니다.
- Vm이 제외 되었을 수 있습니다.

### <a name="resolution"></a>해결 방법

문제에 대 한 잠재적 해결 방법을 보려면 다음 목록을 검토 하세요.

* 업무 시간 외 Vm 시작/중지 솔루션에 대 한 일정을 올바르게 구성 했는지 확인 합니다. 일정을 구성 하는 방법에 대 한 자세한 내용은 [일정](../automation-schedules.md)을 참조 하세요.

* [작업 스트림을](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) 검사 하 여 오류를 찾습니다. 다음 runbook 중 하나에서 작업을 찾습니다.

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* [실행 계정](../manage-runas-account.md) 에 시작 하거나 중지 하려는 vm에 대 한 적절 한 권한이 있는지 확인 합니다. 리소스에 대 한 사용 권한을 확인 하는 방법에 대 한 자세한 내용은 [빠른 시작: Azure Portal 사용 하 여 사용자에 게 할당 된 역할 보기](../../role-based-access-control/check-access.md)를 참조 하세요. 실행 계정에서 사용 하는 서비스 사용자에 대 한 응용 프로그램 ID를 제공 해야 합니다. Azure Portal에서 Automation 계정으로 이동 하 여이 값을 검색할 수 있습니다. **계정 설정**아래에서 **실행 계정** 을 선택 하 고 적절 한 실행 계정을 선택 합니다.

* Vm이 명시적으로 제외 된 경우에는 Vm을 시작 하거나 중지할 수 없습니다. 제외 된 Vm은 솔루션이 배포 `External_ExcludeVMNames` 되는 Automation 계정의 변수에 설정 됩니다. 다음 예에서는 PowerShell을 사용 하 여 해당 값을 쿼리 하는 방법을 보여 줍니다.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>시나리오: 일부 Vm이 시작 되거나 중지 되지 않습니다.

### <a name="issue"></a>문제

끄기 시간 동안 Vm 시작/중지 솔루션을 구성 했지만 구성 된 일부 Vm을 시작 하거나 중지 하지 않습니다.

### <a name="cause"></a>원인

이 오류는 다음과 같은 원인 중 하나로 인해 발생할 수 있습니다.

- 시퀀스 시나리오에서 태그가 없거나 잘못 된 것일 수 있습니다.
- VM을 제외할 수 있습니다.
- 실행 계정에 VM에 대 한 충분 한 권한이 없을 수 있습니다.
- VM을 시작 또는 중지 하는 문제를 발생 시킬 수 있습니다.

### <a name="resolution"></a>해결 방법

다음 목록에서 문제의 잠재적인 해결 방법이나 검색 위치를 확인합니다.

* 작업 시간 외 Vm 시작/중지 솔루션의 [시퀀스 시나리오](../automation-solution-vm-management.md) 를 사용 하는 경우 시작 또는 중지 하려는 각 VM에 적절 한 태그가 있는지 확인 해야 합니다. 시작하려는 VM에는 `sequencestart` 태그가 있는지, 중지하려는 VM에는 `sequencestop` 태그가 있는지 확인합니다. 두 태그 값은 모두 양의 정수여야 합니다. 다음 예제와 비슷한 쿼리를 사용하여 모든 VM과 태그 및 해당 값을 검색할 수 있습니다.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Vm이 명시적으로 제외 된 경우에는 Vm을 시작 하거나 중지할 수 없습니다. 제외 된 Vm은 솔루션이 배포 `External_ExcludeVMNames` 되는 Automation 계정의 변수에 설정 됩니다. 다음 예에서는 PowerShell을 사용 하 여 해당 값을 쿼리 하는 방법을 보여 줍니다.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Vm을 시작 하 고 중지 하려면 Automation 계정에 대 한 실행 계정에 VM에 대 한 적절 한 권한이 있어야 합니다. 리소스에 대 한 사용 권한을 확인 하는 방법에 대 한 자세한 내용은 [빠른 시작: Azure Portal 사용 하 여 사용자에 게 할당 된 역할 보기](../../role-based-access-control/check-access.md)를 참조 하세요. 실행 계정에서 사용 하는 서비스 사용자에 대 한 응용 프로그램 ID를 제공 해야 합니다. Azure Portal에서 Automation 계정으로 이동 하 여이 값을 검색할 수 있습니다. **계정 설정** 아래에서 **실행 계정** 을 선택 하 고 적절 한 실행 계정을 선택 합니다.
* VM을 시작 하거나 할당을 취소 하는 동안 문제가 발생 하는 경우 VM 자체에 문제가 있을 수 있습니다. 예를 들어 VM을 종료 하려고 할 때 적용 되는 업데이트, 중단 된 서비스 등이 있습니다. VM 리소스로 이동 하 고 **활동 로그** 를 확인 하 여 로그에 오류가 있는지 확인 합니다. 또한 VM에 로그인을 시도 하 여 이벤트 로그에 오류가 있는지 확인할 수 있습니다. VM 문제 해결에 대해 자세히 알아보려면 [Azure virtual Machines 문제 해결](../../virtual-machines/troubleshooting/index.yml)을 참조 하세요.
* [작업 스트림을](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) 검사 하 여 오류를 찾습니다. 포털에서 Automation 계정으로 이동 하 고 **프로세스 자동화**아래에서 **작업** 을 선택 합니다.

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>시나리오: 내 사용자 지정 runbook이 Vm을 시작 하거나 중지 하지 못함

### <a name="issue"></a>문제

사용자 지정 runbook을 작성 했거나 PowerShell 갤러리에서 다운로드 한 경우 제대로 작동 하지 않습니다.

### <a name="cause"></a>원인

이 오류의 원인은 여러 가지가 있을 수 있습니다. Azure Portal에서 Automation 계정으로 이동 하 여 **프로세스 자동화**아래에서 **작업** 을 선택 합니다. **작업** 페이지에서 Runbook의 작업을 찾아 작업 실패를 확인합니다.

### <a name="resolution"></a>해결 방법

다음을 권장합니다.

* [작업 시간 외 Vm 시작/중지 솔루션](../automation-solution-vm-management.md) 을 사용 하 여 Azure Automation에서 vm을 시작 하 고 중지할 수 있습니다. 이 솔루션은 Microsoft에서 작성한 것입니다. 
* Microsoft는 사용자 지정 runbook을 지원 하지 않습니다. [Runbook 문제 해결](runbooks.md)에서 사용자 지정 runbook에 대 한 솔루션을 찾을 수 있습니다. [작업 스트림을](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) 검사 하 여 오류를 찾습니다. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>시나리오: Vm이 올바른 순서로 시작 되거나 중지 되지 않습니다.

### <a name="issue"></a>문제

솔루션에서 구성한 VM이 올바른 순서로 시작 또는 중지되지 않습니다.

### <a name="cause"></a>원인

이 문제는 Vm에 대 한 잘못 된 태깅으로 인해 발생 합니다.

### <a name="resolution"></a>해결 방법

솔루션이 올바르게 구성 되었는지 확인 하려면 다음 단계를 수행 합니다.

1. 시작 또는 중지하려는 모든 VM에 상황에 따라 `sequencestart` 또는 `sequencestop` 태그가 있는지 확인합니다. 이러한 태그 값으로 양의 정수가 필요합니다. VM은 이 값의 오름차순으로 처리됩니다.
1. 시작 또는 중지할 VM의 리소스 그룹이 상황에 따라 `External_Start_ResourceGroupNames` 또는 `External_Stop_ResourceGroupNames` 변수에 있는지 확인합니다.
1. 변경 내용을 미리 보려면 True로 `SequencedStartStop_Parent` 설정 된 `WHATIF` 매개 변수를 사용 하 여 runbook을 실행 하 여 변경 내용을 테스트 합니다.

솔루션을 사용 하 여 시퀀스에서 Vm을 시작 및 중지 하는 방법에 대 한 자세한 내용은 [시퀀스에서 Vm 시작/중지](../automation-solution-vm-management.md)를 참조 하세요.

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>시나리오: 시간 초과 시 Vm 시작/중지 작업이 실패 하 고 403 사용할 수 없음 오류가 발생 함

### <a name="issue"></a>문제

업무 시간 외 Vm 시작/중지 `403 forbidden` 솔루션 runbook에 대 한 오류와 함께 실패 한 작업을 찾을 수 있습니다.

### <a name="cause"></a>원인

이 문제는 잘못 구성 되었거나 만료 된 실행 계정으로 인해 발생할 수 있습니다. 또한 실행 계정에 의해 VM 리소스에 대 한 권한이 부족 하기 때문일 수 있습니다.

### <a name="resolution"></a>해결 방법

실행 계정이 올바르게 구성 되었는지 확인 하려면 Azure Portal의 Automation 계정으로 이동 하 고 **계정 설정**아래에서 **실행 계정** 을 선택 합니다. 실행 계정이 잘못 구성 되었거나 만료 된 경우 상태는 조건을 표시 합니다.

실행 계정이 잘못 구성 된 경우 실행 계정을 삭제 하 고 다시 만듭니다. 자세한 내용은 [Azure Automation 실행 계정 관리](../manage-runas-account.md)를 참조 하세요.

실행 계정에 대 한 인증서가 만료 된 경우 [자체 서명 된 인증서 갱신](../manage-runas-account.md#cert-renewal) 의 단계에 따라 인증서를 갱신 합니다.

누락 된 권한이 있는 경우 빠른 시작 [: Azure Portal을 사용 하 여 사용자에 게 할당 된 역할 보기](../../role-based-access-control/check-access.md)를 참조 하세요. 실행 계정에서 사용 하는 서비스 사용자에 대 한 응용 프로그램 ID를 제공 해야 합니다. Azure Portal에서 Automation 계정으로 이동 하 여이 값을 검색할 수 있습니다. **계정 설정**아래에서 **실행 계정** 을 선택 하 고 적절 한 실행 계정을 선택 합니다.

## <a name="scenario-my-problem-isnt-listed-here"></a><a name="other"></a>시나리오: 내 문제가 여기에 나열 되지 않음

### <a name="issue"></a>문제

이 페이지에 나열 되지 않은 시간 외 Vm 시작/중지 솔루션을 사용 하는 경우 문제가 발생 하거나 예기치 않은 결과가 발생 합니다.

### <a name="cause"></a>원인

이전 및 오래된 버전의 솔루션을 사용하면 많은 시간 오류가 발생할 수 있습니다.

> [!NOTE]
> 업무 시간 외 Vm 시작/중지 솔루션은 솔루션을 배포할 때 Automation 계정으로 가져온 Azure 모듈을 사용 하 여 테스트 되었습니다. 솔루션은 현재 최신 버전의 Azure 모듈에서 작동 하지 않습니다. 이 제한은 시간 외 Vm 시작/중지 솔루션을 실행 하는 데 사용 하는 Automation 계정에만 영향을 줍니다. [Azure Automation에서 Azure PowerShell 모듈을 업데이트 하는 방법](../automation-update-azure-modules.md)에 설명 된 대로 다른 Automation 계정에서 최신 버전의 Azure 모듈을 계속 사용할 수 있습니다.

### <a name="resolution"></a>해결 방법

여러 오류를 해결 하려면 [종료/중지 시간 동안 Vm 시작/중지 솔루션](../automation-solution-vm-management.md#update-the-solution)을 제거 하 고 업데이트 합니다. 또한 [작업 스트림을](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) 검사 하 여 오류를 찾을 수 있습니다. 

## <a name="next-steps"></a>다음 단계

여기에 문제가 표시 되지 않거나 문제를 해결할 수 없는 경우 추가 지원을 위해 다음 채널 중 하나를 시도해 보세요.

* Azure [포럼](https://azure.microsoft.com/support/forums/)을 통해 azure 전문가 로부터 답변을 받으세요.
* 사용자 환경을 [@AzureSupport](https://twitter.com/azuresupport)개선 하기 위한 공식 Microsoft Azure 계정인를 사용 하 여 연결 하세요. Azure 지원은 Azure 커뮤니티를 답변, 지원 및 전문가에 게 연결 합니다.
* Azure 지원 인시던트 제출 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동 하 여 **지원 받기**를 선택 합니다.