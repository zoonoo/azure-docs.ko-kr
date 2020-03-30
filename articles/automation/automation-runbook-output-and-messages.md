---
title: Azure Automation에서 Runbook 출력 및 메시지
description: Azure Automation의 Runbook에서 출력 및 오류 메시지를 만들고 검색하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2018
ms.topic: conceptual
ms.openlocfilehash: 457b2d2211ea1ba5fa36cec4b7e9a214f5bcad77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367094"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Azure Automation에서 Runbook 출력 및 메시지

대부분의 Azure Automation Runbook은 일종의 출력 양식을 갖습니다. 이 출력은 사용자에게 오류 메시지또는 다른 Runbook과 함께 사용할 수 있는 복잡한 개체일 수 있습니다. Windows PowerShell은 [여러 스트림](/powershell/module/microsoft.powershell.core/about/about_redirection) 제공하여 스크립트 또는 워크플로에서 출력을 보냅니다. Azure Automation은 이러한 스트림에서 각각 다르게 작동합니다. Runbook을 만들 때 스트림을 사용하는 모범 사례를 따라야 합니다.

다음 표에서는 게시된 Runbook에 대한 Azure 포털의 동작과 [Runbook을 테스트하는](automation-testing-runbook.md)동안 각 스트림에 대해 간략하게 설명합니다. 출력 스트림은 Runbook 간의 통신에 사용되는 기본 스트림입니다. 다른 스트림은 사용자에게 정보를 전달하기 위한 메시지 스트림으로 분류됩니다. 

