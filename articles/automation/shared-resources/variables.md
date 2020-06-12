---
title: Azure Automation의 변수 관리
description: 이 문서에서는 Runbook 및 DSC 구성에서 변수를 사용하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 28f69d3ef8301e00b470ce09353be6ae3259bbe3
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744976"
---
# <a name="manage-variables-in-azure-automation"></a>Azure Automation의 변수 관리

변수 자산은 Automation 계정의 모든 runbook 및 DSC 구성에 사용할 수 있는 값입니다. Azure Portal, PowerShell, Runbook 내 또는 DSC 구성에서 관리할 수 있습니다.

Automation 변수는 다음과 같은 시나리오에 유용합니다.

- 여러 Runbook 또는 DSC 구성 간에 값을 공유하는 경우

- 동일한 Runbook의 여러 작업 또는 DSC 구성 간에 값을 공유하는 경우

- 포털 또는 PowerShell 명령줄에서 Runbook 또는 DSC 구성에 사용되는 값을 관리하는 경우. 한 가지 예로 특정 VM 이름 목록, 특정 리소스 그룹, AD 도메인 이름 등의 공용 구성 항목 세트가 있습니다.  

Azure Automation은 변수를 유지하고 Runbook 또는 DSC 구성이 실패한 경우에도 변수를 사용할 수 있도록 합니다. 이 동작 덕분에 하나의 Runbook 또는 DSC 구성이 설정한 값을 다른 Runbook이 사용하거나 동일한 Runbook이나 DSC 구성이 다음에 실행될 때 다시 사용할 수 있습니다.

Azure Automation은 암호화된 각 변수를 안전하게 저장합니다. 변수를 만들 때 Azure Automation을 사용하여 해당 암호화 및 스토리지를 보안 자산으로 지정할 수 있습니다. 

>[!NOTE]
>Azure Automation의 안전한 자산에는 자격 증명, 인증서, 연결, 암호화된 변수 등이 있습니다. 이러한 자산은 각 Automation 계정에 대해 생성되는 고유 키를 사용하여 암호화되고 Azure Automation에 저장됩니다. Azure Automation은 시스템 관리 Key Vault에 키를 저장합니다. 보안 자산을 저장하기 전에 Automation이 Key Vault에서 키를 로드한 다음, 자산을 암호화하는 데 사용합니다. 

## <a name="variable-types"></a>변수 형식

Azure Portal에서 변수를 만들 때 드롭다운 목록에서 해당 데이터 형식을 지정해야 합니다. 그래야 포털에서 변수 값을 입력할 수 있는 적절한 컨트롤을 표시할 수 있습니다. 다음은 Azure Automation에서 사용할 수 있는 변수 형식입니다.

* String
* 정수
* DateTime
* 부울
* Null

변수가 지정된 데이터 형식으로 제한되지 않습니다. 다른 형식의 값을 지정하려면 Windows PowerShell을 사용하여 변수를 설정해야 합니다. `Not defined`를 지정하면 변수 값이 Null로 설정됩니다. [Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) cmdlet 또는 내부 `Set-AutomationVariable` cmdlet을 사용하여 값을 설정해야 합니다.

Azure Portal을 사용하여 복잡한 변수 형식의 값을 만들거나 변경할 수 없습니다. 그러나 Windows PowerShell을 사용하여 모든 형식의 값을 제공할 수 있습니다. 복잡한 형식은 [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject)로 검색됩니다.

배열 또는 해시 테이블을 만들어 변수에 저장하여 여러 값을 단일 변수에 저장할 수 있습니다.

>[!NOTE]
>VM 이름 변수는 최대 80자까지 가능합니다. 리소스 그룹 변수는 최대 90자까지 가능합니다. [Azure 리소스에 대한 명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-name-rules)을 참조하세요.

## <a name="powershell-cmdlets-to-access-variables"></a>변수에 액세스하는 데 사용되는 PowerShell cmdlet

