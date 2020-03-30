---
title: Azure Automation의 변수 자산
description: 변수 자산은 Azure Automation의 모든 runbook과 DSC 구성에서 사용할 수 있는 값입니다.  이 문서에서는 변수에 대해 자세히 알아보고 텍스트 작성과 그래픽 작성 모두에서 변수를 사용하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d4a4a92feb3e1b400c0f40076148f7898c4bdef1
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365821"
---
# <a name="variable-assets-in-azure-automation"></a>Azure Automation의 변수 자산

가변 자산은 자동화 계정의 모든 Runbook 및 DSC 구성에서 사용할 수 있는 값입니다. Azure 포털, PowerShell, Runbook 내 또는 DSC 구성에서 관리할 수 있습니다.

Automation 변수는 다음과 같은 시나리오에 유용합니다.

- 여러 Runbook 또는 DSC 구성 간에 값을 공유합니다.

- 동일한 Runbook 또는 DSC 구성의 여러 작업 간에 값을 공유합니다.

- 포털 또는 PowerShell 명령줄에서 Runbook 또는 DSC 구성에서 사용하는 값 관리 예를 들어 VM 이름의 특정 목록, 특정 리소스 그룹, AD 도메인 이름 등과 같은 일반적인 구성 항목 집합이 있습니다.  

Azure Automation은 변수를 유지하며 Runbook 또는 DSC 구성이 실패하더라도 변수를 사용할 수 있도록 합니다. 이 동작을 사용하면 한 Runbook 또는 DSC 구성이 다음에 실행할 때 다른 Runbook 또는 동일한 Runbook 또는 DSC 구성에서 사용되는 값을 설정할 수 있습니다.

