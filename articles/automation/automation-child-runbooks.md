---
title: Azure Automation의 자식 runbook
description: 다른 Runbook에서 Azure Automation의 Runbook을 시작하고 서로 정보를 공유하는 다양한 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 42362a170f493afd51a5d4ee139620ad25b54e79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367366"
---
# <a name="child-runbooks-in-azure-automation"></a>Azure Automation의 자식 runbook

Azure Automation에서 다른 Runbook에서 호출되는 개별 함수를 사용하여 재사용 가능한 모듈식 Runbook을 작성하는 것이 좋습니다. 부모 runbook은 하나 이상의 자식 runbook를 자주 호출하여 필요한 기능을 수행합니다. 자식 Runbook을 호출하는 방법에는 두 가지가 있으며 시나리오에 가장 적합한 것을 결정할 수 있도록 이해해야 하는 뚜렷한 차이점이 있습니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 모듈을 업데이트하는 방법을](automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="invoking-a-child-runbook-using-inline-execution"></a>인라인 실행을 사용하여 자식 runbook 호출

다른 Runbook에서 Runbook인을 호출하려면 활동이나 cmdlet을 사용하는 것처럼 Runbook의 이름을 사용하고 해당 매개 변수에 대한 값을 제공합니다. 같은 Automation 계정에서 모든 runbook이 이런 방식으로 사용할 다른 모든 사용자에게 제공됩니다. 상위 Runbook은 다음 줄로 이동하기 전에 자식 Runbook이 완료될 때까지 기다렸다가 모든 출력이 부모에게 직접 반환됩니다.

runbook 인라인을 호출하면 동일한 작업에서 부모 runbook으로 실행됩니다. 자식 Runbook의 작업 기록에는 표시가 없습니다. 모든 예외 및 자식 Runbook의 스트림 출력은 부모와 연결됩니다. 이 동작으로 인해 작업이 줄어들고 쉽게 추적하고 문제를 해결할 수 있습니다.

runbook이 게시되면 호출하는 모든 자식 runbook은 이미 게시되어야 합니다. 그 이유는 Azure Automation이 Runbook을 컴파일할 때 모든 자식 실행 책과의 연결을 구축하기 때문입니다. 자식 Runbook이 아직 게시되지 않은 경우 부모 Runbook이 제대로 게시되는 것처럼 보이지만 시작될 때 예외가 생성됩니다. 이 경우 자식 runbook을 제대로 참조하기 위해 부모 runbook을 다시 게시할 수 있습니다. 연결이 이미 만들어졌기 때문에 자식 Runbook이 변경된 경우 상위 Runbook을 다시 게시할 필요가 없습니다.

인라인이라고 하는 자식 Runbook의 매개 변수는 복잡한 개체를 포함한 모든 데이터 형식일 수 있습니다. Azure 포털을 사용하거나 [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) cmdlet을 사용하여 Runbook을 시작할 때와 같이 [JSON 직렬화가](start-runbooks.md#runbook-parameters)없습니다.

### <a name="runbook-types"></a>Runbook 형식

어떤 Runbook 유형이 서로를 호출할 수 있습니까?

* [PowerShell 런북과](automation-runbook-types.md#powershell-runbooks) [그래픽 런북은](automation-runbook-types.md#graphical-runbooks) 모두 PowerShell 기반이기 때문에 서로를 인라인으로 호출할 수 있습니다.
* [PowerShell 워크플로 우호 런북과](automation-runbook-types.md#powershell-workflow-runbooks) 그래픽 PowerShell 워크플로러 런북은 모두 PowerShell 워크플로 기반이기 때문에 서로를 인라인으로 호출할 수 있습니다.
* PowerShell 형식과 PowerShell 워크플로 유형은 서로 인라인으로 호출할 수 없으며 을 사용해야 `Start-AzAutomationRunbook`합니다.

게시 순서는 언제 중요합니까?

Runbook의 게시 순서는 PowerShell 워크플로 및 그래픽 PowerShell 워크플로런북에만 중요합니다.

Runbook이 인라인 실행을 사용하여 그래픽 또는 PowerShell 워크플로자 식 Runbook을 호출할 때 Runbook의 이름을 사용합니다. 스크립트가 로컬 `.\\` 디렉터리에 있는지 지정하려면 이름을 시작해야 합니다.

### <a name="example"></a>예제

다음 예제에서는 복잡한 개체, 정수 값 및 부울 값을 허용하는 테스트 자식 Runbook을 시작합니다. 자식 runbook의 출력을 변수에 할당합니다. 이 경우 자식 Runbook은 PowerShell 워크플로 Runbook입니다.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

다음은 PowerShell Runbook을 자식으로 사용하는 것과 동일한 예입니다.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-a-cmdlet"></a>cmdlet을 사용하여 자식 런북 시작

> [!IMPORTANT]
> Runbook에서 `Start-AzAutomationRunbook` `Wait` 매개 변수가 있는 cmdlet이 있는 자식 Runbook을 호출하고 자식 Runbook에서 개체 결과를 생성하는 경우 작업에 오류가 발생할 수 있습니다. 오류를 해결하려면 개체 [출력이 있는 자식 실행북을](troubleshoot/runbooks.md#child-runbook-object) 참조하여 [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) cmdlet을 사용하여 결과를 폴링하는 논리를 구현하는 방법을 알아봅니다.

에 설명된 대로 Runbook을 시작하는 데 사용할 `Start-AzAutomationRunbook` 수 [있습니다Windows PowerShell .](start-runbooks.md#start-a-runbook-with-powershell) 이 cmdlet에 사용할 두 가지 모드가 있습니다. 한 모드에서 cmdlet은 자식 Runbook에 대한 작업을 만들 때 작업 ID를 반환합니다. 스크립트가 *Wait* 매개 변수를 지정하여 활성화하는 다른 모드에서는 cmdlet이 자식 작업이 완료될 때까지 대기하고 자식 Runbook의 출력을 반환합니다.

자식 Runbook의 작업은 cmdlet으로 시작하여 부모 Runbook 작업과 별도로 실행됩니다. 이 동작으로 인해 Runbook 인라인을 시작하는 것보다 더 많은 작업이 발생하므로 작업을 추적하기가 더 어려워집니다. 부모는 각각이 완료될 때까지 기다리지 않고 두 개 이상의 자식 Runbook을 비동기적으로 시작할 수 있습니다. 자식 runbook인을 호출하는 이 병렬 실행의 경우 부모 Runbook은 [병렬 키워드를](automation-powershell-workflow.md#parallel-processing)사용해야 합니다.

자식 Runbook 출력 타이밍 때문에 부모 Runbook에 안정적으로 반환 되지 않습니다. 또한 . 및 `$VerbosePreference` `$WarningPreference`기타와 같은 변수는 자식 실행책에 전파되지 않을 수 있습니다. 이러한 문제를 방지하려면 `Start-AzAutomationRunbook` `Wait` 매개 변수를 사용하여 자식 Runbook을 별도의 자동화 작업으로 시작할 수 있습니다. 이 기술은 자식 Runbook이 완료될 때까지 상위 Runbook을 차단합니다.

대기 중 부모 Runbook을 차단하지 않으려면 매개 변수 없이 자식 `Start-AzAutomationRunbook` Runbook을 `Wait` 시작할 수 있습니다. 이 경우 Runbook은 [Get-AzAutomationJob을](/powershell/module/az.automation/get-azautomationjob) 사용하여 작업 완료를 기다려야 합니다. 또한 [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) 및 [Get-AzAutomationJobOutputRecord를](/powershell/module/az.automation/get-azautomationjoboutputrecord) 사용하여 결과를 검색해야 합니다.

cmdlet으로 시작된 자식 Runbook에 대한 매개 변수는 [Runbook 매개 변수에](start-runbooks.md#runbook-parameters)설명된 대로 해시 테이블로 제공됩니다. 단순한 데이터 형식만 사용할 수 있습니다. runbook에 복잡한 데이터 형식을 가진 매개 변수가 있는 경우 인라인으로 호출해야 합니다.

자식 Runbook을 별도 작업으로 시작할 때 구독 컨텍스트가 손실될 수 있습니다. 자식 Runbook이 특정 Azure 구독에 대해 Az 모듈 cmdlet을 실행하려면 자식이 상위 Runbook과 독립적으로 이 구독을 인증해야 합니다.

동일한 Automation 계정 내의 작업이 두 개 이상의 구독에서 작동하는 경우 한 작업에서 구독을 선택하면 다른 작업에 대해 현재 선택된 구독 컨텍스트가 변경될 수 있습니다. 이러한 상황을 방지하려면 `Disable-AzContextAutosave –Scope Process` 각 Runbook의 시작 부분에 사용합니다. 이 작업을 수행하면 컨텍스트가 해당 runbook 실행에만 저장됩니다.

### <a name="example"></a>예제

다음 예제는 매개 변수가 있는 자식 Runbook을 시작한 `Start-AzAutomationRunbook` 다음 `Wait` 매개 변수가 있는 cmdlet을 사용하여 완료될 때까지 기다립니다. 완료되면 이 예제에서는 자식 Runbook에서 cmdlet 출력을 수집합니다. 을 `Start-AzAutomationRunbook`사용하려면 스크립트가 Azure 구독을 인증해야 합니다.

```azurepowershell-interactive
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Get-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –Wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>자식 runbook을 호출하기 위한 방법 비교

다음 표에는 다른 Runbook에서 Runbook을 호출하는 두 가지 방법의 차이점이 요약되어 있습니다.

|  | 인라인 | Cmdlet |
|:--- |:--- |:--- |
| 작업 |자식 runbook은 부모와 동일한 작업을 실행합니다. |자식 runbook에 대한 별도 작업을 만듭니다. |
| 실행 |계속하기 전에 부모 runbook은 자식 runbook이 완료되기를 기다립니다. |자식 Runbook이 시작된 후에 즉시 부모 Runbook이 계속되거나 *또는* 자식 작업이 완료될 때까지 부모 Runbook이 대기합니다. |
| 출력 |부모 runbook은 자식 runbook에서 출력을 직접 가져올 수 있습니다. |부모 Runbook은 자식 Runbook 작업에서 출력을 검색하거나 *또는* 자식 Runbook에서 출력을 직접 가져올 수 있습니다. |
| 매개 변수 |자식 runbook 매개 변수 값은 별도로 지정되며 모든 데이터 형식을 사용할 수 있습니다. |자식 runbook 매개 변수 값은 단일 해시 테이블로 결합해야 합니다. 이 해시 테이블은 JSON 직렬화를 사용하는 단순, 배열 및 개체 데이터 형식만 포함할 수 있습니다. |
| Automation 계정 |상위 Runbook은 동일한 자동화 계정에서만 자식 Runbook을 사용할 수 있습니다. |부모 Runbook은 모든 자동화 계정, 동일한 Azure 구독 및 연결이 있는 다른 구독에서도 자식 Runbook을 사용할 수 있습니다. |
| 게시 |부모 runbook을 게시하기 전에 자식 runbook을 게시해야 합니다. |하위 Runbook은 부모 Runbook이 시작되기 전에 언제든지 게시됩니다. |

## <a name="next-steps"></a>다음 단계

* [Azure Automation에서 Runbook 시작](start-runbooks.md)
* [Azure Automation에서 Runbook 출력 및 메시지](automation-runbook-output-and-messages.md)