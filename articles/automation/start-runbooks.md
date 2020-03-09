---
title: Azure Automation에서 runbook 시작
description: Azure Automation에서 runbook을 시작 하는 데 사용할 수 있는 여러 가지 방법을 요약 하 고 Azure Portal 및 Windows PowerShell을 사용 하는 방법에 대 한 세부 정보를 제공 합니다.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 71dd83db02537ed12dc2e711127e32d90603af6f
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373123"
---
# <a name="start-a-runbook-in-azure-automation"></a>Azure Automation에서 runbook 시작

다음 표에서는 특정 시나리오에 가장 적합 한 Azure Automation에서 runbook을 시작 하는 방법을 결정 하는 데 도움이 됩니다. 이 문서에는 Azure Portal 및 Windows PowerShell을 사용 하 여 runbook을 시작 하는 세부 정보가 포함 되어 있습니다. 다른 방법에 대 한 자세한 내용은 아래 링크에서 액세스할 수 있는 다른 설명서에서 제공 됩니다.

| **방법이** | **적인** |
| --- | --- |
| [Azure Portal](#start-a-runbook-with-the-azure-portal) |<li>대화형 사용자 인터페이스를 사용 하는 가장 간단한 방법입니다.<br> <li>단순한 매개 변수 값을 제공 하는 양식입니다.<br> <li>작업 상태를 쉽게 추적 합니다.<br> <li>Azure 로그인으로 인증 된 액세스. |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Windows PowerShell cmdlet을 사용 하 여 명령줄에서 호출 합니다.<br> <li>여러 단계로 자동화 된 솔루션에 포함할 수 있습니다.<br> <li>요청은 인증서 또는 OAuth 사용자 계정/서비스 사용자로 인증 됩니다.<br> <li>단순 하 고 복잡 한 매개 변수 값을 제공 합니다.<br> <li>작업 상태를 추적 합니다.<br> <li>클라이언트에서 PowerShell cmdlet을 지 원하는 데 필요 합니다. |
| [Azure Automation API](/rest/api/automation/) |<li>가장 유연한 방법 이지만 가장 복잡 합니다.<br> <li>HTTP 요청을 만들 수 있는 모든 사용자 지정 코드에서를 호출 합니다.<br> <li>인증서 또는 Oauth 사용자 계정/서비스 주체를 사용 하 여 요청을 인증 했습니다.<br> <li>단순 하 고 복잡 한 매개 변수 값을 제공 합니다. *API를 사용 하 여 Python runbook을 호출 하는 경우 JSON 페이로드를 직렬화 해야 합니다.*<br> <li>작업 상태를 추적 합니다. |
| [웹 후크](automation-webhooks.md) |<li>단일 HTTP 요청에서 runbook을 시작 합니다.<br> <li>URL의 보안 토큰으로 인증 됩니다.<br> <li>클라이언트는 webhook를 만들 때 지정 된 매개 변수 값을 재정의할 수 없습니다. Runbook은 HTTP 요청 세부 정보로 채워지는 단일 매개 변수를 정의할 수 있습니다.<br> <li>Webhook URL을 통해 작업 상태를 추적할 수 없습니다. |
| [Azure 경고에 응답](../log-analytics/log-analytics-alerts.md) |<li>Azure 경고에 대 한 응답으로 runbook을 시작 합니다.<br> <li>Runbook에 대 한 webhook을 구성 하 고 경고에 연결 합니다.<br> <li>URL의 보안 토큰으로 인증 됩니다. |
| [일정과](automation-schedules.md) |<li>매시간, 매일, 매주 또는 매월 일정에 따라 runbook을 자동으로 시작 합니다.<br> <li>Azure Portal, PowerShell cmdlet 또는 Azure API를 통해 일정을 조작 합니다.<br> <li>Schedule에 사용할 매개 변수 값을 제공 합니다. |
| [다른 Runbook에서](automation-child-runbooks.md) |<li>Runbook을 다른 runbook의 작업으로 사용 합니다.<br> <li>여러 runbook에서 사용 하는 기능에 유용 합니다.<br> <li>자식 runbook에 매개 변수 값을 제공 하 고 부모 runbook에서 출력을 사용 합니다. |

다음 이미지는 runbook의 수명 주기에서 자세한 단계별 프로세스를 보여 줍니다. Runbook은 Azure Automation에서 시작 하는 여러 가지 방법, 즉 Azure Automation runbook을 실행 하 고 여러 구성 요소 간의 상호 작용을 Hybrid Runbook Worker 하는 데 필요한 구성 요소를 포함 합니다. 데이터 센터에서 Automation runbook을 실행 하는 방법에 대 한 자세한 내용은 [hybrid runbook worker](automation-hybrid-runbook-worker.md) 를 참조 하세요.

![Runbook 아키텍처](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="start-a-runbook-with-the-azure-portal"></a>Azure Portal를 사용 하 여 runbook 시작

1. Azure Portal에서 **automation** 을 선택한 다음 automation 계정의 이름을 클릭 합니다.
2. 허브 메뉴에서 **runbook**을 선택 합니다.
3. **Runbook 페이지에서** runbook을 선택 하 고 **시작**을 클릭 합니다.
4. Runbook에 매개 변수가 있는 경우 각 매개 변수에 대 한 텍스트 상자와 함께 값을 제공 하 라는 메시지가 표시 됩니다. 매개 변수에 대 한 자세한 내용은 [Runbook 매개 변수](#runbook-parameters)를 참조 하세요.
5. **작업** 페이지에서 runbook 작업의 상태를 볼 수 있습니다.

## <a name="start-a-runbook-with-powershell"></a>PowerShell을 사용 하 여 runbook 시작

Windows PowerShell을 사용 하 여 runbook을 시작 하려면 [start-azurermautomationrunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) 를 사용할 수 있습니다. 다음 샘플 코드에서는 runbook runbook 이라는 runbook을 시작 합니다.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

Start-azurermautomationrunbook는 runbook이 시작 된 후 해당 상태를 추적 하는 데 사용할 수 있는 작업 개체를 반환 합니다. 그런 다음이 작업 개체를 [get-azurermautomationjob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) 와 함께 사용 하 여 작업 상태를 확인 하 고 [AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) 를 사용 하 여 해당 출력을 가져올 수 있습니다. 다음 샘플 코드는 runbook Runbook 이라는 runbook을 시작 하 고 완료 될 때까지 기다린 다음 해당 출력을 표시 합니다.

```azurepowershell-interactive
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzureRmAutomationRunbook –AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzureRmAutomationJob –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzureRmAutomationJobOutput –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup –Stream Output
```

Runbook에 매개 변수가 필요한 경우 [해시 테이블](https://technet.microsoft.com/library/hh847780.aspx)로 제공 해야 합니다. 해시 테이블의 키는 매개 변수 이름과 일치 해야 하며 값은 매개 변수 값입니다. 다음 예제에서는 FirstName 및 LastName 이라는 두 개의 문자열 매개 변수, RepeatCount 라는 정수 및 Show 라는 부울 매개 변수를 사용 하 여 runbook을 시작 하는 방법을 보여 줍니다. 매개 변수에 대 한 자세한 내용은 아래의 [Runbook 매개 변수](#runbook-parameters) 를 참조 하세요.

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="runbook-parameters"></a>Runbook 매개 변수

Azure Portal 또는 Windows PowerShell에서 runbook을 시작 하면 Azure Automation 웹 서비스를 통해 명령이 전송 됩니다. 이 서비스는 복잡 한 데이터 형식의 매개 변수를 지원 하지 않습니다. 복합 매개 변수의 값을 제공 해야 하는 경우 [Azure Automation의 자식 runbook](automation-child-runbooks.md)에 설명 된 대로 다른 runbook에서 인라인으로 호출 해야 합니다.

Azure Automation 웹 서비스는 다음 섹션에 설명 된 대로 특정 데이터 형식을 사용 하 여 매개 변수에 대 한 특수 기능을 제공 합니다.

### <a name="named-values"></a>명명 된 값

매개 변수가 [object] 데이터 형식인 경우 다음 JSON 형식을 사용 하 여 명명 된 값 목록을 전송할 수 있습니다. *{Name1: ' Value1 ', Name2: ' Value2 ', Name3: ' Value3 '}* . 이러한 값은 단순 형식 이어야 합니다. Runbook은 명명 된 각 값에 해당 하는 속성을 사용 하 여 [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject) 으로 매개 변수를 받습니다.

User 라는 매개 변수를 허용 하는 다음 테스트 runbook을 살펴보세요.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][object]$user
   )
    $userObject = $user | ConvertFrom-JSON
    if ($userObject.Show) {
        foreach ($i in 1..$userObject.RepeatCount) {
            $userObject.FirstName
            $userObject.LastName
        }
    }
}
```

사용자 매개 변수에 다음 텍스트를 사용할 수 있습니다.

```json
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

그러면 다음과 같은 결과가 출력 됩니다.

```output
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>어레이가

매개 변수가 [array] 또는 [string []]과 같은 배열인 경우 다음 JSON 형식을 사용 하 여 값 목록을 전송할 수 있습니다. *[Value1, Value2, Value3]* . 이러한 값은 단순 형식 이어야 합니다.

*User*라는 매개 변수를 허용 하는 다음 테스트 runbook을 살펴보세요.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][array]$user
   )
    if ($user[3]) {
        foreach ($i in 1..$user[2]) {
            $ user[0]
            $ user[1]
        }
    }
}
```

사용자 매개 변수에 다음 텍스트를 사용할 수 있습니다.

```input
["Joe","Smith",2,true]
```

그러면 다음과 같은 결과가 출력 됩니다.

```output
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>자격 증명

매개 변수가 데이터 형식 **PSCredential**인 경우 Azure Automation [자격 증명 자산의](automation-credentials.md)이름을 제공할 수 있습니다. Runbook은 사용자가 지정한 이름으로 자격 증명을 검색 합니다.

Credential 이라는 매개 변수를 허용 하는 다음 테스트 runbook을 고려 합니다.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

다음 텍스트는 *내 자격 증명*이라는 자격 증명 자산이 있다고 가정할 때 user 매개 변수에 사용할 수 있습니다.

```input
My Credential
```

자격 증명의 사용자 이름이 *jsmith*된 것으로 가정 하면 다음과 같은 결과가 출력 됩니다.

```output
jsmith
```

## <a name="next-steps"></a>다음 단계

* 현재 문서의 runbook 아키텍처는 Azure 및 온-프레미스에서 Hybrid Runbook Worker를 사용 하 여 리소스를 관리 하는 runbook에 대 한 개략적인 개요를 제공 합니다. 데이터 센터에서 Automation runbook을 실행 하는 방법에 대 한 자세한 내용은 [Hybrid Runbook worker](automation-hybrid-runbook-worker.md)를 참조 하세요.
* 특정 또는 일반 함수에 대해 다른 runbook에서 사용할 모듈식 runbook을 만드는 방법에 대 한 자세한 내용은 [자식 runbook](automation-child-runbooks.md)을 참조 하세요.
* 언어 참조 및 학습 모듈을 비롯 한 PowerShell에 대 한 자세한 내용은 [Powershell 문서](https://docs.microsoft.com/powershell/scripting/overview)를 참조 하세요.
