---
title: Azure Automation에서 변수 관리
description: 변수 자산은 Azure Automation의 모든 runbook과 DSC 구성에서 사용할 수 있는 값입니다.  이 문서에서는 변수에 대해 자세히 알아보고 텍스트 작성과 그래픽 작성 모두에서 변수를 사용하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4778e9b2c0d3b442b214966ab69810d2f42b70b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732756"
---
# <a name="manage-variables-in-azure-automation"></a>Azure Automation에서 변수 관리

변수 자산은 자동화 계정의 모든 runbook 및 DSC 구성에서 사용할 수 있는 값입니다. Azure Portal, PowerShell, runbook 내 또는 DSC 구성에서 관리할 수 있습니다.

Automation 변수는 다음과 같은 시나리오에 유용합니다.

- 여러 runbook 또는 DSC 구성 간에 값을 공유 합니다.

- 동일한 runbook 또는 DSC 구성에서 여러 작업 간에 값을 공유 합니다.

- 포털 또는 PowerShell 명령줄에서 runbook 또는 DSC 구성에 사용 되는 값을 관리 합니다. 예는 특정 VM 이름 목록, 특정 리소스 그룹, AD 도메인 이름 등의 공통 구성 항목 집합입니다.  

Azure Automation 변수를 유지 하 고 runbook 또는 DSC 구성에 실패 한 경우에도 변수를 사용할 수 있도록 합니다. 이 동작을 통해 하나의 runbook 또는 DSC 구성은 다른 runbook에서 사용 하거나 다음에 실행 될 때 동일한 runbook 또는 DSC 구성에서 사용 하는 값을 설정할 수 있습니다.

Azure Automation는 암호화 된 각 변수를 안전 하 게 저장 합니다. 변수를 만들 때 보안 자산으로 Azure Automation 여 해당 암호화 및 저장소를 지정할 수 있습니다. 기타 보안 자산에는 자격 증명, 인증서 및 연결이 포함 됩니다. Azure Automation는 이러한 자산을 암호화 하 여 각 Automation 계정에 대해 생성 되는 고유 키를 사용 하 여 저장 합니다. 키는 시스템에서 관리 되는 Key Vault에 저장 됩니다. 보안 자산을 저장 하기 전에 Azure Automation Key Vault에서 키를 로드 한 다음이를 사용 하 여 자산을 암호화 합니다. 

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대한 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법](../automation-update-azure-modules.md)을 사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="variable-types"></a>변수 형식

Azure Portal를 사용 하 여 변수를 만들 때는 포털에서 변수 값을 입력 하는 데 적절 한 컨트롤을 표시할 수 있도록 드롭다운 목록에서 데이터 형식을 지정 해야 합니다. Azure Automation에서 사용할 수 있는 변수 형식은 다음과 같습니다.

* 문자열
* 정수
* DateTime
* 부울
* Null

변수는 지정 된 데이터 형식으로 제한 되지 않습니다. 다른 유형의 값을 지정 하려면 Windows PowerShell을 사용 하 여 변수를 설정 해야 합니다. 을 지정 `Not defined`하면 변수 값이 Null로 설정 됩니다. [AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) cmdlet 또는 `Set-AutomationVariable` 작업을 사용 하 여 값을 설정 해야 합니다.

Azure Portal를 사용 하 여 복잡 한 변수 형식의 값을 만들거나 변경할 수 없습니다. 그러나 Windows PowerShell을 사용 하 여 모든 형식의 값을 제공할 수 있습니다. 복합 형식은 [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject)로 검색 됩니다.

배열 또는 해시 테이블을 만들어 변수에 저장하여 여러 값을 단일 변수에 저장할 수 있습니다.

>[!NOTE]
>VM 이름 변수는 최대 80 자까지 가능 합니다. 리소스 그룹 변수는 최대 90 자까지 가능 합니다. [Azure 리소스에 대 한 명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-name-rules)을 참조 하세요.

## <a name="powershell-cmdlets-that-create-and-manage-variable-assets"></a>변수 자산을 만들고 관리 하는 PowerShell cmdlet

