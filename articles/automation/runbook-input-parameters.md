---
title: Runbook 입력 매개 변수
description: Runbook 입력 매개 변수는 Runbook이 시작될 때 Runbook에 데이터를 전달할 수 있도록 하여 Runbook의 유용성을 늘립니다. 이 문서에서는 입력 매개 변수가 사용되는 Runbook의 다양한 시나리오를 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 274ee0fe98281e733994f2d5df38886409cbc913
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273642"
---
# <a name="runbook-input-parameters"></a>Runbook 입력 매개 변수

Runbook 입력 매개 변수는 데이터를 시작할 때 데이터를 전달할 수 있도록 하 여 runbook의 유연성을 향상 시킵니다. 이러한 매개 변수는 runbook 작업을 특정 시나리오 및 환경에 대 한 대상으로 지정할 수 있습니다. 이 문서에서는 runbook에서 입력 매개 변수를 구성 하 고 사용 하는 방법을 설명 합니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대 한 Az module 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조 하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트 하는 방법을](automation-update-azure-modules.md)사용 하 여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="configuring-input-parameters"></a>입력 매개 변수 구성

PowerShell, PowerShell 워크플로, 그래픽 및 Python runbook에 대 한 입력 매개 변수를 구성할 수 있습니다. Runbook에는 여러 데이터 형식을 가진 여러 매개 변수가 있거나 매개 변수가 전혀 없을 수 있습니다. 입력 매개 변수는 필수 또는 선택 사항이 며 선택적 매개 변수에 기본값을 사용할 수 있습니다.

Runbook을 시작할 때 runbook에 대 한 입력 매개 변수에 값을 할당 합니다. Azure Portal, 웹 서비스 또는 PowerShell에서 runbook을 시작할 수 있습니다. 또한 다른 Runbook에서 인라인으로 호출되는 하위 Runbook으로 시작할 수 있습니다.

### <a name="configure-input-parameters-in-powershell-runbooks"></a>PowerShell Runbook에서 입력 매개 변수 구성

Azure Automation PowerShell 및 PowerShell 워크플로 runbook은 다음 속성을 통해 정의 된 입력 매개 변수를 지원 합니다. 

| **속성** | **설명** |
|:--- |:--- |
| Type |필수 사항입니다. 매개 변수 값에 필요한 데이터 형식입니다. 모든 .NET 형식이 유효합니다. |
| 속성 |필수 사항입니다. 매개 변수의 이름입니다. 이 이름은 runbook 내에서 고유 해야 하 고, 문자로 시작 해야 하며, 문자, 숫자 또는 밑줄 문자만 포함할 수 있습니다. |
| 필수 |(선택 사항) 매개 변수에 값이 필요한 경우를 지정 하는 부울 값입니다. 이 값을 **true**로 설정 하면 runbook이 시작 될 때 값을 제공 해야 합니다. 이 값을 **false**로 설정 하면 값은 선택 사항입니다. **필수** 속성의 값을 지정 하지 않으면 PowerShell은 기본적으로 입력 매개 변수를 선택적으로 고려 합니다. |
| 기본값 |(선택 사항) Runbook이 시작 될 때 입력 값이 전달 되지 않는 경우 매개 변수에 사용 되는 값입니다. Runbook은 매개 변수에 대 한 기본값을 설정할 수 있습니다. |

Windows PowerShell은 유효성 검사, 별칭 및 매개 변수 집합과 같이 위에 나열 된 것 보다 더 많은 입력 매개 변수 특성을 지원 합니다. 그러나 현재 Azure Automation는 나열 된 입력 매개 변수 속성만 지원 합니다.

예를 들어 PowerShell 워크플로 runbook의 매개 변수 정의를 살펴보겠습니다. 이 정의의 일반적인 형식은 다음과 같습니다. 여기서 여러 매개 변수는 쉼표로 구분 됩니다.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

