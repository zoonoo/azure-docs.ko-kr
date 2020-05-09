---
title: Azure Automation의 자식 runbook
description: 다른 Runbook에서 Azure Automation의 Runbook을 시작하고 서로 정보를 공유하는 다양한 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 21dc14362fed2abf80c2c5ecf57f688541c9c639
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994787"
---
# <a name="child-runbooks-in-azure-automation"></a>Azure Automation의 자식 runbook

다른 runbook에서 호출 하는 불연속 함수를 사용 하 여 재사용 가능한 모듈식 runbook을 작성 Azure Automation 하는 것이 좋습니다. 부모 runbook은 하나 이상의 자식 runbook를 자주 호출하여 필요한 기능을 수행합니다. 자식 runbook을 호출 하는 방법에는 두 가지가 있으며 시나리오에 가장 적합 한 것을 결정 하기 위해 이해 해야 하는 고유한 차이점이 있습니다.

## <a name="invoking-a-child-runbook-using-inline-execution"></a>인라인 실행을 사용하여 자식 runbook 호출

다른 runbook에서 인라인으로 runbook을 호출 하려면 활동이 나 cmdlet을 사용 하는 것 처럼 runbook의 이름을 사용 하 고 매개 변수에 대 한 값을 제공 합니다. 같은 Automation 계정에서 모든 runbook이 이런 방식으로 사용할 다른 모든 사용자에게 제공됩니다. 부모 runbook은 자식 runbook이 완료 될 때까지 대기한 다음 줄로 이동 하 고 출력은 부모에 직접 반환 됩니다.

runbook 인라인을 호출하면 동일한 작업에서 부모 runbook으로 실행됩니다. 자식 runbook의 작업 기록에는 표시 되지 않습니다. 자식 runbook의 모든 예외와 스트림 출력은 부모와 연결 됩니다. 이 동작으로 인해 작업의 수를 줄이고 쉽게 추적 하 고 문제를 해결할 수 있습니다.

runbook이 게시되면 호출하는 모든 자식 runbook은 이미 게시되어야 합니다. 그 이유는 Azure Automation runbook을 컴파일할 때 자식 runbook과의 연결을 구축 하기 때문입니다. 자식 runbook이 아직 게시 되지 않은 경우 부모 runbook이 올바르게 게시 된 것으로 나타나지만 시작 되 면 예외가 생성 됩니다. 이 경우 자식 runbook을 제대로 참조하기 위해 부모 runbook을 다시 게시할 수 있습니다. 연결이 이미 만들어졌으므로 자식 runbook이 변경 된 경우에는 부모 runbook을 다시 게시할 필요가 없습니다.