Azure Automation은 각 암호화된 변수를 안전하게 저장합니다. 변수를 만들 때 Azure Automation에서 암호화 및 저장소를 보안 자산으로 지정할 수 있습니다. 다른 보안 자산에는 자격 증명, 인증서 및 연결이 포함됩니다. Azure Automation은 이러한 자산을 암호화하고 각 자동화 계정에 대해 생성되는 고유 키를 사용하여 자산을 저장합니다. 키는 시스템 관리 키 볼트에 저장됩니다. 보안 자산을 저장하기 전에 Azure Automation은 Key Vault에서 키를 로드한 다음 이를 사용하여 자산을 암호화합니다. 

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 모듈을 업데이트하는 방법을](../automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="variable-types"></a>변수 형식

Azure 포털을 사용하여 변수를 만들 때 포털이 변수 값을 입력하기 위한 적절한 컨트롤을 표시할 수 있도록 드롭다운 목록에서 데이터 형식을 지정해야 합니다. 다음은 Azure 자동화에서 사용할 수 있는 변수 형식입니다.

* String
* 정수
* DateTime
* 부울
* Null

변수는 지정된 데이터 유형으로 제한되지 않습니다. 다른 형식의 값을 지정하려면 Windows PowerShell을 사용하여 변수를 설정해야 합니다. 을 지정하면 `Not defined`변수 값이 Null로 설정되고 [Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) cmdlet 또는 `Set-AutomationVariable` 활동으로 값을 설정해야 합니다.

Azure 포털을 사용하여 복잡한 변수 형식의 값을 만들거나 변경할 수 없습니다. 그러나 Windows PowerShell을 사용하여 모든 형식의 값을 제공할 수 있습니다. 복잡한 형식은 [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject)로 검색됩니다.

배열 또는 해시 테이블을 만들어 변수에 저장하여 여러 값을 단일 변수에 저장할 수 있습니다.

## <a name="powershell-cmdlets-that-create-and-manage-variable-assets"></a>가변 자산을 만들고 관리하는 PowerShell cmdlet

Az 모듈의 경우 다음 표의 cmdlet을 사용하여 Windows PowerShell을 사용하여 자동화 가변 자산을 만들고 관리합니다. 자동화 런북 및 DSC 구성에 사용할 수 있는 [Az.Automation 모듈의](/powershell/azure/overview)일부로 제공됩니다.

| Cmdlet | 설명 |
|:---|:---|
|[Get-AzAutomation변수](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | 기존 변수의 값을 검색합니다. 이 cmdlet을 사용하여 암호화된 변수의 값을 검색할 수 없습니다. 이 작업을 수행하는 유일한 방법은 `Get-AutomationVariable` Runbook 또는 DSC 구성에서 활동을 사용하는 것입니다. |
|[뉴 아즈오토오토메이션변수](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | 새 변수를 만들고 해당 값을 설정합니다.|
|[제거-아즈자동화변수](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| 기존 변수를 제거합니다.|
|[세트-아즈자동화변수](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| 기존 변수의 값을 설정합니다. |

## <a name="activities-to-access-variables-in-runbooks-and-dsc-configurations"></a>Runbook 및 DSC 구성의 변수에 액세스하는 활동

다음 표의 활동은 Runbook 및 DSC 구성의 변수에 액세스하는 데 사용됩니다. 이러한 활동에 대한 cmdlet에는 전역 `Orchestrator.AssetManagement.Cmdlets`모듈이 함께 제공됩니다.

| 활동 | 설명 |
|:---|:---|
|`Get-AutomationVariable`|기존 변수의 값을 검색합니다.|
|`Set-AutomationVariable`|기존 변수의 값을 설정합니다.|

> [!NOTE]
> Runbook 또는 DSC `Get-AutomationVariable` 구성의 `Name` 매개 변수에서 변수를 사용하지 마십시오. 이 매개 변수를 사용하면 디자인 시 런북 또는 DSC 구성 및 자동화 변수 간의 종속성 검색이 복잡해질 수 있습니다.

PowerShell에서는 작동하지 `Get-AutomationVariable` 않지만 Runbook 또는 DSC 구성에서만 작동합니다. 예를 들어 암호화된 변수의 값을 보려면 Runbook을 만들어 변수를 가져옵니다.
 
```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="functions-to-access-variables-in-python-2-runbooks"></a>파이썬 2 런북의 변수에 액세스하는 함수

다음 표의 함수는 Python 2 Runbook의 변수에 액세스하는 데 사용됩니다.

|파이썬 2 함수|설명|
|:---|:---|
|`automationassets.get_automation_variable`|기존 변수의 값을 검색합니다. |
|`automationassets.set_automation_variable`|기존 변수의 값을 설정합니다. |

> [!NOTE]
> 자산 함수에 `automationassets` 액세스하려면 파이썬 런북 상단에 있는 모듈을 가져와야 합니다.

## <a name="working-with-automation-variables"></a>자동화 변수 작업

>[!NOTE]
>변수에 대한 암호화를 제거하려면 변수를 삭제하고 암호화되지 않은 것으로 다시 만들어야 합니다.

### <a name="create-a-new-variable-using-the-azure-portal"></a>Azure 포털을 사용하여 새 변수 만들기

1. 자동화 계정에서 **자산** 타일, 자산 **블레이드를** 클릭하고 **변수를 선택합니다.**
2. **변수** 타일에서 **변수 추가**를 선택합니다.
3. **새 변수** 블레이드에서 옵션을 완료한 다음 **만들기를** 클릭하여 새 변수를 저장합니다.

> [!NOTE]
> 암호화된 변수를 저장한 후에는 포털에서 볼 수 없습니다. 업데이트만 할 수 있습니다.

### <a name="create-and-use-a-variable-in-windows-powershell"></a>Windows PowerShell에서 변수 만들기 및 사용

PowerShell 스크립트는 `New-AzAutomationVariable` cmdlet 또는 AzureRM 모듈과 동등한 것을 사용하여 새 변수를 만들고 초기 값을 설정합니다. 변수가 암호화된 경우 호출은 `Encrypted` 매개 변수를 사용해야 합니다.

스크립트는 [Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0)를 사용하여 변수의 값을 검색합니다. 값이 단순 형식인 경우 cmdlet은 동일한 형식을 검색합니다. 복잡한 형식인 경우 형식이 `PSCustomObject` 검색됩니다.

>[!NOTE]
>PowerShell 스크립트는 암호화된 값을 검색할 수 없습니다. 이 작업을 수행하는 유일한 방법은 `Get-AutomationVariable` Runbook 또는 DSC 구성에서 활동을 사용하는 것입니다.

다음 예제에서는 String 형식의 변수를 만든 다음 해당 값을 반환 하는 방법을 보여 주어 있습니다.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

다음 예제에서는 복합 형식을 사용하여 변수를 만든 다음 해당 속성을 검색하는 방법을 보여 주며 있습니다. 이 경우 [Get-AzVM의](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) 가상 시스템 개체가 사용됩니다.

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

### <a name="create-and-use-a-variable-in-a-runbook-or-dsc-configuration"></a>Runbook 또는 DSC 구성에서 변수 만들기 및 사용

Runbook 또는 DSC 구성 내에서 새 변수를 만드는 유일한 `New-AzAutomationVariable` 방법은 cmdlet 또는 AzureRM 모듈과 동등한 용도로 사용하는 것입니다. 스크립트는 이 cmdlet을 사용하여 변수의 초기 값을 설정합니다. 그런 다음 스크립트는 을 `Get-AzAutomationVariable`사용하여 값을 검색할 수 있습니다. 값이 단순 형식인 경우 동일한 형식이 검색됩니다. 복잡한 형식인 경우 형식이 `PSCustomObject` 검색됩니다.

>[!NOTE]
>암호화된 값을 검색하는 유일한 방법은 `Get-AutomationVariable` Runbook 또는 DSC 구성의 활동을 사용하는 것입니다. 

### <a name="textual-runbook-samples"></a>텍스트 Runbook 샘플

#### <a name="set-and-retrieve-a-simple-value-from-a-variable"></a>변수에서 단순 값 설정 및 검색

다음 명령 예제에서는 텍스트 Runbook에서 변수를 설정 및 검색하는 방법을 보여 줍니다. 이 샘플에서는 명명된 `NumberOfIterations` 정수 변수와 `NumberOfRunnings` 명명된 문자열 `SampleMessage`변수를 만드는 것을 가정합니다.

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

#### <a name="set-and-retrieve-a-variable-in-a-python-2-runbook"></a>파이썬 2 Runbook에서 변수 설정 및 검색

다음 샘플에서는 파이썬 2 Runbook에서 변수를 사용하고, 변수를 설정하고, 존재하지 않는 변수에 대한 예외를 처리하는 방법을 보여 주며,

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

### <a name="graphical-runbook-samples"></a>그래픽 Runbook 샘플

그래픽 런북에서 `Get-AutomationVariable` 또는 `Set-AutomationVariable` 활동을 추가할 수 있습니다. 그래픽 편집기의 라이브러리 창에서 변수를 마우스 오른쪽 단추로 클릭하고 원하는 활동을 선택하기만 하면 됩니다.

![캔버스에 변수 추가](../media/variables/runbook-variable-add-canvas.png)

#### <a name="set-values-in-a-variable"></a>변수에서 값 설정

다음 그림에서는 그래픽 Runbook에서 단순한 값으로 변수를 업데이트하는 샘플 활동을 보여 줍니다. 이 샘플에서는 `Get-AzVM` 단일 Azure 가상 컴퓨터를 검색하고 컴퓨터 이름을 기존 자동화 문자열 변수에 저장합니다. 코드는 출력에서 단일 개체만 예상하므로 [링크가 파이프라인인지 시퀀스인지는](../automation-graphical-authoring-intro.md#links-and-workflow) 중요하지 않습니다.

![단순한 변수 설정](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>다음 단계

- 그래픽 작성에서 활동을 연결하는 방법에 대해 자세히 알아보려면 [그래픽 작성의 링크를](../automation-graphical-authoring-intro.md#links-and-workflow)참조하십시오.
- 그래픽 런북을 시작하려면 [내 첫 번째 그래픽 실행북을](../automation-first-runbook-graphical.md)참조하십시오.