이제 단일 VM 또는 리소스 그룹 내의 모든 Vm과 같은 가상 컴퓨터에 대 한 세부 정보를 출력 하는 PowerShell 워크플로 runbook에 대 한 입력 매개 변수를 구성 하겠습니다. 이 runbook에는 가상 머신의 이름 (*VMName*)과 리소스 그룹 이름 (*resourceGroupName*)과 같이 두 개의 매개 변수가 있습니다.

![Automation PowerShell 워크플로](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

이 매개 변수 정의에서 입력 매개 변수는 문자열 형식의 단순 매개 변수입니다.

PowerShell 및 PowerShell 워크플로 runbook은 입력 매개 변수의 **개체** 또는 **PSCredential** 과 같은 모든 단순 형식 및 복합 형식을 지원 합니다. Runbook에 개체 입력 매개 변수가 있는 경우 이름-값 쌍이 있는 PowerShell 해시 테이블을 사용 하 여 값을 전달 해야 합니다. 예를 들어 runbook에는 다음 매개 변수가 있습니다.

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

이 경우 매개 변수에 다음 값을 전달할 수 있습니다.

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Null 기본값을 사용 하 여 선택적 문자열 매개 변수에 값을 전달 하지 않는 경우 매개 변수 값은 **null**이 아닌 빈 문자열입니다.

### <a name="configure-input-parameters-in-graphical-runbooks"></a>그래픽 Runbook에서 입력 매개 변수 구성

그래픽 runbook에 대 한 입력 매개 변수의 구성을 보여 주기 위해 단일 VM 또는 리소스 그룹 내의 모든 Vm과 같은 가상 컴퓨터에 대 한 세부 정보를 출력 하는 runbook을 만들어 보겠습니다. 자세한 내용은 [내 첫 번째 그래픽 runbook](automation-first-runbook-graphical.md)을 참조 하세요.

그래픽 runbook은 다음과 같은 주요 runbook 작업을 사용 합니다.

* Azure를 사용 하 여 인증 하는 Azure 실행 계정 구성 
* VM 속성을 가져오는 [new-azvm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) cmdlet에 대 한 정의입니다.
* [쓰기 출력](/powershell/module/microsoft.powershell.utility/write-output) 작업을 사용 하 여 VM 이름을 출력 합니다. 

**New-azvm** 활동은 두 개의 입력 인 VM 이름 및 리소스 그룹 이름을 정의 합니다. Runbook이 시작 될 때마다 이러한 이름이 다를 수 있으므로 입력 매개 변수를 runbook에 추가 하 여 이러한 입력을 수락 해야 합니다. [Azure Automation의 그래픽 작성](automation-graphical-authoring-intro.md)을 참조 하세요.

입력 매개 변수를 구성 하려면 다음 단계를 수행 합니다.

1. **Runbook** 페이지에서 그래픽 runbook을 선택 하 고 **편집**을 클릭 합니다.
2. 그래픽 편집기에서 **입력 및 출력** 단추를 클릭 한 다음 **입력 추가** 를 클릭 하 여 Runbook 입력 매개 변수 창을 엽니다.

   ![자동화 그래픽 Runbook](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. Input 및 Output 컨트롤은 runbook에 대해 정의 된 입력 매개 변수 목록을 표시 합니다. 여기서 새 입력 매개 변수를 추가 하거나 기존 입력 매개 변수의 구성을 편집할 수 있습니다. Runbook에 대 한 새 매개 변수를 추가 하려면 **입력 추가** 를 클릭 하 여 [Azure Automation에서 그래픽 제작](automation-graphical-authoring-intro.md)에 정의 된 속성을 사용 하 여 매개 변수를 구성할 수 있는 **runbook 입력 매개 변수** 블레이드를 엽니다.

    ![새 입력 추가](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. **New-azvm** 활동에서 사용할 다음 속성을 사용 하 여 두 개의 매개 변수를 만든 다음 **확인**을 클릭 합니다.

   * 매개 변수 1:
        * **이름** -- **VMName**
        * **유형** --문자열
        * **필수** -- **아니요**

   * 매개 변수 2:
        * **이름** -- **resourceGroupName**
        * **유형** --문자열
        * **필수** -- **아니요**
        * **사용자 지정** -- **기본값**
        * 사용자 지정 기본값-Vm을 포함 하는 리소스 그룹의 이름

5. Input 및 Output 컨트롤의 매개 변수를 확인 합니다. 
6. **확인** 을 다시 클릭 한 다음 **저장**을 클릭 합니다.
7. **게시** 를 클릭 하 여 runbook을 게시 합니다.

### <a name="configure-input-parameters-in-python-runbooks"></a>Python Runbook에서 입력 매개 변수 구성

PowerShell, PowerShell 워크플로 및 그래픽 runbook과 달리 Python runbook은 명명 된 매개 변수를 사용 하지 않습니다. Runbook 편집기는 모든 입력 매개 변수를 인수 값 배열로 구문 분석 합니다. Python 스크립트에 **sys** 모듈을 가져온 다음, **sys. argv** 배열을 사용 하 여 배열에 액세스할 수 있습니다. 배열의 첫 번째 요소인 `sys.argv[0]`는 스크립트의 이름입니다 (예를 들어, 따라서 첫 번째 실제 입력 매개 변수는 *sys. argv [1]* 입니다.

Python Runbook에서 입력 매개 변수를 사용하는 방법의 예제를 보려면 [Azure Automation의 내 첫 번째 Python Runbook](automation-first-runbook-textual-python2.md)을 참조하세요.

## <a name="assigning-values-to-input-parameters-in-runbooks"></a>Runbook의 입력 매개 변수에 값 할당

이 섹션에서는 runbook의 입력 매개 변수에 값을 전달 하는 여러 가지 방법에 대해 설명 합니다. 다음을 수행할 때 매개 변수 값을 할당할 수 있습니다.

* [Runbook 시작](#start-a-runbook-and-assign-parameters)
* [Runbook 테스트](#test-a-runbook-and-assign-parameters)
* [Runbook에 대 한 일정 연결](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Runbook에 대 한 webhook 만들기](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Runbook 시작 및 매개 변수 할당

Runbook은 여러 가지 방법으로 시작할 수 있습니다. Azure Portal, 웹 후크, PowerShell cmdlet, REST API 또는 SDK를 사용 합니다. 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Azure Portal를 사용 하 여 게시 된 runbook 시작 및 매개 변수 할당

Azure Portal에서 [runbook을 시작](start-runbooks.md#start-a-runbook-with-the-azure-portal) 하면 **runbook 시작** 블레이드가 열리고 만든 매개 변수에 대 한 값을 입력할 수 있습니다.

![포털을 사용하여 시작하기](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

입력 상자 아래의 레이블에 매개 변수 특성을 정의 하도록 설정 된 속성 (예: 필수 또는 선택, 유형, 기본값)을 볼 수 있습니다. 매개 변수 이름 옆에 있는 도움말 풍선을 사용 하면 매개 변수 입력 값에 대 한 결정을 내리는 데 필요한 키 정보도 정의 됩니다. 

> [!NOTE]
> 문자열 매개 변수는 문자열 형식의 빈 값을 지원 합니다. 입력 매개 변수 상자에 **[EmptyString]** 을 입력하면 빈 문자열을 매개 변수에 전달합니다. 또한 문자열 매개 변수는 Null을 지원 하지 않습니다. 문자열 매개 변수에 값을 전달 하지 않으면 PowerShell이 Null로 해석 합니다.

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>PowerShell cmdlet을 사용 하 여 게시 된 runbook 시작 및 매개 변수 할당

* **Azure Resource Manager cmdlet:** [AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.5.0
)를 사용 하 여 리소스 그룹에서 만든 Automation runbook을 시작할 수 있습니다.

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Azure 클래식 배포 모델 cmdlet:** [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook)을 사용하여 기본 리소스 그룹에 생성된 자동화 Runbook을 시작할 수 있습니다
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> PowerShell cmdlet을 사용 하 여 runbook을 시작 하는 경우 값 **powershell**을 사용 하 여 기본 매개 변수 *MicrosoftApplicationManagementStartedBy*가 만들어집니다. 이 매개 변수는 작업 세부 정보 창에서 볼 수 있습니다.  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>SDK를 사용 하 여 runbook 시작 및 매개 변수 할당

* **Azure Resource Manager 메서드:** 프로그래밍 언어의 SDK를 사용 하 여 runbook을 시작할 수 있습니다. 다음은 Automation 계정의 Runbook을 시작하기 위한 C# 코드 조각입니다. [GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs)에서 모든 코드를 볼 수 있습니다.  

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

* **Azure 클래식 배포 모델 방법:** 프로그래밍 언어의 SDK를 사용하여 Runbook을 시작할 수 있습니다. 다음은 Automation 계정의 Runbook을 시작하기 위한 C# 코드 조각입니다. [GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs)에서 모든 코드를 볼 수 있습니다.

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

   이 메서드를 시작 하려면 *VMName* 및 *resourceGroupName* runbook 매개 변수를 저장 하는 사전을 만듭니다. 그런 다음 Runbook을 시작합니다. 다음은 위에 정의된 메서드를 호출하기 위한 C# 코드 조각입니다.

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>REST API를 사용 하 여 runbook 시작 및 매개 변수 할당

다음 요청 URI와 함께 **PUT** 메서드를 사용 하 여 Azure Automation REST API 사용 하 여 runbook 작업을 만들고 시작할 수 있습니다 `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

요청 URI에서 다음 매개 변수를 바꿉니다.

* *subscriptionId*: AZURE 구독 ID입니다.  
* *resourceGroupName*: Automation 계정에 대 한 리소스 그룹의 이름입니다.
* *Automationaccountname*: 지정 된 클라우드 서비스 내에서 호스트 되는 Automation 계정의 이름입니다.  
* *jobName*: 작업에 대 한 GUID입니다. PowerShell의 Guid는 `[GUID]::NewGuid().ToString()*`을 사용 하 여 만들 수 있습니다.

Runbook 작업에 매개 변수를 전달 하려면 요청 본문을 사용 합니다. JSON 형식으로 제공 되는 다음 정보를 사용 합니다.

* Runbook 이름: 필수 항목입니다. 시작할 작업 시작에 대한 Runbook의 이름입니다.  
* Runbook 매개 변수: 선택 사항입니다. (Name, value) 형식의 매개 변수 목록에 대 한 사전입니다. 여기서 name은 문자열 형식 이며 값은 유효한 모든 JSON 값일 수 있습니다.

*VMName* 및 *resourceGroupName* 를 매개 변수로 사용 하 여 이전에 만든 **AzureVMTextual** runbook을 시작 하려는 경우 요청 본문에 대해 다음 JSON 형식을 사용 합니다.

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

작업이 성공적으로 만들어진 경우 HTTP 상태 코드 201이 반환됩니다. 응답 헤더 및 응답 본문에 대 한 자세한 내용은 REST API를 [사용 하 여 runbook 작업 만들기](/rest/api/automation/job/create)를 참조 하세요.

### <a name="test-a-runbook-and-assign-parameters"></a>Runbook 테스트 및 매개 변수 할당

테스트 옵션을 사용 하 여 [runbook의 초안 버전을 테스트](automation-testing-runbook.md) 하면 **테스트** 페이지가 열립니다. 이 페이지를 사용 하 여 만든 매개 변수에 대 한 값을 구성할 수 있습니다.

![매개 변수 테스트 및 할당](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Runbook에 일정 연결 및 매개 변수 할당

Runbook이 특정 시간에 시작하도록 Runbook에 [일정을 연결](automation-schedules.md)할 수 있습니다. 일정을 만드는 동안 입력 매개 변수를 할당하고 Runbook이 일정에 따라 시작될 때 이러한 값을 사용합니다. 모든 필수 매개 변수 값이 제공 될 때까지 일정을 저장할 수 없습니다.

![매개 변수 예약 및 할당](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Runbook에 대한 webhook 만들기 및 매개 변수 할당

Runbook에 대한 [webhook](automation-webhooks.md) 을 만들고 Runbook 입력 매개 변수를 구성할 수 있습니다. 모든 필수 매개 변수 값이 제공 될 때까지 webhook을 저장할 수 없습니다.

![Webhook 만들기 및 매개 변수 할당](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

웹 후크를 사용 하 여 runbook을 실행 하면 사용자가 정의 하는 입력 매개 변수와 함께 미리 정의 된 입력 매개 변수 *[WebhookData](automation-webhooks.md#details-of-a-webhook)* 이 전송 됩니다. 

![WebhookData 매개 변수](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="passing-a-json-object-to-a-runbook"></a>Runbook에 JSON 개체 전달

JSON 파일에서 Runbook에 전달하려는 데이터를 저장하는 것이 유용할 수 있습니다. 예를 들어 runbook에 전달 하려는 모든 매개 변수를 포함 하는 JSON 파일을 만들 수 있습니다. 이렇게 하려면 JSON 코드를 문자열로 변환한 후 runbook에 전달 하기 전에 문자열을 PowerShell 개체로 변환 해야 합니다.

이 섹션에서는 PowerShell 스크립트에서 [AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) 를 호출 하 여 powershell runbook을 시작 하 고 JSON 파일의 내용을 runbook에 전달 하는 예를 사용 합니다. PowerShell runbook은 JSON 개체에서 VM에 대 한 매개 변수를 검색 하 여 Azure VM을 시작 합니다.

### <a name="create-the-json-file"></a>JSON 파일 만들기

텍스트 파일에 다음 코드를 입력 하 고 로컬 컴퓨터의 어딘가에 `test.json` 저장 합니다.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Runbook 만들기

Azure Automation에서 **Test-Json** 이라는 새 PowerShell runbook을 만듭니다. [내 첫 번째 PowerShell runbook](automation-first-runbook-textual-powershell.md)을 참조 하세요.

JSON 데이터를 허용하려면 Runbook에서 개체를 입력 매개 변수로 사용해야 합니다. 그런 다음 runbook은 JSON 파일에 정의 된 속성을 사용할 수 있습니다.

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

1. 표시 된 것 처럼 Azure에 로그인 합니다. 그런 다음 Azure 자격 증명을 입력 하 라는 메시지가 표시 됩니다.

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >PowerShell runbook의 경우 **AzAccount** 및 **connect-azurermaccount** 는 **AzAccount**에 대 한 별칭입니다. 그래픽 runbook에는 이러한 별칭을 사용할 수 없습니다. 그래픽 runbook은 **AzAccount** 자체를 사용할 수 있습니다.

1. 저장 된 JSON 파일의 콘텐츠를 가져와 문자열로 변환 합니다. `JsonPath`는 JSON 파일을 저장한 경로입니다.

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. `$json`의 문자열 콘텐츠를 PowerShell 개체로 변환 합니다.

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. **AzAutomationRunbook**에 대 한 매개 변수에 대 한 해시 테이블을 만듭니다. 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   *매개 변수* 값을 JSON 파일의 값을 포함 하는 PowerShell 개체로 설정 하 고 있습니다.
1. Runbook을 시작 합니다.

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>다음 단계

* Runbook을 시작하는 다양한 방법에 대한 자세한 내용은 [Runbook 시작](automation-starting-a-runbook.md)을 참조하세요.
* 텍스트 Runbook을 편집하려면 [텍스트 Runbook 편집](automation-edit-textual-runbook.md)을 참조하세요.
* 그래픽 Runbook을 편집하려면 [Azure Automation에서 그래픽 작성](automation-graphical-authoring-intro.md)을 참조하세요.
