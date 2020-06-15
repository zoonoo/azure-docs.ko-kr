---
title: Azure Automation 공유 리소스 문제 해결
description: 이 문서에서는 Azure Automation 공유 리소스와 관련된 문제를 해결하는 방법을 설명합니다.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 5b87a98ed38e3af315789adffc11824f2522b802
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680878"
---
# <a name="troubleshoot-shared-resource-issues"></a>공유 리소스 문제 해결

이 문서에서는 Azure Automation에서 [공유 리소스](../automation-intro.md#shared-resources)를 사용할 때 발생할 수 있는 문제에 대해 설명합니다.

## <a name="modules"></a>모듈

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>시나리오: 가져오는 중에 모듈이 중단됨

#### <a name="issue"></a>문제

Azure Automation 모듈을 가져오거나 업데이트하면 모듈이 *가져오는 중* 상태에서 중단됩니다.

#### <a name="cause"></a>원인

PowerShell 모듈 가져오기는 복잡한 다단계 프로세스이므로 모듈을 올바르게 가져오지 못할 수 있으며 일시적인 상태에서 중단될 수 있습니다. 가져오기 프로세스에 대한 자세한 내용은 [PowerShell 모듈 가져오기](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process)를 참조하세요.

#### <a name="resolution"></a>해결 방법

이 문제를 해결하려면 [Remove-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationModule?view=azps-3.7.0) cmdlet을 사용하여 중단된 모듈을 제거해야 합니다. 그 후 모듈 가져오기를 다시 시도할 수 있습니다.

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>시나리오: 업데이트 시도 후 가져오는 중이 AzureRM 모듈이 중단됨

#### <a name="issue"></a>문제

AzureRM 모듈을 업데이트하려고 하면 다음 메시지가 포함된 배너가 계정에 남아 있습니다.

```error
Azure modules are being updated
```

#### <a name="cause"></a>원인

Automation 계정에서 AzureRM 모듈을 업데이트하는 것과 관련된 알려진 문제가 있습니다. 특히 모듈이 0으로 시작하는 숫자 이름의 리소스 그룹에 있으면 이 문제가 발생합니다.

#### <a name="resolution"></a>해결 방법

Automation 계정에서 AzureRM 모듈을 업데이트하려면 계정이 영숫자 이름의 리소스 그룹에 있어야 합니다. 0으로 시작하는 숫자 이름의 리소스 그룹은 현재 AzureRM 모듈을 업데이트할 수 없습니다.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>시나리오: 모듈이 가져오기를 실패하거나 가져오기를 실행한 후 cmdlet을 실행할 수 없음

#### <a name="issue"></a>문제

모듈에서 가져오기에 실패하거나 성공하지만 cmdlet이 추출되지 않습니다.

#### <a name="cause"></a>원인

모듈이 Azure Automation을 가져오지 못하는 몇 가지 일반적인 이유는 다음과 같습니다.

* 구조가 Automation에 필요한 구조와 일치하지 않습니다.
* 모듈이 Automation 계정에 배포되지 않은 다른 모듈에 종속되어 있습니다.
* 모듈 폴더에 종속성이 없습니다.
* [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationModule?view=azps-3.7.0) cmdlet을 사용하여 모듈을 업로드하고 있으며, 전체 스토리지 경로를 제공하지 않았거나 공개적으로 액세스할 수 있는 URL을 사용하여 모듈을 로드하지 않았습니다.

#### <a name="resolution"></a>해결 방법

이 문제를 해결하려면 다음 솔루션 중 하나를 사용합니다.

* 모듈에서 ModuleName.zip -> ModuleName 또는 버전 번호 -> (ModuleName.psm1, ModuleName.psd1) 형식을 따르는지 확인합니다.
* **.psd1** 파일을 열고, 종속성이 모듈에 있는지 확인합니다. 종속성이 있으면 이러한 모듈을 Automation 계정에 업로드합니다.
* 참조되는 **.dll** 파일이 모듈 폴더에 있는지 확인합니다.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>시나리오: 모듈을 업데이트할 때 Update-AzureModule.ps1이 일시 중단됨

#### <a name="issue"></a>문제

[Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) Runbook을 사용하여 Azure 모듈을 업데이트하면 모듈 업데이트 프로세스가 일시 중단됩니다.

#### <a name="cause"></a>원인

이 Runbook의 경우 동시에 업데이트되는 모듈 수를 결정하는 기본 설정은 10입니다. 동시에 너무 많은 모듈을 업데이트하면 업데이트 프로세스에서 오류가 발생할 가능성이 높습니다.

#### <a name="resolution"></a>해결 방법

모든 AzureRM 또는 Az 모듈이 동일한 Automation 계정에 필요한 것은 일반적이지 않습니다. 필요한 특정 모듈만 가져와야 합니다.

> [!NOTE]
> 포함된 모든 모듈을 가져오는 `Az.Automation` 또는 `AzureRM.Automation` 모듈 전체를 가져오지 않도록 하세요.

업데이트 프로세스가 일시 중단되면 `SimultaneousModuleImportJobCount` 매개 변수를 **Update-AzureModules.ps1** 스크립트에 추가하고 10(기본값)보다 낮은 값을 제공합니다. 이 논리를 구현하는 경우 3 또는 5의 값으로 시작해 보세요. `SimultaneousModuleImportJobCount`는 Azure 모듈을 업데이트하는 데 사용되는 **Update-AutomationAzureModulesForAccount** 시스템 Runbook의 매개 변수입니다. 이렇게 조정하면 업데이트 프로세스가 더 오래 실행되지만 완료될 가능성이 더 높아집니다. 다음 예제에서는 매개 변수 및 Runbook에서 매개 변수를 삽입할 위치를 보여줍니다.

 ```powershell
         $Body = @"
            {
               "properties":{
               "runbook":{
                   "name":"Update-AutomationAzureModulesForAccount"
               },
               "parameters":{
                    ...
                    "SimultaneousModuleImportJobCount":"3",
                    ... 
               }
              }
           }
"@
```

## <a name="run-as-accounts"></a>실행 계정

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>시나리오: 실행 계정을 만들거나 업데이트할 수 없음

#### <a name="issue"></a>문제

실행 계정을 만들거나 업데이트하려고 하면 다음과 비슷한 오류가 표시됩니다.

```error
You do not have permissions to create…
```

#### <a name="cause"></a>원인

실행 계정을 만들거나 업데이트하는 데 필요한 권한이 없거나 리소스가 리소스 그룹 수준에서 잠겨 있습니다.

#### <a name="resolution"></a>해결 방법

실행 계정을 만들거나 업데이트하려면 실행 계정에서 사용되는 다양한 리소스에 대한 적절한 [권한](../manage-runas-account.md#permissions)이 있어야 합니다. 

잠금으로 인해 문제가 발생한 경우 잠금을 제거할 수 있는지 확인합니다. 그런 다음, Azure Portal에서 잠겨 있는 리소스로 이동하여 마우스 오른쪽 단추로 잠금을 클릭하고, **삭제**를 선택합니다.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>시나리오: Runbook을 실행하면 "'iplpapi.dll' DLL에서 'GetPerAdapterInfo'라는 진입점을 찾을 수 없습니다"라는 오류가 표시됨

#### <a name="issue"></a>문제

Runbook을 실행하면 다음과 같은 예외가 표시됩니다.

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>원인

이 오류는 잘못 구성된 [실행 계정](../manage-runas-account.md)으로 인해 발생할 수 있습니다.

#### <a name="resolution"></a>해결 방법

실행 계정이 올바르게 구성되어 있는지 확인합니다. 그런 다음, Azure 인증에 적합한 코드가 Runbook에 있는지 확인합니다. 다음 예제에서는 실행 계정을 사용하여 Runbook에서 Azure에 인증하는 코드 조각을 보여 줍니다.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>다음 단계

이 문서에서 문제가 해결되지 않으면 다음 채널 중 하나를 시도하여 추가 지원을 받습니다.

* [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가의 답변을 얻습니다.
* [@AzureSupport](https://twitter.com/azuresupport)에 연결합니다. 이는 Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하기 위한 공식 Microsoft Azure 계정입니다.
* Azure 지원 인시던트 제출 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동하여 **지원 받기**를 선택합니다.

