---
title: Azure Automation에서 Runbook 출력 및 메시지
description: Azure Automation의 runbook에서 출력 및 오류 메시지를 만들고 검색 하는 방법을 설명 합니다.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2018
ms.topic: conceptual
ms.openlocfilehash: 457b2d2211ea1ba5fa36cec4b7e9a214f5bcad77
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367094"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Azure Automation에서 Runbook 출력 및 메시지

대부분의 Azure Automation Runbook은 일종의 출력 양식을 갖습니다. 이 출력은 다른 runbook과 함께 사용 하기 위한 사용자 또는 복합 개체에 대 한 오류 메시지 일 수 있습니다. Windows PowerShell은 [여러 스트림](/powershell/module/microsoft.powershell.core/about/about_redirection) 제공하여 스크립트 또는 워크플로에서 출력을 보냅니다. Azure Automation은 이러한 스트림에서 각각 다르게 작동합니다. Runbook을 만들 때 스트림 사용에 대 한 모범 사례를 따라야 합니다.

다음 표에서는 게시 된 runbook에 대 한 Azure Portal 및 [runbook 테스트](automation-testing-runbook.md)중에 각 스트림에 대 한 동작을 간략하게 설명 합니다. 출력 스트림은 runbook 간 통신에 사용 되는 주 스트림입니다. 다른 스트림은 사용자에 게 정보를 전달 하기 위해 메시지 스트림으로 분류 됩니다. 

| STREAM | Description | 게시 날짜 | 테스트 |
|:--- |:--- |:--- |:--- |
| Error |사용자를 위한 오류 메시지입니다. 예외와 달리 runbook은 기본적으로 오류 메시지가 발생 한 후에도 계속 됩니다. |작업 기록에 기록 됩니다. |테스트 출력 창에 표시 됩니다. |
| 디버그 |대화형 사용자를 위한 메시지입니다. Runbook에서 사용하지 않아야 합니다. |작업 기록에 작성 되지 않음 |테스트 출력 창에 표시 되지 않음 |
| 출력 |다른 runbook에서 사용할 수 있도록 의도된 개체입니다. |작업 기록에 기록 됩니다. |테스트 출력 창에 표시 됩니다. |
| 진행 |runbook의 각 작업 전과 후에 레코드를 자동으로 생성했습니다. Runbook은 대화형 사용자 용 이므로 자체 진행률 레코드를 만들려고 해서는 안 됩니다. |Runbook에 대해 진행률 로깅이 설정 되어 있는 경우에만 작업 기록에 기록 됩니다. |테스트 출력 창에 표시 되지 않음 |
| 자세히 |일반 또는 디버깅 정보를 제공하는 메시지입니다. |Runbook에 대해 자세한 정보 로깅이 설정 되어 있는 경우에만 작업 기록에 기록 됩니다. |Runbook에서 `VerbosePreference` 변수가 Continue로 설정 된 경우에만 테스트 출력 창에 표시 됩니다. |
| Warning |사용자를 위한 경고 메시지입니다. |작업 기록에 기록 됩니다. |테스트 출력 창에 표시 됩니다. |

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대 한 Az module 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조 하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트 하는 방법을](automation-update-azure-modules.md)사용 하 여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="output-stream"></a>출력 스트림

출력 스트림은 스크립트나 워크플로가 제대로 실행 될 때 생성 되는 개체의 출력에 사용 됩니다. Azure Automation는 주로 [현재 runbook](automation-child-runbooks.md)을 호출 하는 부모 runbook에서 사용 될 개체에 대해이 스트림을 사용 합니다. 부모가 runbook을 [인라인으로 호출](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution)하는 경우 자식은 출력 스트림의 데이터를 부모로 반환 합니다. 

