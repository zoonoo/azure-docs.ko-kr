---
title: Azure에서 업데이트 관리 배포의 사전 스크립트 및 사후 스크립트 관리
description: 이 문서에서는 업데이트 배포에 대 한 사전 스크립트 및 사후 스크립트를 구성 하 고 관리 하는 방법을 설명 합니다.
services: automation
ms.subservice: update-management
ms.date: 05/17/2019
ms.topic: conceptual
ms.openlocfilehash: 00cde5255f9c9a2baa7c7042ae2a8f73448da0ae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679980"
---
# <a name="manage-pre-scripts-and-post-scripts"></a>사전 스크립트 및 사후 스크립트 관리

사전 스크립트 및 사후 스크립트는 runbook이 (사전 작업) 이전에 Azure Automation 계정에서 실행 되 고 업데이트 배포 후 (사후 작업)에 실행 됩니다. 사전 스크립트 및 사후 스크립트는 로컬이 아닌 Azure 컨텍스트에서 실행 됩니다. 사전 스크립트는 업데이트 배포를 시작할 때 실행 됩니다. 사후 스크립트는 배포 종료 시와 구성 된 다시 부팅 후에 실행 됩니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대한 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법](automation-update-azure-modules.md)을 사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="pre-script-and-post-script-requirements"></a>사전 스크립트 및 사후 스크립트 요구 사항