다음 표에 나와 있는 cmdlet은 PowerShell을 사용하여 Automation 변수를 만들고 관리합니다. [Az 모듈](modules.md#az-modules)의 일부로 제공됩니다.

| Cmdlet | Description |
|:---|:---|
|[Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | 기존 변수의 값을 검색합니다. 값이 단순 형식이면 동일한 해당 형식이 검색되고, 복합 형식이면 `PSCustomObject` 형식이 검색됩니다. <br>**참고:**  이 cmdlet을 사용하여 암호화된 변수의 값을 검색할 수는 없습니다. 이 작업을 수행하는 유일한 방법은 Runbook 또는 DSC 구성에서 내부 `Get-AutomationVariable` cmdlet을 사용하는 것입니다. [변수에 액세스하는 데 사용되는 내부 cmdlet](#internal-cmdlets-to-access-variables)을 참조하세요. |
|[New-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | 새 변수를 만들고 해당 값을 설정합니다.|
|[Remove-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| 기존 변수를 제거합니다.|
|[Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| 기존 변수의 값을 설정합니다. |

## <a name="internal-cmdlets-to-access-variables"></a>변수에 액세스하는 데 사용되는 내부 cmdlet

다음 표의 내부 cmdlet은 Runbook 및 DSC 구성에서 변수에 액세스하는 데 사용됩니다. 이러한 cmdlet은 글로벌 모듈 `Orchestrator.AssetManagement.Cmdlets`와 함께 제공됩니다. 자세한 내용은 [내부 cmdlet](modules.md#internal-cmdlets)을 참조하세요.

| 내부 Cmdlet | Description |
|:---|:---|
|`Get-AutomationVariable`|기존 변수의 값을 검색합니다.|
|`Set-AutomationVariable`|기존 변수의 값을 설정합니다.|

> [!NOTE]
> Runbook 또는 DSC 구성에서 `Get-AutomationVariable`의 `Name` 매개 변수에 변수를 사용하지 마세요. 변수를 사용하면 디자인 타임에 Runbook과 Automation 변수 간의 종속성 검색이 복잡해질 수 있습니다.

`Get-AutomationVariable`은 PowerShell에서 작동하지 않으며 Runbook 또는 DSC 구성에서만 작동합니다. 예를 들어 암호화된 변수의 값을 보려면 Runbook을 만들어 변수를 가져온 다음, 출력 스트림에 쓸 수 있습니다.
 
```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="python-2-functions-to-access-variables"></a>변수에 액세스하는 Python 2 함수

다음 테이블의 함수는 Python 2 Runbook의 변수에 액세스하는 데 사용됩니다.

|Python 2 함수|Description|
|:---|:---|
|`automationassets.get_automation_variable`|기존 변수의 값을 검색합니다. |
|`automationassets.set_automation_variable`|기존 변수의 값을 설정합니다. |

> [!NOTE]
> 자산 함수에 액세스하려면 Python Runbook 맨 위에서 `automationassets` 모듈을 가져와야 합니다.

## <a name="create-and-get-a-variable"></a>변수를 만들고 가져오기

>[!NOTE]
>변수에 대한 암호화를 제거하려면 변수를 삭제하고 암호화되지 않은 상태로 다시 만들어야 합니다.

### <a name="create-and-get-a-variable-using-the-azure-portal"></a>Azure Portal을 사용하여 변수를 만들고 가져오기

1. Automation 계정에서 **자산** 타일을 클릭한 후 **자산** 블레이드에서 **변수**를 선택합니다.
2. **변수** 타일에서 **변수 추가**를 선택합니다.
3. **새 변수** 블레이드에서 옵션을 완료한 후 **만들기**를 클릭하여 새 변수를 저장합니다.

> [!NOTE]
> 암호화된 변수를 저장하면 포털에서 해당 변수를 볼 수 없습니다. 업데이트할 수만 있습니다.

### <a name="create-and-get-a-variable-in-windows-powershell"></a>Windows PowerShell에서 변수를 만들고 가져오기

Runbook 또는 DSC 구성은 `New-AzAutomationVariable` cmdlet을 사용하여 새 변수를 만들고 초기 값을 설정합니다. 변수가 암호화되면 호출에서 `Encrypted` 매개 변수를 사용해야 합니다. 스크립트는 `Get-AzAutomationVariable`을 사용하여 변수 값을 검색할 수 있습니다. 

>[!NOTE]
>PowerShell 스크립트는 암호화된 값을 검색할 수 없습니다. 이 작업을 수행하는 유일한 방법은 내부 `Get-AutomationVariable` cmdlet을 사용하는 것입니다.

다음 예제에서는 문자열 변수를 만든 다음, 해당 값을 반환하는 방법을 보여 줍니다.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

다음 예제에서는 복잡한 형식의 변수를 만들고 해당 속성을 검색하는 방법을 보여 줍니다. 이 예제에서는 [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0)의 가상 머신 개체가 사용되었습니다.

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="textual-runbook-examples"></a>텍스트 Runbook 예제

### <a name="retrieve-and-set-a-simple-value-from-a-variable"></a>변수에서 단순 값 검색 및 설정

다음 예제에서는 텍스트 Runbook에서 변수를 설정 및 검색하는 방법을 보여 줍니다. 이 예제에서는 `NumberOfIterations` 및 `NumberOfRunnings`라는 정수 변수를 만들고 `SampleMessage`라는 문자열 변수를 만든다고 가정합니다.

```powershell
$NumberOfIterations = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

### <a name="retrieve-and-set-a-variable-in-a-python-2-runbook"></a>Python 2 Runbook에서 변수 검색 및 설정

다음 샘플은 Python 2 Runbook에서 변수를 가져오고, 변수를 설정하고, 없는 변수에 대한 예외를 처리하는 방법을 보여 줍니다.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a variable
value = automationassets.get_automation_variable("test-variable")
print value

# set a variable (value can be int/bool/string)
automationassets.set_automation_variable("test-variable", True)
automationassets.set_automation_variable("test-variable", 4)
automationassets.set_automation_variable("test-variable", "test-string")

# handle a non-existent variable exception
try:
    value = automationassets.get_automation_variable("nonexisting variable")
except AutomationAssetNotFound:
    print "variable not found"
```

## <a name="graphical-runbook-examples"></a>그래픽 Runbook 예제

그래픽 Runbook에서 내부 cmdlet `Get-AutomationVariable` 또는 `Set-AutomationVariable`에 대한 작업을 추가할 수 있습니다. 그래픽 편집기의 라이브러리 창에서 각 변수를 마우스 오른쪽 단추로 클릭하고 원하는 작업을 선택하면 됩니다.

![캔버스에 변수 추가](../media/variables/runbook-variable-add-canvas.png)

다음 그림에서는 그래픽 Runbook에서 단순한 값으로 변수를 업데이트하는 예제 작업을 보여 줍니다. 이 예제에서 `Get-AzVM`에 대한 작업은 단일 Azure 가상 머신을 검색하고 기존 Automation 문자열 변수에 컴퓨터 이름을 저장합니다. 코드 출력에 단일 개체만 필요하므로 [링크가 파이프라인인지 시퀀스인지](../automation-graphical-authoring-intro.md#use-links-for-workflow) 는 중요하지 않습니다.

![단순한 변수 설정](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>다음 단계

* 변수에 액세스하는 데 사용되는 cmdlet에 대해 자세히 알아보려면 [Azure Automation에서 모듈 관리](modules.md)를 참조하세요.
* Runbook에 대한 일반적인 정보는 [Azure Automation에서 Runbook 실행](../automation-runbook-execution.md)을 참조하세요.
* DSC 구성에 대한 자세한 내용은 [Azure Automation State Configuration 개요](../automation-dsc-overview.md)를 참조하세요.