Az module의 경우 다음 표의 cmdlet은 Windows PowerShell을 사용 하 여 자동화 변수 자산을 만들고 관리 하는 데 사용 됩니다. Automation runbook 및 DSC 구성에서 사용할 수 있는 [Az. automation 모듈](/powershell/azure/overview)의 일부로 제공 됩니다.

| Cmdlet | Description |
|:---|:---|
|[AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | 기존 변수의 값을 검색합니다. 이 cmdlet을 사용 하 여 암호화 된 변수의 값을 검색할 수 없습니다. Runbook 또는 DSC 구성의 `Get-AutomationVariable` 작업을 사용 하 여이 작업을 수행할 수 있습니다. |
|[AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | 새 변수를 만들고 해당 값을 설정합니다.|
|[AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| 기존 변수를 제거합니다.|
|[AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| 기존 변수의 값을 설정합니다. |

## <a name="activities-to-access-variables-in-runbooks-and-dsc-configurations"></a>Runbook 및 DSC 구성에서 변수에 액세스 하는 작업

다음 표의 활동은 runbook 및 DSC 구성에서 변수에 액세스 하는 데 사용 됩니다. 이러한 활동에 대 한 cmdlet에는 전역 모듈과 `Orchestrator.AssetManagement.Cmdlets`함께 제공 됩니다.

| 작업 | Description |
|:---|:---|
|`Get-AutomationVariable`|기존 변수의 값을 검색합니다.|
|`Set-AutomationVariable`|기존 변수의 값을 설정합니다.|

> [!NOTE]
> Runbook 또는 DSC 구성의 `Name` 매개 변수 `Get-AutomationVariable` 에서 변수를 사용 하지 마십시오. 이 매개 변수를 사용 하면 디자인 타임에 runbook 또는 DSC 구성과 자동화 변수 간의 종속성을 검색 하는 작업이 복잡 해질 수 있습니다.

`Get-AutomationVariable` 은 PowerShell에서 작동 하지 않지만 RUNBOOK 또는 DSC 구성 에서만 작동 합니다. 예를 들어, 암호화 된 변수의 값을 보려면 runbook을 만들어 변수를 가져온 다음 출력 스트림에 쓸 수 있습니다.
 
```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="functions-to-access-variables-in-python-2-runbooks"></a>Python 2 runbook에서 변수에 액세스 하는 함수

다음 표의 함수는 Python 2 runbook에서 변수에 액세스 하는 데 사용 됩니다.

|Python 2 함수|Description|
|:---|:---|
|`automationassets.get_automation_variable`|기존 변수의 값을 검색합니다. |
|`automationassets.set_automation_variable`|기존 변수의 값을 설정합니다. |

> [!NOTE]
> 자산 기능에 액세스 `automationassets` 하려면 Python runbook의 맨 위에 있는 모듈을 가져와야 합니다.

## <a name="working-with-automation-variables"></a>자동화 변수 작업

>[!NOTE]
>변수에 대 한 암호화를 제거 하려면 변수를 삭제 하 고 암호화 되지 않은 상태로 다시 만들어야 합니다.

### <a name="create-a-new-variable-using-the-azure-portal"></a>Azure Portal를 사용 하 여 새 변수를 만듭니다.

1. Automation 계정에서 **자산** 타일을 클릭 한 다음 **자산** 블레이드를 클릭 하 고 **변수**를 선택 합니다.
2. **변수** 타일에서 **변수 추가**를 선택합니다.
3. **새** 변수 블레이드에서 옵션을 완료 하 고 **만들기** 를 클릭 하 여 새 변수를 저장 합니다.

> [!NOTE]
> 암호화 된 변수를 저장 한 후에는 포털에서 볼 수 없습니다. 업데이트할 수만 있습니다.

### <a name="create-and-use-a-variable-in-windows-powershell"></a>Windows PowerShell에서 변수 만들기 및 사용

PowerShell 스크립트는 `New-AzAutomationVariable` cmdlet 또는이에 해당 하는 AzureRM 모듈을 사용 하 여 새 변수를 만들고 해당 초기 값을 설정 합니다. 변수가 암호화 되 면 호출에서 매개 변수를 `Encrypted` 사용 해야 합니다.

이 스크립트는 [AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0)를 사용 하 여 변수 값을 검색 합니다. 값이 단순 형식이 면 cmdlet은 동일한 형식을 검색 합니다. 복합 형식인 경우 `PSCustomObject` 형식이 검색 됩니다.

>[!NOTE]
>PowerShell 스크립트는 암호화 된 값을 검색할 수 없습니다. Runbook 또는 DSC 구성에서 `Get-AutomationVariable` 작업을 사용 하는 방법만이 작업을 수행할 수 있습니다.

다음 예제에서는 문자열 형식의 변수를 만든 다음 해당 값을 반환 하는 방법을 보여 줍니다.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

다음 예제에서는 복합 형식을 사용 하 여 변수를 만든 다음 해당 속성을 검색 하는 방법을 보여 줍니다. 이 경우 [new-azvm](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) 의 가상 컴퓨터 개체가 사용 됩니다.

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

### <a name="create-and-use-a-variable-in-a-runbook-or-dsc-configuration"></a>Runbook 또는 DSC 구성에서 변수 만들기 및 사용

Runbook 또는 DSC 구성 내에서 새 변수를 만드는 유일한 방법은 `New-AzAutomationVariable` cmdlet 또는이에 해당 하는 AzureRM 모듈을 사용 하는 것입니다. 이 스크립트는이 cmdlet을 사용 하 여 변수의 초기 값을 설정 합니다. 그런 다음 스크립트는를 사용 하 여 `Get-AzAutomationVariable`값을 검색할 수 있습니다. 값이 단순 형식이 면 동일한 형식이 검색 됩니다. 복합 형식인 경우 `PSCustomObject` 형식이 검색 됩니다.

>[!NOTE]
>암호화 된 값을 검색 하는 유일한 방법은 runbook 또는 DSC `Get-AutomationVariable` 구성의 작업을 사용 하는 것입니다. 

### <a name="textual-runbook-samples"></a>텍스트 Runbook 샘플

#### <a name="set-and-retrieve-a-simple-value-from-a-variable"></a>변수에서 단순 값 설정 및 검색

다음 명령 예제에서는 텍스트 Runbook에서 변수를 설정 및 검색하는 방법을 보여 줍니다. 이 샘플에서는 라는 정수 변수와 `NumberOfIterations` `NumberOfRunnings` 이라는 `SampleMessage`문자열 변수를 만드는 것으로 가정 합니다.

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

#### <a name="set-and-retrieve-a-variable-in-a-python-2-runbook"></a>Python 2 runbook에서 변수 설정 및 검색

다음 샘플에서는 변수를 사용 하 고, 변수를 설정 하 고, Python 2 runbook에서 존재 하지 않는 변수에 대 한 예외를 처리 하는 방법을 보여 줍니다.

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

그래픽 runbook에서 `Get-AutomationVariable` 또는 `Set-AutomationVariable` 작업을 추가할 수 있습니다. 그래픽 편집기의 라이브러리 창에서 변수를 마우스 오른쪽 단추로 클릭 하 고 원하는 작업을 선택 하면 됩니다.

![캔버스에 변수 추가](../media/variables/runbook-variable-add-canvas.png)

#### <a name="set-values-in-a-variable"></a>변수에 값 설정

다음 그림에서는 그래픽 Runbook에서 단순한 값으로 변수를 업데이트하는 샘플 활동을 보여 줍니다. 이 샘플에서는 단일 `Get-AzVM` Azure 가상 머신을 검색 하 고 기존 자동화 문자열 변수에 컴퓨터 이름을 저장 합니다. 코드에서 출력에 단일 개체만 필요 하므로 [링크가 파이프라인 인지 시퀀스](../automation-graphical-authoring-intro.md#links-and-workflow) 인지는 중요 하지 않습니다.

![단순한 변수 설정](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>다음 단계

- 그래픽 제작에서 작업을 연결 하는 방법에 대 한 자세한 내용은 [그래픽 작성의 링크](../automation-graphical-authoring-intro.md#links-and-workflow)를 참조 하세요.
- 그래픽 runbook을 시작 하려면 [내 첫 번째 그래픽 runbook](../automation-first-runbook-graphical.md)을 참조 하세요.