인라인으로 호출 되는 자식 runbook의 매개 변수는 복합 개체를 비롯 한 모든 데이터 형식일 수 있습니다. Azure Portal 또는 [AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) cmdlet을 사용 하 여 runbook을 시작할 때와 마찬가지로 [JSON serialization](start-runbooks.md#runbook-parameters)은 없습니다.

### <a name="runbook-types"></a>Runbook 형식

서로 호출할 수 있는 runbook 형식은 무엇입니까?

* Powershell [runbook](automation-runbook-types.md#powershell-runbooks) 및 [그래픽 Runbook](automation-runbook-types.md#graphical-runbooks) 은 모두 powershell 기반 이므로 서로 다른 인라인을 호출할 수 있습니다.
* Powershell 워크플로 [runbook](automation-runbook-types.md#powershell-workflow-runbooks) 및 그래픽 powershell 워크플로 runbook은 모두 powershell 워크플로 기반 이므로 서로 다른 인라인을 호출할 수 있습니다.
* PowerShell 유형 및 PowerShell 워크플로 유형은 서로 다른 인라인을 호출할 수 없으며를 사용 `Start-AzAutomationRunbook`해야 합니다.

게시 순서가 중요 한 경우

Runbook의 게시 순서는 PowerShell 워크플로 및 그래픽 PowerShell 워크플로 runbook에만 중요 합니다.

Runbook이 인라인 실행을 사용 하 여 그래픽 또는 PowerShell 워크플로 자식 runbook을 호출 하는 경우 runbook의 이름을 사용 합니다. 스크립트를 로컬 디렉터리에 `.\\` 배치 하도록 지정 하려면 이름을로 시작 해야 합니다.

### <a name="example"></a>예제

다음 예제에서는 복합 개체, 정수 값 및 부울 값을 허용 하는 테스트 자식 runbook을 시작 합니다. 자식 runbook의 출력을 변수에 할당합니다. 이 경우 자식 Runbook은 PowerShell 워크플로 Runbook입니다.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

다음은 PowerShell runbook을 자식으로 사용 하는 동일한 예제입니다.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-a-cmdlet"></a>Cmdlet을 사용 하 여 자식 runbook 시작

> [!IMPORTANT]
> Runbook이 `Start-AzAutomationRunbook` `Wait` 매개 변수를 사용 하 여 cmdlet을 사용 하 여 자식 runbook을 호출 하 고 자식 runbook에서 개체 결과를 생성 하는 경우 작업에 오류가 발생할 수 있습니다. 이 오류를 해결 하려면 [AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) cmdlet을 사용 하 여 결과를 폴링하는 논리를 구현 하는 방법을 알아보려면 [개체 출력이 있는 자식 runbook](troubleshoot/runbooks.md#child-runbook-object) 을 참조 하세요.

Windows PowerShell을 `Start-AzAutomationRunbook` 사용 하 [여 Runbook을 시작 하려면](start-runbooks.md#start-a-runbook-with-powershell)에 설명 된 대로를 사용 하 여 runbook을 시작할 수 있습니다. 이 cmdlet에 사용할 두 가지 모드가 있습니다. 한 모드에서 cmdlet은 자식 runbook에 대해 작업을 만들 때 작업 ID를 반환 합니다. 스크립트가 *Wait* 매개 변수를 지정 하 여 사용 하도록 설정 하는 다른 모드에서 cmdlet은 자식 작업이 완료 될 때까지 대기 하 고 자식 runbook의 출력을 반환 합니다.

Cmdlet을 사용 하 여 시작한 자식 runbook의 작업은 부모 runbook 작업과는 별도로 실행 됩니다. 이 동작은 runbook을 인라인으로 시작 하는 것 보다 더 많은 작업을 수행 하 고 작업을 추적 하기가 더 어려워집니다. 부모는 각 runbook이 완료 될 때까지 기다리지 않고 비동기적으로 둘 이상의 자식 runbook을 시작할 수 있습니다. 자식 runbook을 인라인으로 호출 하는이 병렬 실행에서는 부모 runbook이 [parallel 키워드](automation-powershell-workflow.md#parallel-processing)를 사용 해야 합니다.

시간 때문에 자식 runbook 출력이 부모 runbook으로 안정적으로 반환 되지 않습니다. 또한, 및 기타와 `$VerbosePreference` `$WarningPreference`같은 변수는 자식 runbook으로 전파 되지 않을 수 있습니다. 이러한 문제를 방지 하려면 `Start-AzAutomationRunbook` `Wait` 매개 변수를 사용 하 여를 사용 하 여 개별 Automation 작업으로 자식 runbook을 시작할 수 있습니다. 이 기술은 자식 runbook이 완료 될 때까지 부모 runbook을 차단 합니다.

대기 중인 부모 runbook을 차단 하지 않으려면 `Start-AzAutomationRunbook` `Wait` 매개 변수 없이를 사용 하 여 자식 runbook을 시작할 수 있습니다. 이 경우 runbook에서 작업이 완료 될 때까지 대기 하려면 [AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) 를 사용 해야 합니다. 또한 [AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) 및 [AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) 를 사용 하 여 결과를 검색 해야 합니다.

[Runbook 매개 변수](start-runbooks.md#runbook-parameters)에서 설명한 대로 cmdlet으로 시작 하는 자식 runbook의 매개 변수는 해시 테이블로 제공 됩니다. 단순한 데이터 형식만 사용할 수 있습니다. runbook에 복잡한 데이터 형식을 가진 매개 변수가 있는 경우 인라인으로 호출해야 합니다.

자식 Runbook을 별도 작업으로 시작할 때 구독 컨텍스트가 손실될 수 있습니다. 자식 runbook이 특정 Azure 구독에 대해 Az module cmdlet을 실행 하려면 자식은 부모 runbook과 별개로이 구독에 인증 해야 합니다.

동일한 Automation 계정 내의 작업이 둘 이상의 구독과 함께 작동 하는 경우 한 작업에서 구독을 선택 하면 현재 선택한 구독 컨텍스트가 다른 작업에 대해 변경 될 수 있습니다. 이러한 상황을 방지 하려면 각 `Disable-AzContextAutosave –Scope Process` runbook의 시작 부분에서를 사용 합니다. 이 작업을 수행하면 컨텍스트가 해당 runbook 실행에만 저장됩니다.

### <a name="example"></a>예제

다음 예에서는 매개 변수가 있는 자식 runbook을 시작한 다음 `Start-AzAutomationRunbook` `Wait` 매개 변수와 함께 cmdlet을 사용 하 여 완료 될 때까지 기다립니다. 완료 되 면이 예제에서는 자식 runbook의 cmdlet 출력을 수집 합니다. 를 사용 `Start-AzAutomationRunbook`하려면 스크립트가 Azure 구독에 인증 해야 합니다.

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

다음 표에는 다른 runbook에서 runbook을 호출 하는 두 가지 방법 간의 차이점이 요약 되어 있습니다.

|  | 인라인 | Cmdlet |
|:--- |:--- |:--- |
| 작업 |자식 runbook은 부모와 동일한 작업을 실행합니다. |자식 runbook에 대한 별도 작업을 만듭니다. |
| 실행 |계속하기 전에 부모 runbook은 자식 runbook이 완료되기를 기다립니다. |자식 Runbook이 시작된 후에 즉시 부모 Runbook이 계속되거나 *또는* 자식 작업이 완료될 때까지 부모 Runbook이 대기합니다. |
| 출력 |부모 runbook은 자식 runbook에서 출력을 직접 가져올 수 있습니다. |부모 Runbook은 자식 Runbook 작업에서 출력을 검색하거나 *또는* 자식 Runbook에서 출력을 직접 가져올 수 있습니다. |
| 매개 변수 |자식 runbook 매개 변수 값은 별도로 지정되며 모든 데이터 형식을 사용할 수 있습니다. |자식 runbook 매개 변수 값은 단일 해시 테이블로 결합해야 합니다. 이 해시 테이블은 JSON 직렬화를 사용하는 단순, 배열 및 개체 데이터 형식만 포함할 수 있습니다. |
| Automation 계정 |부모 runbook은 동일한 Automation 계정에서 자식 runbook을 사용할 수 있습니다. |부모 runbook은 모든 Automation 계정, 동일한 Azure 구독 및 연결 된 다른 구독에서 자식 runbook을 사용할 수 있습니다. |
| 게시 |부모 runbook을 게시하기 전에 자식 runbook을 게시해야 합니다. |자식 runbook은 부모 runbook이 시작 되기 전에 언제 든 지 게시 됩니다. |

## <a name="next-steps"></a>다음 단계

* [Azure Automation에서 Runbook 시작](start-runbooks.md)
* [Azure Automation에서 Runbook 출력 및 메시지](automation-runbook-output-and-messages.md)