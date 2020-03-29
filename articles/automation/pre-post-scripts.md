---
title: Azure에서 업데이트 관리 배포에 대한 사전 및 사후 스크립트 구성
description: 이 문서에서는 업데이트 배포를 위해 사전 스크립트 및 사후 스크립트를 구성하고 관리하는 방법을 설명합니다.
services: automation
ms.subservice: update-management
ms.date: 05/17/2019
ms.topic: conceptual
ms.openlocfilehash: 35fba966fcdb6d1c5cd7c531bb22c9c78ae16ff3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75417793"
---
# <a name="manage-pre-and-post-scripts"></a>스크립트 전과 후 관리

사전 스크립트 및 사후 스크립트를 사용하면 업데이트 배포 전(사전 작업) 및 사후(작업 후)에서 Azure Automation 계정에서 PowerShell Runbook을 실행할 수 있습니다. 사전 및 사후 스크립트는 로컬이 아닌 Azure 컨텍스트에서 실행됩니다. 사전 스크립트는 업데이트 배포가 시작될 때 실행됩니다. 사후 스크립트는 배포가 끝나고 구성된 재부팅 후에 실행됩니다.

## <a name="runbook-requirements"></a>Runbook 요구 사항

Runbook을 사전 또는 사후 스크립트로 사용하려면 Runbook을 자동화 계정으로 가져와서 게시해야 합니다. 이 프로세스에 대한 자세한 내용은 [Runbook 게시를](manage-runbooks.md#publish-a-runbook)참조하십시오.

## <a name="using-a-pre-script-or-post-script"></a>사전 스크립트 또는 사후 스크립트 사용

업데이트 배포에서 사전 스크립트 또는 사후 스크립트를 사용하려면 업데이트 배포를 만드는 것으로 시작합니다. **사전 스크립트 + 사후 스크립트를 선택합니다.** 그러면 **사전 스크립트 + 사후 스크립트 선택** 페이지가 열립니다.

![스크립트 선택](./media/pre-post-scripts/select-scripts.png)

사용할 스크립트를 선택합니다. 이 예제에서는 **UpdateManagement-TurnOnVms** 실행책을 사용합니다. Runbook을 선택하면 스크립트 **구성** 페이지가 열립니다. **스크립트 이전**을 선택한 다음 **확인을**선택합니다.

**UpdateManagement-TurnOffVms** 스크립트에 대해 이 프로세스를 반복합니다. 그러나 **스크립트 유형을**선택하면 **스크립트 후**를 선택합니다.

이제 **선택한 항목** 섹션에 선택한 스크립트가 모두 표시됩니다. 하나는 사전 스크립트이고 다른 하나는 사후 스크립트입니다.

![선택한 항목](./media/pre-post-scripts/selected-items.png)

업데이트 배포 구성을 완료합니다.

업데이트 배포가 완료되면 **업데이트 배포로** 이동하여 결과를 볼 수 있습니다. 당신이 볼 수 있듯이, 상태는 사전 스크립트 및 사후 스크립트에 대 한 제공 됩니다.

![업데이트 결과](./media/pre-post-scripts/update-results.png)

업데이트 배포 실행을 선택하면 사전 및 사후 스크립트에 추가 세부 정보가 표시됩니다. 실행 시 스크립트 원본에 대한 링크가 제공됩니다.

![배포 실행 결과](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>매개 변수 전달

스크립트 이전 및 사후 를 구성할 때 Runbook 을 예약하는 것과 같은 매개 변수를 전달할 수 있습니다. 매개 변수는 업데이트 배포를 만들 때 정의됩니다. 스크립트 이전 및 사후 는 다음 형식을 지원합니다.

* [char]
* [byte]
* [int]
* [long]
* [decimal]
* [single]
* [double]
* [DateTime]
* [string]

다른 개체 형식이 필요한 경우 Runbook에서 사용자 고유의 논리를 사용하여 다른 형식으로 캐스트할 수 있습니다.

표준 Runbook 매개 변수 외에도 다른 매개 변수가 제공됩니다: **SoftwareUpdateConfigurationRunContext**

이 매개 변수는 JSON 문자열이며 사전 또는 사후 스크립트에서 매개 변수를 정의하는 경우 업데이트 배포에 의해 자동으로 전달됩니다. 매개 변수에는 [SoftwareUpdateconfigurations API에서](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration)반환되는 정보의 하위 집합인 업데이트 배포에 대한 정보가 포함되어 있습니다. 

다음 표에서는 변수에 제공된 속성을 보여 주며,

### <a name="softwareupdateconfigurationruncontext-properties"></a>SoftwareUpdateConfigurationRunContext 속성

|속성  |설명  |
|---------|---------|
|SoftwareUpdateConfigurationName     | 소프트웨어 업데이트 구성의 이름입니다.        |
|SoftwareUpdateConfigurationRunId     | 실행에 대한 고유 ID입니다.        |
|SoftwareUpdateConfigurationSettings     | 소프트웨어 업데이트 구성과 관련된 속성의 컬렉션입니다.         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | 업데이트 배포를 대상으로 하는 운영 체제입니다.         |
|SoftwareUpdateConfigurationSettings.duration     | 업데이트 배포의 최대 기간은 `PT[n]H[n]M[n]S` ISO8601에 따라 실행됩니다. *유지 관리 기간이라고도*합니다.          |
|SoftwareUpdateConfigurationSettings.Windows     | Windows 컴퓨터와 관련된 속성의 컬렉션입니다.         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | 업데이트 배포에서 제외된 B의 목록입니다.        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | 업데이트 배포에 대해 선택한 분류를 업데이트합니다.        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | 업데이트 배포에 대한 설정을 다시 부팅합니다.        |
|azureVirtualMachines     | 업데이트 배포에서 Azure VM에 대한 resourceId 의 목록입니다.        |
|nonAzureComputerNames|업데이트 배포에서 Azure가 아닌 컴퓨터 FQDNs의 목록입니다.|

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

모든 속성이 있는 전체 예제는 다음에서 [찾을](/rest/api/automation/softwareupdateconfigurations/getbyname#examples)수 있습니다.

> [!NOTE]
> 개체에는 `SoftwareUpdateConfigurationRunContext` 컴퓨터에 대한 중복 항목이 포함될 수 있습니다. 이로 인해 사전 및 사후 스크립트가 동일한 컴퓨터에서 여러 번 실행될 수 있습니다. 이 동작을 해결하려면 `Sort-Object -Unique` 스크립트에서 고유한 VM 이름만 선택하는 데 사용합니다.


## <a name="stopping-a-deployment"></a>배포 중지

사전 스크립트를 기반으로 배포를 중지하려면 예외를 [throw해야](automation-runbook-execution.md#throw) 합니다. 그렇지 않으면 배포 및 사후 스크립트가 계속 실행됩니다. 다음 코드 조각에서는 예외를 throw하는 방법을 보여 주며

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

## <a name="samples"></a>샘플

스크립트 이전 및 사후 스크립트에 대한 샘플은 [스크립트 센터 갤러리](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) 및 [PowerShell 갤러리에서](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22)찾을 수 있으며 Azure 포털을 통해 가져올 수 있습니다. 이렇게 하려면 자동화 계정, **프로세스 자동화에서** **Runbook 갤러리를**선택합니다. 필터에 대해 **업데이트 관리**를 사용합니다.

![갤러리 목록](./media/pre-post-scripts/runbook-gallery.png)

또는 다음 목록과 같이 스크립트 이름으로 검색할 수 있습니다.

* 업데이트 관리 - VM 켜기
* 업데이트 관리 - VM 끄기
* 업데이트 관리 - 로컬에서 스크립트 실행
* 업데이트 관리 - 사전/사후 스크립트에 대한 템플릿
* 업데이트 관리 - 실행 명령을 사용하여 스크립트 실행

> [!IMPORTANT]
> Runbook을 가져온 후 게시해야 사용할 수 있습니다. 이렇게 하려면 자동화 계정에서 runbook을 찾고 **편집을**선택한 다음 **게시를**선택합니다.

샘플은 모두 다음 예제에 정의된 기본 템플릿을 기반으로 합니다. 이 템플릿은 사전 및 사후 스크립트와 함께 사용할 사용자 고유의 Runbook을 만드는 데 사용할 수 있습니다. Azure를 사용 하 여 인증 하 `SoftwareUpdateConfigurationRunContext` 고 매개 변수를 처리 하는 데 필요한 논리가 포함 됩니다.

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

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
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
New-AzureRmAutomationVariable -ResourceGroupName $ResourceGroup –AutomationAccountName $AutomationAccount –Name $runId -Value "" –Encrypted $false
#Set value of variable
Set-AutomationVariable –Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

## <a name="interacting-with-machines"></a>기계와의 상호 작용

사전 및 사후 작업은 자동화 계정에서 실행되지 않고 배포의 컴퓨터에서 직접 실행되지 않습니다. 사전 및 사후 작업도 Azure 컨텍스트에서 실행되며 Azure 가 아닌 컴퓨터에 액세스할 수 없습니다. 다음 섹션에서는 Azure VM이든 Azure가 아닌 컴퓨터이든 관계없이 컴퓨터와 직접 상호 작용하는 방법을 보여 줍니다.

### <a name="interacting-with-azure-machines"></a>Azure 컴퓨터와의 상호 작용

사전 및 사후 작업은 런북으로 실행되며 기본적으로 배포의 Azure VM에서 실행되지 않습니다. Azure VM과 상호 작용하려면 다음 항목이 있어야 합니다.

* 실행 계정
* 실행하려는 Runbook

Azure 컴퓨터와 상호 작용하려면 [호출-AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) cmdlet을 사용하여 Azure VM과 상호 작용해야 합니다. 이 작업을 수행하는 방법에 대한 예는 Runbook 예제 [업데이트 관리 - Run 명령을 사용하여 스크립트를 실행합니다.](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc)

### <a name="interacting-with-non-azure-machines"></a>Azure가 아닌 컴퓨터와의 상호 작용

사전 및 사후 작업은 Azure 컨텍스트에서 실행되며 Azure 가 아닌 컴퓨터에 액세스할 수 없습니다. Azure가 아닌 컴퓨터와 상호 작용하려면 다음 항목이 있어야 합니다.

* 실행 계정
* 머신에 설치된 Hybrid Runbook Worker
* 로컬에서 실행하려는 Runbook
* 상위 런북

Azure가 아닌 컴퓨터와 상호 작용하기 위해 상위 Runbook은 Azure 컨텍스트에서 실행됩니다. 이 Runbook은 [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) cmdlet을 사용하여 자식 Runbook을 호출합니다. `-RunOn` 매개 변수를 지정하고, 스크립트가 실행될 Hybrid Runbook Worker의 이름을 제공해야 합니다. 자세한 내용은 Runbook 예제 [업데이트 관리 - 로컬에서 스크립트를 실행합니다.](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44)

## <a name="abort-patch-deployment"></a>패치 배포 중단

사전 스크립트에서 오류를 반환하는 경우 배포를 중단할 수 있습니다. 이렇게 하려면 오류를 구성하는 논리에 대해 스크립트에 오류를 [throw해야](/powershell/module/microsoft.powershell.core/about/about_throw) 합니다.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="known-issues"></a>알려진 문제

* 사전 및 사후 스크립트를 사용하는 경우 부울, 개체 또는 배열을 매개 변수에 전달할 수 없습니다. 이렇게 하면 Runbook이 실패합니다. 지원되는 형식의 전체 목록은 [매개 변수 전달을](#passing-parameters)참조하십시오.

## <a name="next-steps"></a>다음 단계

Windows 가상 컴퓨터에 대한 업데이트를 관리하는 방법을 알아보려면 다음 자습서로 이동하십시오.

> [!div class="nextstepaction"]
> [Azure Windows VM에 대한 업데이트 및 패치 관리](automation-tutorial-update-management.md)

