---
title: Runbook 입력 매개 변수
description: Runbook 입력 매개 변수는 Runbook이 시작될 때 Runbook에 데이터를 전달할 수 있도록 하여 Runbook의 유용성을 늘립니다. 이 문서에서는 입력 매개 변수가 사용되는 Runbook의 다양한 시나리오를 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 3741adbab6bcbc68f266c331e3056013afc0105e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656022"
---
# <a name="runbook-input-parameters"></a>Runbook 입력 매개 변수

Runbook 입력 매개 변수는 시작할 때 데이터를 전달할 수 있도록 하여 Runbook의 유연성을 높입니다. 이러한 매개 변수를 사용하면 Runbook 작업을 특정 시나리오 및 환경에 대한 대상으로 지정할 수 있습니다. 이 문서에서는 Runbook에서 입력 매개 변수의 구성 및 사용에 대해 설명합니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 모듈을 업데이트하는 방법을](automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="configuring-input-parameters"></a>입력 매개 변수 구성

PowerShell, PowerShell 워크플로, 그래픽 및 파이썬 런북에 대한 입력 매개 변수를 구성할 수 있습니다. Runbook에는 여러 데이터 형식을 가진 여러 매개 변수가 있거나 매개 변수가 전혀 없을 수 있습니다. 입력 매개 변수는 필수 또는 선택 사항일 수 있으며 선택적 매개 변수에 대한 기본값을 사용할 수 있습니다.

Runbook을 시작할 때 Runbook의 입력 매개 변수에 값을 할당합니다. Azure 포털, 웹 서비스 또는 PowerShell에서 Runbook을 시작할 수 있습니다. 또한 다른 Runbook에서 인라인으로 호출되는 하위 Runbook으로 시작할 수 있습니다.

### <a name="configure-input-parameters-in-powershell-runbooks"></a>PowerShell Runbook에서 입력 매개 변수 구성

Azure 자동화의 PowerShell 및 PowerShell 워크플로런북은 다음 속성을 통해 정의된 입력 매개 변수를 지원합니다. 

| **속성** | **설명** |
|:--- |:--- |
| Type |필수 사항입니다. 매개 변수 값에 필요한 데이터 형식입니다. 모든 .NET 형식이 유효합니다. |
| 이름 |필수 사항입니다. 매개 변수의 이름입니다. 이 이름은 Runbook 내에서 고유해야 하며 문자로 시작해야 하며 문자, 숫자 또는 밑줄 문자만 포함할 수 있습니다. |
| 필수 |(선택 사항) 매개 변수에 값이 필요한지 지정하는 부울 값입니다. True로 설정하면 Runbook이 시작될 때 값을 제공해야 합니다. False로 설정하면 값은 선택 사항입니다. `Mandatory` 속성에 대한 값을 지정하지 않으면 PowerShell은 입력 매개 변수를 기본적으로 선택 사항으로 간주합니다. |
| 기본값 |(선택 사항) Runbook이 시작될 때 입력 값이 전달되지 않으면 매개 변수에 사용되는 값입니다. Runbook은 모든 매개 변수에 대한 기본값을 설정할 수 있습니다. |

Windows PowerShell은 유효성 검사, 별칭 및 매개 변수 집합과 같이 위에 나열된 것보다 더 많은 입력 매개 변수 특성을 지원합니다. 그러나 Azure Automation은 현재 나열된 입력 매개 변수 속성만 지원합니다.

예를 들어 PowerShell 워크플로 런북에서 매개 변수 정의를 살펴보겠습니다. 이 정의에는 여러 매개 변수가 쉼표로 구분되는 다음과 같은 일반 형식이 있습니다.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

이제 단일 VM 또는 리소스 그룹 내의 모든 VM중 하나인 가상 컴퓨터에 대한 세부 정보를 출력하는 PowerShell 워크플로 우송자 런북의 입력 매개 변수를 구성해 보겠습니다. 이 Runbook에는 다음 스크린샷과 같이 가상 컴퓨터() 이름()과`VMName`리소스 그룹 이름()의`resourceGroupName`두 가지 매개 변수가 있습니다.

![Automation PowerShell 워크플로](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

이 매개 변수 정의에서 입력 매개 변수는 형식 문자열의 간단한 매개 변수입니다.

PowerShell 및 PowerShell 워크플로런북은 입력 매개 변수와 `Object` `PSCredential` 같은 모든 단순 유형 및 복잡한 형식을 지원합니다. Runbook에 개체 입력 매개 변수가 있는 경우 이름 값 쌍이 있는 PowerShell 해시 테이블을 사용하여 값을 전달해야 합니다. 예를 들어 Runbook에 다음 매개 변수가 있습니다.

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

이 경우 다음 값을 매개 변수에 전달할 수 있습니다.

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> null 기본값이 있는 선택적 String 매개 변수에 값을 전달하지 않으면 매개 변수의 값은 Null 대신 빈 문자열입니다.

### <a name="configure-input-parameters-in-graphical-runbooks"></a>그래픽 Runbook에서 입력 매개 변수 구성

그래픽 Runbook에 대한 입력 매개 변수의 구성을 설명하기 위해 단일 VM 또는 리소스 그룹 내의 모든 VM중 하나인 가상 시스템에 대한 세부 정보를 출력하는 Runbook을 만들어 보겠습니다. 자세한 내용은 [내 첫 번째 그래픽 실행책을](automation-first-runbook-graphical.md)참조하십시오.

그래픽 Runbook은 다음과 같은 주요 Runbook 활동을 사용합니다.

* Azure Run As 계정의 구성으로 Azure를 사용하여 인증합니다. 
* VM 속성을 얻으려면 [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) cmdlet의 정의입니다.
* [쓰기 출력](/powershell/module/microsoft.powershell.utility/write-output) 활동을 사용하여 VM 이름을 출력합니다. 

활동은 `Get-AzVM` VM 이름과 리소스 그룹 이름이라는 두 가지 입력을 정의합니다. Runbook이 시작될 때마다 이러한 이름은 다를 수 있으므로 이러한 입력을 허용하려면 Runbook에 입력 매개 변수를 추가해야 합니다. Azure [자동화에서 그래픽 작성을](automation-graphical-authoring-intro.md)참조하십시오.

다음 단계에 따라 입력 매개 변수를 구성합니다.

1. Runbook 페이지에서 그래픽 런북을 선택한 다음 **편집을**클릭합니다.
2. 그래픽 편집기에서 입력 **및 출력** 버튼을 클릭한 다음 **입력을 추가하여** Runbook 입력 매개 변수 창을 엽니다.

   ![자동화 그래픽 Runbook](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. 입력 및 출력 컨트롤은 Runbook에 대해 정의된 입력 매개 변수 목록을 표시합니다. 여기서 새 입력 매개 변수를 추가하거나 기존 입력 매개 변수의 구성을 편집할 수 있습니다. Runbook에 대한 새 매개 변수를 추가하려면 **입력 추가를** 클릭하여 **Runbook 입력 매개 변수** 블레이드를 열고 Azure [Automation에서 그래픽 작성에](automation-graphical-authoring-intro.md)정의된 속성을 사용하여 매개 변수를 구성할 수 있습니다.

    ![새 입력 추가](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. 활동에 사용할 다음 속성으로 두 개의 `Get-AzVM` 매개 변수를 만든 다음 **확인을**클릭합니다.

   * 매개 변수 1:
        * **이름** -- **VMName**
        * **유형** -- 문자열
        * **필수** -- **없음**

   * 매개 변수 2:
        * **이름** -- **리소스그룹이름**
        * **유형** -- 문자열
        * **필수** -- **없음**
        * **기본값** -- **사용자 지정**
        * 사용자 지정 기본값 -- VM을 포함하는 리소스 그룹의 이름

5. 입력 및 출력 컨트롤의 매개 변수를 봅니다. 
6. **확인을** 다시 클릭한 다음 **에 저장을**클릭합니다.
7. **게시를** 클릭하여 Runbook을 게시합니다.

### <a name="configure-input-parameters-in-python-runbooks"></a>Python Runbook에서 입력 매개 변수 구성

PowerShell, PowerShell 워크플로, 그래픽 런북과 달리 파이썬 런북은 명명된 매개 변수를 사용하지 않습니다. Runbook 편집기는 모든 입력 매개 변수를 인수 값의 배열로 구문 분석합니다. `sys` 모듈을 Python 스크립트로 가져온 다음 배열을 사용하여 배열에 `sys.argv` 액세스 할 수 있습니다. 배열의 `sys.argv[0]`첫 번째 요소는 스크립트의 이름입니다. 따라서 첫 번째 실제 `sys.argv[1]`입력 매개 변수는 .

Python Runbook에서 입력 매개 변수를 사용하는 방법의 예제를 보려면 [Azure Automation의 내 첫 번째 Python Runbook](automation-first-runbook-textual-python2.md)을 참조하세요.

## <a name="assigning-values-to-input-parameters-in-runbooks"></a>Runbook의 입력 매개 변수에 값 할당

이 섹션에서는 Runbook의 입력 매개 변수에 값을 전달하는 몇 가지 방법을 설명합니다. 다음과 같은 경우 매개 변수 값을 할당할 수 있습니다.

* [Runbook 시작](#start-a-runbook-and-assign-parameters)
* [Runbook 테스트](#test-a-runbook-and-assign-parameters)
* [Runbook에 대한 일정 연결](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Runbook에 대한 웹후크 만들기](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Runbook 시작 및 매개 변수 할당

Runbook은 Azure 포털, 웹후크, PowerShell cmdlet, REST API 또는 SDK를 통해 여러 가지 방법으로 시작할 수 있습니다. 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Azure 포털을 사용하여 게시된 Runbook을 시작하고 매개 변수를 할당합니다.

Azure 포털에서 [Runbook을 시작하면](start-runbooks.md#start-a-runbook-with-the-azure-portal) **Runbook 시작** 블레이드가 열리고 만든 매개 변수에 대한 값을 입력할 수 있습니다.

![포털을 사용하여 시작하기](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

입력 상자 아래의 레이블에서 필수 또는 선택적, 유형, 기본값 과 같은 매개 변수 특성을 정의하도록 설정된 속성을 볼 수 있습니다. 매개 변수 이름 옆에 있는 도움말 풍선은 매개 변수 입력 값에 대한 결정을 내리는 데 필요한 주요 정보도 정의합니다. 

> [!NOTE]
> 문자열 매개 변수는 문자열 형식의 빈 값을 지원합니다. 입력 `[EmptyString]` 매개 변수 상자에 입력하면 빈 문자열이 매개 변수에 전달됩니다. 또한 문자열 매개 변수Null을 지원 하지 않습니다. 문자열 매개 변수에 값을 전달하지 않으면 PowerShell이 값을 Null로 해석합니다.

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>PowerShell cmdlet을 사용하여 게시된 Runbook을 시작하고 매개 변수할당

* **Azure 리소스 관리자 cmdlet:** [시작-AzAutomationRunbook을](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.5.0
)사용 하 여 리소스 그룹에서 만든 자동화 실행 책을 시작할 수 있습니다.

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Azure 클래식 배포 모델 cmdlet: **[Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook)을 사용하여 기본 리소스 그룹에 생성된 자동화 Runbook을 시작할 수 있습니다
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> PowerShell cmdlet을 사용하여 Runbook을 시작하면 기본 `MicrosoftApplicationManagementStartedBy`매개 변수인 `PowerShell`에서 값이 만들어집니다. 작업 세부 정보 창에서 이 매개변수를 볼 수 있습니다.  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>SDK를 사용하여 Runbook을 시작하고 매개 변수를 할당합니다.

* **Azure 리소스 관리자 메서드:** 프로그래밍 언어의 SDK를 사용하여 Runbook을 시작할 수 있습니다. 다음은 Automation 계정의 Runbook을 시작하기 위한 C# 코드 조각입니다. [GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)에서 모든 코드를 볼 수 있습니다.

   ```csharp
   public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
      {
        var response = AutomationClient.Jobs.Create(resourceGroupName, automationAccount, new JobCreateParameters
         {
            Properties = new JobCreateProperties
             {
                Runbook = new RunbookAssociationProperty
                 {
                   Name = runbookName
                 },
                   Parameters = parameters
             }
         });
      return response.Job;
      }
   ```

* **Azure 클래식 배포 모델 방법:** 프로그래밍 언어의 SDK를 사용하여 Runbook을 시작할 수 있습니다. 다음은 Automation 계정의 Runbook을 시작하기 위한 C# 코드 조각입니다. [GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)에서 모든 코드를 볼 수 있습니다.

   ```csharp
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
    {
      var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
         {
           Runbook = new RunbookAssociationProperty
         {
           Name = runbookName
              },
                Parameters = parameters
              }
       });
      return response.Job;
    }
   ```

   이 메서드를 시작 하려면 Runbook 매개 변수 `VMName` `resourceGroupName` 및 해당 값을 저장 하는 사전을 만듭니다. 그런 다음 Runbook을 시작합니다. 다음은 위에 정의된 메서드를 호출하기 위한 C# 코드 조각입니다.

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>REST API를 사용하여 Runbook을 시작하고 매개 변수를 할당합니다.

다음 요청 URI와 함께 메서드를 사용 하 여 `PUT` Azure 자동화 REST API를 사용 하 여 Runbook 작업을 만들고 시작할 수 있습니다.`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

요청 URI에서 다음 매개 변수를 바꿉니다.

* `subscriptionId`: Azure 구독 ID입니다.  
* `resourceGroupName`: 자동화 계정의 리소스 그룹의 이름입니다.
* `automationAccountName`: 지정된 클라우드 서비스 내에서 호스팅되는 자동화 계정의 이름입니다.  
* `jobName`: 작업에 대한 GUID입니다. PowerShell의 GUID를 사용하여 `[GUID]::NewGuid().ToString()*`만들 수 있습니다.

Runbook 작업에 매개 변수를 전달하려면 요청 본문을 사용합니다. JSON 형식으로 제공되는 다음 정보를 받습니다.

* Runbook 이름: 필수입니다. 시작할 작업 시작에 대한 Runbook의 이름입니다.  
* Runbook 매개 변수: 선택 사항입니다. 이름이 문자열 형식이고 값이 유효한 JSON 값이 될 수 있는 매개 변수 목록(이름, 값) 형식의 사전입니다.

이전 `VMName` 및 `resourceGroupName` 매개 변수로 만든 **Get-AzureVMTextual** Runbook을 시작하려면 요청 본문에 다음 JSON 형식을 사용합니다.

```json
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WindowsVM",
         "resourceGroupName":"ContosoSales"}
        }
    }
```

작업이 성공적으로 만들어진 경우 HTTP 상태 코드 201이 반환됩니다. 응답 헤더 및 응답 본문에 대한 자세한 내용은 [REST API를 사용하여 Runbook 작업 만들기를](/rest/api/automation/job/create)참조하십시오.

### <a name="test-a-runbook-and-assign-parameters"></a>Runbook 테스트 및 매개 변수 할당

테스트 옵션을 사용하여 [Runbook의 초안 버전을 테스트하면](automation-testing-runbook.md) 테스트 페이지가 열립니다. 이 페이지를 사용하여 만든 매개 변수에 대한 값을 구성합니다.

![매개 변수 테스트 및 할당](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Runbook에 일정 연결 및 매개 변수 할당

Runbook이 특정 시간에 시작하도록 Runbook에 [일정을 연결](automation-schedules.md)할 수 있습니다. 일정을 만드는 동안 입력 매개 변수를 할당하고 Runbook이 일정에 따라 시작될 때 이러한 값을 사용합니다. 모든 필수 매개 변수 값이 제공될 때까지 일정을 저장할 수 없습니다.

![매개 변수 예약 및 할당](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Runbook에 대한 webhook 만들기 및 매개 변수 할당

Runbook에 대한 [webhook](automation-webhooks.md) 을 만들고 Runbook 입력 매개 변수를 구성할 수 있습니다. 모든 필수 매개 변수 값이 제공될 때까지 웹후크를 저장할 수 없습니다.

![Webhook 만들기 및 매개 변수 할당](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

웹후크를 사용하여 Runbook을 실행하면 정의한 입력 `[WebhookData](automation-webhooks.md)` 매개 변수와 함께 미리 정의된 입력 매개 변수가 전송됩니다. 

![WebhookData 매개 변수](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="passing-a-json-object-to-a-runbook"></a>JSON 개체를 Runbook에 전달

JSON 파일에서 Runbook에 전달하려는 데이터를 저장하는 것이 유용할 수 있습니다. 예를 들어 Runbook에 전달하려는 모든 매개 변수를 포함하는 JSON 파일을 만들 수 있습니다. 이렇게 하려면 JSON 코드를 문자열로 변환한 다음 문자열을 PowerShell 개체로 변환한 다음 Runbook에 전달해야 합니다.

이 섹션에서는 PowerShell 스크립트가 [Start-AzAutomationRunbook을](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) 호출하여 PowerShell 실행책을 시작하여 JSON 파일의 내용을 Runbook에 전달하는 예제를 사용합니다. PowerShell 실행북은 JSON 개체에서 VM에 대한 매개 변수를 검색하여 Azure VM을 시작합니다.

### <a name="create-the-json-file"></a>JSON 파일 만들기

텍스트 파일에 다음 코드를 입력하고 로컬 **컴퓨터의 어딘가에 test.json으로** 저장합니다.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Runbook 만들기

Azure 자동화에서 **Test-Json이라는** 새 PowerShell 실행책을 만듭니다. [내 첫 번째 PowerShell 실행록을](automation-first-runbook-textual-powershell.md)참조하십시오.

JSON 데이터를 허용하려면 Runbook에서 개체를 입력 매개 변수로 사용해야 합니다. 그런 다음 Runbook에서 JSON 파일에 정의된 속성을 사용할 수 있습니다.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

이 Runbook을 Automation 계정에 저장하고 게시합니다.

### <a name="call-the-runbook-from-powershell"></a>PowerShell에서 Runbook 호출

이제 로컬 컴퓨터에서 Azure PowerShell을 사용하여 Runbook을 호출할 수 있습니다. 

1. 그림과 같이 Azure에 로그인합니다. 그런 다음 Azure 자격 증명을 입력하라는 메시지가 표시됩니다.

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >PowerShell `Add-AzAccount` 런북의 `Add-AzureRMAccount` 경우 에 대한 `Connect-AzAccount`별칭입니다. 이러한 별칭은 그래픽 런북에 사용할 수 없습니다. 그래픽 런북은 자체만 `Connect-AzAccount` 사용할 수 있습니다.

1. 저장된 JSON 파일의 내용을 가져옵니다 및 문자열로 변환합니다. `JsonPath`은 JSON 파일을 저장한 경로를 나타냅니다.

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. PowerShell 개체의 `$json` 문자열 내용을 변환합니다.

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. 에 대한 매개 변수에 대한 `Start-AzAutomationRunbook`해시 테이블을 만듭니다. 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   `Parameters`의 값을 JSON 파일의 값이 포함된 PowerShell 개체로 설정하고 있는 것입니다.
1. Runbook을 시작합니다.

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>다음 단계

* Runbook을 시작하는 다양한 방법에 대한 자세한 내용은 [Runbook 시작](automation-starting-a-runbook.md)을 참조하세요.
* 텍스트 Runbook을 편집하려면 [텍스트 Runbook 편집](automation-edit-textual-runbook.md)을 참조하세요.
* 그래픽 Runbook을 편집하려면 [Azure Automation에서 그래픽 작성](automation-graphical-authoring-intro.md)을 참조하세요.