Runbook은 다른 runbook에서 호출 되지 않는 경우에만 출력 스트림을 사용 하 여 일반 정보를 클라이언트에 전달 합니다. 그러나 일반적으로 runbook은 일반적으로 자세한 정보 표시 [스트림을](#verbose-stream) 사용 하 여 사용자에 게 일반 정보를 전달 해야 합니다.

Runbook이 [쓰기 출력](https://technet.microsoft.com/library/hh849921.aspx)을 사용 하 여 출력 스트림에 데이터를 쓰도록 합니다. 또는 스크립트에서 자체 줄에 개체를 배치할 수 있습니다.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="handling-output-from-a-function"></a>함수의 출력 처리

Runbook 함수에서 출력 스트림에 쓸 때 출력은 runbook으로 다시 전달 됩니다. Runbook이 해당 출력을 변수에 할당 하면 출력은 출력 스트림에 기록 되지 않습니다. 함수 내에서 다른 스트림에 기록하면 Runbook의 해당하는 스트림에 기록됩니다. 다음 샘플 PowerShell 워크플로 runbook을 참조 하세요.

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

Runbook 작업의 출력 스트림은 다음과 같습니다.

```output
Output inside of function
Output outside of function
```

Runbook 작업에 대 한 자세한 정보 스트림은 다음과 같습니다.

```output
Verbose outside of function
Verbose inside of function
```

Runbook을 게시 하 고 시작 하기 전에 runbook 설정에서 자세한 정보 로깅을 설정 하 여 자세한 정보 표시 스트림 출력을 가져와야 합니다.

### <a name="declaring-output-data-type"></a>출력 데이터 형식 선언

다음은 출력 데이터 형식의 예입니다.

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>워크플로에서 출력 데이터 형식 선언

워크플로는 [OutputType 특성](https://technet.microsoft.com/library/hh847785.aspx)을 사용 하 여 출력의 데이터 형식을 지정 합니다. 이 특성은 런타임 중에는 영향을 주지 않지만 runbook의 예상 출력 디자인 타임에 표시를 제공 합니다. Runbook에 대 한 도구 집합이 계속 진화 하기 때문에 디자인 타임에 출력 데이터 형식을 선언 하는 중요성이 증가 합니다. 따라서 사용자가 만드는 모든 runbook에이 선언을 포함 하는 것이 가장 좋습니다.

다음 샘플 runbook은 문자열 개체를 출력하고 해당 출력 형식의 선언을 포함합니다. Runbook이 특정 유형의 배열을 출력하면 형식의 배열과 달리 형식을 지정해야 합니다.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>그래픽 runbook에서 출력 데이터 형식 선언

그래픽 또는 그래픽 PowerShell 워크플로 runbook에서 출력 형식을 선언 하려면 **입력 및 출력** 메뉴 옵션을 선택 하 고 출력 유형을 입력 하면 됩니다. 부모 runbook에서 참조 하는 경우 형식을 쉽게 식별할 수 있도록 전체 .NET 클래스 이름을 사용 하는 것이 좋습니다. 전체 이름을 사용 하면 클래스의 모든 속성이 runbook의 databus에 노출 되 고 속성이 조건부 논리, 로깅 및 다른 runbook 작업의 값으로 사용 되는 경우 유연성이 향상 됩니다.<br> ![Runbook 입력 및 출력 옵션](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>입력 및 출력 속성 창의 **출력 형식** 필드에 값을 입력 한 후에는 해당 항목을 인식할 수 있도록 컨트롤 바깥쪽을 클릭 해야 합니다.

다음 예에서는 입력 및 출력 기능을 보여 주는 두 개의 그래픽 runbook을 보여 줍니다. 모듈식 runbook 디자인 모델을 적용 하는 경우 실행 계정을 사용 하 여 Azure로 인증을 관리 하는 runbook을 인증 하는 runbook이 하나 있습니다. 일반적으로 특정 시나리오를 자동화 하는 핵심 논리를 수행 하는 두 번째 runbook은 Runbook 인증 템플릿을 실행 합니다. 테스트 출력 창에 결과를 표시 합니다. 정상적인 상황에서 자식 Runbook의 출력을 활용하는 리소스에 대해 이 Runbook이 작업을 수행하도록 합니다.

**AuthenticateTo-Azure** Runbook의 기본 논리는 다음과 같습니다.<br> ![Runbook 템플릿 예제 인증](media/automation-runbook-output-and-messages/runbook-authentication-template.png)할 경우 Azure 관리 포털에서 각 스트림 및 동작에 대한 간략한 설명을 제공합니다.

Runbook에는 인증 프로필 속성을 반환 하는 `Microsoft.Azure.Commands.Profile.Models.PSAzureContext`출력 형식이 포함 되어 있습니다.<br> ![Runbook 출력 형식 예제](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

이 runbook은 간단 하지만 여기에서 호출할 하나의 구성 항목이 있습니다. 마지막 작업은 `Write-Output` cmdlet을 실행 하 여 `Inputobject` 매개 변수에 대 한 PowerShell 식을 사용 하 여 변수에 프로필 데이터를 씁니다. 이 매개 변수는 `Write-Output`에 필요 합니다.

이 예제의 두 번째 runbook 인 **Test-ChildOutputType**은 두 활동을 정의 합니다.<br> ![예제 자식 출력 형식 Runbook](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

첫 번째 작업은 **authenticateto-azure** runbook을 호출 합니다. 두 번째 활동은 **데이터 원본이** **활동 출력**으로 설정 된 `Write-Verbose` cmdlet을 실행 합니다. 또한 **필드 경로** 는 **authenticateto-azure** runbook의 컨텍스트 출력 인 **SubscriptionName**로 설정 됩니다.<br> ![쓰기-자세한 Cmdlet 매개 변수 데이터 원본](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

결과 출력은 구독의 이름입니다.<br> ![Test-ChildOutputType Runbook 결과](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="message-streams"></a>메시지 스트림

출력 스트림과는 달리 메시지 스트림은 사용자에 게 정보를 전달 합니다. 여러 종류의 정보에 대 한 여러 메시지 스트림이 있으며 Azure Automation는 각 스트림을 다르게 처리 합니다.

### <a name="warning-and-error-streams"></a>경고 및 오류 스트림

경고 및 오류 스트림은 runbook에서 발생 하는 문제를 기록 합니다. Azure Automation는 runbook을 실행할 때 이러한 스트림을 작업 기록에 기록 합니다. 자동화는 runbook을 테스트할 때 Azure Portal의 테스트 출력 창에 스트림을 포함 합니다. 

기본적으로 runbook은 경고나 오류가 발생 한 후에도 계속 실행 됩니다. Runbook이 메시지를 만들기 전에 [기본 설정 변수](#preference-variables) 를 설정 하도록 하 여 경고 또는 오류 시 runbook이 일시 중단 되도록 지정할 수 있습니다. 예를 들어 runbook이 예외와 마찬가지로 오류로 인해 일시 중단 되도록 하려면 `ErrorActionPreference` 변수를 Stop으로 설정 합니다.

[Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) 또는 [Write-Error](https://technet.microsoft.com/library/hh849962.aspx) cmdlet를 사용하여 경고 또는 오류 메시지를 만듭니다. 활동은 경고 및 오류 스트림에 쓸 수도 있습니다.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="debug-stream"></a>디버그 스트림

Azure Automation는 대화형 사용자를 위해 디버그 메시지 스트림을 사용 합니다. Runbook에서는 사용 하면 안 됩니다.

### <a name="verbose-stream"></a>자세한 정보 표시 스트림

자세한 정보 메시지 스트림은 runbook 작업에 대 한 일반 정보를 지원 합니다. Runbook에서는 디버그 스트림을 사용할 수 없으므로 디버그 정보에 대 한 자세한 정보 메시지를 사용 해야 합니다. 

기본적으로 작업 기록은 게시 된 runbook의 자세한 정보 메시지를 저장 하지 않으며 성능상의 이유로 수행 됩니다. 자세한 정보 메시지를 저장 하려면 자세한 정보 표시 **레코드** 설정을 사용 하 여 Azure Portal **구성** 탭을 사용 하 여 게시 된 runbook에서 자세한 정보 메시지를 기록 하도록 구성 합니다. 옵션을 켜서 문제를 해결하거나 runbook 디버그합니다. 대부분의 경우에는 자세한 정보 표시 레코드를 기록 하지 않는 기본 설정을 유지 해야 합니다.

[Runbook을 테스트](automation-testing-runbook.md)할 경우 Runbook이 자세한 정보 표시 레코드를 기록하도록 구성되면 자세한 정보 표시 메시지는 표시되지 않습니다. [Runbook을 테스트](automation-testing-runbook.md)하는 동안 자세한 정보 메시지를 표시 하려면 계속 하려면 `VerbosePreference` 변수를 설정 해야 합니다. 해당 변수를 설정 하면 자세한 정보 메시지가 Azure Portal의 테스트 출력 창에 표시 됩니다.

다음 코드는 [쓰기 세부 정보](https://technet.microsoft.com/library/hh849951.aspx) 표시 cmdlet을 사용 하 여 자세한 정보 메시지를 만듭니다.

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

## <a name="progress-records"></a>진행률 레코드

Azure Portal **구성** 탭을 사용 하 여 진행률 레코드를 기록 하도록 runbook을 구성할 수 있습니다. 기본 설정은 레코드를 기록 하지 않고 성능을 최대화 하는 것입니다. 대부분의 경우에는 기본 설정을 유지 해야 합니다. 옵션을 켜서 문제를 해결하거나 runbook 디버그합니다. 

진행률 레코드 로깅을 사용 하도록 설정 하면 runbook은 각 작업이 실행 되기 전과 후에 레코드를 작업 기록에 기록 합니다. Runbook을 테스트 하면 진행률 레코드를 기록 하도록 runbook이 구성 된 경우에도 진행률 메시지가 표시 되지 않습니다.

>[!NOTE]
>[Write-progress](https://technet.microsoft.com/library/hh849902.aspx) cmdlet은 대화형 사용자로 사용하기 위한 것이기 때문에 Runbook에 유효하지 않습니다.

## <a name="preference-variables"></a>기본 설정 변수

Runbook에서 특정 Windows PowerShell [기본 설정 변수](https://technet.microsoft.com/library/hh847796.aspx) 를 설정 하 여 다른 출력 스트림으로 전송 되는 데이터에 대 한 응답을 제어할 수 있습니다. 다음 표에서는 runbook에서 사용할 수 있는 기본 설정 변수와 기본 값 및 유효한 값을 보여 줍니다. Azure Automation 외부의 Windows PowerShell에서 사용 하는 경우 기본 설정 변수에 대 한 추가 값을 사용할 수 있습니다.

| 변수 | 기본값 | 유효한 값 |
|:--- |:--- |:--- |
| `WarningPreference` |계속 |중지<br>계속<br>SilentlyContinue |
| `ErrorActionPreference` |계속 |중지<br>계속<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |중지<br>계속<br>SilentlyContinue |

다음 표에서는 runbook에서 유효한 기본 설정 변수 값에 대 한 동작을 나열 합니다.

| 값 | 동작 |
|:--- |:--- |
| 계속 |메시지를 기록하고 runbook 실행을 계속합니다. |
| SilentlyContinue |메시지를 기록하지 않고 Runbook을 계속 실행합니다. 이 값은 메시지를 무시하는 효과가 있습니다. |
| 중지 |메시지를 기록하고 runbook을 일시 중단합니다. |

## <a name="runbook-output"></a>Runbook 출력 및 메시지 검색

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Azure Portal에서 runbook 출력 및 메시지 검색

Runbook에 대 한 **작업** 탭을 사용 하 여 Azure Portal에서 runbook 작업의 세부 정보를 볼 수 있습니다. 작업 요약에는 작업 및 발생 한 예외에 대 한 일반 정보 외에도 입력 매개 변수 및 [출력 스트림이](#output-stream)표시 됩니다. 작업 기록에는 출력 스트림 및 [경고 및 오류 스트림의](#warning-and-error-streams)메시지가 포함 됩니다. Runbook이 자세한 정보 표시 및 진행률 레코드를 기록 하도록 구성 된 경우 [자세한 정보 표시 스트림](#verbose-stream) 및 [진행률 레코드](#progress-records) 의 메시지도 포함 됩니다.

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Windows PowerShell에서 runbook 출력 및 메시지 검색

Windows PowerShell에서는 [AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) cmdlet을 사용 하 여 runbook에서 출력과 메시지를 검색할 수 있습니다. 이 cmdlet에는 작업의 ID가 필요 하며 검색할 스트림을 지정할 `Stream` 라는 매개 변수가 있습니다. 이 매개 변수에 값을 지정 하 여 작업에 대 한 모든 스트림을 검색할 수 있습니다.

다음 예제는 샘플 runbook를 시작한 다음 완료되기를 기다립니다. Runbook의 실행이 완료 되 면 스크립트는 작업에서 runbook 출력 스트림을 수집 합니다.

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

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>그래픽 runbook에서 runbook 출력 및 메시지 검색

그래픽 runbook의 경우 출력 및 메시지에 대 한 추가 로깅은 활동 수준 추적 형식으로 제공 됩니다. 추적에는 기본 추적과 자세히 추적이 있습니다. 기본 추적은 runbook의 각 작업에 대 한 시작 및 종료 시간과 활동 다시 시도와 관련 된 정보를 표시 합니다. 몇 가지 예는 활동의 시도 횟수와 시작 시간입니다. 자세한 추적에는 기본 추적 기능과 각 작업의 입력 및 출력 데이터 로깅이 포함 됩니다. 

현재 활동 수준 추적은 자세한 정보 표시 스트림을 사용 하 여 레코드를 기록 합니다. 따라서 추적을 사용 하도록 설정 하는 경우 자세한 정보 로깅을 사용 하도록 설정 해야 합니다. 추적이 활성화된 그래픽 Runbook의 경우 진행률 레코드를 기록할 필요가 없습니다. 기본 추적은 동일한 용도를 제공하며 정보가 더욱 자세합니다.

![그래픽 작성 작업 스트림 보기](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

그래픽 runbook에 대 한 자세한 정보 로깅 및 추적을 사용 하면 프로덕션 **작업 스트림** 보기에서 훨씬 더 많은 정보를 사용할 수 있는 이미지에서 볼 수 있습니다. 이 추가 정보는 runbook을 사용 하 여 프로덕션 문제를 해결 하는 데 필수적입니다. 

그러나 문제 해결을 위해 runbook의 진행률을 추적 하는 데이 정보가 필요 하지 않으면 일반적인 방법으로 추적을 해제 하는 것이 좋습니다. 추적 레코드는 특히 여러 가지가 있을 수 있습니다. 그래픽 runbook 추적을 사용 하면 기본 또는 자세한 추적의 구성에 따라 작업당 2 ~ 4 개의 레코드를 가져올 수 있습니다.

**활동 수준 추적을 설정 하려면:**

1. Azure Portal에서 Automation 계정을 엽니다.
2. **프로세스 자동화** 섹션에서 **runbook** 을 선택 하 여 runbook 목록을 엽니다.
3. Runbook 페이지의 runbook 목록에서 그래픽 runbook을 선택 합니다.
4. **설정**에서 **로깅 및 추적**을 클릭합니다.
5. 로깅 및 추적 페이지의 **상세 레코드**기록에서 자세한 정보 로깅을 사용 **하려면 설정을 클릭 합니다** .
6. **작업 수준 추적**에서 필요한 추적 수준에 따라 추적 수준을 **기본** 또는 **자세히**로 변경 합니다.<br>

   ![그래픽 작성 로깅 및 추적 페이지](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Microsoft Azure 모니터 로그에서 runbook 출력 및 메시지 검색

Azure Automation는 runbook 작업 상태 및 작업 스트림을 Log Analytics 작업 영역으로 보낼 수 있습니다. Azure Monitor는 다음을 수행할 수 있는 로그를 지원 합니다.

* Automation 작업에 대한 통찰력 확보
* Runbook 작업 상태 (예: 실패 또는 일시 중단)를 기반으로 전자 메일 또는 경고를 트리거합니다.
* 작업 스트림에서 고급 쿼리를 작성 합니다.
* Automation 계정 간에 작업 상호 연결
* 작업 기록 시각화

작업 데이터를 수집 하 고, 상관 관계를 설정 하 고, 작업을 수행 하기 위해 Azure Monitor 로그와의 통합을 구성 하는 방법에 대 한 자세한 내용은 [자동화에서 작업 상태 및 작업 스트림을 Azure Monitor 로그로 전달](automation-manage-send-joblogs-log-analytics.md)

## <a name="next-steps"></a>다음 단계

* Runbook 실행, runbook 작업 모니터링 및 기타 기술 세부 정보에 대해 자세히 알아보려면 [runbook 작업 추적](automation-runbook-execution.md)을 참조 하세요.
* 자식 runbook을 디자인 하 고 사용 하는 방법을 이해 하려면 [Azure Automation의 자식 runbook](automation-child-runbooks.md)을 참조 하세요.
* 언어 참조 및 학습 모듈을 비롯 한 PowerShell에 대 한 자세한 내용은 [Powershell 문서](/powershell/scripting/overview)를 참조 하세요.