| 스트림 | 설명 | 게시 날짜 | 테스트 |
|:--- |:--- |:--- |:--- |
| Error |사용자를 위한 오류 메시지입니다. 예외와 달리 Runbook은 기본적으로 오류 메시지 후에 계속됩니다. |직무 기록에 기록 |테스트 출력 창에 표시 |
| 디버그 |대화형 사용자를 위한 메시지입니다. Runbook에서 사용하지 않아야 합니다. |작업 기록에 기록되지 않음 |테스트 출력 창에 표시되지 않음 |
| 출력 |다른 runbook에서 사용할 수 있도록 의도된 개체입니다. |직무 기록에 기록 |테스트 출력 창에 표시 |
| 진행 |runbook의 각 작업 전과 후에 레코드를 자동으로 생성했습니다. Runbook은 대화형 사용자를 위한 것이므로 자체 진행률 레코드를 만들려고 시도해서는 안 됩니다. |Runbook에 대해 진행률 로깅이 켜져 있는 경우에만 작업 기록에 기록됩니다. |테스트 출력 창에 표시되지 않음 |
| 자세히 |일반 또는 디버깅 정보를 제공하는 메시지입니다. |Runbook에 대한 자세한 로깅이 켜져 있는 경우에만 작업 기록에 기록됩니다. |`VerbosePreference` 런북에서 변수가 계속되도록 설정된 경우에만 테스트 출력 창에 표시됩니다. |
| Warning |사용자를 위한 경고 메시지입니다. |직무 기록에 기록 |테스트 출력 창에 표시 |

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 모듈을 업데이트하는 방법을](automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="output-stream"></a>출력 스트림

출력 스트림은 올바르게 실행될 때 스크립트 또는 워크플로에 의해 생성된 개체의 출력에 사용됩니다. Azure Automation은 주로 [현재 Runbook을](automation-child-runbooks.md)호출하는 상위 Runbook에서 사용할 개체에 대해 이 스트림을 사용합니다. 부모가 [Runbook 인라인을 호출하면](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution)자식이 출력 스트림에서 상위로 데이터를 반환합니다. 

Runbook은 다른 Runbook에서 호출되지 않는 경우에만 출력 스트림을 사용하여 일반 정보를 클라이언트에 전달합니다. 그러나 가장 좋은 방법은 Runbook에서 일반적으로 [Verbose 스트림을](#verbose-stream) 사용하여 사용자에게 일반적인 정보를 전달해야 합니다.

[Runbook이 쓰기 출력을](https://technet.microsoft.com/library/hh849921.aspx)사용하여 출력 스트림에 데이터를 작성하도록 합니다. 또는 스크립트에서 개체를 자체 줄에 배치할 수 있습니다.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="handling-output-from-a-function"></a>함수의 출력 처리

Runbook 함수가 Output 스트림에 기록되면 출력이 Runbook에 다시 전달됩니다. Runbook에서 해당 출력을 변수에 할당하면 출력이 출력 스트림에 기록되지 않습니다. 함수 내에서 다른 스트림에 기록하면 Runbook의 해당하는 스트림에 기록됩니다. 다음 샘플 PowerShell 워크플로 런북을 고려하십시오.

```powershell
Workflow Test-Runbook
{
  Write-Verbose "Verbose outside of function" -Verbose
  Write-Output "Output outside of function"
  $functionOutput = Test-Function
  $functionOutput

  Function Test-Function
  {
    Write-Verbose "Verbose inside of function" -Verbose
    Write-Output "Output inside of function"
  }
}
```

Runbook 작업의 출력 스트림은 다음과 같은 것입니다.

```output
Output inside of function
Output outside of function
```

Runbook 작업에 대한 자세한 내용은 다음과 같은 것입니다.

```output
Verbose outside of function
Verbose inside of function
```

Runbook을 게시하고 시작하기 전에 Runbook 설정에서 자세한 로깅을 켜서 자세한 스트림 출력을 얻어야 합니다.

### <a name="declaring-output-data-type"></a>출력 데이터 형식 선언

다음은 출력 데이터 형식의 예입니다.

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>워크플로에서 출력 데이터 형식 선언

워크플로는 [OutputType 특성을](https://technet.microsoft.com/library/hh847785.aspx)사용하여 출력의 데이터 형식을 지정합니다. 이 특성은 런타임 동안에는 영향을 주지 않지만 Runbook의 예상 출력의 디자인 시간에 표시를 제공합니다. Runbook에 대한 도구 집합이 계속 발전함에 따라 디자인 타임에 출력 데이터 형식을 선언하는 것이 중요해지고 있습니다. 따라서 이 선언을 만드는 모든 Runbook에 포함하는 것이 좋습니다.

다음 샘플 runbook은 문자열 개체를 출력하고 해당 출력 형식의 선언을 포함합니다. Runbook이 특정 유형의 배열을 출력하면 형식의 배열과 달리 형식을 지정해야 합니다.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>그래픽 런북에서 출력 데이터 형식 선언

그래픽 또는 그래픽 PowerShell 워크플로 런북에서 출력 유형을 선언하려면 **입력 및 출력** 메뉴 옵션을 선택하고 출력 유형을 입력할 수 있습니다. 전체 .NET 클래스 이름을 사용하여 부모 Runbook에서 참조할 때 형식을 쉽게 식별할 수 있도록 하는 것이 좋습니다. 전체 이름을 사용하면 클래스의 모든 속성이 Runbook의 databus에 노출되고 조건이 있는 논리, 로깅 및 다른 Runbook 활동에 대한 값으로 참조하는 데 속성을 사용할 때 유연성이 향상됩니다.<br> ![Runbook 입력 및 출력 옵션](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>입력 및 출력 속성 창에서 **출력 유형** 필드에 값을 입력한 후 항목을 인식할 수 있도록 컨트롤 외부를 클릭해야 합니다.

다음 예제에서는 입력 및 출력 기능을 보여 주는 두 개의 그래픽 런북을 보여 줍니다. 모듈식 Runbook 디자인 모델을 적용하면 Runbook을 하나 개의 Runbook이 인증 Runbook 템플릿으로 사용하여 Run As 계정을 사용하여 Azure에서 인증을 관리합니다. 일반적으로 지정된 시나리오를 자동화하기 위해 코어 논리를 수행하는 두 번째 Runbook은 이 경우 인증 Runbook 템플릿을 실행합니다. 테스트 출력 창에 결과를 표시합니다. 정상적인 상황에서 자식 Runbook의 출력을 활용하는 리소스에 대해 이 Runbook이 작업을 수행하도록 합니다.

**AuthenticateTo-Azure** Runbook의 기본 논리는 다음과 같습니다.<br> ![Runbook 템플릿 예제 인증](media/automation-runbook-output-and-messages/runbook-authentication-template.png)할 경우 Azure 관리 포털에서 각 스트림 및 동작에 대한 간략한 설명을 제공합니다.

Runbook에는 인증 프로필 `Microsoft.Azure.Commands.Profile.Models.PSAzureContext`속성을 반환하는 출력 유형이 포함됩니다.<br> ![Runbook 출력 형식 예제](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

이 Runbook은 간단하지만 여기에서 호출할 구성 항목이 하나 있습니다. 마지막 활동은 cmdlet을 실행하여 매개 변수에 `Write-Output` 대한 PowerShell 식을 사용하여 프로필 데이터를 변수에 `Inputobject` 씁니다. 이 매개 변수는 `Write-Output`에 필요합니다.

이 예제의 두 번째 Runbook인 **테스트-자-자OutputTypeType은**단순히 두 가지 활동을 정의합니다.<br> ![예제 자식 출력 형식 Runbook](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

첫 번째 활동은 **인증받는 Azure** 실행책을 호출합니다. 두 번째 활동은 `Write-Verbose` **데이터 원본을** 활동 **출력으로**설정한 cmdlet을 실행합니다. 또한 **필드 경로는** **Context.Subscription.SubscriptionName,** **인증된 Azure** 실행책의 컨텍스트 출력으로 설정됩니다.<br> ![쓰기-자세한 Cmdlet 매개 변수 데이터 소스](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

결과 출력은 구독의 이름입니다.<br> ![Test-ChildOutputType Runbook 결과](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="message-streams"></a>메시지 스트림

출력 스트림과 달리 메시지 스트림은 사용자에게 정보를 전달합니다. 다양한 종류의 정보에 대해 여러 개의 메시지 스트림이 있으며 Azure Automation은 각 스트림을 다르게 처리합니다.

### <a name="warning-and-error-streams"></a>경고 및 오류 스트림

경고 및 오류는 Runbook에서 발생하는 로그 문제를 스트리밍합니다. Azure Automation은 Runbook을 실행할 때 이러한 스트림을 작업 기록에 기록합니다. 자동화에는 Runbook을 테스트할 때 Azure 포털의 테스트 출력 창에 있는 스트림이 포함됩니다. 

기본적으로 실행책은 경고 또는 오류 후에도 계속 실행됩니다. Runbook이 메시지를 만들기 전에 [기본 설정 변수를](#preference-variables) 설정하도록 하여 Runbook이 경고 또는 오류에 대해 일시 중단되도록 지정할 수 있습니다. 예를 들어 예외에서와 마찬가지로 Runbook이 오류에 대해 일시 중단되도록 하려면 변수를 `ErrorActionPreference` 중지로 설정합니다.

[Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) 또는 [Write-Error](https://technet.microsoft.com/library/hh849962.aspx) cmdlet를 사용하여 경고 또는 오류 메시지를 만듭니다. 활동은 경고 및 오류 스트림에 쓸 수도 있습니다.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="debug-stream"></a>디버그 스트림

Azure Automation은 대화형 사용자를 위해 디버그 메시지 스트림을 사용합니다. Runbook에서 사용해서는 안 됩니다.

### <a name="verbose-stream"></a>자세한 정보 표시 스트림

자세한 내용 은 런북 작업에 대한 일반적인 정보를 지원합니다. Runbook에는 디버그 스트림을 사용할 수 없으므로 Runbook에서 디버그 정보에 대한 자세한 메시지를 사용해야 합니다. 

기본적으로 작업 기록은 성능상의 이유로 게시된 Runbook의 자세한 메시지를 저장하지 않습니다. 자세한 메시지를 저장하려면 **로그 세부 레코드** 설정이 있는 Azure 포털 **구성** 탭을 사용하여 게시된 Runbook을 구성하여 자세한 메시지를 기록합니다. 옵션을 켜서 문제를 해결하거나 runbook 디버그합니다. 대부분의 경우 자세한 레코드를 로깅하지 않는 기본 설정을 유지해야 합니다.

[Runbook을 테스트](automation-testing-runbook.md)할 경우 Runbook이 자세한 정보 표시 레코드를 기록하도록 구성되면 자세한 정보 표시 메시지는 표시되지 않습니다. [Runbook을 테스트하는](automation-testing-runbook.md)동안 자세한 메시지를 표시하려면 `VerbosePreference` 변수를 계속으로 설정해야 합니다. 이 변수 집합을 사용하면 자세한 메시지가 Azure 포털의 테스트 출력 창에 표시됩니다.

다음 코드는 [쓰기-자세한](https://technet.microsoft.com/library/hh849951.aspx) 문자 쓰기 cmdlet을 사용 하 여 자세한 메시지를 만듭니다.

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

## <a name="progress-records"></a>진행률 레코드

Azure 포털의 **구성** 탭을 사용하여 진행률 레코드를 기록하도록 Runbook을 구성할 수 있습니다. 기본 설정은 성능을 최대화하기 위해 레코드를 기록하지 않는 것입니다. 대부분의 경우 기본 설정을 유지해야 합니다. 옵션을 켜서 문제를 해결하거나 runbook 디버그합니다. 

진행률 레코드 로깅을 사용하도록 설정하면 Runbook은 각 활동이 실행되기 전과 후에 작업 기록에 레코드를 씁니다. Runbook이 진행률 레코드를 기록하도록 구성된 경우에도 Runbook을 테스트해도 진행 메시지가 표시되지 않습니다.

>[!NOTE]
>[Write-progress](https://technet.microsoft.com/library/hh849902.aspx) cmdlet은 대화형 사용자로 사용하기 위한 것이기 때문에 Runbook에 유효하지 않습니다.

## <a name="preference-variables"></a>기본 설정 변수

Runbook에서 특정 Windows PowerShell [기본 설정 변수를](https://technet.microsoft.com/library/hh847796.aspx) 설정하여 다른 출력 스트림으로 전송되는 데이터에 대한 응답을 제어할 수 있습니다. 다음 표에는 Runbook에서 사용할 수 있는 기본 설정 변수와 기본값 및 유효한 값이 나열되어 있습니다. Azure 자동화 외부의 Windows PowerShell에서 사용할 경우 기본 설정 변수에 대해 추가 값을 사용할 수 있습니다.

| 변수 | 기본값 | 유효한 값 |
|:--- |:--- |:--- |
| `WarningPreference` |계속 |중지<br>계속<br>SilentlyContinue |
| `ErrorActionPreference` |계속 |중지<br>계속<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |중지<br>계속<br>SilentlyContinue |

다음 표에는 Runbook에서 유효한 기본 설정 변수 값에 대한 동작이 나열됩니다.

| 값 | 동작 |
|:--- |:--- |
| 계속 |메시지를 기록하고 runbook 실행을 계속합니다. |
| SilentlyContinue |메시지를 기록하지 않고 Runbook을 계속 실행합니다. 이 값은 메시지를 무시하는 효과가 있습니다. |
| 중지 |메시지를 기록하고 runbook을 일시 중단합니다. |

## <a name="retrieving-runbook-output-and-messages"></a><a name="runbook-output"></a>Runbook 출력 및 메시지 검색

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Azure 포털에서 Runbook 출력 및 메시지 검색

Runbook에 대한 **작업** 탭을 사용하여 Azure 포털에서 Runbook 작업의 세부 정보를 볼 수 있습니다. 작업 요약에는 작업에 대한 일반 정보 및 발생한 예외 외에 입력 매개 변수 및 [출력 스트림이](#output-stream)표시됩니다. 작업 기록에는 출력 스트림 및 경고 및 오류 스트림의 메시지가 [포함됩니다.](#warning-and-error-streams) 또한 자세한 내용 및 [진행률 레코드를](#progress-records) 기록하도록 Runbook이 구성된 경우 [자세한 스트림](#verbose-stream) 및 진행률 레코드의 메시지도 포함됩니다.

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Windows PowerShell에서 런북 출력 및 메시지 검색

Windows PowerShell에서 [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) cmdlet을 사용하여 Runbook에서 출력 및 메시지를 검색할 수 있습니다. 이 cmdlet은 작업의 ID를 필요로 하며 `Stream` 검색할 스트림을 지정하는 매개 변수가 있습니다. 이 매개 변수에 대한 Any 값을 지정하여 작업에 대한 모든 스트림을 검색할 수 있습니다.

다음 예제는 샘플 runbook를 시작한 다음 완료되기를 기다립니다. Runbook이 실행을 완료하면 스크립트는 작업에서 Runbook 출력 스트림을 수집합니다.

```powershell
$job = Start-AzAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzAutomationJob -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output

# For more detailed job output, pipe the output of Get-AzAutomationJobOutput to Get-AzAutomationJobOutputRecord
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzAutomationJobOutputRecord
```

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>그래픽 런북에서 Runbook 출력 및 메시지 검색

그래픽 런북의 경우 활동 수준 추적 의 형태로 출력 및 메시지의 추가 로깅을 사용할 수 있습니다. 추적에는 기본 추적과 자세히 추적이 있습니다. 기본 추적에는 Runbook의 각 활동에 대한 시작 및 종료 시간과 모든 활동 재시도와 관련된 정보가 표시됩니다. 몇 가지 예로는 시도 횟수와 활동의 시작 시간이 있습니다. 자세한 추적에는 기본 추적 기능과 각 활동에 대한 입력 및 출력 데이터 로깅이 포함됩니다. 

현재 활동 수준 추적은 자세한 스트림을 사용하여 레코드를 기록합니다. 따라서 추적을 활성화할 때 자세한 로깅을 사용하도록 설정해야 합니다. 추적이 활성화된 그래픽 Runbook의 경우 진행률 레코드를 기록할 필요가 없습니다. 기본 추적은 동일한 용도를 제공하며 정보가 더욱 자세합니다.

![그래픽 작성 작업 스트림 보기](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

이미지에서 그래픽 런북에 대한 자세한 로깅 및 추적을 사용하면 프로덕션 **작업 스트림** 보기에서 훨씬 더 많은 정보를 사용할 수 있습니다. 이 추가 정보는 Runbook에서 생산 문제를 해결하는 데 필수적일 수 있습니다. 

그러나 문제 해결을 위해 Runbook의 진행 상황을 추적하기 위해 이 정보가 필요하지 않는 한 일반적인 경우 추적을 해제할 수 있습니다. 추적 레코드는 특히 많을 수 있습니다. 그래픽 런북 추적을 사용하면 기본 또는 상세 추적 구성에 따라 활동당 2~4개의 레코드를 얻을 수 있습니다.

**활동 수준 추적을 사용하려면 다음을 수행합니다.**

1. Azure Portal에서 Automation 계정을 엽니다.
2. **프로세스 자동화** 섹션에서 **Runbook을** 선택하여 Runbook 목록을 엽니다.
3. Runbook 페이지에서 Runbook 목록에서 그래픽 런북을 선택합니다.
4. **설정**에서 **로깅 및 추적**을 클릭합니다.
5. 로깅 및 추적 페이지에서 **로그 자세한 기록**에서 자세한 로깅을 사용하려면 **계속을** 클릭합니다.
6. **활동 수준 추적에서**필요한 추적 수준에 따라 추적 수준을 **기본** 또는 **세부**수준으로 변경합니다.<br>

   ![그래픽 작성 로깅 및 추적 페이지](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Microsoft Azure 모니터 로그에서 런북 출력 및 메시지 검색

Azure Automation은 Runbook 작업 상태 및 작업 스트림을 Log Analytics 작업 영역으로 보낼 수 있습니다. Azure Monitor는 다음을 수행할 수 있는 로그를 지원합니다.

* Automation 작업에 대한 통찰력 확보
* Runbook 작업 상태에 따라 이메일 또는 경고를 트리거합니다(예: 실패 또는 일시 중단).
* 작업 스트림 간에 고급 쿼리를 작성합니다.
* Automation 계정 간에 작업 상호 연결
* 작업 기록을 시각화합니다.

작업 데이터를 수집, 상관 관계 및 작동하기 위해 Azure Monitor 로그와의 통합구성을 구성하는 방법에 대한 자세한 내용은 [자동화에서 Azure Monitor 로그로의 작업 상태 및 작업 스트림 을](automation-manage-send-joblogs-log-analytics.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

* Runbook 실행, Runbook 작업 모니터링 및 기타 기술 세부 정보에 대한 자세한 내용은 [Runbook 작업 추적을](automation-runbook-execution.md)참조하십시오.
* 자식 실행책을 디자인하고 사용하는 방법을 이해하려면 [Azure Automation의 자식 실행책을](automation-child-runbooks.md)참조하십시오.
* 언어 참조 및 학습 모듈을 포함한 [PowerShell에](/powershell/scripting/overview)대한 자세한 내용은 PowerShell 문서를 참조하십시오.