Runbook을 사전 스크립트 또는 사후 스크립트로 사용 하려면 해당 runbook을 Automation 계정으로 가져온 후 [runbook을 게시](manage-runbooks.md#publishing-a-runbook)해야 합니다.

## <a name="pre-script-and-post-script-parameters"></a>사전 스크립트 및 사후 스크립트 매개 변수

사전 스크립트 및 사후 스크립트를 구성 하는 경우 runbook을 예약 하는 것 처럼 매개 변수를 전달할 수 있습니다. 매개 변수는 업데이트 배포를 만들 때 정의됩니다. 사전 스크립트 및 사후 스크립트는 다음 형식을 지원 합니다.

* [char]
* [byte]
* [int]
* [long]
* [decimal]
* [single]
* [double]
* [DateTime]
* [string]

사전 스크립트 및 사후 스크립트 runbook 매개 변수는 부울, 개체 또는 배열 형식을 지원 하지 않습니다. 이러한 값은 runbook을 실패 시킵니다. 

다른 개체 형식이 필요한 경우 Runbook에서 사용자 고유의 논리를 사용하여 다른 형식으로 캐스트할 수 있습니다.

표준 runbook 매개 변수 외에도 `SoftwareUpdateConfigurationRunContext` 매개 변수 (JSON 문자열 형식)가 제공 됩니다. 사전 스크립트 또는 사후 스크립트 runbook에서 매개 변수를 정의 하는 경우 업데이트 배포가 자동으로 전달 됩니다. 매개 변수는 [SOFTWAREUPDATECONFIGURATIONS API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration)에서 반환 하는 정보의 하위 집합인 업데이트 배포에 대 한 정보를 포함 합니다. 다음 섹션에서는 연결 된 속성을 정의 합니다.

### <a name="softwareupdateconfigurationruncontext-properties"></a>SoftwareUpdateConfigurationRunContext 속성

|속성  |Description  |
|---------|---------|
|SoftwareUpdateConfigurationName     | 소프트웨어 업데이트 구성의 이름입니다.        |
|SoftwareUpdateConfigurationRunId     | 실행의 고유 ID입니다.        |
|SoftwareUpdateConfigurationSettings     | 소프트웨어 업데이트 구성과 관련 된 속성의 컬렉션입니다.         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | 업데이트 배포를 대상으로 하는 운영 체제입니다.         |
|SoftwareUpdateConfigurationSettings.duration     | 업데이트 배포의 최대 기간은 ISO8601 `PT[n]H[n]M[n]S` 로 실행 됩니다. 유지 관리 기간이 라고도 합니다.          |
|SoftwareUpdateConfigurationSettings.Windows     | Windows 컴퓨터와 관련 된 속성의 컬렉션입니다.         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | 업데이트 배포에서 제외 되는 Kb의 목록입니다.        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | 업데이트 배포를 위해 선택한 업데이트 분류입니다.        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | 업데이트 배포를 위한 다시 부팅 설정입니다.        |
|azureVirtualMachines     | 업데이트 배포의 Azure Vm에 대 한 Resourceid의 목록입니다.        |
|nonAzureComputerNames|업데이트 배포의 비 Azure 컴퓨터 Fqdn 목록입니다.|

다음 예제는 **SoftwareUpdateConfigurationRunContext** 매개 변수에 전달되는 JSON 문자열입니다.

```json
"SoftwareUpdateConfigurationRunContext":{
      "SoftwareUpdateConfigurationName":"sampleConfiguration",
      "SoftwareUpdateConfigurationRunId":"00000000-0000-0000-0000-000000000000",
      "SoftwareUpdateConfigurationSettings":{
         "operatingSystem":"Windows",
         "duration":"PT2H0M",
         "windows":{
            "excludedKbNumbers":[
               "168934",
               "168973"
            ],
            "includedUpdateClassifications":"Critical",
            "rebootSetting":"IfRequired"
         },
         "azureVirtualMachines":[
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-01",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-02",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-03"
         ],
         "nonAzureComputerNames":[
            "box1.contoso.com",
            "box2.contoso.com"
         ]
      }
   }
```

모든 속성을 포함 하는 전체 예제는 [이름으로 소프트웨어 업데이트 구성 가져오기](/rest/api/automation/softwareupdateconfigurations/getbyname#examples)에서 찾을 수 있습니다.

> [!NOTE]
> 개체 `SoftwareUpdateConfigurationRunContext` 는 컴퓨터에 대해 중복 된 항목을 포함할 수 있습니다. 이로 인해 사전 스크립트와 사후 스크립트가 동일한 컴퓨터에서 여러 번 실행 될 수 있습니다. 이 동작을 해결 하려면를 사용 `Sort-Object -Unique` 하 여 고유한 VM 이름만 선택 합니다.

## <a name="using-a-pre-script-or-post-script-in-a-deployment"></a>배포에 사전 스크립트 또는 사후 스크립트 사용

업데이트 배포에서 사전 스크립트 또는 사후 스크립트를 사용 하려면 먼저 업데이트 배포를 만듭니다. **사전 스크립트 + 사후 스크립트**를 선택 합니다. 그러면 **사전 스크립트 + 사후 스크립트 선택** 페이지가 열립니다.

![스크립트 선택](./media/pre-post-scripts/select-scripts.png)

사용할 스크립트를 선택 합니다. 이 예제에서는 **UpdateManagement-vm** runbook을 사용 합니다. Runbook을 선택 하면 **스크립트 구성** 페이지가 열립니다. **사전 스크립트**를 선택 하 고 **확인**을 선택 합니다.

**UpdateManagement-TurnOffVms** 스크립트에 대해 이 프로세스를 반복합니다. 그러나 **스크립트 유형을**선택 하는 경우 **사후 스크립트**를 선택 합니다.

이제 **선택한 항목** 섹션에는 선택한 스크립트가 모두 표시 됩니다. 하나는 사전 스크립트이 고 다른 하나는 사후 스크립트입니다.

![선택한 항목](./media/pre-post-scripts/selected-items.png)

업데이트 배포 구성을 완료 합니다.

업데이트 배포가 완료 되 면 **업데이트** 배포로 이동 하 여 결과를 볼 수 있습니다. 여기에서 볼 수 있듯이 사전 스크립트 및 사후 스크립트에 대 한 상태가 제공 됩니다.

![업데이트 결과](./media/pre-post-scripts/update-results.png)

업데이트 배포 실행을 선택 하 여 사전 스크립트 및 사후 스크립트에 대 한 추가 세부 정보를 볼 수 있습니다. 실행 시 스크립트 원본에 대한 링크가 제공됩니다.

![배포 실행 결과](./media/pre-post-scripts/deployment-run.png)

스크립트에 있습니다.

## <a name="stopping-a-deployment"></a>배포 중지

사전 스크립트를 기반으로 배포를 중지 하려면 예외를 [throw](automation-runbook-execution.md#throw) 해야 합니다. 그렇지 않으면 배포 및 사후 스크립트가 계속 실행 됩니다. 다음 코드 조각에서는 예외를 throw 하는 방법을 보여 줍니다.

```powershell
#In this case, we want to terminate the patch job if any run fails.
#This logic might not hold for all cases - you might want to allow success as long as at least 1 run succeeds
foreach($summary in $finalStatus)
{
    if ($summary.Type -eq "Error")
    {
        #We must throw in order to fail the patch deployment.
        throw $summary.Summary
    }
}
```



## <a name="interacting-with-machines"></a>컴퓨터와 상호 작용

사전 스크립트 및 사후 작업은 Automation 계정에서 runbook으로 실행 되며 배포의 컴퓨터에서 직접 실행 되지 않습니다. 사전 작업 및 사후 작업은 Azure 컨텍스트에서도 실행 되며 비 Azure 컴퓨터에 대 한 액세스 권한이 없습니다. 다음 섹션에서는 Azure Vm 또는 비 Azure 컴퓨터와 상관 없이 컴퓨터와 직접 상호 작용할 수 있는 방법을 보여 줍니다.

### <a name="interact-with-azure-machines"></a>Azure 컴퓨터와 상호 작용

사전 작업 및 사후 작업은 runbook으로 실행 되며 배포의 Azure Vm에서 기본적으로 실행 되지 않습니다. Azure Vm과 상호 작용 하려면 다음 항목이 있어야 합니다.

* 실행 계정
* 실행 하려는 runbook

Azure 컴퓨터와 상호 작용 하려면 [AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0) cmdlet을 사용 하 여 azure vm과 상호 작용 해야 합니다. 이 작업을 수행 하는 방법에 대 한 예는 runbook 예제 [업데이트 관리 – run 명령을 사용 하 여 스크립트 실행](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc)을 참조 하세요.

### <a name="interact-with-non-azure-machines"></a>비 Azure 컴퓨터와 상호 작용

사전 작업 및 사후 작업은 Azure 컨텍스트에서 실행 되며 비 Azure 컴퓨터에 대 한 액세스 권한이 없습니다. 비 Azure 컴퓨터와 상호 작용 하려면 다음 항목이 있어야 합니다.

* 실행 계정
* 머신에 설치된 Hybrid Runbook Worker
* 로컬에서 실행하려는 Runbook
* 부모 runbook

비 Azure 컴퓨터와 상호 작용 하기 위해 부모 runbook은 Azure 컨텍스트에서 실행 됩니다. 이 runbook은 [AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) cmdlet을 사용 하 여 자식 runbook을 호출 합니다. `RunOn` 매개 변수를 지정하고, 스크립트가 실행될 Hybrid Runbook Worker의 이름을 제공해야 합니다. Runbook 예제 [업데이트 관리 – 로컬에서 스크립트 실행](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44)을 참조 하세요.

## <a name="aborting-patch-deployment"></a>패치 배포 중단

사전 스크립트가 오류를 반환 하는 경우 배포를 중단할 수 있습니다. 이렇게 하려면 오류를 구성 하는 논리에 대 한 스크립트에서 오류를 [throw](/powershell/module/microsoft.powershell.core/about/about_throw) 해야 합니다.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="samples"></a>샘플

사전 스크립트 및 사후 스크립트에 대 한 샘플은 [스크립트 센터 갤러리](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) 와 [PowerShell 갤러리](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22)에서 찾을 수 있거나 Azure Portal를 통해 가져올 수 있습니다. 이렇게 하려면 Automation 계정에서 **프로세스 자동화**아래에 있는 **runbook 갤러리**를 선택 합니다. 필터에 대해 **업데이트 관리**를 사용합니다.

![갤러리 목록](./media/pre-post-scripts/runbook-gallery.png)

또는 다음 목록에 표시 된 것 처럼 해당 스크립트 이름으로 검색할 수 있습니다.

* 업데이트 관리 - VM 켜기
* 업데이트 관리 - VM 끄기
* 업데이트 관리 - 로컬에서 스크립트 실행
* 업데이트 관리 - 사전/사후 스크립트에 대한 템플릿
* 업데이트 관리 - 실행 명령을 사용하여 스크립트 실행

> [!IMPORTANT]
> Runbook을 가져온 후에는 runbook을 사용할 수 있도록 먼저 게시 해야 합니다. 이렇게 하려면 Automation 계정에서 runbook을 찾고 **편집**을 선택한 다음 **게시**를 선택 합니다.

샘플은 모두 다음 예제에 정의 된 기본 템플릿을 기반으로 합니다. 이 템플릿을 사용 하 여 사전 스크립트 및 사후 스크립트와 함께 사용할 사용자 고유의 runbook을 만들 수 있습니다. Azure를 사용 하 여 인증 하 고 매개 변수 `SoftwareUpdateConfigurationRunContext` 를 처리 하는 데 필요한 논리가 포함 됩니다.

```powershell
<#
.SYNOPSIS
 Barebones script for Update Management Pre/Post

.DESCRIPTION
  This script is intended to be run as a part of Update Management pre/post-scripts.
  It requires a RunAs account.

.PARAMETER SoftwareUpdateConfigurationRunContext
  This is a system variable which is automatically passed in by Update Management during a deployment.
#>

param(
    [string]$SoftwareUpdateConfigurationRunContext
)
#region BoilerplateAuthentication
#This requires a RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
#endregion BoilerplateAuthentication

#If you wish to use the run context, it must be converted from JSON
$context = ConvertFrom-Json  $SoftwareUpdateConfigurationRunContext
#Access the properties of the SoftwareUpdateConfigurationRunContext
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines | Sort-Object -Unique
$runId = $context.SoftwareUpdateConfigurationRunId

Write-Output $context

#Example: How to create and write to a variable using the pre-script:
<#
#Create variable named after this run so it can be retrieved
New-AzAutomationVariable -ResourceGroupName $ResourceGroup –AutomationAccountName $AutomationAccount –Name $runId -Value "" –Encrypted $false
#Set value of variable
Set-AutomationVariable –Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

> [!NOTE]
> 비그래픽 PowerShell runbook의 경우 `Add-AzAccount` 및 `Add-AzureRMAccount` 는 [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0)에 대 한 별칭입니다. 이러한 cmdlet을 사용하거나 Automation 계정의 [모듈을 최신 버전으로 업데이트](automation-update-azure-modules.md)할 수 있습니다. 새 Automation 계정을 만든 경우에도 모듈을 업데이트해야 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Windows 가상 머신에 대 한 업데이트를 관리 하는 방법을 알아보려면 다음 자습서로 이동 하세요.

> [!div class="nextstepaction"]
> [Azure Windows VM에 대한 업데이트 및 패치 관리](automation-tutorial-update-management.md)